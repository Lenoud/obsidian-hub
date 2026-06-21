# 项目一 Docker基础认知与安装
## 任务1 认识Docker
### 任务引入
        想象一下，你是一家在线书店的技术负责人。随着业务的增长，你需要快速部署新的服务器来应对流量高峰，同时确保新旧系统之间的兼容性，以及开发、测试和生产环境的一致性。你的团队使用多种编程语言和框架开发不同的服务，这使得环境配置变得复杂且容易出错。此外，你还需要确保系统的可扩展性和灵活性，以便在不同的云服务提供商之间迁移，而不会影响服务的连续性。

### 为什么需要Docker
Docker提供了一个解决方案，通过容器化技术，帮助你解决上述挑战：

1. **环境一致性**：Docker允许你将应用程序及其依赖打包到一个轻量级的容器中，确保在不同环境（开发、测试、生产）中的一致性。
2. **快速部署**：容器启动速度快于传统虚拟机，这意味着你可以更快响应流量高峰，快速部署新服务。
3. **资源效率**：容器共享宿主机的操作系统内核，不需要为每个应用程序运行一个完整的操作系统，从而节省资源。
4. **可移植性**：Docker容器可以在任何支持Docker的系统上运行，无论是本地机器还是云服务，这为你提供了更大的灵活性。
5. **隔离性**：每个容器都是相互隔离的，一个容器的故障不会影响到其他容器，提高了系统的稳定性。

### Docker能解决的问题
+ **环境差异**：开发环境与生产环境不一致导致的问题。
+ **资源浪费**：传统虚拟机占用大量资源的问题。
+ **部署速度**：应用程序部署缓慢的问题。
+ **系统兼容性**：不同操作系统和平台之间的兼容性问题。
+ **可扩展性**：系统难以水平扩展的问题

Docker是一个开源的容器引擎项目，它使用Go语言开发实现，遵从Apache2.0协议。作为运行和管理容器的容器引擎，Docker让开发人员可以将应用程序及其依赖打包到一个可移植的镜像中，然后发布到任何使用流行的操作系统（如Linux、Windows和macOS）的计算机上，也可以发布到云端进行部署。Docker是传统虚拟机的替代解决方案，越来越多的应用程序以容器（一种操作系统层虚拟化方式）方式在开发、测试和生产环境中运行。

2013年发布至今， Docker 一直广受瞩目，被认为可能会改变软件行业。另外“容器化”已经成为当今IT界的热门词汇之一。

### 任务分析

本任务的具体要求如下。

+ 理解Docker的概念。
+ 了解Docker的优势和应用。
+ 了解容器与虚拟机的区别。

完成本任务后，你将获得以下知识和技能：

+ **Docker基础**：理解Docker的核心概念和工作原理。
+ **容器化优势**：了解Docker容器化技术相比传统虚拟机的优势。
+ **部署策略**：学习如何利用Docker快速部署应用程序。
+ **技术对比**：能够比较容器与虚拟机的不同，并根据需求选择合适的技术。

### 知识讲解
### 1.1.1 Docker简介
### Docker社区和生态系统介绍
Docker不仅仅是一个容器引擎，它还拥有一个庞大且活跃的社区和生态系统，这些资源为Docker用户提供了极大的便利和支持。

1. **Docker Hub**： Docker Hub是Docker的官方仓库，提供了超过100万个预构建的容器镜像，用户可以直接下载并使用这些镜像，极大地简化了容器的部署和管理。它还支持私有仓库功能，允许用户创建和分享自己的容器镜像。
2. **Docker Desktop**： Docker Desktop是一个易于安装和使用的桌面版本，支持Mac和Windows操作系统。它包括Docker Engine、Docker CLI客户端、Docker Compose等工具，使得在本地机器上开发和测试容器化应用程序变得简单。
3. **Kubernetes**： Kubernetes是一个开源的平台，用于自动化部署、扩展和管理容器化应用程序。它与Docker紧密结合，提供了强大的容器编排能力，使得在生产环境中运行和管理容器变得更加高效和可靠。
4. **DevOps与Docker**： Docker在DevOps中扮演着关键角色，它通过容器化技术简化了应用程序的部署和交付过程。Docker容器的轻量级和可移植性使得应用程序能够在任何环境中以相同的方式运行，从而实现了开发、测试和生产环境的一致性。Docker还与持续集成/持续部署（CI/CD）工具集成，自动化构建和部署流程，提高交付效率。
5. **微服务架构与Docker**： Docker的轻量级、可移植性和隔离性使其成为微服务架构的理想选择。微服务架构将应用程序拆分成小型、独立的服务单元，每个服务都可以独立开发、部署和扩展。Docker容器为每个微服务提供了隔离的环境，简化了服务的部署和管理。
6. **Docker的重要性和广泛应用**： Docker通过容器化技术，降低了软件环境配置的复杂性，提高了开发和部署的效率。它在DevOps、微服务架构中的应用，使得开发、测试和生产环境的一致性得以实现，加快了软件的迭代和交付速度，提高了系统的可维护性和可扩展性。

综上所述，Docker的社区和生态系统提供了强大的支持，包括Docker Hub、Docker Desktop、Kubernetes等工具和服务，这些资源共同推动了Docker在DevOps和微服务架构中的广泛应用。

