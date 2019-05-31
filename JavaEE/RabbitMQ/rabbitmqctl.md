# RabbitMQ  命令

```bash
➜  sbin ./rabbitmq-server &
[1] 38248
```





## rabbitmqctl

> 这个功能比 rabbitmq-server 更为强大

```bash
./rabbitmqctl status // 查看节点状态
```

```bash
➜  sbin ./rabbitmqctl add_user username password // 添加用户
Adding user "username" ...
```

```bash
➜  sbin ./rabbitmqctl list_users // 列出所有用户
Listing users ...
user	tags
username	[]
testMqUser	[]
guest	[administrator]
```

```bash
rabbitmqctl delete_user username // 删除用户
rabbitmqctl clear_permissions -p vhostpath username // 清除用户权限
rabbitmqctl list_user_permissions username // 显示用户的权限
rabbitmqctl change_password username newpassword // 修改用户的密码
rabbitmqctl set_permissions -p vhostpath username ".*"// 设置用户的权限
rabbitmqctl add_vhost vhostpath // 创建虚拟主机
rabbitmqctl list_vhosts // 列出所有虚拟主机
rabbitmqctl list_permissions -p vhostpath // 列出虚拟主机上所有权限
rabbitmqctl delete_vhost vhostpath // 删除虚拟主机
rabbitmqctl list_queues // 查看所有队列信息
rabbitmqctl -p vhostpath purge_queue blue // 清除队列的消息
rabbitmqctl reset // 移除所有数据 要在 rabbitmqctl stop_app 后使用
rabbitmqctl join_cluster <clusternode> [--ram] // 组成集群命令
rabbitmqctl cluster_status // 查看集群状态
rabbitmqctl change_cluster_node_type disc | ram // 修改集群节点的存储形式
rabbitmqctl forget_cluster_node [--offline] // 忘记节点 (摘除节点)
rabbitmqctl rename_cluster_node oldnode1 newnode1 [oldnode2] [newnode2..] // 修改节点名称

```

