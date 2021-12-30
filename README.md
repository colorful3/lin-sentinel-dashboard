# lin-sentinel-dashboard
## alibaba sentinel dashboard 规则push模式持久化到 nacos 数据源，基于 Sentinel 1.8.2 版本重构

## 0.概述
Sentinel 是面向分布式服务架构的流量控制组件。
如果你直接从官方仓库下载使用Sentinel，通过 dashboard 配置的规则，这些规则默认是存储在内存中的，重启应用后就会消失，
换句话说，默认情况下，这些规则是不会被持久化的。试想如果在生产环境中一旦重启就丢失规则，这样的运维体验简直堪称灾难。
但 Sentinel 早已为我们有所考虑，它提供多种不同的数据源来持久化规则配置，包括file，redis、nacos、zoomkeeper等。  
网络上可以很容易地搜索到如何通过重构代码实现流控规则的持久化。  
但要知道，我们需要持久化的不仅仅只有流控规则，诸如 gateway 的 API分组、热点规则、系统规则等等也都是需要持久化的。本工程将实现 dashboard 中所有类型数据的持久化。

## 1.启动控制台
### 1.1 依赖
- JDK 1.8 or later
- nacos 2.0.x

### 1.2 获取控制台
您可以从最新版本的源码自行构建 Sentinel 控制台：

- 下载 [控制台](https://github.com/colorful3/lin-sentinel-dashboard) 工程
- 使用以下命令将代码打包成一个 fat jar: `mvn clean install -Dmaven.test.skip=true` 

(todo 发布一个release) 您也可以从 [release](todo) 页面 下载最新版本的控制台 jar 包。  
由于一般需要修改 nacos 相关配置，建议使用第一种方式。

### 1.3 修改配置
修改配置文件 lin-sentinel-dashboard/src/main/resources/application.properties，根据您的实际情况修改 nacos 相关配置：
```properties
# the nacos datasource config of sentinel
# nacos 服务地址和端口号
spring.cloud.sentinel.datasource.nacos.server-addr=http://localhost:8848
# 规则的分组
spring.cloud.sentinel.datasource.nacos.group-id=sentinel-rule
# 命名空间
spring.cloud.sentinel.datasource.nacos.namespace=
```

### 1.4 启动服务
使用如下命令启动控制台：
```bash
java -Dserver.port=8080 -Dcsp.sentinel.dashboard.server=localhost:8080 -Dproject.name=sentinel-dashboard -jar sentinel-dashboard.jar
```
其中 -Dserver.port=8080 用于指定 Sentinel 控制台端口为 8080。

