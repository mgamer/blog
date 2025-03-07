### 导读

随着国家新基建的开展，在工业领域，如何应用物联网、边缘计算、大数据分析等技术推进「中国制造 2025」以及「工业 4.0」进程成为备受关注的热点。 **本文将介绍如何基于社区中的开源和商业化软件，搭建一个集工业数据采集、汇聚、清洗、存储分析以及可视化展示等能力于一体的工业互联网平台。** 在此方案基础上，读者可以根据自身需求调整方案设计，从而搭建满足实际业务需求的工业互联网平台，加速实现工业智能化转型。

### 工业互联网的挑战

据中国工业和信息化部信息中心最新的[《工业互联网体系架构（版本2.0）》](https://www.miit.gov.cn/ztzl/rdzt/gyhlw/jyjl/art/2020/art_e8333f59a1af4c0590247987108d318c.html)所述，工业互联网架构下的边缘层主要实现以下功能：

> 边缘层提供海量工业数据接入、转换、数据预处理和边缘分析应用等功能。
>
> - 一是工业数据接入，包括机器人、机床、高炉等工业设备数据接入能力，以及 ERP、MES、WMS 等信息系统数据接入能力，实现对各类工业数据的大范围、深层次采集和连接；
>
> - 二是协议解析与数据预处理，将采集连接的各类多源异构数据进行格式统一和语义解析，并进行数据剔除、压缩、缓存等操作后传输至云端；
> - 三是边缘分析应用，重点是面向高实时应用场景，在边缘侧开展实时分析与反馈控制，并提供边缘应用开发所需的资源调度、运行维护、开发调试等各类功能；

然而工业领域多种协议并存，如何把异构设备连接并将数据汇聚，实现后续的边缘或云端计算，是无法回避的问题。目前一般的方案有以下两种：

- **在边缘端采用传统程序直接连接和控制设备。** 这种方式一般针对于特定设备或型号，定制化程度比较高，但是 **可移植性、可重用性、可扩展性和灵活性均较差，** 无法对接现在的大数据和 AI 后台，不具备对数据进行深入分析并产生更多价值的能力；
- **将云端架构的软件部署到边缘端来实现。** 云端由 IT 产业主导，信息化程度非常高。但基于成本考虑，边缘端的硬件设备一般计算能力有限，因此将云端架构的软件直接迁移到边缘设备上并不可行。 **在边缘端实现上述功能的软件必须加以优化，才能适应边缘端的实际运行情况；** 

为了融合在边缘 CT 和 IT 端的不同需求， **EMQ 正式发布[工业互联网一体化解决方案](https://github.com/emqx/edge-stack/)，** 以帮助领域内相关企业应对工业互联网边缘端面临的问题与挑战。这一方案适合在边缘端部署，具备各类协议解析、多源数据接入和数据分析能力，以云边协同的方式快速实现工业互联网架构下边缘层的功能。

### 解决方案1：轻量级边缘计算工业互联网平台

该方案可实现边缘端的工业协议解析、数据汇聚和流式分析，将通过流式分析的数据存入在边缘部署的轻量级时序数据库中，在边缘端运行的应用即可从时序数据库中获取和处理数据，呈现给最终用户；在边缘端运行的 `Edge Manager` 提供了一个管理控制台，可以很方便地实现软件配置和管理。

![iiot_sol_1.jpg](https://static.emqx.net/images/27216751b3021928e075832638200834.jpg)

#### 适用场景

对实时性要求比较高，可以 **作为边缘自主独立应用运行在边缘的网关或者工控机上，** 与云端没有交互。该方案中所有的计算和存储等都在边缘端实现，因此对硬件的计算、存储等有一定的要求。用户可以根据实际情况，将软件和应用分开部署在多个硬件设备上。

#### 软件列表

本方案会用到以下列表中的软件产品。

| 编号 | 名称                                                       | 提供商       | 开源              |
| ---- | ---------------------------------------------------------- | ------------ | ----------------- |
| 1    | [EMQ X Neuron](https://www.emqx.com/zh/products/neuron)     | EMQ          | 否 - `1`          |
| 2    | [EMQ X Edge](https://www.emqx.com/zh/products/emqx)         | EMQ          | 是, Apache 2.0    |
| 3    | [EMQ X Kuiper](https://github.com/lf-edge/ekuiper)     | EMQ          | 是, Apache 2.0    |
| 4    | [Edge manager](https://hub.docker.com/r/emqx/edge-manager) | EMQ          | 否 - `2`          |
| 5    | TDengine                                                   | Taosdata     | 是, GNU AGPL v3.0 |
| 6    | Grafana                                                    | Grafana Labs | 是, Apache 2.0    |

`1:` Neuron 未来规划会将基础功能进行开源，目前用户下载后可以免费使用，如果内置试用版本的数据采集点数不够，可以通过 EMQ 网站进行[在线申请](https://www.emqx.com/zh/downloads?product=neuron)。

`2:` 用户可以免费使用，免费版本中除了管理的节点数目受限之外，用户可以使用所有功能。如果用户想试用更多的节点管理功能，可以通过 EMQ 网站进行[在线申请](https://www.emqx.com/zh/downloads?product=neuron)。

**产品基本功能描述**

1. Neuron：工业物联网数据采集
   - 支持了 Modbus，OPCUA，IEC61850，IEC104 和 BACnet 等众多协议和设备；
   - 管理控制台，用户可以在浏览器中进行可视化的配置，实现跨工业设备数据的接入；
   - 北向标准 MQTT 数据发送，根据用户指定配置，将数据发送至指定的 MQTT 消息服务器中；
   - 南向控制接口，结合 Kuiper 提供的规则引擎功能，实现基于规则的设备控制；
   - 本地数据存储，实现设备原始数据的存储和查看；
2. Edge：轻量级 MQTT 消息服务器
   - 实现工业设备消息汇聚；
   - 连接流式处理软件，处理工业数据；
   - 接受规则引擎的控制消息，传递至 Neuron 实现设备控制；
   - 对接云端消息服务器，实现离线消息缓存；
3. Kuiper：基于 SQL 的 IoT 流式处理框架
   - 可以持续地消费、过滤、转换和路由来自于 Edge 中的数据；
   - 基于此实现流分析、规则引擎和消息推送；
   - 可以扩展实现对不同的数据源的支持，实现在边缘端 ERP、MES、WMS 和工业数据的互联互通，以及实时分析与处理；
4. Edge manager：集成了 Neuron、Edge 和 Kuiper 三者的网页管理控制台
   - 在 web 端轻松进行统一可视化管理
   - 实现了包括对 Neuron 的配置下发，数据发送目标的配置；
   - Edge 状态的管理；
   - Kuiper 流、规则和插件等管理；
5. TDengine：开源时序数据库，实现对采集数据的处理
   - 将原始数据，或者是经过流式处理之后的数据存储到数据库中；
   - 应用程序可以通过 SQL 来实现对数据的访问和分析处理；
6. Grafana：模拟客户的应用，将存储在 TDengine 中的数据进行展示

**支持的软硬件环境**

- 树莓派、网关、工控机
- x86 & ARM - 64位和32位
- 常见 Linux 系统的支持
- 物理机，Docker & KubeEdge 等支持

| 编号 | 名称                                                       | x86*32 | x86*64 | ARM 7 | ARM 64 | PPC64 | Mac  | Docker |
| ---- | ---------------------------------------------------------- | ------ | ------ | ----- | ------ | ----- | ---- | ------ |
| 1    | [EMQ X Neuron](https://www.emqx.com/zh/products/neuron)     |        | ☑      | ☑     | ☑      | ☑     |      | ☑      |
| 2    | [EMQ X Edge](https://www.emqx.com/zh/products/emqx)         | ☑      | ☑      | ☑     | ☑      | ☑     | ☑    | ☑      |
| 3    | [EMQ X Kuiper](https://github.com/lf-edge/ekuiper)     | ☑      | ☑      | ☑     | ☑      | ☑     | ☑    | ☑      |
| 4    | [Edge manager](https://hub.docker.com/r/emqx/edge-manager) | ☑      | ☑      | ☑     | ☑      | ☑     | ☑    | ☑      |
| 5    | TDengine                                                   | ☑      | ☑      |       | ☑      |       |      | ☑      |

#### 开始试用

为了使用户的试用更加简单，演示场景利用 Docker 和 Docker compose 技术进行快速的部署，用户可以在虚拟主机、工控机或者运算能力较强的网关上按照[教程](https://github.com/emqx/edge-stack/blob/master/developer-scripts/README-CN.MD)来体验该方案。在实际的业务系统部署过程中，用户可以根据需要在生产环境中直接采用二进制安装包进行部署，这样运行的效率会更高。

该样例场景中，数据通过 Modbus TCP 协议，发出模拟的温度与湿度数据，这些数据进入系统后实现数据采集、汇聚、清洗、存储分析和可视化等能力，以下是在 Grafana 中呈现的温度与湿度的可视化报告。

![edge_sol.png](https://static.emqx.net/images/75ddaac7e174e12137caa4433ca7e818.png)



注：TDengine 发布的容器镜像缺省为 x86*64 环境，如果想切换为 ARM 架构，需要手工改一下 `docker-compose.yml`，以指向正确的版本。

### 解决方案2：云边协同工业互联网平台

这一方案与上述方案不同的地方在于 **引入云边协同的概念，** 通过底层类似于 KubeEdge/IEF 等边缘端基于容器应用的分发与编排能力，利用 KubeEdge/IEF 提供的云边管理通道，在云端可以对部署在边缘端的 Neuron、Edge 和 Kuiper 等实例进行集中管理，从而可以 **在云端实现对边缘端的数据采集、汇聚和分析逻辑等进行在线管理和更新。** 另外在云端，通过部署 EMQ X Enterprise 分布式、高可用的集群功能，将分布在不同边缘端点的设备数据进行接入和分析处理。

![iiot_sol_2.png](https://static.emqx.net/images/b2159860f854daaface005fbf339dcd7.png)

#### 适用场景

该方案的使用场景是有多个管理节点、需要在云端 **通过集中统一的方式对分布在不同地点的边缘节点实现控制和管理** 的业务场景。通过该方案，用户无需到物理边缘节点所在的位置对其进行管理，极大提升了管理效率。

- 多地、多节点分布式支持：存在多个边缘节点，可能是厂区的不同车间，甚至是不同城市的工业园区；
- 大规模设备接入：需要在云端对工业数据和来自于其它业务系统的数据进行分析（比如 ERP、CRM 等），以获取更有价值的分析结果；
- 云边协同
  - 边缘节点管控：云端实现对边缘端的物理节点管控，并且打通从云端到处于 NAT 后的网关、工控机等网络连接，实现从云端到边缘的控制
  - 应用和中间件的部署：实现客户应用和软件的分发与部署
  - 运维管理：监控软件的运行状态，自动恢复等功能，实现日志管理

#### 软件列表

除了在方案 1 边缘端会用到的产品之外，本方案还会用到以下软件产品。

| 编号 | 名称             | 供应商 | 开源     |
| ---- | ---------------- | ------ | -------- |
| 1    | EMQ X Enterprise | EMQ    | 否 - `1` |
| 2    | IEF              | 华为   | 否 - `2` |

`1`: EMQ X Enterprise 企业级物联网 MQTT 消息平台，支持百万级物联网设备一站式接入、MQTT&CoAP 多协议处理、低时延实时消息通信。支持基于 SQL 的内置规则引擎，灵活处理/转发消息到后端服务，存储消息数据到各种数据库，或桥接 Kafka、RabbitMQ 等企业中间件。

`2`: [IEF 智能边缘平台（Intelligent EdgeFabric）](https://www.huaweicloud.com/product/ief.html)是华为开源云边协同平台 KubeEdge 的商业化版本，满足客户对边缘计算资源的远程管控、数据处理、分析决策、智能化的诉求， 为用户提供完整的边缘和云协同的一体化服务。

#### 开始试用

- IEF 平台是华为公有云服务，用户可以直接访问和试用。

- [Kuiper](https://marketplace.huaweicloud.com/product/OFFI474477808879489024) 在 IEF 上已经上线，可以通过 IEF 平台直接部署、安装和运维（读者可以参考视频「[云边协同高效实现物联网边缘流式业务处理 - 华为 IEF & EMQ X Kuiper 轻量级边缘数据解决方案](https://www.bilibili.com/video/BV1hQ4y1A7Vy?from=search&seid=15773267177167801393) 」来了解如何在 IEF 中使用 Kuiper）；Neuron 和 Edge 目前暂未在 IEF 上线，但是用户可以直接通过 Docker Hub 安装至边缘节点中；然后在云端通过部署 Edge Manager 来进行统一的管理和控制。
- [EMQ X Enterprise](https://marketplace.huaweicloud.com/product/00301-511011-0--0) 已经在华为云上线，用户也可以使用由 EMQ 提供的[在线云服务](https://www.emqx.com/zh/cloud)； 
- 云端的数据持久化或者桥接方式，用户可以根据需要进行选择，相关信息可以参考 [EMQ 网站](https://www.emqx.com/zh/products/emqx)；

### 总结

基于本文方案所搭建的工业互联网基础能力平台，用户可以实现高效、低成本的工业互联网设备连接、采集和分析。无论是轻量级的纯边缘方案，还是部署环境较为复杂的云边一体协同方案，均可通过本文方案得以实现。

------

### 活动预告

- EMQ 近期将与涛思数据等合作伙伴联合举办线上 Meetup，针对本文所发布的方案进行更为详细的解读与演示，敬请期待。

- 在 11 月 7 日的全球边缘计算大会上，EMQ 联合创始人金发华也将带来有关本文方案的内容分享，赠票活动进行中，详情请点击[《节日送温暖｜一次近距离感受边缘计算前沿成果的机会》](https://mp.weixin.qq.com/s?__biz=Mzg3NjAyMjM0NQ==&mid=2247486161&idx=1&sn=683a22653594567133ee0fae1fa46597&chksm=cf39d5f7f84e5ce1a3648b5558135e1224e46950a23650c224736bfde075132ff7a1d1e15a9a&token=298274395&lang=zh_CN#rd)
