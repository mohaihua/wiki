## 安装

安装

```sh
shell> wget -O - 'https://dl.bintray.com/rabbitmq/Keys/rabbitmq-release-signing-key.asc' | sudo apt-key add -
shell> echo 'deb https://dl.bintray.com/rabbitmq/debian xenial main erlang' > /etc/apt/sources.list.d/bintray.rabbitmq.list
shell> apt-get update
shell> apt-get install rabbitmq-server
```

- [Installing on Debian and Ubuntu](https://www.rabbitmq.com/install-debian.html)

创建配置文件

```sh
shell> cd /etc/rabbitmq
shell> wget https://raw.githubusercontent.com/rabbitmq/rabbitmq-server/master/docs/rabbitmq.conf.example
shell> mv rabbitmq.conf.example rabbitmq.conf
```

- [Configuration](https://www.rabbitmq.com/configure.html)

安装控制台

```sh
shell> rabbitmq-plugins enable rabbitmq_management
```

允许 guest 帐号远程登录

```sh
shell> vim /etc/rabbitmq/rabbitmq.conf
loopback_users.guest = false
```

重启服务

```sh
shell> service rabbitmq-server restart
```

访问控制台 

http://localhost:15672

## 管理

启动服务

```sh
shell> service rabbitmq-server start
```

查看运行状态，比如：版本信息

```sh
shell> rabbitmqctl status
```

查看配置信息

```sh
shell> rabbitmqctl environment
```

## 组建集群

修改 hosts 

```sh
shell> vim /etc/hosts
192.168.31.200 rabbit1
192.168.31.201 rabbit2
192.168.31.202 rabbit3
```

复制 Erlang Cookie

```sh
root@rabbit1:> scp /var/lib/rabbitmq/.erlang.cookie root@192.168.31.201:/var/lib/rabbitmq/.erlang.cookie
root@rabbit1:> scp /var/lib/rabbitmq/.erlang.cookie root@192.168.31.202:/var/lib/rabbitmq/.erlang.cookie
```

重启节点，让 cookie 生效

```sh
root@rabbit2:> service rabbitmq-server restart
root@rabbit3:> service rabbitmq-server restart
```

加入集群

```sh
root@rabbit2:> rabbitmqctl stop_app
root@rabbit2:> rabbitmqctl join_cluster rabbit@rabbit1
root@rabbit2:> rabbitmqctl start_app
root@rabbit2:> rabbitmqctl cluster_status
root@rabbit3:> rabbitmqctl stop_app
root@rabbit3:> rabbitmqctl join_cluster rabbit@rabbit1
root@rabbit3:> rabbitmqctl start_app
root@rabbit3:> rabbitmqctl cluster_status
```

- [Clustering Guide](https://www.rabbitmq.com/clustering.html)

## 配置镜像队列

添加 Policy 即可，只要队列名称与策略匹配上即会应用镜像，可在任一节点上执行添加操作。

以 `two.` 开头的队列配置一个镜像

```sh
root@rabbit2:> rabbitmqctl set_policy ha-two "^two\." \
'{"ha-mode":"exactly","ha-params":2,"ha-sync-mode":"automatic"}'
```

以 `ha.` 开头的队列在所有节点配置一份镜像

```
root@rabbit2:> rabbitmqctl set_policy ha-all "^ha\." '{"ha-mode":"all"}'
```

以 `nodes.` 开头的队列在节点 rabbit1 和 rabbit2 各配一份镜像

```sh
root@rabbit2:> rabbitmqctl set_policy ha-nodes "^nodes\." \
'{"ha-mode":"nodes","ha-params":["rabbit@rabbit1", "rabbit@rabbit2"]}'
```

所有队列都配置一份镜像

```sh
root@rabbit2:> rabbitmqctl set_policy ha-two-all ".*" \
'{"ha-mode":"exactly","ha-params":2,"ha-sync-mode":"automatic"}'
```

查看所有策略

```sh
root@rabbit2:> rabbitmqctl list_policies
Listing policies for vhost "/" ...
/       ha-all  ^ha\.   all     {"ha-mode":"all"}       0
/       ha-two  ^two\.  all     {"ha-mode":"exactly","ha-params":2,"ha-sync-mode":"automatic"}  0
/       ha-nodes ^nodes\. all   {"ha-mode":"nodes","ha-params":["rabbit@rabbit1","rabbit@rabbit2"]}  0
```

删除指定策略

```sh 
root@rabbit2:> rabbitmqctl clear_policy ha-two
```

镜像策略可以在 RabbitMQ 运行期间随时取消或变更，会即时生效。

控制台显示效果，队列创建示例 [amqp_ha_consumer.php](https://github.com/aisuhua/rabbit/blob/master/demo01/amqp_ha_consumer.php)

![](https://github.com/aisuhua/rabbit/raw/master/demo01/amqp_ha_consumer.png)

- [Highly Available (Mirrored) Queues](https://www.rabbitmq.com/ha.html#mirroring-arguments)
- [Breaking things with RabbitMQ 3.0](http://www.rabbitmq.com/blog/2012/11/19/breaking-things-with-rabbitmq-3-0/)

## 技巧

清空所有队列

```sh
shell> rabbitmqctl list_queues | awk '{ print $1 }' | sed 's/Listing//' | xargs -L1 rabbitmqctl purge_queue
```

删除所有队列

```sh
shell> rabbitmqctl list_queues | awk '{ print $1 }' | sed 's/Listing//' | xargs -L1 rabbitmqctl delete_queue
```

- [Delete all the queues from RabbitMQ?](https://stackoverflow.com/questions/11459676/delete-all-the-queues-from-rabbitmq)
