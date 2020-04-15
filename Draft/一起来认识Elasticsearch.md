### 一起来认识Elasticsearch

#### 1.介绍

#### 2.安装

下载最新的es版本elasticsearch-7.6.2-linux-x86_64.tar.gz，解压到elasticsearch目录

执行如下命令启动es

```
./bin/elasticsearch
```

执行命令后发现有报错

```
ERROR: [3] bootstrap checks failed
[1]: max file descriptors [4096] for elasticsearch process is too low, increase to at least [65535]
[2]: max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]
[3]: the default discovery settings are unsuitable for production use; at least one of [discovery.seed_hosts, discovery.seed_providers, cluster.initial_master_nodes] must be configured
ERROR: Elasticsearch did not exit normally - check the logs at /home/mindspore/software/elasticsearch/elasticsearch-7.6.2/logs/elasticsearch.log
```

根据提示信息，可以发现是本机，`fd` 虚拟内存等配置问题，解决办法如下:

1. max file descriptors [4096] for elasticsearch process is too low, increase to at least [65535]

   每个进程最大打开的文件句柄个数，可以通过命令查看当前设置情况

   ```
   ulimit -Hn // 最大个数
   ulimit -Sn // 最小个数
   ```

   修改`/etc/security/limits.conf`文件可以配置最大最下文件句柄个数，修改后用户重新登录后生效。增加如下2行配置

   ```
   * soft nofile 65536
   * hard nofile 65536
   ```

2. max virtual memory areas vm.max_map_count [65530] is too low, increase to at least [262144]

   修改/etc/sysctl.conf文件，增加配置vm.max_map_count=262144

   ```
   vi /etc/sysctl.conf
   sysctl -p
   ```

3. the default discovery settings are unsuitable for production use; at least one of [discovery.seed_hosts, discovery.seed_providers, cluster.initial_master_nodes] must be configured

   修改配置文件`elasticsearch.yml`，修改如下配置

   ```
   vim config/elasticsearch.yml
   node.name: node-1 前面的#打开
   network.host: 0.0.0.0 // 任何ip都可以访问
   cluster.initial_master_nodes: ["node-1"] // 为node.name对应值
   // 如下这2项设置，可使外表浏览器顺利访问
   http.cors.enabled: true
   http.cors.allow-origin: "*"
   ```

   

   https://www.cnblogs.com/zhi-leaf/p/8484337.html

   

#### 3.认识ES