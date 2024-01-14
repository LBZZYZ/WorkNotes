---
title: protobuf-Install and Use
date: 2021-05-31 23:43:16
tags: protobuf
description: 本文主要讲解protobuf的安装与使用
---
# 前言
我们知道，如果想要开发一款通讯软件，则需要对各个终端传输的数据设计合适的数据协议。将数据按照拟定协议进行装载，再对其进行序列化操作后，发送端即可借助网络模块将数据发送至接收端；接收端再进行反序列化操作后，即可拿到所需数据。

而本文所介绍的protobuf就是一种序列化与反序列化的协议。下面给出Google官方的定义：
```bash
Protocol buffers are Google's language-neutral, platform-neutral, extensible mechanism for serializing structured data – think XML, but smaller, faster, and simpler. You define how you want your data to be structured once, then you can use special generated source code to easily write and read your structured data to and from a variety of data streams and using a variety of languages.
```

[github项目地址](https://github.com/protocolbuffers/protobuf)

[开发文档](https://developers.google.com/protocol-buffers/)

Protobuf支持多种语言进行序列化，本文仅以 C++ 介绍如何安装并使用protobuf，系统为Mac OS。

# 安装
这边介绍两种安装方式，分别是binary安装以及source code编译安装。

## binary 安装
这种安装方式有一个非常大的优点就是安装过程十分简单，如果你不需要对protobuf进行客设化修改，只是想再项目中使用的话，推荐这种安装方式。

[包下载地址](https://github.com/protocolbuffers/protobuf/releases)

在上面的地址中下载名字中带有protoc的包就可以获取到protobuf的编译器了，这种方式虽然简便，但是没办法获取到运行时库，因此如果想要在项目中使用protobuf，推荐直接使用下面的源代码安装方式进行安装。

## 源码编译安装
我这边使用的版本为：protobuf-cpp-3.17.1.tar.gz，你也可以按需下载自己想要的安装包版本。

下载完成后可以在终端使用```tar -zxvf protobuf-cpp-3.17.1.tar.gz```进行解压。

解压完成后：
1. cd protobuf-cpp-3.17.1
2. ./autogen.sh
3. ./configure --prefix=/usr/local/protobuf(这里制定你希望安装到的路径)
4. make 源码编译，这一步大概需要十分钟。
5. make check
6. make install

source code编译安装完成后，会在--prefix所制定的路径下生成一个bin:protoc，我们需要把这个bin链接到/usr/local/bin下才可以在终端正常使用，链接操作可以使用下方指令：
```bash
ln -s /usr/local/protobuf/bin/protoc /usr/local/bin/protoc
```

链接成功后可以在终端下查看是否安装成功：
```bash
protoc --version
```
# protobuf的使用
安装完成后，我们就可以开始书写.proto文件，并利用生成的编译器做相关操作了。本文以官方文档给出的“address book”的例子简单介绍一下protobuf for c++的简单使用。

## 地址簿程序
这是一个可以从文件中读取和像文件中写入人们的联系方式的应用程序。地址簿中的每个人都有姓名，ID，e-mail以及电话号码。

我们可以使用三种方法序列化这些数据：
1. 二进制，长期来看不便于维护，程序将变得脆弱。
2. 自定义协议，对大数据量稍显无力。
3. XML，这种方法可读性很好，但序列化与反序列化性能损失较大。

使用protobuf可以解决上述三种方式存在的缺陷。

## .proto定义
将数据结构序列化的第一步就是书写.proto文件。首先，为每个想要序列化的数据结构定义一个```message```字段，然后为这个字段的每一项制定```name```和```type```。


下面就是地址簿程序所使用的.proto文件。


```bash
syntax = "proto2";

package tutorial;

message Person {
  optional string name = 1;
  optional int32 id = 2;
  optional string email = 3;

  enum PhoneType {
    MOBILE = 0;
    HOME = 1;
    WORK = 2;
  }

  message PhoneNumber {
    optional string number = 1;
    optional PhoneType type = 2 [default = HOME];
  }

  repeated PhoneNumber phones = 4;
}

message AddressBook {
  repeated Person people = 1;
}
```

我们一步一步来看这个proto文件。首先，在文件中的第三行有```package tutorial```。对于这句话，你需要明确两件事情，第一，package名称是唯一的，这就避免了在同一项目中的不同proto文件产生冲突；第二，在C++中，package名同样代表编译生成的类所在的命名空间。


接下来就是消息的定义部分。我们看到上文中定义了三个message，其中```Person```包含了```PhoneNumber```，```AddressBook```又包含了```Person```。

有很多基础类型都可以作为一个消息中的字段类型，如bool,int32,float,double和string。已经定义的message也可以作为其他message的一个字段值。

enum可以在指定的取值中选取一个。

也可以在message中定义另一个message，如在Person中定义的PhoneNumber。

### 消息编号
编号必须指定且不能重复，如Person中e-mail字段的编号为3。

每个字段必须使用以下修饰符之一进行注释：

optional: 该字段可以设置0次或1次。如果未设置可选字段值，则使用默认值。对于简单类型，您可以指定自己的默认值，就像我们type在示例中为电话号码所做的那样。否则，使用系统默认值：数字类型为零，字符串为空字符串，布尔值为 false。对于嵌入的消息，默认值始终是消息的“默认实例”或“原型”，它没有设置任何字段。调用访问器以获取尚未显式设置的可选（或必需）字段的值始终返回该字段的默认值。


repeated：该字段可以重复任意次数（包括零次）。重复值的顺序将保留在协议缓冲区中。将重复字段视为动态大小的数组。


required：必须提供该字段的值，否则消息将被视为“未初始化”。如果libprotobuf在调试模式下编译，序列化未初始化的消息将导致断言失败。在优化的构建中，会跳过检查，无论如何都会写入消息。但是，解析未初始化的消息总是会失败（通过false从 parse 方法返回）。除此之外，必填字段的行为与可选字段完全相同。```proto3中此字段已经被删除。```

## 编译.proto
.proto文件书写完成后，即可通过proto程序生成读取和写入AddressBook message所需的类。

```bash
protoc -I=$SRC --cpp_out=$DST $SRC/addressbook.proto
```


这会在--cpp_out指定的目录下生成addressbook.pb.h与addressbook.pb.cc

## API的使用
打开addressbook.pb.h可以看到，protobuf为每一个message都生成了一个单独的类，并且为每个message中的表项都生成了一些获取与设置的api。下面截取了addressboot.pb.h中的部分片段：
```cpp
  // repeated .tutorial.Person.PhoneNumber phones = 4;
  int phones_size() const;
  private:
  int _internal_phones_size() const;
  public:
  void clear_phones();
  ::tutorial::Person_PhoneNumber* mutable_phones(int index);
  ::PROTOBUF_NAMESPACE_ID::RepeatedPtrField< ::tutorial::Person_PhoneNumber >*
      mutable_phones();
  private:
  const ::tutorial::Person_PhoneNumber& _internal_phones(int index) const;
  ::tutorial::Person_PhoneNumber* _internal_add_phones();
  public:
  const ::tutorial::Person_PhoneNumber& phones(int index) const;
  ::tutorial::Person_PhoneNumber* add_phones();
  const ::PROTOBUF_NAMESPACE_ID::RepeatedPtrField< ::tutorial::Person_PhoneNumber >&
      phones() const;

  // optional string name = 1;
  bool has_name() const;
  private:
  bool _internal_has_name() const;
  public:
  void clear_name();
  const std::string& name() const;
  template <typename ArgT0 = const std::string&, typename... ArgT>
  void set_name(ArgT0&& arg0, ArgT... args);
  std::string* mutable_name();
  PROTOBUF_FUTURE_MUST_USE_RESULT std::string* release_name();
  void set_allocated_name(std::string* name);
  private:
  const std::string& _internal_name() const;
  inline PROTOBUF_ALWAYS_INLINE void _internal_set_name(const std::string& value);
  std::string* _internal_mutable_name();
  public:

  // optional string email = 3;
  bool has_email() const;
  void clear_email();
  const std::string& email() const;
  template <typename ArgT0 = const std::string&, typename... ArgT>
  void set_email(ArgT0&& arg0, ArgT... args);
  std::string* mutable_email();
  PROTOBUF_FUTURE_MUST_USE_RESULT std::string* release_email();
  void set_allocated_email(std::string* email);

  // optional int32 id = 2;
  bool has_id() const;
  private:
  bool _internal_has_id() const;
  public:
  void clear_id();
  ::PROTOBUF_NAMESPACE_ID::int32 id() const;
  void set_id(::PROTOBUF_NAMESPACE_ID::int32 value);
  private:
  ::PROTOBUF_NAMESPACE_ID::int32 _internal_id() const;
  void _internal_set_id(::PROTOBUF_NAMESPACE_ID::int32 value);
  public:

  // @@protoc_insertion_point(class_scope:tutorial.Person)
 private:
  class _Internal;

  template <typename T> friend class ::PROTOBUF_NAMESPACE_ID::Arena::InternalHelper;
  typedef void InternalArenaConstructable_;
  typedef void DestructorSkippable_;
  ::PROTOBUF_NAMESPACE_ID::internal::HasBits<1> _has_bits_;
  mutable ::PROTOBUF_NAMESPACE_ID::internal::CachedSize _cached_size_;
  ::PROTOBUF_NAMESPACE_ID::RepeatedPtrField< ::tutorial::Person_PhoneNumber > phones_;
  ::PROTOBUF_NAMESPACE_ID::internal::ArenaStringPtr name_;
  ::PROTOBUF_NAMESPACE_ID::internal::ArenaStringPtr email_;
  ::PROTOBUF_NAMESPACE_ID::int32 id_;
  friend struct ::TableStruct_address_2eproto;
};
```

我们可以看到，对于message中的每个字段，protobuf生成了四个关键的方法：
1. 获取字段值：函数名称与字段命名完全相同，如name()
2. 设置字段值：函数名称以set_为开头
3. 判断字段是否被设置：这个函数只存在于optional字段中，以has_开头，如果字段有被设置，则返回true
4. 清空已经被设置的变量：函数名称以clear_开头。

除了上述四个基础方法外，对于name和email这类字符串还会存在一些特有方法:
1. mutable_方法：直接指向字符串的setter和getter,当字段没有被设置时，这个函数也可以被调用，并初始化该字段为空，并返回指向该字段的指针。

重复字段也有一些特殊的方法——如果你查看重复phones字段的方法，你会发现你可以

检查重复字段的_size（换句话说，有多少电话号码与此相关联Person）。
使用其索引获取指定的电话号码。
更新指定索引处的现有电话号码。
将另一个电话号码添加到消息中，然后您可以对其进行编辑（重复的标量类型有一个add_只允许您传入新值）。
有关协议编译器为任何特定字段定义确切生成哪些成员的更多信息，请参阅C++ 生成的代码参考。





# 参考链接
1. https://blog.csdn.net/huangdenan/article/details/111867350
