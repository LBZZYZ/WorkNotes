---
title: TeamTalk-1 db_porxy_server
date: 2021-03-06 23:56:00
tags: 
	- Im
	- TeamTalk
categories: TeamTalk
---


## 1. 初始化redis缓存数据库   
```bash
	CacheManager* pCacheManager = CacheManager::getInstance();
	if (!pCacheManager) {
		log("CacheManager init failed");
		return -1;
	}
```
进到CacheManager，这里用了一个```单例模式```得到CacheManager的实例
```bash
CacheManager* CacheManager::getInstance()
{
	if (!s_cache_manager) {
		s_cache_manager = new CacheManager();
		if (s_cache_manager->Init()) {
			delete s_cache_manager;
			s_cache_manager = NULL;
		}
	}

	return s_cache_manager;
}
```
上面的s_cache_manager是CacheManager类的私有static变量```static CacheManager* 	s_cache_manager;```

由于是单例,因此CacheManager的构造函数是一个private的空构造，实际上的初始化步骤在Init内


```bash
int CacheManager::Init()
{
	CConfigFileReader config_file("dbproxyserver.conf");

	char* cache_instances = config_file.GetConfigName("CacheInstances");
	if (!cache_instances) {
		log("not configure CacheIntance");
		return 1;
	}

	char host[64];
	char port[64];
	char db[64];
    char maxconncnt[64];
	CStrExplode instances_name(cache_instances, ',');
	for (uint32_t i = 0; i < instances_name.GetItemCnt(); i++) {
		char* pool_name = instances_name.GetItem(i);
		//printf("%s", pool_name);
		snprintf(host, 64, "%s_host", pool_name);
		snprintf(port, 64, "%s_port", pool_name);
		snprintf(db, 64, "%s_db", pool_name);
        snprintf(maxconncnt, 64, "%s_maxconncnt", pool_name);

		char* cache_host = config_file.GetConfigName(host);
		char* str_cache_port = config_file.GetConfigName(port);
		char* str_cache_db = config_file.GetConfigName(db);
        char* str_max_conn_cnt = config_file.GetConfigName(maxconncnt);
		if (!cache_host || !str_cache_port || !str_cache_db || !str_max_conn_cnt) {
			log("not configure cache instance: %s", pool_name);
			return 2;
		}

		CachePool* pCachePool = new CachePool(pool_name, cache_host, atoi(str_cache_port),
				atoi(str_cache_db), atoi(str_max_conn_cnt));
		if (pCachePool->Init()) {
			log("Init cache pool failed");
			return 3;
		}

		m_cache_pool_map.insert(make_pair(pool_name, pCachePool));
	}

	return 0;
}
```

init函数有点长，具体可解释为以下几个步骤
```bash
init
{
    #1.解析配置文件dbproxyserver.conf，获取缓存数据库实例
    #  CacheInstances=unread,group_set,token,sync,group_member

    #2. 根据配置文件的实例建立数据库连接池
    #CachePool* pCachePool = new CachePool(pool_name, cache_host, atoi(str_cache_port),
	#			atoi(str_cache_db), atoi(str_max_conn_cnt));
	#if (pCachePool->Init()) {
	#	log("Init cache pool failed");
	#	return 3;
	#}

    #3. 将建立好的池子加入映射表，name和池子指针的映射，这张map属于CacheManager类，便于对池子管理
    map<string, CachePool*>	m_cache_pool_map;
}



```
这里把几个Cache instance列出来方便后续梳理   
 | Instance Name | Comment |
 | ---- | ---- |
 | unread | |
 | group_set | |
 | token | |
 | sync | |
 | group_member | |






下面走进CachePool类的构造函数和init函数，先看构造，很简单，单纯的给成员变量赋值而已
```bash
CachePool::CachePool(const char* pool_name, const char* server_ip, int server_port, int db_num, int max_conn_cnt)
{
	m_pool_name = pool_name;
	m_server_ip = server_ip;
	m_server_port = server_port;
	m_db_num = db_num;
	m_max_conn_cnt = max_conn_cnt;
	m_cur_conn_cnt = MIN_CACHE_CONN_CNT;
}
```
接下来是CachePool的init函数,这是关键，通过对m_cur_conn_cnt的判断，建立这么多根连接，进到真正去连接数据库的CacheConn类，然后很关键：把建立好的新连接Push back进去一个m_free_list，这是CachePool维护的一个空闲连接的链表

```bash
int CachePool::Init()
{
	for (int i = 0; i < m_cur_conn_cnt; i++) {
		CacheConn* pConn = new CacheConn(this);
		if (pConn->Init()) {
			delete pConn;
			return 1;
		}

		m_free_list.push_back(pConn);
	}

	log("cache pool: %s, list size: %lu", m_pool_name.c_str(), m_free_list.size());
	return 0;
}
```

真正去建立连接的构造函数，这里面有个4s重连的机制，要注意一下m_last_connect_time

```bash
CacheConn::CacheConn(CachePool* pCachePool)
{
	m_pCachePool = pCachePool;
	m_pContext = NULL;
	m_last_connect_time = 0;
}
```

CacheConn的init函数
```bash
int CacheConn::Init()
{
	if (m_pContext) {
		return 0;
	}

	// 4s 尝试重连一次
	uint64_t cur_time = (uint64_t)time(NULL);
	if (cur_time < m_last_connect_time + 4) {
		return 1;
	}

	m_last_connect_time = cur_time;

	// 200ms超时
	struct timeval timeout = {0, 200000};
	m_pContext = redisConnectWithTimeout(m_pCachePool->GetServerIP(), m_pCachePool->GetServerPort(), timeout);
	if (!m_pContext || m_pContext->err) {
		if (m_pContext) {
			log("redisConnect failed: %s", m_pContext->errstr);
			redisFree(m_pContext);
			m_pContext = NULL;
		} else {
			log("redisConnect failed");
		}

		return 1;
	}

	redisReply* reply = (redisReply *)redisCommand(m_pContext, "SELECT %d", m_pCachePool->GetDBNum());
	if (reply && (reply->type == REDIS_REPLY_STATUS) && (strncmp(reply->str, "OK", 2) == 0)) {
		freeReplyObject(reply);
		return 0;
	} else {
		log("select cache db failed");
		return 2;
	}
}
```
这里用到的redis client是hiredis，头文件：hiredis.h;库文件：libhiredis.a

