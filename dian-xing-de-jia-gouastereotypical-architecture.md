---
description: A Stereotypical Architecture 一章的翻译
icon: '1'
---

# 典型的架构（A Stereotypical Architecture ）

Before moving into architectures for Domain Driven Design based projects it is important to start off by\
analyzing what is generally considered to be the standard architecture that many try to apply to\
projects. We can from that point attempt to improve upon the stereotypical architecture in small\
rational steps while trying to minimize the cost in terms of productivity for each step towards a better\
architecture.

在深入考虑DDD架构之前，很重要的事情是分析被大部分项目视作并应用的标准架构。我们可以在此基础上，尝试小步改进典型架构，向着更好的架构演进，并最小化对团队生产力的影响。

&#x20;

Below is shown a diagram of a stereotypical architecture.

下面展示了一个典型架构图。

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption><p>Figure 1 一个典型的架构图</p></figcaption></figure>

### Application Server

The above architecture is centered upon a backing data storage system. This system although typically a RDBMS does not have to be, it could just as easily be a key/value store, and object database, or even\
plain XML files. The important aspect of the backing store is that it is representing the current state of\
objects in the domain.

上面的架构以后台的存储系统为核心搭建。存储系统没必要是一个典型的RDBMS系统，它可以是一个key-value的存储系统，一个对象存储系统，甚至多个XML都行。存储系统重要的是其代表了领域对象的状态。

> RDBMS（Relational Database Management System，关系型数据库管理系统）是一种基于关系模型管理结构化数据的软件系统，其核心是通过表格形式组织数据，并通过预定义的关系实现不同表之间的逻辑关联。

Above the backing data storage lies an Application Server. An area of logic, labeled as the domain in\
Figure 1 contains the business logic of the system. In this area validation and orchestration logic exists\
for the processing of requests given to the Application Server.

在后台存储系统更上层的是Application Server 。在图1中被标记为domain的层包含系统的业务逻辑。在domain层中，校验和逻辑编排的目的都是为了处理给Application Server层的请求。

\
It is important to note that although Figure 1 is drawn without a data tier one could place a data tier in\
between the Application Server and the Data Storage. It is also important to note that a “domain” is not\
necessary to achieve this architecture, one could also use other patterns such as Table Module or\
Transaction Script. With these only existing as Application Services.

需要指出的是，图1未明确绘制数据层（data tier），但实际可以将数据层置于Application Server与数据存储层（Data Storage）之间。此外，实现这种架构并不强制要求使用"领域模型"（domain），开发者也可以采用其他模式，例如表模块（Table Module）或事务脚本（Transaction Script），这些模式仅以应用服务（Application Services）的形式存在。

> * **表模块（Table Module）**：以数据表为核心组织业务逻辑，适合批量数据处理场景，通过类封装对整张表的操作。
> * **事务脚本（Transaction Script）**：以过程化方式处理单个业务事务，通过脚本直接操作数据库，适用于简单业务逻辑。 这两种模式均可替代领域模型，通过应用服务层实现业务逻辑的封装。

\
Abstracting the “domain” one will find a facade known as Application Services. Application Services\
provide a simple interface to the domain and underlying data, they also limit coupling between the\
consumers of the domain and the domain itself.

\
On the outside of the Application Server sits some type of Remote Facade. This could be many things\
such as SOAP, custom TCP/IP, XML over HTTP, TomCat, or even a person manually typing messages that arrive tied to the legs of pigeons. The Remote Facade may or may not be abstracted away from its\
underlying technology mechanism depending on the situation and tools that are involved.

\
The overall usage of an Application Server to abstract away the data storage of a system and to provide\
a centralized location of business logic has become very popular over the years and at the time of this\
writing is in many circumstances considered to be the default architecture applied to many systems.
