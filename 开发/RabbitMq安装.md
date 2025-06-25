# RabbitMq安装及其使用

## 消息队列说明

**消息队列中间件是分布式系统中重要的组件，主要解决应用耦合，异步消息，流量削锋等问题。
实现高性能，高可用，可伸缩和最终一致性架构。是大型分布式系统不可缺少的中间件。  
目前在生产环境，使用较多的消息队列有ActiveMQ，RabbitMQ，ZeroMQ，Kafka，MetaMQ，RocketMQ等。**

## 消息队列应用场景

**消息队列在实际应用中常用的使用场景。异步处理，应用解耦，流量削锋和消息通讯四个场景。**

## 异步处理

**异步处理是消息队列最常见的使用场景。在实际应用中，一个请求需要执行多个操作，或者一个操作需要调用其他系统接口，这些操作都可以使用消息队列来完成。**

## 应用解耦

**应用解耦是指将消息队列放在应用系统之间，用于应用解耦。在实际应用中，一个请求需要调用多个接口，或者一个操作需要调用其他系统接口，这些操作都可以使用消息队列来完成。**

## 流量削锋

**流量削锋是指在高并发的情况下，为了保护系统的稳定性，使用消息队列来削峰填谷。在实际应用中，一个请求需要调用多个接口，或者一个操作需要调用其他系统接口，这些操作都可以使用消息队列来完成。**

## 消息通讯

**消息通讯是指在分布式系统中，各个节点之间使用消息队列来通讯。在实际应用中，一个请求需要调用多个接口，或者一个操作需要调用其他系统接口，这些操作都可以使用消息队列来完成。**

## 下面主要讲述RabbitMQ

**RabbitMQ是一个由erlang开发的AMQP(Advaned Message Queue Protocol)的开源实现。**

## RabbitMQ的安装

### 安装Erlang环境

**RabbitMQ是由Erlang语言开发的，所以在安装RabbitMQ之前，需要先安装Erlang环境。**

**Erlang官网：<http://www.erlang.org/>**

**Erlang下载地址：<http://www.erlang.org/downloads>**

### 安装[Erlang](https://so.csdn.net/so/search?q=Erlang&spm=1001.2101.3001.7020)环境

```shell
wget http://erlang.org/download/otp_src_22.0.tar.gz tar -zxvf otp_src_22.0.tar.gz cd otp_src_22.0 ./configure make && make install
```

**安装完成后执行 `erl`是否安装成功**

### 安装 [RabbitMq](https://so.csdn.net/so/search?q=RabbitMq&spm=1001.2101.3001.7020)

```shell
rpm --import https://www.rabbitmq.com/rabbitmq-signing-key-public.asc yum -y install https://github.com/rabbitmq/rabbitmq-server/releases/download/v3.7.14/rabbitmq-server-3.7.14-1.el7.noarch.rpm
```

### 安装WEB管理插件

```shell
rabbitmq-plugins enable rabbitmq_management
```

### 启动RabbitMq

```shell
systemctl start rabbitmq-server
```

默认端口为：15672  访问地址为：localhost:15672  默认的账号：guest  密码：guest

RabbitMQ Server 默认`guest`用户，只能`localhost`地址访问，我们还需要创建管理员用户。

参考下面 创建管理用户命令

### 卸载 RabbitMQ 与 Erlang

```shell
/sbin/service rabbitmq-server stopyum list | grep rabbitmqyum -y remove rabbitmq-server.noarch yum list | grep erlangyum -y remove erlang-*yum remove erlang.x86_64 rm -rf /usr/lib64/erlang rm -rf /var/lib/rabbitmq/     # 清除rabbitmq配置文件
```

### RabbitMq 的使用

- 启动

```shell
service rabbitmq-server start
```

- 停止

```shell
service rabbitmq-server stop
```

- 查看运行状态

```shell
service rabbitmq-server status
```

- 创建管理用户

```shell
rabbitmqctl add_user jiaflu 123456
```

- 设置管理员

RabbitMQ Server 默认`guest`用户，只能`localhost`地址访问，我们还需要创建管理员用户。

```shell
rabbitmqctl set_user_tags jiaflu administrator
```

- 设置权限

```shell
rabbitmqctl set_permissions jiaflu ".*" ".*" ".*"  # 赋予 all
```

- 查看（指定 hostpath）所有用户的权限信息

```less
rabbitmqctl  list_permissions  [-p  VHostPath]
```

- 查看指定用户的权限信息

```shell
rabbitmqctl  list_user_permissions  jiaflu
```

- 清除用户的权限信息

```less
rabbitmqctl  clear_permissions  [-p VHostPath]  jiaflu
```

- 删除用户

```shell
rabbitmqctl delete_user jiaflu
```

- 查看所有用户

```shell
rabbitmqctl list_users
```

### 应用管理指令

- `rabbitmqctl stop [pid_file]`

用于停止运行 RabbitMQ 的 Erlang 虚拟机和 RabbitMQ 服务应用。如果指定了 pid\_file，还需要等待指定进程的结束。

- `rabbitmqctl shutdown`

用于停止运行 RabbitMQ 的 Erlang 虚拟机和 RabbitMQ 服务应用。执行这个命令会阻塞直到 Erlang 虚拟机进程退出。如果 RabbitMQ 没有关闭成功，则会返回一个非零值。

这个命令和 `rabbitmqctl stop` 不同的是，它不需要指定 pid\_file 而可以阻塞等待指定进程的关闭。

- `rabbitmqctl stop_app`

停止 RabbitMQ 服务应用，但是 Erlang 虚拟机还是处于运行状态。

- `rabbitmqctl start_app`

启动 RabbitMQ 应用。

- `rabbitmqctl wait [pid_file]`

等待 RabbitMQ 应用的启动。

- `rabbitmqctl reset`

将 RabbitMQ 节点重置还原到最初状态。包括从原来所在的集群中删除此节点，从管理数据库中删除所有的配置数据，如已配置的用户、vhost等，以及删除所有的持久化消息。

- `rabbitmqctl force_reset`

强制将 RabbitMQ 节点重置还原到最初状态。它只能在数据库或集群配置已损坏的情况下使用。与 `rabbitmqctl reset` 命令一样，执行 `rabbitmqctl force_reset` 命令前必须先停止 RabbitMQ 应用。

- `rabbitmqctl rotate_logs {suffix}`

指示 RabbitMQ 节点轮换日志文件。

- `rabbitmqctl status`

显示 RabbitMQ 节点的状态信息。

- `rabbitmqctl cluster_status`

显示 RabbitMQ 集群的状态信息。
