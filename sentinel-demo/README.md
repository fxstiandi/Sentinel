# Sentinel Examples

The examples demonstrate:

- How to leverage basic features (e.g. flow control, circuit breaking, load protection) of Sentinel
- How to use various data source extensions of Sentinel (e.g. file, Nacos, ZooKeeper)
- How to use Dubbo with Sentinel
- How to use Apache RocketMQ client with Sentinel
- How to use Sentinel annotation support
- How to add your own logic to Sentinel using Slot Chain SPI

# Sentinel改造

**分支：cluster-demo-apollo-datasouce_1.8.6**

* 改造sentinel-demo-cluster-embedded以支持apollo配置中心的动态数据源（**Apollo配置中心 → Sentinel 数据源 → Sentinel ClusterFlowRuleManager）**
* vm options

  ```
  -Dcsp.sentinel.log.use.pid=true //日志文件名中是否加入进程号，区分同一机器上的多个sentinel应用；若在本地启动多个 Demo 示例，需要加上 `-Dcsp.sentinel.log.use.pid=true` 参数，否则控制台显示监控会不准确。
  -Dapollo.meta=https://apollo-config-cn-dev.nioint.com
  -Dapp.id=uds-user-docker // apollo中的app
  -Dproject.name=uds-user-docker // sentinel集群中使用，用于标识不同的namespace
  -Dserver.port=8086
  -Dcsp.sentinel.dashboard.server=localhost:8088 // dashboard的地址
  -Dcsp.sentinel.api.port=8286 // dashboard控制台监控客户端API 的端口，本地启动transport HTTP API Server 的端口号
  ```
* apollo 配置
  ```
  uds-user-docker-flow-rules = [
    {
        "resource": "sayHello",
        "grade": 1,
        "count": 3,
        "strategy": 0,
        "controlBehavior": 0,
        "clusterMode": true,
        "clusterConfig": {
            "flowId": 111,
            "thresholdType": 1,
            "fallbackToLocalWhenFail": false
        }
    },
    {
        "resource": "sayHi",
        "grade": 1,
        "count": 3,
        "strategy": 0,
        "controlBehavior": 0,
        "clusterMode": true,
        "clusterConfig": {
            "flowId": 112,
            "thresholdType": 1,
            "fallbackToLocalWhenFail": false
        }
    }
  ]
  uds-user-docker-cluster-client-config = {"requestTimeout": 500}
  uds-user-docker-cluster-map= [
    {
        "clientSet": [
            "10.111.136.21@8283",
            "10.111.136.21@8286"
        ],
        "ip": "10.111.136.21",
        "machineId": "10.111.136.21@8282",
        "port": 12356,
        "globalFlow": 900
    }
  ]
  ```
