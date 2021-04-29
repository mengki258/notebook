&nbsp;&nbsp;补充上次关于[oracle的网络配置](https://blog.csdn.net/Msy_xiaodu/article/details/109105976)介绍
这次主要介绍`tnsnames.ora`,`listener.ora`,以及`plsql`在其中的使用
### 1. tnsnames.ora
> 用来映射指定服务的实例，相当于常见dns网络与ip映射。发生的位置在`客户端`，相对发生作用在服务端的便是监听器

我们先看下有哪些配置、是有哪些相应的作用
```sql
<data source alias> =
  (DESCRIPTION =
    (ADDRESS = (PROTOCOL = TCP)(HOST = <hostname or IP>)(PORT = <port>))
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = <database service name>)
    )
  )
  ---------------------------------------------------
服务名=
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = localhost)(PORT = 1521)）
      (ADDRESS = (PROTOCOL = TCP)(HOST = 192.168.43.113)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = 对应上述地址的实例名)
    )
  )

```
---
上面主要就是将已经有的服务用一个名称来代替，如在ip为192.168.1.23、端口为1521，上有一个oracle服务，实例名时orcl,而我们用23orcl服务名来代替，写成配置便是下面
```sql
23orcl=
  (DESCRIPTION =
    (ADDRESS_LIST =
      (ADDRESS = (PROTOCOL = TCP)(HOST = 192.168.1.23)(PORT = 1521))
    )
    (CONNECT_DATA =
      (SERVER = DEDICATED)
      (SERVICE_NAME = orcl)
    )
  )
```
使用plsql时显示的效果是在登录的时候，可以选择相应的服务名

### 2. listener.ora
> 主要作用在服务端，负责监听地址列表来的请求，并转发到实例处理

语法格式如下 ：
```sql
 LISTENER =
  (ADDRESS_LIST=
(ADDRESS=(PROTOCOL=tcp)(HOST=localhost)(PORT=1521))
(ADDRESS=(PROTOCOL=ipc)(KEY=PNPKEY)))
```
如我本机有两个ip(192.168.1.32,localhost)要监听的，都是用tcp通信的。
则配置应配置成如下：
```sql
 LISTENER =
  (ADDRESS_LIST=
(ADDRESS=(PROTOCOL=tcp)(HOST=localhost)(PORT=1521))
(ADDRESS=(PROTOCOL=tcp)(HOST=192.168.1.32)(PORT=1521))
```
更改好后需要重启监听器使其生效，有三种方式
- 1`services.msc`,打开服务，找到oracle的监听程序，右键选择重启
- 2`lsnrctl`命令，通过加载配置来实现，命令为
```cmd
lsnrctl reload
```

- 3 同2，不过要两步，先关闭，再启动
```cmd
lsnrctl stop
lsnrctl start
```