Docker 是一个容器引擎，基于LXC(Linux Container，后来改用了自己的libContainer），可以让开发者把他们的应用程序以及依赖包放到容器中，然后发布到任何主流平台上，比如Linux, Mac, Windows。这个容器管理引擎大大降低了容器技术的使用门槛，轻量级，可移植，虚拟化，语言无关，写了程序扔上去做成镜像可以随处部署和运行，开发、测试和生产环境彻底统一了，还能进行资源管控和虚拟化。

在Docker 之前，开发者都深陷软件环境的配置之苦，不同环境下的配置问题层出不穷，想用一款开源软件，结果配置好久都运行不起来，查看网上的各种教程，还是不行，最后不得不放弃使用。环境差异，使原本简单的问题复杂化，拖慢开发进程。拿来主义对开发、运维、测试人员是件好事，开箱即用，让我们把有限精力放到自己专注的事情上。

或许有人会说，用虚拟机，的确，在Docker之前，业界的宠儿是虚拟机。但是虚拟机太笨重了，对资源也是一种浪费。一台服务器可以运行十几个虚拟机实例，但是现在它可以运行几百个Docker 实例，而且秒级启动，可以简单理解为Docker就是轻量级的虚拟机。

**Docker的发展历史和技术细节**

Docker 最初是 dotCloud 公司创始人所罗门 · 海克斯 （Solomon Hykes） 在法国期间发起的一个公司内部项目，它是基于 dotCloud 公司多年云服务技术的一次革新，并于 2013 年 3 月以 Apache 2.0 授权协议开源。Docker 项目后来还加入了 Linux 基金会，并成立推动开放容器联盟（OCI）。

Docker 自开源后受到广泛的关注和讨论，甚至由于 Docker 项目的火爆，在 2013 年底，dotCloud 公司决定改名为 Docker。Docker 最初是在 Ubuntu 12.04 上开发实现的；Red Hat 则从 RHEL 6.5 开始对 Docker 进行支持；Google 也在其 PaaS 产品中广泛应用 Docker。

Docker 使用 Google 公司推出的 Go 语言 进行开发实现，基于 Linux 内核的 cgroup，namespace，以及 OverlayFS 类的 Union FS 等技术，对进程进行封装隔离，属于操作系统层面的虚拟化技术。由于隔离的进程独立于宿主和其它的隔离的进程，因此也称其为容器。最初实现是基于 LXC，从 0.7 版本以后开始去除 LXC，转而使用自行开发的 libcontainer，从 1.11版本 开始，则进一步演进为使用 runC 和 containerd。

### 1.1.2 Docker的优势与应用场景
#### Docker的优势
        Docker作为一种容器化技术，具有许多优势，并在多个领域有着广泛的应用。

### Docker的优势
1. **更高效的利用系统资源**
    - Docker容器不需要模拟整个操作系统，因此它们比虚拟机更为轻量，能够更高效地利用系统资源。这意味着在相同的硬件上可以运行更多的容器化应用。
2. **更快速的启动时间**
    - 由于Docker容器直接运行在宿主机的内核上，它们可以秒级甚至毫秒级启动，相比虚拟机需要数分钟的启动时间，Docker大大减少了应用启动的等待时间。
3. **一致的运行环境**
    - Docker通过镜像提供了一个一致的运行环境，这有助于解决开发、测试和生产环境不一致导致的问题，确保了环境的一致性。
4. **持续交付和部署**
    - Docker支持持续集成和持续部署（CI/CD），使得开发和运维人员可以轻松地构建、测试和部署应用。Dockerfile的使用使得构建过程透明化，便于理解和协作。
5. **更轻松的迁移**
    - Docker容器可以在多种环境中运行，包括物理机、虚拟机、公有云、私有云等，这使得应用迁移变得容易，无需担心环境变化导致的问题。
6. **更轻松的维护和扩展**
    - Docker的分层存储和镜像技术使得应用的维护和更新变得更加简单。官方镜像的提供也降低了应用服务的镜像制作成本。

### Docker的应用场景
1. **云计算**
    - 在云计算领域，Docker因其轻量级和可移植性成为理想选择。云平台如AWS、Azure、GCP等都支持Docker，提供了容器服务和编排工具，如AWS ECS和EKS，以实现应用的高效部署和管理。
2. **大数据**
    - Docker在大数据领域也有广泛应用，例如通过Docker Compose可以轻松搭建Hadoop、Spark和Hive等大数据处理环境，降低了大数据处理的门槛，使得普通开发者也能进行数据分析和应用开发。
3. **人工智能**
    - 在人工智能领域，Docker支持AI模型的开发、训练、部署以及服务器集群管理。Docker容器的轻量级和隔离性为AI应用提供了灵活的开发和部署解决方案，同时简化了环境配置和依赖管理。
4. **微服务架构**
    - Docker容器的设计原则是一个容器一个服务，这与微服务架构的理念相契合。容器之间的相互隔离使得它们成为部署微服务的理想单元。
5. **弹性伸缩**
    - Docker容器的快速启动特性使其在集群模式下可以快速响应业务访问量的变化，实现弹性伸缩。例如，AWS AutoScaling可以自动添加EC2云主机以应对业务高峰。
6. **临时和批处理任务**
    - Docker适用于需要临时运行的任务，如数据处理和定时任务。容器可以在任务完成后被销毁，释放资源，提高资源利用率。

Docker的这些优势和应用场景展示了其在现代软件开发和运维中的重要作用，它不仅提高了开发效率，还简化了应用的部署和管理。

作为一种新兴的虚拟化方式，Docker 跟传统的虚拟化方式相比具有众多的优势。

1. 更高效的利用系统资源

由于容器不需要进行硬件虚拟以及运行完整操作系统等额外开销，Docker 对系统资源的利用率更高。无论是应用执行速度、内存损耗或者文件存储速度，都要比传统虚拟机技术更高效。因此，相比虚拟机技术，一个相同配置的主机，往往可以运行更多数量的应用。

2. 更快速的启动时间

传统的虚拟机技术启动应用服务往往需要数分钟，而 Docker 容器应用，由于直接运行于宿主内核，无需启动完整的操作系统，因此可以做到秒级、甚至毫秒级的启动时间。大大的节约了开发、测试、部署的时间。

3. 一致的运行环境

开发过程中一个常见的问题是环境一致性问题。由于开发环境、测试环境、生产环境不一致，导致有些 bug 并未在开发过程中被发现。而 Docker 的镜像提供了除内核外完整的运行时环境，确保了应用运行环境一致性，从而不会再出现 「这段代码在我机器上没问题啊」 这类问题。

4. 持续交付和部署

对开发和运维（DevOps）人员来说，最希望的就是一次创建或配置，可以在任意地方正常运行。

使用 Docker 可以通过定制应用镜像来实现持续集成、持续交付、部署。开发人员可以通过 Dockerfile 来进行镜像构建，并结合持续集成(Continuous Integration) 系统进行集成测试，而运维人员则可以直接在生产环境中快速部署该镜像，甚至结合持续部署(Continuous Delivery/Deployment) 系统进行自动部署。

而且使用 Dockerfile 使镜像构建透明化，不仅仅开发团队可以理解应用运行环境，也方便运维团队理解应用运行所需的条件，帮助在更好的生产环境中部署该镜像。

5. 更轻松的迁移

由于 Docker 确保了执行环境的一致性，使得应用的迁移更加容易。Docker 可以在很多平台上运行，无论是物理机、虚拟机、公有云、私有云，甚至是笔记本，其运行结果是一致的。因此用户可以很轻易的将在一个平台上运行的应用，迁移到另一个平台上，而不用担心运行环境的变化导致应用无法正常运行的情况。

6. 更轻松的维护和扩展

Docker 使用的分层存储以及镜像的技术，使得应用重复部分的复用更为容易，也使得应用的维护更新更加简单，基于基础镜像进一步扩展镜像也变得非常简单。此外，Docker 团队同各个开源项目团队一起维护了一大批高质量的官方镜像，既可以直接在生产环境使用，又可以作为基础进一步定制，大大的降低了应用服务的镜像制作成本。

#### Docker的应用场景
1. 简化配置

这是Docker公司宣传的Docker的主要使用场景。虚拟机的最大好处是能在你的硬件设施上运行各种配置不一样的平台（软件、系统），Docker在降低额外开销的情况下提供了同样的功能。

它能让你将运行环境和配置放在代码中，再部署，同一个Docker的配置可以在不同的环境中使用，这样就降低了硬件要求和应用环境之间的耦合度。

2. 代码流水线管理

前一个场景对于管理代码的流水线起到了很大的帮助。代码从开发者的机器到最终在生产环境上的部署，需要经过很多的中间环境。

而每一个中间环境都有自己微小的差别，Docker给应用提供了一个从开发到上线均一致的环境，让代码的流水线变得简单不少。

3. 提高开发效率

Docker能提升开发者的开发效率。

不同的开发环境中，我们都想把两件事做好。一是我们想让开发环境尽量贴近生产环境，二是我们想快速搭建开发环境。

理想状态中，要达到第一个目标，我们需要将每一个服务都跑在独立的虚拟机中以便监控生产环境中服务的运行状态。然而，我们却不想每次都需要网络连接，每次重新编译的时候远程连接上去特别麻烦。

这就是Docker做的特别好的地方，开发环境的机器通常内存比较小，之前使用虚拟机的时候，我们经常需要为开发环境的机器加内存，而现在Docker可以轻易的让几十个服务在Docker中跑起来。

4. 隔离应用

有很多种原因会让你选择在一个机器上运行不同的应用，比如之前提到的提高开发效率的场景等。

我们经常需要考虑两点，一是因为要降低成本而进行服务器整合，二是将一个整体式的应用拆分成松耦合的单个服务（微服务架构）。

5. 整合服务器

正如通过虚拟机来整合多个应用，Docker隔离应用的能力使得Docker可以整合多个服务器以降低成本。

由于没有多个操作系统的内存占用，以及能在多个实例之间共享没有使用的内存，Docker可以比虚拟机提供更好的服务器整合解决方案。

6. 调试能力

Docker提供了很多的工具，这些工具不一定只是针对容器，但是却适用于容器。它们提供了很多的功能，包括可以为容器设置检查点、设置版本和查看两个容器之间的差别，这些特性可以帮助调试Bug。

7. 多租户环境

 在多租户的应用中，Docker可以避免关键应用的重写。在IoT（物联网）的应用中开发一个快速、易用的多租户环境。这种多租户的基本代码非常复杂，很难处理，重新规划这样一个应用不但消耗时间，也浪费金钱。

使用Docker，可以为每一个租户的应用层的多个实例创建隔离的环境，这不仅简单而且成本低廉，当然这一切得益于Docker环境的启动速度和其高效的diff命令。

8. 快速开发

在虚拟机之前，引入新的硬件资源需要消耗几天的时间。Docker的虚拟化技术将这个时间降到了几分钟，Docker只是创建一个容器进程而无需启动操作系统，这个过程只需要秒级的时间。这正是Google和Facebook都看重的特性。你可以在数据中心创建销毁资源而无需担心重新启动带来的开销。通常数据中心的资源利用率只有30%，通过使用Docker并进行有效的资源分配可以提高资源的利用率。

### 1.1.3 Docker与虚拟机的区别
#### 虚拟机的痛点
---

##### 痛点1-环境配置的难题
软件开发最大的麻烦事之一，就是环境配置。用户计算机的环境都不相同，你怎么知道自家的软件，能在其他机器跑起来？

用户必须保证两件事：操作系统的设置，各种库和组件的安装。只有它们都正确，软件才能运行。举例来说，安装一个 Python 应用，计算机必须有 Python 引擎，还必须有各种依赖，可能还要配置环境变量。

如果某些老旧的模块与当前环境不兼容，那就麻烦了。开发者常常会说："它在我的机器可以跑了"（It works on my machine），言下之意就是，其他机器很可能跑不了。

环境配置如此麻烦，换一台机器，就要重来一次，旷日费时。很多人想到，能不能从根本上解决问题，软件可以带环境安装？也就是说，安装的时候，把原始环境一模一样地复制过来。

##### 痛点2-运维部署的难题
软件开发完成后，需要对系统进行部署。在小型系统中，每个客户都要重新部署安装，随时可能会出现一些不同的问题，为了解决这些问题，又得投入人力物力，而在大型系统中，往往使用分布式架构，如果使用手动的方式进行，部署难度和可维护程度可想而知。

##### 痛点3-版本管理的难题
对于版本更新，代码集成，修复bug的部署工作往往是重复且很麻烦的，另外手动操作往往还会造成版本混乱等问题。

##### 痛点4-虚拟机效率低
![[1582771057626-7979d381-cd80-4436-addc-e8bc0df27f8a.png]]

虚拟机（virtual machine）就是带环境安装的一种解决方案。它可以在一种操作系统里面运行另一种操作系统，比如在 Windows 系统里面运行 Linux 系统。应用程序对此毫无感知，因为虚拟机看上去跟真实系统一模一样，而对于底层系统来说，虚拟机就是一个普通文件，不需要了就删掉，对其他部分毫无影响。

虽然用户可以通过虚拟机还原软件的原始环境。但是，这个方案有几个缺点。

（1）资源占用多

虚拟机会独占一部分内存和硬盘空间。它运行的时候，其他程序就不能使用这些资源了。哪怕虚拟机里面的应用程序，真正使用的内存只有 1MB，虚拟机依然需要几百 MB 的内存才能运行。

（2）冗余步骤多

虚拟机是完整的操作系统，一些系统级别的操作步骤，往往无法跳过，比如用户登录。

（3）启动慢

启动操作系统需要多久，启动虚拟机就需要多久。可能要等几分钟，应用程序才能真正运行。

#### 解决痛点-Linux容器（Docker属于LXC）

![[1582770982250-079b9377-5697-47c9-a8bd-6de1ec41a957.png]]

由于虚拟机存在这些缺点，Linux 发展出了另一种虚拟化技术：Linux 容器（Linux Containers，缩写为 LXC）。

Linux 容器不是模拟一个完整的操作系统，而是对进程进行隔离。或者说，在正常进程的外面套了一个保护层。对于容器里面的进程来说，它接触到的各种资源都是虚拟的，从而实现与底层系统的隔离。

由于容器是进程级别的，相比虚拟机有很多优势。

（1）启动快

容器里面的应用，直接就是底层系统的一个进程，而不是虚拟机内部的进程。所以，启动容器相当于启动本机的一个进程，而不是启动一个操作系统，速度就快很多。

（2）资源占用少

容器只占用需要的资源，不占用那些没有用到的资源；虚拟机由于是完整的操作系统，不可避免要占用所有资源。另外，多个容器可以共享资源，虚拟机都是独享资源。

（3）体积小

容器只要包含用到的组件即可，而虚拟机是整个操作系统的打包，所以容器文件比虚拟机文件要小很多。

总之，容器有点像轻量级的虚拟机，能够提供虚拟化的环境，但是成本开销小得多。

容器是一种轻量级的虚拟技术，而虚拟机则是重量级的虚拟技术。如图xxx所示，左边是Docker容器的结构, 右边是传统虚拟机。

![[1582773241360-e7697c7c-45fd-4414-8f65-e3046931b956.png]]

##### 对比传统虚拟机的特点
传统虚拟机技术是虚拟出一套硬件后，在其上运行一个完整操作系统，在该系统上再运行所需应用进程；而容器内的应用进程直接运行于宿主的内核，容器内没有自己的内核，而且也没有进行硬件虚拟。因此容器要比传统虚拟机更为轻便，如表xxx所示。

| 特性    | 容器    | 虚拟机    |
| --- | --- | --- |
| 启动    | 秒级    | 分钟级    |
| 硬盘使用    | 一般为 MB    | 一般为 GB    |
| 性能    | 接近原生    | 弱于    |
| 系统支持量    | 单机支持上千个容器    | 一般几十个    |

### 任务实施：Docker基础理论学习与实践准备
### 知识拓展

## 任务2 安装Docker
### 任务引入
Docker 分为 CE 和 EE 两大版本。CE 即社区版（免费，支持周期 7 个月），EE 即企业版，强调安全，付费使用，支持周期 24 个月。
Docker CE 分为 stable、test 和 nightly 三个更新频道。
官方网站上有各种环境下的安装指南，本书主要介绍 Docker CE 在 Linux 、Windows 10 和 macOS 上的安装

### 任务分析
+ 掌握VMware Workstation软件的基本使用
+ 掌握Centos7操作系统的安装
+ 掌握管理远程服务器的方法
+ 掌握Docker的部署步骤与方法

### 知识讲解
### 1.2.1 Docker的安装前提条件
#### 准备安装环境
+ 虚拟机软件：VMware Workstation 16.2.0
+ 操作系统镜像：CentOS-7-x86_64-DVD-1810.iso

在安装 Linux 之前，请确电脑已开启虚拟化支持，启用后，如图xxx所示。

![[1731398990860-7dcf6606-9573-4446-9f90-d9dd5c3fa14d.png]]

##### VMware虚拟化软件的安装
我们将使用 VMware Workstation 进行虚拟化。VMware Workstation 是一款强大的桌面虚拟机软件，能够在单一桌面上同时运行不同操作系统，适合程序开发、调试和部署等任务.

（1）下载VMware-workstation-full-16.2.0-18760230安装包，双击安装包，弹出VMware安装主界面，如图xxx所示。

![[1731377249688-453baa9c-a460-4e8c-8049-c95d0f710b80.png]]

（2）单击“下一步”按钮，弹出VMware安装界面，如图xxx所示。

![[1731377357452-de6d11b7-c5dc-4667-85ea-393e75e975f4.png]]

（3）选中“我接受许可协议中的条款”复选框，如图xxx所示。

![[1731377328823-6f2fe672-907a-457e-8979-16dad92f58b4.png]]

（4）单击“下一步”按钮，弹出VMware自定义安装界面，如图xxx所示。

![[1731377481048-4287be1f-5d93-4df2-8202-30842c83f192.png]]

（5）单击“下一步”按钮，进入用户体验设置，取消勾选“启动时检查产品更新”、“加入vmware客户体验提升计划”复选框，如图xxx所示。

![[1731377839641-d98b9ebe-5efb-47b2-a1e2-2e00a8e1ddf2.png]]

（6）单击“下一步”按钮

![[1731377872297-51cb1ef9-77e1-46a3-8d7d-7bcc11623bb7.png]]

（7）单击“安装”按钮，开始VMware Workstation 16的安装，如图xxx所示。

![[1731378036423-9e220c1c-6fb3-4a5f-a268-dbceee7f4a93.png]]

（8）耐心等待进度条加载

![[1731378160265-56f2a15f-f94e-4150-b718-f02a224dccc1.png]]

（8）单击“完成”按钮，完成VMware Workstation 16的安装。

![[1731378673573-4ec9ce2d-60bd-40a8-9cf5-5e61660e7eaf.png]]

（9）

![[1731378722744-7e0fc698-53ef-4c27-b010-c87df6d15e91.png]]

（10）点击“完成按钮”完成VMware Workstation 16的安装，如图xxx所示。

![[1731378761635-5e044adb-a106-4f9d-a37f-d21110020305.png]]

（11）

![[1731382430820-31b7660f-ede4-49b2-bae5-f52b5df4bbd4.png]]

（12）一般情况下，安装的虚拟机内容都在C盘 ，为了节约宝贵的C盘空间，我们把他改在D盘，依次打开 ： 编辑→首选项，如图xxx所示。

![[1731378918895-9f6b64bd-2686-4f9a-b709-d229a3fa93b8.png]]

（）点击浏览，在非系统盘位置，创建存储虚拟机文件的默认位置，点击“确定”按钮，应用设置，如图xxx所示。

![[1731378965458-b2a43bc2-b17e-45c4-9c17-a6410992adec.png]]

##### Linux操作系统安装
（1）创建虚拟机

虚拟机的基本要求如下。

● 内存：建议不低于 4GB

● 硬盘容量：建议不低于 40GB

● 网卡模式：使用 NAT 模式或桥接模式，推荐使用 NAT 模式。

打开VMware Workstation Pro应用程序，点击创建新的虚拟机，在新建虚拟机向导窗口中选择“自定义（高级）”，如图xxx所示。

![[1729914111574-c1f9c489-cc41-4819-a699-16641db952d7.png]]

（2）点击下一步，直到安装客户机操作系统步骤，选择“稍后安装操作系统”，如图xxx所示。

![[1729914341261-3718d6b4-819b-4deb-ae6d-a3297679746b.png]]

（3）继续点击下一步，选择Linux操作系统选项框，点击版本下拉框，选择CentOS 7 64位，如图xxx所示。

![[1729914390851-55cc7df5-5687-4192-bb02-b775fc1ab124.png]]

（4）继续点击下一步，将虚拟机名称改为Centos7-docker26.1.4，存储位置我们在上方的VMware应用安装中已经默认修改到了“D:\VMServerData\”，可以不做其它修改，如未操作修改默认目录，建议安装到非C盘目录，节省C盘空间，如图xxx所示。

![[1729914579289-026a233b-eb4b-4116-bf61-e63d4cf2367f.png]]

（5）继续点击下一步，修改对应参数，将处理器内核总数改为4，如图xxx所示。

![[1729914780366-de2694bd-41c0-4bb3-9ec9-11177f3de86f.png]]

（6）继续点击下一步，将内存修改为4096MB，如图xxx所示。

![[1729914862943-7a61e444-f630-4f1a-9f73-2d180b2a2557.png]]

（7）继续点击下一步，网络类型选择NAT网络地址转换，如图xxx所示。

![[1729914906741-c0cbce79-c5c3-484e-b39f-468c910e2456.png]]

（8）点击下一步，直到出现指定磁盘容量时，将容量改成40G，如图xxx所示。

![[1729915024042-cbd98d4e-08e6-4fd1-bd80-5c3bd8de096d.png]]

（9）点击“下一步”直到完成虚拟机的创建，创建完成后，在左侧我的计算机中会出现我们刚才创建的虚拟机名称，点击后会出现其对应的窗口，我们点击窗口中的“编辑虚拟机设置”，打开设置窗口，如图xxx所示。

![[1731382277027-30afd27d-5efd-47d3-8f36-40c4f558f212.png]]

（10）点击“CD/DVD（IDE）”，将“自动检测”改为“使用ISO映像文件”，点击“浏览”，选择我们下载的“CentOS-7-x86_64-DVD-1810.iso”ISO映像文件，单击“确定”，如图xxx所示。

![[1729915535763-cee573b6-7ec0-4980-8fcd-6c04f272c38d.png]]

（11）选择映像文件后即可开始系统的安装，点击“开启此虚拟机”，随后会进入虚拟机的选择界面，选择“Install CentOS 7”，如图xxx所示。

![[1729917850308-2c2ec3d8-8391-42ea-b8fd-b5d64f9822f3.png]]

（12）稍作等待，系统加载完毕后，会进入 CentOS7 的图形化安装界面，我们可以在此，设置语言，选择“中文”→“简体中文（中国）”选项，如图xxx所示，单击“继续”按钮。

![[1729918442389-a3c99ea8-53ee-41a8-8705-3186d2c1753c.png]]

（13）进行安装信息摘要的配置，如图xxx所示。

![[1729918521372-e41a23e4-e634-4ab9-bf77-e61302189195.png]]

（14）进行软件选择的配置，可以安装桌面化CentOS。选择安装“GNOME桌面”，如图xxx所示。

![[1732428736498-5ad89f99-8f8c-4f0c-924e-60d075c32ef5.png]]

（15）配置“安装位置”，并配置分区，选择“自动配置分区”，单击“完成”按钮，返回CentOS7安装界面，继续进行安装，如图xxx所示。

![[1731381496346-2331d9e7-76f8-4625-af9d-c8ad7a4a79f3.png]]

（16）点击“开始安装”，安装CentOS7的时间稍长，请耐心等待。

![[1732428893939-d95b7478-bd84-468b-9e0e-3cce7a8ecb40.png]]

在安装过程中我们可以选择为root用户设置密码，点击“ROOT密码”选项，设置完成后单击“完成”按钮，如图xxx所示。也可选选择“创建用户”，创建一个普通用户。

![[1729919423685-98e6f50f-bfbd-4804-bfd9-2bd98140439c.png]]

（17）CentOS7安装完成，如图xxx所示。

![[1729921436964-f068d7e9-eea9-4b0e-bf0a-6e83ba33c743.png]]

（18）单击“重启”按钮，系统重启后，进入系统，可以进行系统初始设置，如图xxx所示。

![[1729921522135-082d4f4e-272f-4bc4-9fbc-9dff06098574.png]]

（19）单击“LICENSE INFORMATION”，弹出 CentOS7 Linux EULA 许可协议界面，选中“我同意许可协议”复选框，单击“完成”按钮，如图xxx所示。

![[1729921698684-07c6bbfb-e3a2-4342-a95a-2eb494fb5f8c.png]]

（20）单击“完成配置”按钮，弹出欢迎界面，选择语言为汉语，如图xxx所示。

![[1729921845012-9d03e036-d0c7-48df-aa11-db3492ae0b78.png]]

（21）单击“前进”按钮，弹出时区界面，在查找地址栏中输入“shang”，选择上海选项，如图xxx所示。

![[1729922132116-51ce0d05-7428-4af5-b2eb-6bcca10c62c5.png]]

（22）单击“前进”按钮，如图xxx所示。

![[1729922331001-08267df4-bcce-48a4-bbad-127e57942a4d.png]]

（23）单击“跳过”按钮，进入关于您页面，输入docker，创建用户名为docker的普通用户，如图xxx所示。

![[1729922481984-152065b7-399f-499c-abb1-4db3bf22722a.png]]

（24）单击“前进”按钮，进入用户密码设置页面，密码规则需要符合大小写加数字和字符的要求才能通过复杂度检测，如图xxx所示。

![[1729922757654-fa07e7df-71a7-422c-8365-4624ede41682.png]]

（25）单击“前进”按钮，出现“一切就绪，开始用吧！”即完成了Centos7.6的安装，如图xxx所示。

![[1729922869473-ad496adb-63d6-43a7-9eb0-7c4509da5df6.png]]

（26）安装完成后我们可以为Centos7.6系统拍摄快照，可以因误操作导致系统崩溃无法使用时，实现快速回滚到拍摄快照时的状态，右击虚拟机选项卡->快照->拍摄快照，如图xxx所示。

![[1729923632239-7029c27f-251d-4ba0-aec2-21c7119340f3.png]]

（28）单击“拍摄快照”按钮，如图xxx所示。

![[1729924058316-962cd0e2-b79a-4206-bd9a-43bb720c29b8.png]]

(29)要想快速回滚到拍摄快照时的状态，右击“虚拟机选项卡->快照->恢复到快照(R)：安装完成”，如图xxx所示。注意，一旦恢复快照，在拍摄状态前的所有操作都会失效，数据也会丢失，请谨慎使用回滚操作。

![[1729924131401-7d816a93-a430-4c83-961d-1d9017d61245.png]]

注意事项：

如果在安装 Linux 后出现启动失败，检查BIOS虚拟化支持是否已启用。

确保下载的 CentOS 镜像文件完整无损，避免安装过程中出现问题。

##### MobaXterm远程连接工具安装
MobaXterm 是一款功能强大的跨平台远程计算机管理工具，适合系统管理员、开发人员和网络工程师。它集成了多种功能，提供便捷的远程访问和系统管理。

安装步骤

###### （1）下载 MobaXterm
[https://mobaxterm.mobatek.net/download.html](https://mobaxterm.mobatek.net/download.html)

前往 MobaXterm 官网 下载最新版本的 MobaXterm。

###### （2）选择版本
MobaXterm 提供多个版本，选择便携式版本（Portable Edition），无须安装，解压即可直接运行，如图xxx所示。

![[1729924849969-6bafe3b0-f990-4bef-929b-985821b71e5d.png]]

###### （3）解压文件
解压缩下载的MobaXterm_Portable_v24.2.zip到目录。

###### （4）启动 MobaXterm
双击解压后的 MobaXterm.exe 文件启动程序，如图xxx所示。

![[1729924996149-7e34d674-cc9c-4b55-9f69-071ed6cf2291.png]]

功能简介

+ **远程访问**：支持 SSH、RDP、VNC 等多种协议，轻松连接到远程服务器。
+ **文件传输**：集成 SFTP 和 SCP 客户端，支持简单的文件上传、下载和跨主机复制。
+ **会话管理**：支持将会话和设置导出和导入，方便在不同计算机间使用。

注意事项

+ 确保在使用远程连接时，目标主机已开启相应服务，并具备正确的网络配置。

##### 远程连接、管理Linux操作系统
###### （1）设置虚拟网络编辑器
VMware选项卡中“编辑->虚拟网络编辑器”打开虚拟网络编辑器，如图xxx所示。

![[1729929624130-1f687e1a-18d2-47ec-961f-2a339c940efb.png]]

###### （2）修改对应的网段地址
选择VMnet8，修改子网IP为192.168.100.0，子网掩码为255.255.255.0，点击“NAT设置”，将网关改成192.168.100.2，如图xxx所示。

![[1729927436462-dc462917-32e3-46d7-9de0-adc4711c2d5d.png]]

###### （3）配置虚拟机静态网络
虚拟机网段和网关的设置需要参考VMware虚拟网络编辑器中的NAT模式设置，虚拟机的IP地址应选手动，建议通过NAT模式直接访问外网。本例中虚拟机的IP地止设置为192.168.100.100，默认网关为192.168.100.2，DNS为192.168.100.2，如图xxx所示。

![[1729927003863-c01147e2-bbd7-4876-ab18-c068400d1a3b.png]]

修改完后，需要在详细信息页面勾选“自动连接”，防止重启后需要手动开启网络连接，点击“应用”按钮，应用设置，如图xxx所示。

![[1731381099860-74d27b8c-82a2-4bd4-a98f-1e28cc35cc58.png]]

###### （4）重启网络服务
修改后手动将虚拟机有线连接开关一次，使其生效，如图xxx所示。

![[1729927485067-9c2aafca-9cf9-4f8f-87b7-c5fa7a657cdd.png]]

###### （5）设置远程连接
使用MobaXterm连接ssh终端，点击“Session->SSH”输入Remote host为上一步修改的IP地址：192.168.100.100，Specify username为root，如图xxx所示。

![[1729928223096-456a8ce1-9e4d-456b-951c-d34dc6878beb.png]]

###### （6）登录远程服务器
点击“OK”按钮，输入root用户的密码，连接虚拟机的ssh服务，如图xxx所示。注意：输入密码时不会有任何输入反馈，仔细输入正确的root密码后按回车即可。

![[1729928310390-c5c469d7-529f-40f6-a244-7cdd79086f7d.png]]

######
### 1.2.2 在Windows上安装Docker的步骤
在docker官方网站上下载Windows对应的安装文件

![[1731405232330-9c68ea72-0041-42c0-85fb-cf34af516eb8.png]]

![[1731405250891-01ea78e0-43c7-4648-a91e-0d6baa00e12b.png]]

Docker Desktop版本：4.35.1

![[1731398990860-7dcf6606-9573-4446-9f90-d9dd5c3fa14d.png]]

（）启用Hyper-V虚拟化功能能，如图xxx所示。

![[1731399365845-652981ee-0285-4d3e-9f4f-651311a84aca.png]]

（）点击“立即重新启动”，电脑会重启，重启后功能即可正常使用。

![[1731399489942-29a01c6c-43c9-4489-9858-ca66fdfdbf02.png]]

（）双击Docker Desktop Installer.exe可执行文件，进入安装界面，这里我们选择的是Hyper-V作为docker的运行环境，取消勾选“Use WSL 2 instead of Hyper-V(recommended)”，如图xxx所示

![[1731403416183-11ea26b4-1f2c-4fa5-8371-40efb5755f7b.png]]

（）点击“OK”按钮开始docker desktop的安装

![[1731397624542-baf89574-f686-44f6-aac0-570d35fad11d.png]]

（）点击“Close and restart”按钮，重启电脑，完成Docker Desktop的安装，如图xxx所示。

![[1731397796260-7ff52a52-883a-44ee-8a70-8d1b023aa818.png]]

（）安装完成后运行双击运行Docke Desktop，选择Accept接受，如图xxx所示。

![[1731397947688-ed7577e9-f660-4d44-afa5-3c97087c5a09.png]]

（）跳过登录操作，如图xxx所示。

![[1731404098342-69779b0b-749a-4868-bca0-48845a6ff991.png]]

()点击skip

![[1731404125259-87e7ee33-db31-4b7d-b675-a7207643275d.png]]

（）启动后，进入Settings界面配置国内镜像代理，拉取docker hub中的镜像，点击齿轮图标，如图xxx所示。

![[1731404324689-931265bb-3aee-4459-8048-294f8622470e.png]]

（）点击“Docker Engine”修改docker的配置文件，点击“Apply&restart” 应用配置文件，如图xxx所示。

![[1731400695572-763d184f-8b3e-423b-9182-601e641f7b61.png]]

完整配置如下，只需添加“registry-mirrors” 即可。

```text
{
  "builder": {
    "gc": {
      "defaultKeepStorage": "20GB",
      "enabled": true
    }
  },
  "experimental": false,
  "registry-mirrors": [
    "https://docker.1panel.live",
    "https://docker.1panelproxy.com",
    "https://proxy.1panel.live"
  ]
}
```

（）以管理员权限打开powershell或者windows命令提示符，即可使用docker命令与docker交互，验证docker命令的使用。

```powershell
PS C:\Users\Administrator> docker --version
Docker version 27.3.1, build ce12230
```

![[1731401333048-3a12d3ca-9575-4a94-b409-a6ab458cbc00.png]]

拉取nginx镜像，并运行

```powershell
PS C:\Users\Administrator> docker pull nginx:1.25
1.25: Pulling from library/nginx
09f376ebb190: Pull complete
a11fc495bafd: Pull complete
933cc8470577: Pull complete
999643392fb7: Pull complete
971bb7f4fb12: Pull complete
45337c09cd57: Pull complete
de3b062c0af7: Pull complete
Digest: sha256:a484819eb60211f5299034ac80f6a681b06f89e65866ce91f356ed7c72af059c
Status: Downloaded newer image for nginx:1.25
docker.io/library/nginx:1.25

PS C:\Users\Administrator> docker run -d --name nginx -p 80:80 nginx:1.25
3e2302e7e763c64666b6d19c25645f2a414b8b833d79beec274891f9e5428a2e

PS C:\Users\Administrator> docker ps
CONTAINER ID   IMAGE        COMMAND                   CREATED         STATUS         PORTS                NAMES
3e2302e7e763   nginx:1.25   "/docker-entrypoint.…"   7 seconds ago   Up 7 seconds   0.0.0.0:80->80/tcp   nginx
```

（）进入浏览器输入localhost或Windows主机的IP地址，访问启动的nginx容器

![[1731401487691-e828ed8a-a645-4488-9f18-a7fb961292ca.png]]

### 1.2.3 在Linux上安装Docker的步骤
在Linux平台上安装Docker时，用户可以选择不同的安装方式。最常见和推荐的方法是通过Docker的软件仓库进行安装。另外，有些用户可能会选择手动下载软件包进行安装，这样可以完全自主管理升级，特别适合在没有互联网连接的系统中使用。在测试和开发环境中，用户也可以利用自动化脚本快速安装Docker。

如果用户希望试用Docker或在不受支持的操作系统上进行测试，另一个选择是通过二进制文件进行安装。不过，最好还是使用专为当前操作系统构建的软件包，并通过操作系统的包管理系统来管理Docker的安装和更新。

#### 1.安装docker
以下是通过软件仓库在CentOS 7上在线安装Docker CE的具体步骤。

（1）安装完centos7后主机名默认为localhost，这里将其修改为docker1，代码如下：

```text
hostnamectl set-hostname docker1
```

（2）禁用防火墙与SELinux，为方便测试，建议初学者禁用防火墙与SELinux。要禁用SELinux，可修改/etc/selinux/config文件，将“SELINUX”选项设置为“disabled”，重启系统使之生效，执行以下命令禁用防火墙与SELinux。

```bash
systemctl disable firewalld
systemctl stop firewalld
sed -i "s/enforcing/disabled/g" /etc/selinux/config
```

查看防火墙是否关闭，执行命令如下：

```text

[root@docker1 ~]# systemctl status firewalld
```

成功关闭则输出如下内容：

```yaml
● firewalld.service - firewalld - dynamic firewall daemon
   Loaded: loaded (/usr/lib/systemd/system/firewalld.service; disabled; vendor preset: enabled)
   Active: inactive (dead)
     Docs: man:firewalld(1)
```

查看selinux配置文件是否成功修改，执行如下命令：

```text
//将SELINUX=enforcing改为SELINUX=disabled
[root@docker1 ~]# cat /etc/selinux/config
```

结果如下：

```text
# This file controls the state of SELinux on the system.
# SELINUX= can take one of these three values:
#     disabled - SELinux security policy is enforced.
#     permissive - SELinux prints warnings instead of disabled.
#     disabled - No SELinux policy is loaded.
SELINUX=disabled
# SELINUXTYPE= can take one of three values:
#     targeted - Targeted processes are protected,
#     minimum - Modification of targeted policy. Only selected processes are protected.
#     mls - Multi Level Security protection.
SELINUXTYPE=targeted
```

（3）测试与外网的联通性，尝试ping百度服务器，执行如下命令：

```text
[root@docker1 ~]# ping www.baidu.com
```

成功，结果如下：

```text
PING www.baidu.com (183.240.98.198) 56(84) bytes of data.
64 bytes from 183.240.98.198 (183.240.98.198): icmp_seq=1 ttl=128 time=40.5 ms
64 bytes from 183.240.98.198 (183.240.98.198): icmp_seq=2 ttl=128 time=56.2 ms
64 bytes from 183.240.98.198 (183.240.98.198): icmp_seq=3 ttl=128 time=42.4 ms
64 bytes from 183.240.98.198 (183.240.98.198): icmp_seq=4 ttl=128 time=36.9 ms
64 bytes from 183.240.98.198 (183.240.98.198): icmp_seq=5 ttl=128 time=42.9 ms
64 bytes from 183.240.98.198 (183.240.98.198): icmp_seq=6 ttl=128 time=38.5 ms
^C
--- www.baidu.com ping statistics ---
6 packets transmitted, 6 received, 0% packet loss, time 5009ms
rtt min/avg/max/mdev = 36.951/42.944/56.214/6.289 ms
```

（4）centos7官方不再进行支持维护，如果使用官方yum源下载软件包将会报错，将软件源更换为阿里云的镜像仓库，先将官方文件备份，再从网络下载阿里云的镜像源，执行如下命令：

```bash
[root@docker1 ~]# mkdir /root/bak
[root@docker1 ~]# mv /etc/yum.repos.d/* /root/bak
[root@docker1 ~]# curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
```

执行结果如下：

```text
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  2523  100  2523    0     0   7700      0 --:--:-- --:--:-- --:--:--  7715
```

查看/etc/yum.repos.d/CentOS-Base.repo中的内容，执行如下命令：

```bash
[root@docker1 ~]# cat /etc/yum.repos.d/CentOS-Base.repo
```

执行结果如下：

```ini
# CentOS-Base.repo
#
# The mirror system uses the connecting IP address of the client and the
# update status of each mirror to pick mirrors that are updated to and
# geographically close to the client.  You should use this for CentOS updates
# unless you are manually picking other mirrors.
#
# If the mirrorlist= does not work for you, as a fall back you can try the
# remarked out baseurl= line instead.
#
#

[base]
name=CentOS-$releasever - Base - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever/os/$basearch/
        http://mirrors.aliyuncs.com/centos/$releasever/os/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos/$releasever/os/$basearch/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7

#released updates
[updates]
name=CentOS-$releasever - Updates - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever/updates/$basearch/
        http://mirrors.aliyuncs.com/centos/$releasever/updates/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos/$releasever/updates/$basearch/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7

#additional packages that may be useful
[extras]
name=CentOS-$releasever - Extras - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever/extras/$basearch/
        http://mirrors.aliyuncs.com/centos/$releasever/extras/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos/$releasever/extras/$basearch/
gpgcheck=1
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7

#additional packages that extend functionality of existing packages
[centosplus]
name=CentOS-$releasever - Plus - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever/centosplus/$basearch/
        http://mirrors.aliyuncs.com/centos/$releasever/centosplus/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos/$releasever/centosplus/$basearch/
gpgcheck=1
enabled=0
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7

#contrib - packages by Centos Users
[contrib]
name=CentOS-$releasever - Contrib - mirrors.aliyun.com
failovermethod=priority
baseurl=http://mirrors.aliyun.com/centos/$releasever/contrib/$basearch/
        http://mirrors.aliyuncs.com/centos/$releasever/contrib/$basearch/
        http://mirrors.cloud.aliyuncs.com/centos/$releasever/contrib/$basearch/
gpgcheck=1
enabled=0
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7

```

（5）使用yum命令安装必要的包，执行如下命令：

```text
[root@docker1 ~]# yum install -y  yum-utils device-mapper-persistent-data lvm2
```

（6）添加Docker社区版稳定版（Stable）的仓库地址，使用阿里云的镜像仓库源，执行命令如下：

```text
[root@docker1 ~]# yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
```

执行结果如下：

```text
已加载插件：fastestmirror, langpacks
adding repo from: http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
grabbing file http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo to /etc/yum.repos.d/docker-ce.repo
repo saved to /etc/yum.repos.d/docker-ce.repo
```

（7）查看镜像仓库中所有镜像的版本，并且由高到低排序，并且筛选出前10行，执行命令如下：

```text
[root@docker1 ~]# yum list docker-ce --showduplicates |sort -r|head -n 15
```

执行结果如下：

```text
已加载插件：fastestmirror, langpacks
可安装的软件包
 * updates: mirrors.aliyun.com
Loading mirror speeds from cached hostfile
 * extras: mirrors.aliyun.com
docker-ce.x86_64            3:26.1.4-1.el7                      docker-ce-stable
docker-ce.x86_64            3:26.1.3-1.el7                      docker-ce-stable
docker-ce.x86_64            3:26.1.2-1.el7                      docker-ce-stable
docker-ce.x86_64            3:26.1.1-1.el7                      docker-ce-stable
docker-ce.x86_64            3:26.1.0-1.el7                      docker-ce-stable
docker-ce.x86_64            3:26.0.2-1.el7                      docker-ce-stable
docker-ce.x86_64            3:26.0.1-1.el7                      docker-ce-stable
docker-ce.x86_64            3:26.0.0-1.el7                      docker-ce-stable
docker-ce.x86_64            3:25.0.5-1.el7                      docker-ce-stable
docker-ce.x86_64            3:25.0.4-1.el7                      docker-ce-stable
```

以上列表显示的是 Docker CE 在 CentOS 7 上的多个版本，版本号格式为 3:<版本号>-1.el7，其中每个条目代表一个特定的发布版本，均来自 docker-ce-stable 仓库。

（8）使用命令安装指定版本的 Docker CE，执行命令如下：

```text
[root@docker1 ~]# yum install -y docker-ce-26.1.4-1.el7
```

（9）安装完成后，启动 Docker 服务并设置开机自启，执行命令如下：

```text
[root@docker1 ~]# systemctl start docker
[root@docker1 ~]# systemctl enable docker
```

（10）因为国内网络原因，可能无法拉取到docker hub中的镜像，修改daemon.json配置文件，改成国内镜像源，解决无法拉取镜像问题，执行命令如下：

```bash
cat >> /etc/docker/daemon.json << EOF
{
"registry-mirrors": ["https://docker.1panel.live","https://docker.1panelproxy.com","https://proxy.1panel.live","https://dockerproxy.1panel.live"]
}
EOF
```

（11）重新加载docker配置，执行命令如下：

```text
[root@docker1 ~]# systemctl daemon-reload
[root@docker1 ~]# systemctl restart docker
```

（12）验证docker是否正常安装，可以通过运行hello-world镜像来测试，执行命令如下：

```yaml
[root@docker1 ~]# docker run hello-world
Unable to find image 'hello-world:latest' locally
latest: Pulling from library/hello-world
c1ec31eb5944: Pull complete
Digest: sha256:d211f485f2dd1dee407a80973c8f129f00d54604d2c90732e8e320e5038a0348
Status: Downloaded newer image for hello-world:latest

Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

```

当终端打印出以上消息，就代表安装的docker可以正常工作了，为了生成此消息，docker采取了以下步骤。

+ Docker 客户端请求：你在命令行中输入 docker run hello-world，Docker 客户端向 Docker 守护进程发送请求。
+ 查找本地镜像：Docker 守护进程检查本地是否已存在 hello-world 镜像。如果没有找到，守护进程会准备从 Docker Hub 下载该镜像。
+ 拉取镜像：守护进程从 Docker Hub 下载最新的 hello-world 镜像。在这个过程中，会显示拉取进度和状态信息。
+ 创建容器：下载完成后，Docker 守护进程使用该镜像创建一个新的容器实例。
+ 运行容器：容器启动后，运行其中的可执行文件，这个文件会输出“Hello from Docker!”的信息。
+ 输出结果：最后，Docker 守护进程将输出结果通过 Docker 客户端发送到你的终端，显示在屏幕上。

#### 2.升级docker
要想升级docker版本，只需要查看仓库中的所有docker版本，选择新的版本安装即可。

#### 3.卸载docker
（1）停止 Docker 服务

在卸载之前，停止 Docker 服务：

```text
systemctl stop docker
```

（2）使用以下命令卸载 Docker：

```text
yum remove docker-ce docker-ce-cli containerd.io
```

（3）完全删除 Docker 的数据，可以执行：

```bash
rm -rf /var/lib/docker
```

这个命令会删除 Docker 数据目录，包括所有容器、镜像和数据卷。

### 1.2.4 在macOS上安装Docker的步骤
其它：暂时略过mac桌面平台安装docker

### 任务实施：验证Docker安装是否成功
1.运行一个nginx容器，来验证docker的安装

```bash
[root@docker1 docker]# docker run -d --name nginx -p 80:80 nginx:1.25
Unable to find image 'nginx:1.25' locally
1.25: Pulling from library/nginx
09f376ebb190: Pull complete
a11fc495bafd: Pull complete
933cc8470577: Pull complete
999643392fb7: Pull complete
971bb7f4fb12: Pull complete
45337c09cd57: Pull complete
de3b062c0af7: Pull complete
Digest: sha256:a484819eb60211f5299034ac80f6a681b06f89e65866ce91f356ed7c72af059c
Status: Downloaded newer image for nginx:1.25
3618806c5e0d7d619806f1067bf6faf31b27d79fbd77d9b2e3810a75d4171b0e

[root@docker1 docker]# docker ps
CONTAINER ID   IMAGE        COMMAND                   CREATED          STATUS          PORTS                               NAMES
3618806c5e0d   nginx:1.25   "/docker-entrypoint.…"   21 seconds ago   Up 19 seconds   0.0.0.0:80->80/tcp, :::80->80/tcp   nginx

```

2.使用浏览器url输入框输入虚拟机的IP地址，访问nginx的默认页面，出现如下图xxx所示，代表nginx容器已经成功启动，docker正确安装。

![[1731396189443-929ba3fd-da79-449f-942e-2e73d06111da.png]]

### 知识拓展
