# RabbitMQ Configure

## /etc/rabbitmq/rabbitmq.conf

```ini
# rabbitmq main configuring
```

## /etc/rabbitmq/rabbitmq-env.conf

```ini
RABBITMQ_NODE_PORT=5672
RABBITMQ_DIST_PORT=25672
ERL_EPMD_PORT=4369
RABBITMQ_DISTRIBUTION_BUFFER_SIZE=128000
RABBITMQ_NODENAME=rabbit@ops-lo-vm-rabbitmq-1
RABBITMQ_CONFIG_FILES=/etc/rabbitmq/conf.d
RABBITMQ_ADVANCED_CONFIG_FILE=/etc/rabbitmq/advanced.config
RABBITMQ_CONF_ENV_FILE=/etc/rabbitmq/rabbitmq-env.conf
RABBITMQ_MNESIA_BASE=/data/rabbitmq/mnesia
RABBITMQ_MNESIA_DIR=$RABBITMQ_MNESIA_BASE/$RABBITMQ_NODENAME
RABBITMQ_LOG_BASE=/data/rabbitmq/logs
RABBITMQ_LOGS=$RABBITMQ_LOG_BASE/$RABBITMQ_NODENAME.log
RABBITMQ_PID_FILE=$RABBITMQ_MNESIA_DIR.pid
```

```shell
# 创建配置目录
sudo mkdir -p /etc/rabbitmq/conf.d
sudo mkdir -p /data/rabbitmq/{mnesia,logs}
sudo chown rabbitmq:rabbitmq -R /data/rabbitmq
sudo chown rabbitmq:rabbitmq -R /etc/rabbitmq/conf.d
```

## /etc/rabbitmq/conf.d/00-defaults.conf

```ini
# This is rabbitmq default configure
# listen address or port
listeners.tcp.default = 5672

# open ssl
ssl_options = none

# tcp listen options. Change when must be troubleshoot network issues
tcp_listen_options.backlog = 128
tcp_listen_options.nodelay = true
tcp_listen_options.linger.on = true
tcp_listen_options.linger.timeout = 0
tcp_listen_options.exit_on_close = false
tcp_listen_options.keepalive = false

# Controls what network interface will be used for communication with other cluster menbers and CLI tools
distribution.listener.interface = 0.0.0.0
distribution.listener.port_range.min = 25672
distribution.listener.port_range.max = 25672

# tcp connections num
num_acceptors.tcp = 10
num_acceptors.ssl = 10

# after socket connection or tls handshake, millseconds,
handshake_timeout = 10000
ssl_handshake_timeout = 5000

# channel option, donn`t set channel_max to 0, it means unlimited
channel_max = 20470

# log level
log.file.level = debug

# cpu
delegate_count = 4

# memory
vm_memory_high_watermark.relative = 0.5
vm_memory_calculation_strategy = allocated
vm_memory_high_watermark_paging_ratio = 0.6

# disk
disk_free_limit.absolute = 5GB

# message
max_message_size = 134217728

## default user
# default_vhost = /
# default_user = admin
# default_pass = du8q6Rhdm&CEpVXL
default_user_tags.administrator = true
default_permissions.configure = .*
default_permissions.read = .*
default_permissions.write = .*

## 仅允许本地登录的用户
# loopback_users.guest = true
# loopback_users.monitoring = true

# cluster
heartbeat = 60
cluster_formation.classic_config.nodes.1 = rabbit@ops-lo-vm-rabbitmq-1
cluster_formation.classic_config.nodes.2 = rabbit@ops-lo-vm-rabbitmq-2
cluster_formation.classic_config.nodes.3 = rabbit@ops-lo-vm-rabbitmq-3

auth_mechanisms.1 = PLAIN
auth_mechanisms.2 = AMQPLAIN
auth_backends.1 = internal
reverse_dns_lookups = false

cluster_partition_handling = ignore
cluster_keepalive_interval = 10000
queue_index_embed_msgs_below = 4096
mnesia_table_loading_retry_timeout = 30000
mnesia_table_loading_retry_limit = 10
mirroring_sync_batch_size = 4096
queue_leader_locator = balanced
proxy_protocol = false
```
