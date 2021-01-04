# kubernetes

Kubernetes 脱胎于 Google 的 Borg 系统，是一个功能强大的容器编排系统。Kubernetes 及其整个生态系统（工具、模块、插件等）均使用 Go 语言编写，从而构成一套面向 API、可高速运行的程序集合，这些程序文档精良、易于参与贡献或在其上构建应用程序。每个开发、运维或感兴趣的读者都应熟悉它的一些核心概念，以便理解这个系统及其不同的功能，以及为什么几乎所有人都在使用它。在继续之前，我想提一下 Kubernetes 的几个顶级朋友（或竞争对手）：ECS、Nomad 和 Mesos。ECS 是 AWS 自己的编排解决方案，最近它又推出了托管在 AWS 上的 Kubernetes 系统——EKS。两者都支持 FARGATE，让用户无须关心所运行的物理资源。Kubernetes 毫无疑问是最大赢家，作为一个开源系统，三大主流云服务商都以托管的方式提供了这项功能。但是，它比其他几个产品都要来得复杂和混乱。Kubernetes 可以处理几乎任何类型的容器负载，也有很多技巧，但这并不意味着每个人都应该使用它。其他解决方案或许也能满足某些公司的要求，例如，完全部署在 AWS 上的互联网产品公司，使用 ECS 会比 Kubernetes 具有更佳的生产环境体验，是的，也好于 EKS。话虽如此，Kubernetes 的魔力在于：它可以部署在任何地方、它拥有一个活跃的社区，有数百个核心开发人员及数千个生态系统开源贡献者。它运行快速、具有创新性、模块化且面向 API，是一个对构建插件或服务非常友好的系统。


# 官网


https://kubernetes.io


# Kubernetes 的 11 个部分


## 1.PodPod


PodPod 是 Kubernetes 中最小的可互动单元。一个 Pod 可以由多个容器组成，这些容器共同部署在单个节点上形成一个单元。一个 Pod 具有一个 IP，该 IP 在其容器之间共享。在微服务世界中，一个 Pod 可以是执行后台工作或服务请求的微服务的单个实例。


## 2. Node（节点）


Node 是机器。它们是 Kubernetes 用于部署 Pod 的“裸机”（或虚拟机）。Node 为 Kubernetes 提供可用的集群资源用于以保持数据、运行作业、维护工作负载、创建网络路由等。


## 3. Label（标签）与 Annotation（注解）


Label 是 Kubernetes 及其最终用户用于过滤系统中相似资源的方式，也是资源与资源相互“访问”或关联的粘合剂。比如说，为 Deployment 打开端口的 Service。不论是监控、日志、调试或是测试，任何 Kubernetes 资源都应打上标签以供后续查验。例如，给系统中所有 Worker Pod 打上标签：app=worker，之后即可在 kubectl 或 Kubernetes API 中使用 --selector 字段对其进行选择。


Annotation 与 Label 非常相似，但通常用于以自由的字符串形式保存不同对象的元数据，例如“更改原因: 安全补丁升级”。


## 4. Service Discovery（服务发现）


作为编排系统，Kubernetes 控制着不同工作负载的众多资源，负责管理 Pod、作业及所有需要通信的物理资源的网络。为此，Kubernetes 使用了 ETCD。ETCD 是 Kubernetes 的“内部”数据库，Master 通过它来获取所有资源的位置。Kubernetes 还为服务提供了实际的“服务发现”——所有 Pod 使用了一个自定义的 DNS 服务器，通过解析其他服务的名称以获取其 IP 地址和端口。它在 Kubernetes 集群中“开箱即用”，无须进行设置。


## 5. ReplicaSet（副本集）


虽然 Pod 是一个物理性的运行任务，但通常使用单个实例是不够的。为了冗余并处理负载，出于某种原因（比如“伸缩”）需要对 Pod 进行复制。为了实现负责扩展和复制的层，Kubernetes 使用了 ReplicaSet。这个层以副本的数量表示系统的期望状态，并在任意给定时刻保持该系统的当前状态。这也是配置自动伸缩的所在，在系统高负载时创建额外的副本，并在不再需要这些资源来支撑所运行的工作负载时进行缩容。


## 6. DaemonSet（守护进程集）


有时候，应用程序每个节点需要的实例不超过一个。比如 FileBeat 这类日志收集器就是个很好的例子。为了从各个节点收集日志，其代理需要运行在所有节点上，但每个节点只需要一个实例。Kubernetes 的 DaemonSet 即可用于创建这样的工作负载。


## 7. StatefulSet（有状态集）


