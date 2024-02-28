---
id: 6/1
---
# 获取全局安装包
```bash
npm ls -g
```

# 查看全局包位置
```bash
npm config get prefix
```

# 更换源
```bash
# 设置
npm config set registry https://registry.npmjs.org/

# 读取
npm config get registry
```

# 全局安装/删除
```bash
# 安装
npm install -g <pkg-name>
npm i -g <pkg-name>

# 删除
npm uninstall -g <pkg-name>
npm r -g <pkg-name> # r 代表 remove
```

# 查看包最新版本
```bash
npm view <pkg-name> version
```