redis数据库默认不需要账号密码，redisConnectWithTimeout只要提供Ip,port以及超时时间即可连接，调用后会返回一个redisContext类型的指针，这是连接的具体实例。

REDIS_REPLY_STATUS：

返回执行结果为状态的命令。比如set命令的返回值的类型是REDIS_REPLY_STATUS，然后只有当返回信息是"OK"时，才表示该命令执行成功。可以通过reply->str得到文字信息，通过reply->len得到信息长度。

redisCommand：向redis写入一个命令，下面这一段我个人认为是测试一下redis连接成功了没有，如果连接成功就返回0，否则返回2
```bash
	redisReply* reply = (redisReply *)redisCommand(m_pContext, "SELECT %d", m_pCachePool->GetDBNum());
	if (reply && (reply->type == REDIS_REPLY_STATUS) && (strncmp(reply->str, "OK", 2) == 0)) {
		freeReplyObject(reply);
		return 0;
	} else {
		log("select cache db failed");
		return 2;
	}
```



函数原型void freeReplyObject(void *reply);

说明：释放redisCommand执行后返回的redisReply所占用的内存

 

函数原型：void redisFree(redisContext *c);

说明：释放redisConnect()所产生的连接。



### 简单总结：
CacheManager维护一个Instance名和CachePool指针键值对的map,CachePool维护一张：某个Instance的所有connect的列表，ManagerCache运用单例避免多次初始化,redis client采用的是hiredis

## 2.初始化存储数据库
存储数据库的初始化流程与redis大同小异，Teamtalk中选用的存储数据库是Mysql
```bash
	CDBManager* pDBManager = CDBManager::getInstance();
	if (!pDBManager) {
		log("DBManager init failed");
		return -1;
	}
```
s_db_manager就是DBManager的实例
```bash
CDBManager* CDBManager::getInstance()
{
	if (!s_db_manager) {
		s_db_manager = new CDBManager();
		if (s_db_manager->Init()) {
			delete s_db_manager;
			s_db_manager = NULL;
		}
	}

	return s_db_manager;
}
```

```bash
int CDBManager::Init()
{
	CConfigFileReader config_file("dbproxyserver.conf");

	char* db_instances = config_file.GetConfigName("DBInstances");

	if (!db_instances) {
		log("not configure DBInstances");
		return 1;
	}

	char host[64];
	char port[64];
	char dbname[64];
	char username[64];
	char password[64];
    char maxconncnt[64];
	CStrExplode instances_name(db_instances, ',');

	for (uint32_t i = 0; i < instances_name.GetItemCnt(); i++) {
		char* pool_name = instances_name.GetItem(i);
		snprintf(host, 64, "%s_host", pool_name);
		snprintf(port, 64, "%s_port", pool_name);
		snprintf(dbname, 64, "%s_dbname", pool_name);
		snprintf(username, 64, "%s_username", pool_name);
		snprintf(password, 64, "%s_password", pool_name);
        snprintf(maxconncnt, 64, "%s_maxconncnt", pool_name);

		char* db_host = config_file.GetConfigName(host);
		char* str_db_port = config_file.GetConfigName(port);
		char* db_dbname = config_file.GetConfigName(dbname);
		char* db_username = config_file.GetConfigName(username);
		char* db_password = config_file.GetConfigName(password);
        char* str_maxconncnt = config_file.GetConfigName(maxconncnt);

		if (!db_host || !str_db_port || !db_dbname || !db_username || !db_password || !str_maxconncnt) {
			log("not configure db instance: %s", pool_name);
			return 2;
		}

		int db_port = atoi(str_db_port);
        int db_maxconncnt = atoi(str_maxconncnt);
		CDBPool* pDBPool = new CDBPool(pool_name, db_host, db_port, db_username, db_password, db_dbname, db_maxconncnt);
		if (pDBPool->Init()) {
			log("init db instance failed: %s", pool_name);
			return 3;
		}
		m_dbpool_map.insert(make_pair(pool_name, pDBPool));
	}

	return 0;
}
```

存储数据库的Manager也维护了一张Instance Name和pool指针的map。存储数据库在构造函数上与缓存数据库有些不同，他需要增加用户名和密码，因为存储数据库需要账户密码。

进入到存储数据库的初始化，同样维护了一张新建立的连接的链表：```m_free_list```。
```bash
int CDBPool::Init()
{
	for (int i = 0; i < m_db_cur_conn_cnt; i++) {
		CDBConn* pDBConn = new CDBConn(this);
		int ret = pDBConn->Init();
		if (ret) {
			delete pDBConn;
			return ret;
		}

		m_free_list.push_back(pDBConn);
	}

	log("db pool: %s, size: %d", m_pool_name.c_str(), (int)m_free_list.size());
	return 0;
}
```

真正初始化单根连接
```bash
int CDBConn::Init()
{
	m_mysql = mysql_init(NULL);
	if (!m_mysql) {
		log("mysql_init failed");
		return 1;
	}

	my_bool reconnect = true;
	mysql_options(m_mysql, MYSQL_OPT_RECONNECT, &reconnect);
	mysql_options(m_mysql, MYSQL_SET_CHARSET_NAME, "utf8mb4");

	if (!mysql_real_connect(m_mysql, m_pDBPool->GetDBServerIP(), m_pDBPool->GetUsername(), m_pDBPool->GetPasswrod(),
			m_pDBPool->GetDBName(), m_pDBPool->GetDBServerPort(), NULL, 0)) {
		log("mysql_real_connect failed: %s", mysql_error(m_mysql));
		return 2;
	}

	return 0;
}
```
设置自动重连   
mysql_options(m_mysql, MYSQL_OPT_RECONNECT, &reconnect);

更改字符集为utp-8   
mysql_options(m_mysql, MYSQL_SET_CHARSET_NAME, "utf8mb4");

```bash
#configure for mysql
DBInstances=teamtalk_master,teamtalk_slave
#teamtalk_master
teamtalk_master_host=127.0.0.1
teamtalk_master_port=3306
teamtalk_master_dbname=teamtalk
teamtalk_master_username=test
teamtalk_master_password=12345
teamtalk_master_maxconncnt=16

#teamtalk_slave
teamtalk_slave_host=127.0.0.1
teamtalk_slave_port=3306
teamtalk_slave_dbname=teamtalk
teamtalk_slave_username=test
teamtalk_slave_password=12345
teamtalk_slave_maxconncnt=16
```