尽管多数微服务涉及的都是不可变的无状态应用程序，但也有例外。有状态的工作负载有赖于磁盘卷的可靠支持。虽然应用程序容器本身可以是不可变的，可以使用更新的版本或更健康的实例来替代，但是所有副本还是需要数据的持久化。StatefulSet 即是用于这类需要在整个生命周期内使用同一节点的应用程序的部署。它还保留了它的“名称”：容器内的 hostname 以及整个集群中服务发现的名称。3 个 ZooKeeper 构成的 StatefulSet 可以被命名 zk-1、zk-2 及 zk-3，也可以扩展到更多的成员 zk-4、zk-5 等等…… StatefulSets 还负责管理 PersistentVolumeClaim（Pod 上连接的磁盘）。


## 8. Job（任务）


Kubernetes 核心团队考虑了大部分使用编排系统的应用程序。虽然多数应用程序要求持续运行以同时处理服务器请求（比如 Web 服务器），但有时还是需要生成一批作业并在其完成后进行清理。比如，一个迷你的无服务器环境。为了在 Kubernetes 中实现这一点，可以使用 Job 资源。正如其名，Job 的工作是生成容器来完成特定的工作，并在成功完成时销毁。举个例子，一组 Worker 从待处理和存储的数据队列中读取作业。一旦队列空了，就不再需要这些 Worker 了，直到下个批次准备好。


## 9. ConfigMap（配置映射）及 Secret（机密配置）


如果你还不熟悉十二要素应用清单，请先行了解。现代应用程序的一个关键概念是无环境，并可通过注入的环境变量进行配置。应用程序应与其位置完全无关。为了在 Kubernetes 中实现这个重要的概念，就有了 ConfigMap。实际上这是一个环境变量键值列表，它们会被传递给正在运行的工作负载以确定不同的运行时行为。在同样的范畴下，Secret 与正常的配置条目类似，只是会进行加密以防类似密钥、密码、证书等敏感信息的泄漏。我个人认为 Hashicorp 的 Vault 是使用机密配置的最佳方案。请务必阅读一下我去年写的有关文章，文章讲述了将 Vault 作为生产环境一部分的原因，以及我的一位同事写的另一篇更技术性的文章。


## 10. Deployment（部署）


一切看起来都很美好，Pod 可以正常运行，如果上层有 ReplicaSet，还可以根据负载进行伸缩。不过，大家蜂拥而来，为的是能用新版本快速替换应用程序。我们想小规模地进行构建、测试和发布，以缩短反馈周期。使用 Deployments 即可持续地部署新软件，这是一组描述特定运行工作负载新需求的元数据。举个例子，发布新版本、错误修复，甚至是回滚（这是 Kubernetes 的另一个内部选项）。


在 Kubernetes 中部署软件可使用 2 个主要策略：
- 替换——正如其名，使用新需求替换全部负载，自然会强制停机。对于快速替换非生产环境的资源，这很有帮助。
- 滚动升级——通过监听两个特定配置慢慢地将容器替换成新的：


MaxAvailable——设置在部署新版本时可用的工作负载比例（或具体数量），100% 表示“我有 2 个容器，在部署时要保持 2 个存活以服务请求”；b. MaxSurge——设置在当前存活容器的基础上部署的工作负载比例（或数量），100% 表示“我有 X 个容器，部署另外 X 个容器，然后开始滚动移除旧容器”。


## 11. Storage（存储）


Kubernetes 在存储之上添加了一层抽象。工作负载可以为不同任务请求特定存储，甚至可以管理超过 Pod 生命周期的持久化。为简短起见，请阅读作者之前发布的关于 Kubernetes 存储的文章，特别重点看看为什么它不能完全解决类似数据库部署这样的数据持久性要求。



# 概念性理解


Kubernetes（现在仍然）是根据一些指导原则进行设计和开发的，构建在系统里的每个功能、概念和想法都考虑了社区因素。此外，最终用户会被引导以某种方式使用该系统，但这不是强迫的；最佳实践也是公开的，但作为一个开源免费的系统，你完全可以根据自身需要进行操作。


面向 API——系统每个部分构建时通过优良的文档和可操作的 API 来实现可交互性。核心开发人员会确保最终用户可以进行更改、查询和更新，以免将其阻挡在外或有不想要的过滤器。


欢迎包装工具——作为前一点的衍生产品，Kubernetes 对在其 API 之上构建的工具和包装器表示欢迎。作为一个原始平台，Kubernetes 是以一个非常可定制的方式进行构建的，以便他人使用，并进一步开发用于不同用例的工具。有些工具已经变得非常有名并被广泛使用，比如 Spinnaker、Istio 等等。


声明性状态——鼓励用户在系统中使用声明性描述而非命令式描述。这意味着，系统的状态和组件最好被描述为在某种版本控制（如 Git）中管理的代码，以此避免手工修改造成的困扰。因此，Kubernetes 减少了灾难恢复的难度、更易于在团队之间分享并传递责任。