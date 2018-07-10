# Verdaccio 部署 - 起步文档

---

## 优势:

1. 第一次下载存储后, 以后下包直接使用缓存
2. 私有包的更新/发布/共享

## 服务端部署

> 参考我的配置文件 <a href="./config.yaml">config.yaml</a>


### 依赖

`verdaccio`, `pm2`, `nrm`(客户端), `yrm`(若客户端使用yarn)

全局安装 verdaccio 和 pm2

```shell
npm i -g verdaccio pm2
```

### 配置verdaccio

> 配置前若无文件请先执行成功一次 verdaccio

找到 `config.yaml` 文件, 通常在 `C:\Users\${user}\.config\verdaccio`, ${user}为系统帐户名

打开后找到**着重**关注以下配置

```yaml
# 通常第10行
# 该配置为包存储路径, 默认相对路径, 所以此处是C盘的${user}/.config下, 建议修改
storage: ./storage

# 若修改到其他磁盘, 使用以下:
storage: f:/storage

# 通常第22行
# 修改 npm 源(也可不修改, 单纯添加配置)
uplinks:
  # ...
  npmjs:
    url: https://regtistry.npmjs.org/
  # 添加
  taobao:
    url: https://registry.npm.taobao.org
  
packages:
  '@*/*':
    # ...
    # 此处使用taobao源, 如果直接修改的npmjs地址, 该步骤不用配置
    proxy: taobao

# ...
# 通常在 58行左右
listen:
  # 使内网可以访问地址, 默认端口为 4873(固定配置)
  - localhost: 4873
  - 0.0.0.0:4873
  # 内网ip配置(若以上配置无法生效, 添加此条配置) x 更换为自己数值 (ipconfig查看)
  - http://192.168.x.x:4873
  
```

### 使用 PM2 启动并常驻进程

**警告**: 请在含有verdaccio配置目录下启动, 否则会报错

> 若已经报错, 使用以下命令删除记录

```
pm2 list
# * 为显示的进程名
pm2 delete *
```

通常使用全局包目录下的verdaccio:

```shell
cd 'C:\Program Files\nodejs\node_global\node_modules\verdaccio\bin'
pm2 start verdaccio
```

查看状态:

```shell
pm2 show verdaccio

# 若第一行为 status   online, 则启动成功
```



## 客户端配置

客户端修改 registry 源即可, 此处使用nrm配置

nrm:
```shell
# 添加内网地址
nrm add verdaccio http://192.168.x.x:4873
nrm use verdaccio
```

yrm:
```shell
yrm add verdaccio http://192.168.x.x:4873
yrm use verdaccio
```

## 客户端发布package

> 按照web界面教程即可:

新建账号:

```shell
npm add test --registry http://192.168.x.x:4873
username: test
password: ****
```

登陆账号(若和新建账号同一窗口, 跳过此步):

```shell
npm login --registry http://192.168.x.x:4873
```

发布私有包:
```shell
npm publish --registry http://192.168.x.x:4873
```