一共配置了2个存储库：主&从，每个库的最大连接数都是16


到这里，缓存数据库和存储数据库的初始化工作就完成了。

## 3.将主线程初始化为单例
```bash
	// 主线程初始化单例，不然在工作线程可能会出现多次初始化
	if (!CAudioModel::getInstance()) {
		return -1;
	}
    
    if (!CGroupMessageModel::getInstance()) {
        return -1;
    }
    
    if (!CGroupModel::getInstance()) {
        return -1;
    }
    
    if (!CMessageModel::getInstance()) {
        return -1;
    }

	if (!CSessionModel::getInstance()) {
		return -1;
	}
    
    if(!CRelationModel::getInstance())
    {
        return -1;
    }
    
    if (!CUserModel::getInstance()) {
        return -1;
    }
    
    if (!CFileModel::getInstance()) {
        return -1;
    }
```
## 4.这段应该是给Audio Model用的，也写在main里面
```bash
	CConfigFileReader config_file("dbproxyserver.conf");

	char* listen_ip = config_file.GetConfigName("ListenIP");
	char* str_listen_port = config_file.GetConfigName("ListenPort");
	char* str_thread_num = config_file.GetConfigName("ThreadNum");
    char* str_file_site = config_file.GetConfigName("MsfsSite");
    char* str_aes_key = config_file.GetConfigName("aesKey");

	if (!listen_ip || !str_listen_port || !str_thread_num || !str_file_site || !str_aes_key) {
		log("missing ListenIP/ListenPort/ThreadNum/MsfsSite/aesKey, exit...");
		return -1;
	}
    
    if(strlen(str_aes_key) != 32)
    {
        log("aes key is invalied");
        return -2;
    }
    string strAesKey(str_aes_key, 32);
    CAes cAes = CAes(strAesKey);
    string strAudio = "[语音]";
    char* pAudioEnc;
    uint32_t nOutLen;
    if(cAes.Encrypt(strAudio.c_str(), strAudio.length(), &pAudioEnc, nOutLen) == 0)
    {
        strAudioEnc.clear();
        strAudioEnc.append(pAudioEnc, nOutLen);
        cAes.Free(pAudioEnc);
    }

	uint16_t listen_port = atoi(str_listen_port);
	uint32_t thread_num = atoi(str_thread_num);
    
    string strFileSite(str_file_site);
    CAudioModel::getInstance()->setUrl(strFileSite);

	int ret = netlib_init();

	if (ret == NETLIB_ERROR)
		return ret;
    
    /// yunfan add 2014.9.28
    // for 603 push
    curl_global_init(CURL_GLOBAL_ALL);
    /// yunfan add end
```

## 5.启动任务队列，用于处理任务
```cpp
int init_proxy_conn(uint32_t thread_num)
{
	s_handler_map = CHandlerMap::getInstance();
	g_thread_pool.Init(thread_num);

	netlib_add_loop(proxy_loop_callback, NULL);

	signal(SIGTERM, sig_handler);

	return netlib_register_timer(proxy_timer_callback, NULL, 1000);
}
```
### 5.1 创建HandlerMap对象并初始化
```cpp
CHandlerMap* CHandlerMap::getInstance()
{
	if (!s_handler_instance) {
		s_handler_instance = new CHandlerMap();
		s_handler_instance->Init();
	}

	return s_handler_instance;
}
```
#### 数据类型的定义
```cpp
HandlerMap_t 	m_handler_map;  
typedef map<uint32_t, pdu_handler_t> HandlerMap_t;
typedef void (*pdu_handler_t)(CImPdu* pPdu, uint32_t conn_uuid);
```
pdu_handler_t是逻辑处理函数的函数指针


#### CHandlerMap的init函数  
```cpp
/**
 *  初始化函数,加载了各种commandId 对应的处理函数
 */
void CHandlerMap::Init()
{
	// Login validate
	m_handler_map.insert(make_pair(uint32_t(CID_OTHER_VALIDATE_REQ), DB_PROXY::doLogin));
    m_handler_map.insert(make_pair(uint32_t(CID_LOGIN_REQ_PUSH_SHIELD), DB_PROXY::doPushShield));
    m_handler_map.insert(make_pair(uint32_t(CID_LOGIN_REQ_QUERY_PUSH_SHIELD), DB_PROXY::doQueryPushShield));
    
    // recent session
    m_handler_map.insert(make_pair(uint32_t(CID_BUDDY_LIST_RECENT_CONTACT_SESSION_REQUEST), DB_PROXY::getRecentSession));
    m_handler_map.insert(make_pair(uint32_t(CID_BUDDY_LIST_REMOVE_SESSION_REQ), DB_PROXY::deleteRecentSession));
    
    // users
    m_handler_map.insert(make_pair(uint32_t(CID_BUDDY_LIST_USER_INFO_REQUEST), DB_PROXY::getUserInfo));
    m_handler_map.insert(make_pair(uint32_t(CID_BUDDY_LIST_ALL_USER_REQUEST), DB_PROXY::getChangedUser));
    m_handler_map.insert(make_pair(uint32_t(CID_BUDDY_LIST_DEPARTMENT_REQUEST), DB_PROXY::getChgedDepart));
    m_handler_map.insert(make_pair(uint32_t(CID_BUDDY_LIST_CHANGE_SIGN_INFO_REQUEST), DB_PROXY::changeUserSignInfo));

    
    // message content
    m_handler_map.insert(make_pair(uint32_t(CID_MSG_DATA), DB_PROXY::sendMessage));
    m_handler_map.insert(make_pair(uint32_t(CID_MSG_LIST_REQUEST), DB_PROXY::getMessage));
    m_handler_map.insert(make_pair(uint32_t(CID_MSG_UNREAD_CNT_REQUEST), DB_PROXY::getUnreadMsgCounter));
    m_handler_map.insert(make_pair(uint32_t(CID_MSG_READ_ACK), DB_PROXY::clearUnreadMsgCounter));
    m_handler_map.insert(make_pair(uint32_t(CID_MSG_GET_BY_MSG_ID_REQ), DB_PROXY::getMessageById));
    m_handler_map.insert(make_pair(uint32_t(CID_MSG_GET_LATEST_MSG_ID_REQ), DB_PROXY::getLatestMsgId));
    
    // device token
    m_handler_map.insert(make_pair(uint32_t(CID_LOGIN_REQ_DEVICETOKEN), DB_PROXY::setDevicesToken));
    m_handler_map.insert(make_pair(uint32_t(CID_OTHER_GET_DEVICE_TOKEN_REQ), DB_PROXY::getDevicesToken));
    
    //push 推送设置
    m_handler_map.insert(make_pair(uint32_t(CID_GROUP_SHIELD_GROUP_REQUEST), DB_PROXY::setGroupPush));
    m_handler_map.insert(make_pair(uint32_t(CID_OTHER_GET_SHIELD_REQ), DB_PROXY::getGroupPush));
    
    
    // group
    m_handler_map.insert(make_pair(uint32_t(CID_GROUP_NORMAL_LIST_REQUEST), DB_PROXY::getNormalGroupList));
    m_handler_map.insert(make_pair(uint32_t(CID_GROUP_INFO_REQUEST), DB_PROXY::getGroupInfo));
    m_handler_map.insert(make_pair(uint32_t(CID_GROUP_CREATE_REQUEST), DB_PROXY::createGroup));
    m_handler_map.insert(make_pair(uint32_t(CID_GROUP_CHANGE_MEMBER_REQUEST), DB_PROXY::modifyMember));

    
    // file
    m_handler_map.insert(make_pair(uint32_t(CID_FILE_HAS_OFFLINE_REQ), DB_PROXY::hasOfflineFile));
    m_handler_map.insert(make_pair(uint32_t(CID_FILE_ADD_OFFLINE_REQ), DB_PROXY::addOfflineFile));
    m_handler_map.insert(make_pair(uint32_t(CID_FILE_DEL_OFFLINE_REQ), DB_PROXY::delOfflineFile));

}
```
[make_pair的使用方法](https://blog.csdn.net/weixin_42825576/article/details/81571419)

### 5.2 初始化工作线程
```cpp
static CThreadPool g_thread_pool;  //定义一个基础的线程池类
``` 


```cpp
g_thread_pool.Init(thread_num);   //初始化线程池
```


根据```init_proxy_conn```函数传入的线程个数创建线程，并加入队列```m_worker_list```管理
```cpp
int CThreadPool::Init(uint32_t worker_size)
{
    m_worker_size = worker_size;
	m_worker_list = new CWorkerThread [m_worker_size];
	if (!m_worker_list) {
		return 1;
	}

	for (uint32_t i = 0; i < m_worker_size; i++) {
		m_worker_list[i].SetThreadIdx(i);
		m_worker_list[i].Start();
	}

	return 0;
}
```

linux下创建进程函数:[pthread_create](https://www.cnblogs.com/amanlikethis/p/5537175.html)

```cpp
void CWorkerThread::Start()
{
	(void)pthread_create(&m_thread_id, NULL, StartRoutine, this);
}
```

创建线程时指定的线程函数```StartRoutine```，pthread_create的第四个参数是线程函数的传入参数，上文在线程创建时传入了```this```指针，因此下文中的arg为```CWorkerThread* this```。

```cpp
void* CWorkerThread::StartRoutine(void* arg)
{
	CWorkerThread* pThread = (CWorkerThread*)arg;

	pThread->Execute();

	return NULL;
}
```
#### 工作线程阻塞等待事件Notify

```cpp
//数据定义
CThreadNotify	m_thread_notify;   
list<CTask*>	m_task_list;
```

```cpp
void CWorkerThread::Execute()
{
	while (true) {
		m_thread_notify.Lock();

		// put wait in while cause there can be spurious wake up (due to signal/ENITR)
		while (m_task_list.empty()) {
			m_thread_notify.Wait();
		}

		CTask* pTask = m_task_list.front();
		m_task_list.pop_front();
		m_thread_notify.Unlock();

		pTask->run();

		delete pTask;

		m_task_cnt++;
		//log("%d have the execute %d task\n", m_thread_idx, m_task_cnt);
	}
}
```
#### ```Execute()```一直循环阻塞等待任务到来，而```AddTask()```向```m_worker_list```中添加一个Task则会停止这种等待状态，从而执行pTask->run()。
```cpp
void CThreadPool::AddTask(CTask* pTask)
{
	/*
	 * select a random thread to push task
	 * we can also select a thread that has less task to do
	 * but that will scan the whole thread list and use thread lock to get each task size
	 */
	uint32_t thread_idx = random() % m_worker_size;
	m_worker_list[thread_idx].PushTask(pTask);
}
```
这里m_thread_notify会触发一个signal从而使m_thread_notify从wait状态解除出来，这里要加锁操作，避免其他Task影响。
```cpp
void CWorkerThread::PushTask(CTask* pTask)
{
	m_thread_notify.Lock();
	m_task_list.push_back(pTask);
	m_thread_notify.Signal();
	m_thread_notify.Unlock();
}
```
### 5.3 调用网络库，注册callback函数

```cpp
netlib_add_loop(proxy_loop_callback, NULL);
```

```cpp
//
void proxy_loop_callback(void* callback_data, uint8_t msg, uint32_t handle, void* pParam)
{
	CProxyConn::SendResponsePduList();
}
```

```cpp
void CProxyConn::SendResponsePduList()
{
	s_list_lock.lock();
	while (!s_response_pdu_list.empty()) {
		ResponsePdu_t* pResp = s_response_pdu_list.front();
		s_response_pdu_list.pop_front();
		s_list_lock.unlock();

		CProxyConn* pConn = get_proxy_conn_by_uuid(pResp->conn_uuid);
		if (pConn) {
			if (pResp->pPdu) {
				pConn->SendPdu(pResp->pPdu);
			} else {
				log("close connection uuid=%d by parse pdu error\b", pResp->conn_uuid);
				pConn->Close();
			}
		}

		if (pResp->pPdu)
			delete pResp->pPdu;
		delete pResp;

		s_list_lock.lock();
	}

	s_list_lock.unlock();
}
```

```cpp
int netlib_add_loop(callback_t callback, void* user_data)
{
	CEventDispatch::Instance()->AddLoop(callback, user_data);
	return 0;
}
```

```cpp
void CEventDispatch::AddLoop(callback_t callback, void* user_data)
{
    TimerItem* pItem = new TimerItem;
    pItem->callback = callback;
    pItem->user_data = user_data;
    m_loop_list.push_back(pItem);
}
```

### 5.4 [signal函数](https://www.runoob.com/cprogramming/c-function-signal.html)

为SIGTERM信号设置处理函数sig_handler
```cpp
signal(SIGTERM, sig_handler);
```

信号处理函数
```cpp
static void sig_handler(int sig_no)
{
	if (sig_no == SIGTERM) {
		log("receive SIGTERM, prepare for exit");
        CImPdu cPdu;
        IM::Server::IMStopReceivePacket msg;
        msg.set_result(0);
        cPdu.SetPBMsg(&msg);
        cPdu.SetServiceId(IM::BaseDefine::SID_OTHER);
        cPdu.SetCommandId(IM::BaseDefine::CID_OTHER_STOP_RECV_PACKET);
        for (ConnMap_t::iterator it = g_proxy_conn_map.begin(); it != g_proxy_conn_map.end(); it++) {
            CProxyConn* pConn = (CProxyConn*)it->second;
            pConn->SendPdu(&cPdu);
        }
        // Add By ZhangYuanhao
        // Before stop we need to stop the sync thread,otherwise maybe will not sync the internal data any more
        CSyncCenter::getInstance()->stopSync();
        
        // callback after 4 second to exit process;
		netlib_register_timer(exit_callback, NULL, 4000);
	}
}
```



### 5.5 注册定时器，用于保活
```cpp
netlib_register_timer(proxy_timer_callback, NULL, 1000);

//
int netlib_register_timer(callback_t callback, void* user_data, uint64_t interval)
{
	CEventDispatch::Instance()->AddTimer(callback, user_data, interval);
	return 0;
}

//
void CEventDispatch::AddTimer(callback_t callback, void* user_data, uint64_t interval)
{
	list<TimerItem*>::iterator it;
	for (it = m_timer_list.begin(); it != m_timer_list.end(); it++)
	{
		TimerItem* pItem = *it;
		if (pItem->callback == callback && pItem->user_data == user_data)
		{
			pItem->interval = interval;
			pItem->next_tick = get_tick_count() + interval;
			return;
		}
	}

	TimerItem* pItem = new TimerItem;
	pItem->callback = callback;
	pItem->user_data = user_data;
	pItem->interval = interval;
	pItem->next_tick = get_tick_count() + interval;
	m_timer_list.push_back(pItem);
}
```

```cpp
void proxy_timer_callback(void* callback_data, uint8_t msg, uint32_t handle, void* pParam)
{
	uint64_t cur_time = get_tick_count();
	for (ConnMap_t::iterator it = g_proxy_conn_map.begin(); it != g_proxy_conn_map.end(); ) {
		ConnMap_t::iterator it_old = it;
		it++;

		CProxyConn* pConn = (CProxyConn*)it_old->second;
		pConn->OnTimer(cur_time);
	}
}
```

如果当前时间大于```SERVER_HEARTBEAT_INTERVAL```，则发出一个心跳包;   
如果当前时间大于```SERVER_TIMEOUT```,则Time out,断开连接;
```cpp
void CProxyConn::OnTimer(uint64_t curr_tick)
{
	if (curr_tick > m_last_send_tick + SERVER_HEARTBEAT_INTERVAL) {
        
        CImPdu cPdu;
        IM::Other::IMHeartBeat msg;
        cPdu.SetPBMsg(&msg);
        cPdu.SetServiceId(IM::BaseDefine::SID_OTHER);
        cPdu.SetCommandId(IM::BaseDefine::CID_OTHER_HEARTBEAT);
		SendPdu(&cPdu);
	}

	if (curr_tick > m_last_recv_tick + SERVER_TIMEOUT) {
		log("proxy connection timeout %s:%d", m_peer_ip.c_str(), m_peer_port);
		Close();
	}
}
```

## 6. 存储服务器同步至缓存服务器

```cpp
CSyncCenter::getInstance()->init();
CSyncCenter::getInstance()->startSync();
```

```cpp
/*
 * 初始化函数，从cache里面加载上次同步的时间信息等
 */
void CSyncCenter::init()
{
    // Load total update time
    CacheManager* pCacheManager = CacheManager::getInstance();
    // increase message count
    CacheConn* pCacheConn = pCacheManager->GetCacheConn("unread");
    if (pCacheConn)
    {
        string strTotalUpdate = pCacheConn->get("total_user_updated");

        string strLastUpdateGroup = pCacheConn->get("last_update_group");
        pCacheManager->RelCacheConn(pCacheConn);
		if(strTotalUpdate != "")
        {
            m_nLastUpdate = string2int(strTotalUpdate);
        }
        else
        {
            updateTotalUpdate(time(NULL));
        }
        if(strLastUpdateGroup.empty())
        {
            m_nLastUpdateGroup = string2int(strLastUpdateGroup);
        }
        else
        {
            updateLastUpdateGroup(time(NULL));
        }
    }
    else
    {
        log("no cache connection to get total_user_updated");
    }
}
```


```cpp
/**
 *  开启内网数据同步以及群组聊天记录同步
 */
void CSyncCenter::startSync()
{
#ifdef _WIN32
    (void)CreateThread(NULL, 0, doSyncGroupChat, NULL, 0, &m_nGroupChatThreadId);
#else
    (void)pthread_create(&m_nGroupChatThreadId, NULL, doSyncGroupChat, NULL);
#endif
}
```

```cpp
/**
 *  同步群组聊天信息
 *
 *  @param arg NULL
 *
 *  @return NULL
 */
void* CSyncCenter::doSyncGroupChat(void* arg)
{
    m_bSyncGroupChatRuning = true;
    CDBManager* pDBManager = CDBManager::getInstance();
    map<uint32_t, uint32_t> mapChangedGroup;
    do{
        mapChangedGroup.clear();
        CDBConn* pDBConn = pDBManager->GetDBConn("teamtalk_slave");
        if(pDBConn)
        {
            string strSql = "select id, lastChated from IMGroup where status=0 and lastChated >=" + int2string(m_pInstance->getLastUpdateGroup());
            CResultSet* pResult = pDBConn->ExecuteQuery(strSql.c_str());
            if(pResult)
            {
                while (pResult->Next()) {
                    uint32_t nGroupId = pResult->GetInt("id");
                    uint32_t nLastChat = pResult->GetInt("lastChated");
                    if(nLastChat != 0)
                    {
                        mapChangedGroup[nGroupId] = nLastChat;
                    }
                }
                delete pResult;
            }
            pDBManager->RelDBConn(pDBConn);
        }
        else
        {
            log("no db connection for teamtalk_slave");
        }
        m_pInstance->updateLastUpdateGroup(time(NULL));
        for (auto it=mapChangedGroup.begin(); it!=mapChangedGroup.end(); ++it)
        {
            uint32_t nGroupId =it->first;
            list<uint32_t> lsUsers;
            uint32_t nUpdate = it->second;
            CGroupModel::getInstance()->getGroupUser(nGroupId, lsUsers);
            for (auto it1=lsUsers.begin(); it1!=lsUsers.end(); ++it1)
            {
                uint32_t nUserId = *it1;
                uint32_t nSessionId = INVALID_VALUE;
                nSessionId = CSessionModel::getInstance()->getSessionId(nUserId, nGroupId, IM::BaseDefine::SESSION_TYPE_GROUP, true);
                if(nSessionId != INVALID_VALUE)
                {
                    CSessionModel::getInstance()->updateSession(nSessionId, nUpdate);
                }
                else
                {
                    CSessionModel::getInstance()->addSession(nUserId, nGroupId, IM::BaseDefine::SESSION_TYPE_GROUP);
                }
            }
        }
//    } while (!m_pInstance->m_pCondSync->waitTime(5*1000));
    } while (m_pInstance->m_bSyncGroupChatWaitting && !(m_pInstance->m_pCondGroupChat->waitTime(5*1000)));
//    } while(m_pInstance->m_bSyncGroupChatWaitting);
    m_bSyncGroupChatRuning = false;
    return NULL;
}
```
## 7. 监听端口，接收数据收发
```cpp
	CStrExplode listen_ip_list(listen_ip, ';');
	for (uint32_t i = 0; i < listen_ip_list.GetItemCnt(); i++) {
		ret = netlib_listen(listen_ip_list.GetItem(i), listen_port, proxy_serv_callback, NULL);
		if (ret == NETLIB_ERROR)
			return ret;
	}
```
```cpp
int CBaseSocket::Listen(const char* server_ip, uint16_t port, callback_t callback, void* callback_data)
{
	m_local_ip = server_ip;
	m_local_port = port;
	m_callback = callback;
	m_callback_data = callback_data;

	m_socket = socket(AF_INET, SOCK_STREAM, 0);
	if (m_socket == INVALID_SOCKET)
	{
		printf("socket failed, err_code=%d\n", _GetErrorCode());
		return NETLIB_ERROR;
	}

	_SetReuseAddr(m_socket);
	_SetNonblock(m_socket);

	sockaddr_in serv_addr;
	_SetAddr(server_ip, port, &serv_addr);
    int ret = ::bind(m_socket, (sockaddr*)&serv_addr, sizeof(serv_addr));
	if (ret == SOCKET_ERROR)
	{
		log("bind failed, err_code=%d", _GetErrorCode());
		closesocket(m_socket);
		return NETLIB_ERROR;
	}

	ret = listen(m_socket, 64);
	if (ret == SOCKET_ERROR)
	{
		log("listen failed, err_code=%d", _GetErrorCode());
		closesocket(m_socket);
		return NETLIB_ERROR;
	}

	m_state = SOCKET_STATE_LISTENING;

	log("CBaseSocket::Listen on %s:%d", server_ip, port);

	AddBaseSocket(this);
	CEventDispatch::Instance()->AddEvent(m_socket, SOCKET_READ | SOCKET_EXCEP);
	return NETLIB_OK;
}
```


```cpp
void CEventDispatch::AddEvent(SOCKET fd, uint8_t socket_event)
{
	CAutoLock func_lock(&m_lock);

	if ((socket_event & SOCKET_READ) != 0)
	{
		FD_SET(fd, &m_read_set);
	}
		
	if ((socket_event & SOCKET_WRITE) != 0)
	{
		FD_SET(fd, &m_write_set);
	}

	if ((socket_event & SOCKET_EXCEP) != 0)
	{
		FD_SET(fd, &m_excep_set);
	}
}
```

```cpp
void netlib_eventloop(uint32_t wait_timeout)
{
	CEventDispatch::Instance()->StartDispatch(wait_timeout);
}
```

```cpp
void CEventDispatch::StartDispatch(uint32_t wait_timeout)
{
	fd_set read_set, write_set, excep_set;
	timeval timeout;
	timeout.tv_sec = 0;
	timeout.tv_usec = wait_timeout * 1000;	// 10 millisecond

    if(running)
        return;
    running = true;
    
    while (running)
	{
		_CheckTimer();
        _CheckLoop();

		if (!m_read_set.fd_count && !m_write_set.fd_count && !m_excep_set.fd_count)
		{
			Sleep(MIN_TIMER_DURATION);
			continue;
		}

		m_lock.lock();
		memcpy(&read_set, &m_read_set, sizeof(fd_set));
		memcpy(&write_set, &m_write_set, sizeof(fd_set));
		memcpy(&excep_set, &m_excep_set, sizeof(fd_set));
		m_lock.unlock();

		int nfds = select(0, &read_set, &write_set, &excep_set, &timeout);

		if (nfds == SOCKET_ERROR)
		{
			log("select failed, error code: %d", GetLastError());
			Sleep(MIN_TIMER_DURATION);
			continue;			// select again
		}

		if (nfds == 0)
		{
			continue;
		}

		for (u_int i = 0; i < read_set.fd_count; i++)
		{
			//log("select return read count=%d\n", read_set.fd_count);
			SOCKET fd = read_set.fd_array[i];
			CBaseSocket* pSocket = FindBaseSocket((net_handle_t)fd);
			if (pSocket)
			{
				pSocket->OnRead();
				pSocket->ReleaseRef();
			}
		}

		for (u_int i = 0; i < write_set.fd_count; i++)
		{
			//log("select return write count=%d\n", write_set.fd_count);
			SOCKET fd = write_set.fd_array[i];
			CBaseSocket* pSocket = FindBaseSocket((net_handle_t)fd);
			if (pSocket)
			{
				pSocket->OnWrite();
				pSocket->ReleaseRef();
			}
		}

		for (u_int i = 0; i < excep_set.fd_count; i++)
		{
			//log("select return exception count=%d\n", excep_set.fd_count);
			SOCKET fd = excep_set.fd_array[i];
			CBaseSocket* pSocket = FindBaseSocket((net_handle_t)fd);
			if (pSocket)
			{
				pSocket->OnClose();
				pSocket->ReleaseRef();
			}
		}

	}
}
```

这里真正去call db_proxy_server.app的回调函数
```cpp
void CBaseSocket::OnRead()
{
	if (m_state == SOCKET_STATE_LISTENING)
	{
		_AcceptNewSocket();
	}
	else
	{
		u_long avail = 0;
		if ( (ioctlsocket(m_socket, FIONREAD, &avail) == SOCKET_ERROR) || (avail == 0) )
		{
			m_callback(m_callback_data, NETLIB_MSG_CLOSE, (net_handle_t)m_socket, NULL);
		}
		else
		{
			m_callback(m_callback_data, NETLIB_MSG_READ, (net_handle_t)m_socket, NULL);
		}
	}
}
```



回调函数
```cpp
// this callback will be replaced by imconn_callback() in OnConnect()
void proxy_serv_callback(void* callback_data, uint8_t msg, uint32_t handle, void* pParam)
{
	if (msg == NETLIB_MSG_CONNECT)
	{
		CProxyConn* pConn = new CProxyConn();
		pConn->OnConnect(handle);
	}
	else
	{
		log("!!!error msg: %d", msg);
	}
}
```

```cpp
void CProxyConn::OnConnect(net_handle_t handle)
{
	m_handle = handle;

	g_proxy_conn_map.insert(make_pair(handle, this));

	netlib_option(handle, NETLIB_OPT_SET_CALLBACK, (void*)imconn_callback);
	netlib_option(handle, NETLIB_OPT_SET_CALLBACK_DATA, (void*)&g_proxy_conn_map);
	netlib_option(handle, NETLIB_OPT_GET_REMOTE_IP, (void*)&m_peer_ip);
	netlib_option(handle, NETLIB_OPT_GET_REMOTE_PORT, (void*)&m_peer_port);

	log("connect from %s:%d, handle=%d", m_peer_ip.c_str(), m_peer_port, m_handle);
}
```

```cpp
void imconn_callback(void* callback_data, uint8_t msg, uint32_t handle, void* pParam)
{
	NOTUSED_ARG(handle);
	NOTUSED_ARG(pParam);

	if (!callback_data)
		return;

	ConnMap_t* conn_map = (ConnMap_t*)callback_data;
	CImConn* pConn = FindImConn(conn_map, handle);
	if (!pConn)
		return;

	//log("msg=%d, handle=%d ", msg, handle);

	switch (msg)
	{
	case NETLIB_MSG_CONFIRM:
		pConn->OnConfirm();
		break;
	case NETLIB_MSG_READ:
		pConn->OnRead();
		break;
	case NETLIB_MSG_WRITE:
		pConn->OnWrite();
		break;
	case NETLIB_MSG_CLOSE:
		pConn->OnClose();
		break;
	default:
		log("!!!imconn_callback error msg: %d ", msg);
		break;
	}

	pConn->ReleaseRef();
}
```

```cpp
// 由于数据包是在另一个线程处理的，所以不能在主线程delete数据包，所以需要Override这个方法
void CProxyConn::OnRead()
{
	for (;;) {
		uint32_t free_buf_len = m_in_buf.GetAllocSize() - m_in_buf.GetWriteOffset();
		if (free_buf_len < READ_BUF_SIZE)
			m_in_buf.Extend(READ_BUF_SIZE);

		int ret = netlib_recv(m_handle, m_in_buf.GetBuffer() + m_in_buf.GetWriteOffset(), READ_BUF_SIZE);
		if (ret <= 0)
			break;

		m_recv_bytes += ret;
		m_in_buf.IncWriteOffset(ret);
		m_last_recv_tick = get_tick_count();
	}

	uint32_t pdu_len = 0;
    try {
        while ( CImPdu::IsPduAvailable(m_in_buf.GetBuffer(), m_in_buf.GetWriteOffset(), pdu_len) ) {
            HandlePduBuf(m_in_buf.GetBuffer(), pdu_len);
            m_in_buf.Read(NULL, pdu_len);
        }
    } catch (CPduException& ex) {
        log("!!!catch exception, err_code=%u, err_msg=%s, close the connection ",
            ex.GetErrorCode(), ex.GetErrorMsg());
        OnClose();
    }
	
}
```


读完数据包之后处理包,核心处理函数
```cpp
void CProxyConn::HandlePduBuf(uchar_t* pdu_buf, uint32_t pdu_len)
{
    CImPdu* pPdu = NULL;
    pPdu = CImPdu::ReadPdu(pdu_buf, pdu_len);
    if (pPdu->GetCommandId() == IM::BaseDefine::CID_OTHER_HEARTBEAT) {
        return;
    }
    
    pdu_handler_t handler = s_handler_map->GetHandler(pPdu->GetCommandId());
    
    if (handler) {
        CTask* pTask = new CProxyTask(m_uuid, handler, pPdu);
        g_thread_pool.AddTask(pTask);
    } else {
        log("no handler for packet type: %d", pPdu->GetCommandId());
    }
}
```


## 业务逻辑函数
```cpp
void doLogin(CImPdu* pPdu, uint32_t conn_uuid)
{
    
    CImPdu* pPduResp = new CImPdu;
    
    IM::Server::IMValidateReq msg;
    IM::Server::IMValidateRsp msgResp;
    if(msg.ParseFromArray(pPdu->GetBodyData(), pPdu->GetBodyLength()))
    {
        
        string strDomain = msg.user_name();
        string strPass = msg.password();
        
        msgResp.set_user_name(strDomain);
        msgResp.set_attach_data(msg.attach_data());
        
        do
        {
            CAutoLock cAutoLock(&g_cLimitLock);
            list<uint32_t>& lsErrorTime = g_hmLimits[strDomain];
            uint32_t tmNow = time(NULL);
            
            //清理超过30分钟的错误时间点记录
            /*
             清理放在这里还是放在密码错误后添加的时候呢？
             放在这里，每次都要遍历，会有一点点性能的损失。
             放在后面，可能会造成30分钟之前有10次错的，但是本次是对的就没办法再访问了。
             */
            auto itTime=lsErrorTime.begin();
            for(; itTime!=lsErrorTime.end();++itTime)
            {
                if(tmNow - *itTime > 30*60)
                {
                    break;
                }
            }
            if(itTime != lsErrorTime.end())
            {
                lsErrorTime.erase(itTime, lsErrorTime.end());
            }
            
            // 判断30分钟内密码错误次数是否大于10
            if(lsErrorTime.size() > 10)
            {
                itTime = lsErrorTime.begin();
                if(tmNow - *itTime <= 30*60)
                {
                    msgResp.set_result_code(6);
                    msgResp.set_result_string("用户名/密码错误次数太多");
                    pPduResp->SetPBMsg(&msgResp);
                    pPduResp->SetSeqNum(pPdu->GetSeqNum());
                    pPduResp->SetServiceId(IM::BaseDefine::SID_OTHER);
                    pPduResp->SetCommandId(IM::BaseDefine::CID_OTHER_VALIDATE_RSP);
                    CProxyConn::AddResponsePdu(conn_uuid, pPduResp);
                    return ;
                }
            }
        } while(false);
        
        log("%s request login.", strDomain.c_str());
        
        
        
        IM::BaseDefine::UserInfo cUser;
        
        if(g_loginStrategy.doLogin(strDomain, strPass, cUser))
        {
            IM::BaseDefine::UserInfo* pUser = msgResp.mutable_user_info();
            pUser->set_user_id(cUser.user_id());
            pUser->set_user_gender(cUser.user_gender());
            pUser->set_department_id(cUser.department_id());
            pUser->set_user_nick_name(cUser.user_nick_name());
            pUser->set_user_domain(cUser.user_domain());
            pUser->set_avatar_url(cUser.avatar_url());
            
            pUser->set_email(cUser.email());
            pUser->set_user_tel(cUser.user_tel());
            pUser->set_user_real_name(cUser.user_real_name());
            pUser->set_status(0);

            pUser->set_sign_info(cUser.sign_info());
           
            msgResp.set_result_code(0);
            msgResp.set_result_string("成功");
            
            //如果登陆成功，则清除错误尝试限制
            CAutoLock cAutoLock(&g_cLimitLock);
            list<uint32_t>& lsErrorTime = g_hmLimits[strDomain];
            lsErrorTime.clear();
        }
        else
        {
            //密码错误，记录一次登陆失败
            uint32_t tmCurrent = time(NULL);
            CAutoLock cAutoLock(&g_cLimitLock);
            list<uint32_t>& lsErrorTime = g_hmLimits[strDomain];
            lsErrorTime.push_front(tmCurrent);
            
            log("get result false");
            msgResp.set_result_code(1);
            msgResp.set_result_string("用户名/密码错误");
        }
    }
    else
    {
        msgResp.set_result_code(2);
        msgResp.set_result_string("服务端内部错误");
    }
    
    
    pPduResp->SetPBMsg(&msgResp);
    pPduResp->SetSeqNum(pPdu->GetSeqNum());
    pPduResp->SetServiceId(IM::BaseDefine::SID_OTHER);
    pPduResp->SetCommandId(IM::BaseDefine::CID_OTHER_VALIDATE_RSP);
    CProxyConn::AddResponsePdu(conn_uuid, pPduResp);
}

};
```

## End--过路小菜

```bash 
signal(SIGCHLD, SIG_IGN);
```

因为并发服务器常常fork很多子进程，子进程终结之后需要服务器进程去wait清理资源。如果将此信号的处理方式设为忽略，可让内核把僵尸子进程转交给init进程去处理，省去了大量僵尸进程占用系统资源。(Linux Only)

对于某些进程，特别是服务器进程往往在请求到来时生成子进程处理请求。如果父进程不等待子进程结束，子进程将成为僵尸进程（zombie）从而占用系统资源。如果父进程等待子进程结束，将增加父进程的负担，影响服务器进程的并发性能。在Linux下可以简单地将 SIGCHLD信号的操作设为SIG_IGN。

```bash
signal(SIGPIPE, SIG_IGN);
```
TCP是全双工的信道, 可以看作两条单工信道, TCP连接两端的两个端点各负责一条. 当对端调用close时, 虽然本意是关闭整个两条信道,
但本端只是收到FIN包. 按照TCP协议的语义, 表示对端只是关闭了其所负责的那一条单工信道, 仍然可以继续接收数据. 也就是说, 因为TCP协议的限制,
一个端点无法获知对端的socket是调用了close还是shutdown.

对一个已经收到FIN包的socket调用read方法,
如果接收缓冲已空, 则返回0, 这就是常说的表示连接关闭. 但第一次对其调用write方法时, 如果发送缓冲没问题, 会返回正确写入(发送).
但发送的报文会导致对端发送RST报文, 因为对端的socket已经调用了close, 完全关闭, 既不发送, 也不接收数据. 所以,
第二次调用write方法(假设在收到RST之后), 会生成SIGPIPE信号, 导致进程退出.

为了避免进程退出, 可以捕获SIGPIPE信号, 或者忽略它, 给它设置SIG_IGN信号处理函数:

signal(SIGPIPE, SIG_IGN);

这样, 第二次调用write方法时, 会返回-1, 同时errno置为SIGPIPE. 程序便能知道对端已经关闭.


### 为什么redisConnectWithTimeout只给了ip+port而没给账户密码
关于“访问redis不需要用户名密码吗”这个问题,我认为:默认不需要的。
你可以在redis.conf 中 修改下面的配置,加上认证。
(把下面配置去掉注释,然后修改foobared为你指定的密码,重启redis-ass="aliyun-text-card" data-text-url="searchblock/database/dts" href= "https://www.aliyun.com/product/dts">server即可生效。)
```bash
# requirepass foobared
```
然后,客户端连接的时候,输入auth 密码 即可认证。
