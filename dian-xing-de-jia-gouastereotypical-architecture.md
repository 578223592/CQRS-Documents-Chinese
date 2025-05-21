---
description: A Stereotypical Architecture 一章的翻译
icon: '1'
---

# 典型的架构（A Stereotypical Architecture ）

Before moving into architectures for Domain Driven Design based projects it is important to start off by analyzing what is generally considered to be the standard architecture that many try to apply to projects. We can from that point attempt to improve upon the stereotypical architecture in small rational steps while trying to minimize the cost in terms of productivity for each step towards a better architecture.

在深入考虑DDD架构之前，很重要的事情是分析被大部分项目视作并应用的标准架构。我们可以在此基础上，尝试小步改进典型架构，向着更好的架构演进，并最小化对团队生产力的影响。

Below is shown a diagram of a stereotypical architecture.

下面展示了一个典型架构图。

<figure><img src=".gitbook/assets/image (1) (1).png" alt=""><figcaption><p>Figure 1 一个典型的架构图</p></figcaption></figure>

### 应用服务 Application Server

The above architecture is centered upon a backing data storage system. This system although typically a RDBMS does not have to be, it could just as easily be a key/value store, and object database, or even plain XML files. The important aspect of the backing store is that it is representing the current state of objects in the domain.

上面的架构以后台的存储系统为核心搭建。存储系统没必要是一个典型的RDBMS系统，它可以是一个key-value的存储系统，一个对象存储系统，甚至多个XML都行。存储系统重要的是其代表了领域对象的状态。

> RDBMS（Relational Database Management System，关系型数据库管理系统）是一种基于关系模型管理结构化数据的软件系统，其核心是通过表格形式组织数据，并通过预定义的关系实现不同表之间的逻辑关联。

Above the backing data storage lies an Application Server. An area of logic, labeled as the domain in Figure 1 contains the business logic of the system. In this area validation and orchestration logic exists for the processing of requests given to the Application Server.

在后台存储系统更上层的是Application Server 。在图1中被标记为domain的层包含系统的业务逻辑。在domain层中，校验和逻辑编排的目的都是为了处理给Application Server层的请求。

It is important to note that although Figure 1 is drawn without a data tier one could place a data tier in between the Application Server and the Data Storage. It is also important to note that a “domain” is not necessary to achieve this architecture, one could also use other patterns such as Table Module or Transaction Script. With these only existing as Application Services.

需要指出的是，图1未明确绘制数据层（data tier），但实际可以将数据层置于应用服务（Application Server）与数据存储层（Data Storage）之间。此外，实现这种架构并不强制要求使用"领域模型"（domain），开发者也可以采用其他模式，例如表模块（Table Module）或事务脚本（Transaction Script），这些模式仅以应用服务（Application Services）的形式存在。

> * **表模块（Table Module）**：以数据表为核心组织业务逻辑，适合批量数据处理场景，通过类封装对整张表的操作。
> * **事务脚本（Transaction Script）**：以过程化方式处理单个业务事务，通过脚本直接操作数据库，适用于简单业务逻辑。 这两种模式均可替代领域模型，通过应用服务层实现业务逻辑的封装。

Abstracting the “domain” one will find a facade known as Application Services. Application Services provide a simple interface to the domain and underlying data, they also limit coupling between the consumers of the domain and the domain itself.

抽象“domain”的时候会发现应用服务（Application Services）是一个“门面”。应用服务（Application Services）提供了面向领域和依赖领域的数据之间的简单接口，其也减少了领域和领域消费方的耦合。

On the outside of the Application Server sits some type of Remote Facade. This could be many things such as SOAP, custom TCP/IP, XML over HTTP, TomCat, or even a person manually typing messages that arrive tied to the legs of pigeons. The Remote Facade may or may not be abstracted away from its underlying technology mechanism depending on the situation and tools that are involved.

在应用服务（Application Services）之外有很多类型的远程接口（Remote Facade），它们可能是类似于SOAP、定制化的TCP/IP协议，XML服务，TomCat服务器，甚至是绑在🕊鸽子腿上的某些手动输入的信息。远程接口（Remote Facade）可能已经也可能没有被抽象出来，这取决于具体的情况。

The overall usage of an Application Server to abstract away the data storage of a system and to provide a centralized location of business logic has become very popular over the years and at the time of this writing is in many circumstances considered to be the default architecture applied to many systems.

应用服务（Application Services）的整体用途是抽象出来系统的数据存储层和内聚业务逻辑（business logic）。这个观点在过去几年变得非常流行，在此刻（原作者写作时）这样的架构已然变成了很多系统的默认架构。

## 客户端交互 Client Interaction

Interacting with the Application Server there is a / are many client(s). The general interaction of the clients can be seen in Figure 2.1

会有单个或者多个客户端与应用服务（Application Services）交互。图2.1展示了了一个普遍的客户端交互示意图。

<figure><img src=".gitbook/assets/image (1) (1) (1).png" alt=""><figcaption><p>Figure 2 典型的客户端交互</p></figcaption></figure>

The basic interaction of the client can be described as a DTO (Data Transfer Object) up/down interaction. Going through the lifecycle of an operation is the easiest way to show the functioning of the API. A user goes to a screen, perhaps to edit a customer. The client sends a request to the remote\
facade for a DTO representing Customer #id. The Remote facade loads up the domain objects required, and maps the domain objects to a DTO that is then returned to the client. An example of DTO in XML format can be seen in Figure 3 but the basic explanation is that the DTO in this stereotypical architecture contains the current state of the object in questions.

客户端的基本交互可以认为是DTO（Data Transfer Object）的上传和下载的交互。想要了解API的功能，最简单的方式就是经历一个操作的全流程（lifecycle）。用户点击屏幕📱，可能是想要编辑「顾客」的信息。客户端向远端接口发送请求以获取可以表示 「顾客#id」的DTO。远端接口加载领域对象，并将领域对应映射成需要返回客户端的DTO。下面代码块（原文称为图3）展示了一个返回的DTO的示例，更关键的是理解在典型架构中，DTO包含传输对象的当前状态。

```xml
<Contact id=”1234”>  
    <Name>Greg Young</Name> 
    <Address> 
        <Street>111 Some St.</Street> 
        <City>Vernon</City> 
        <State>CT</State> 
        <Zip>06066</Zip> 
        <Country>USA</Country> 
   </Address> 
</Contact>
```

The client will then display the information received from the Remote Facade on the screen allowing the user to interact with it. This is very often done utilizing a view model and/or data binding with the view.

客户端收到远端接口（Remote Facade）的响应后会在屏幕上展示用户可以交互的信息。大家通常会使用视图模型（view model）和/或数据绑定来完成这一步。

At some point the user will be complete with the editing of the data on the screen and will through some action cause the UI to “Save” the data. Generally this is implemented through a “Save Button” although some User Interfaces will instead just have you leave the current data which forces a save.

在某一时刻，用户将会完成数据编辑操作，并通过某些操作使得用户界面（UI）“保存”数据。一般来说，这一操作是通过“保存按钮”实现的，虽然某些用户接口会再用户离开界面的时候强制保存。

The processing of a Save on the client will take the data that has been edited by the user on the screen,pack it back into a DTO (usually identical to the DTO it requested from the Remote Façade for displaying to the user ). It will then send this DTO back up to the Application Server.

在客户端保存的处理过程中，客户端会携带用户刚在屏幕📱上编辑过的数据，将其包装成DTO（通常来说这个DTO与客户端从远端接口中获取的用于给用户展示的DTO是等价的），并返回给应用服务器。

The Application Server receiving the DTO will then start a transaction/session, map the DTO back to the domain objects, allow the domain objects to verify any changes, then save the changes within the domain objects back to the data storage likely through the use of something like an Object Relational Mapper that has the ability to distinguish what has changed in the domain objects and update the data storage accordingly. The Application Server will return to the client either an Acknowledgement (Ack) that the change has been persisted or it will return a series of errors as to why it was unable to persist the changes.

应用服务器接收到DTO对象之后就会开启一个 事务/会话（session），将DTO重新映射（转换）回领域对象，让领域对象们去校验数据的更改，之后会通过将领域对象转化成持久化对象的方式保存这些修改，领域对象转化成持久化对象是通过一些可以识别出领域对象有哪些修改和对应的数据持久化对象应该有哪些修改的技术，比如说ORM（Object Relational Mapper）。最后，应用服务器会给客户端返回一些信息，要么是让客户端知道更改已经持久化，要么是发送一些错误用于解释为什么没能持久化这些更改。

> **Object Relational Mapper（ORM，对象关系映射器）** 是一种将面向对象编程中的对象与关系型数据库中的表进行映射的技术工具。比如说golang中gorm就是一个很常用的ORM框架。

## 分析典型的架构 Analysis of the Stereotypical Architecture

The architecture provided above as with any architecture has many properties. Some of these properties are good under certain scenarios and other properties can be extremely bad in others. As architects we should really be trying to align many of these properties to best fit our needs.

上面提供的架构和其他任何架构一样有许多的特性。其中的一些只适用于一些场景而在其他场景表现糟糕。作为架构师，我们应该调整这些特性以最好的满足需求。

### 简单性 Simplicity

When looking at properties it is important to note what the most likely property is for a given architecture becoming popular. In the architecture above the most likely property defining its popularity is that it is simple. One could teach a Junior developer how to interact with a system built using this architecture in a very short period of time. Going along with the simplicity, the architecture is completely generic. One could use this architecture on every project. Along with being able to use it on every project, because many people are doing it, its likely that if a team brings on a new member the new member will be intimately familiar with the general architecture of their system again lowering on ramp up costs.

当了解特性时，关注使得特定架构流行起来的关键特性非常重要。对于上面的架构，定义其流行度的最关键的就是它很简单。只需要非常短的时间一个初级开发就可以学会如何与基于这样的架构开发起来的系统交互。由于简单，架构完全通用---可以在任何项目中应用这样的架构。由于架构得到了普遍使用，如果团队来了一个新同学，新同学可以很快的对系统的通用架构熟悉起来，这又降低了学习培训的成本。

The combination of these two items allows teams to become extremely adept at applying this architecture and more important it allows them to use it as a default architecture. The thought process of needing to align non-functional requirements really goes away as they know that this architecture will be “good enough” for 80% of the projects that they run into.

这两者（简单性和学习成本）的结合让团队对应用这种架构具有很高的适应性，更重要的是，这种架构可以是一种默认架构。由于他们清楚这种架构足以应对80%的项目需求，因此那些需要反复权衡非功能性需求的思考过程也随之消失——他们知道这种方案已经"足够好用"了。

### 工具 Tooling

Many frameworks exist for the optimization of time required to create systems utilizing the architecture provided above. ORM’s are the largest single example as they provide valuable services such as change tracking and transaction management with complex object graphs. Other examples would include the automapping frameworks that map from the domain objects to DTOs on both sides resulting in largely removing the amount of “plumbing code” required to map the DTOs back and forth in the Application Server.

许多现有的框架工程可用于减少创建基于上述架构创建的系统所花费的时间。ORM框架是其中最大的例子，因为ORM提供了诸如变更最终和复杂的对象图的事务管理。另一个例子是自动映射（automapping）框架，其可以完成领域对象和DTO对象的双向映射，因此在应用服务可以减少大量的从DTO对象来回映射的“管道代码”。

\
Of course there are however also many not-so-good things associated with the architecture provided above. It being that this document is associated with Domain Driven Design the single most important of the not-so-good properties would be that it is impossible to apply Domain Driven Design with the architecture provided.

当然，上述架构提供的并非都是如此美好的东西。这篇文档是关于领域驱动设计的，上述架构并不是那么美好的特质中一个简单的重要特质就是其无法应用领域驱动设计。

### 领域驱动设计 Domain Driven Design

The application of Domain Driven Design is not possible in the above architecture though many who are “practicing” Domain Driven Design use this architecture. The reasoning for why it is impossible can easily be seen when one looks at how the Ubiquitous Language is represented by the object model.\
In the architecture above there are only four verbs (and of course synonyms for those four such as edit instead of update). The four verbs are Create, Read, Update, and Delete or CRUD as they are commonly known in the industry. Because the Remote Façade has a data oriented interface the Application Services must necessarily have the same interface.

虽然很多人都使用上述架构“实践”领域驱动设计，但领域驱动设计在上述架构中是不可能实现的。不可能的原因很简单，关键点在于对象模型（object model）是如何表征通用语言（译者：在领域驱动设计中，**对象模型必须严格遵循通用语言**）。在上述架构中只有4个动词（当然，4个动词的同义词也算，比如编辑与更新）。4个动词就是：增删改查，也就是业界常说的CRUD。因为远端接口有数据导向的接口，因此应用服务必须有同样的接口。

> 通用语言（Ubiquitous Language）：是领域驱动设计（DDD）中的核心概念，是团队（包括开发人员、领域专家、产品经理等）在业务领域内共享的统一语言，用于精确描述业务概念、规则和流程。

\
This means that there are no other verbs within the domain. When however one talks with domain experts in an effort to refine an Ubiquitous Language, it is extremely rare that one ends up with a language that is focused on these four verbs.

这就意味着领域内没有其他的动词了。然而，当人们与领域专家交流以精炼统一语言（Ubiquitous Language）时，最终得到的语言很少能完全聚焦于这四个动词。

\
There is a related well-known anti-pattern of domain modeling known as an “Anemic Model”.\
“The basic symptom of an Anemic Domain Model is that at first blush it looks like the real thing. There are objects, many named after the nouns in the domain space, and these objects are connected with the rich relationships and structure that true domain models have. The catch comes when you look at the behavior, and you realize that there is hardly any behavior on these objects, making them little more than bags of getters and setters. Indeed these models often come with design rules that say that you are not to put any domain logic into the domain objects. Instead there are a set of service objects which capture all of the domain logic. These services live on top of the domain model and use the domain model for data” (Fowler, 2003)

有一种众所周知的反范式的领域模型，“贫血模型”。“贫血领域模型的基本特征是：乍一看，它们看起来非常像真实的东西。许多对象以对应的领域空间命名，且与真正的领域模型拥有的丰富的关系和结构相关联。但是当你观察他们行为的时候，你就会发现问题，这些对象很难有任何行为，不过就是一堆堆的getter和setter。实际上，这些模型通常伴随着设计规则，这些设计规则表明不能将任何域逻辑放入域对象中。实际上全部的领域逻辑都在service对象中。这些service对象在领域对象之上，只是将领域对象用于数据处理。”--Fowler, 2003。

The model that is being built in this architecture sounds at first to be an anemic domain model. Because the Application Services map data back and forth to DTO’s the domain objects have little behavior and are littered with getters and setters to be used in the mapping process. There is a structure to the domain showing how objects relate with one another but ...

这种架构下的模型乍一看是贫血领域模型。因为应用服务来回映射DTO，领域对象基本没有行为，而且在映射过程中都是使用setter和getter方法。这种结构展示对象之间如何相互关联，但是。。。

\
One cannot even create and Anemic Domain Model with this architecture as then all of the business logic would be in services, here the services themselves are really just mapping DTO’s to domain objects, there is no actual business logic in them. In this case a large amount of business logic is not existing in the domain at all, nor in the Application Server, it may exist on the client but more likely it exists on either pieces of paper in a manual or in the heads of the people using the system.

你甚至没办法使用这样的架构创建贫血领域模型，因为贫血领域模型全部的业务逻辑都在服务层，而在这，服务层的作用真的只是将DTO映射到领域对象，服务层中没有任何业务逻辑。在这个例子中，大量的业务逻辑压根不在领域中（跟领域毫无关系），也不在应用服务器中，可能存在客户端中，也可能存在手写的某堆纸上，或者是在系统使用人员的心中。

\
Architectures like the one being viewed tend to come with instructions of how to complete complex tasks by editing data in many parts of the system. A stereotypical example of this would be when changing the sex of an employee you must after go edit their health insurance information. This is far worse than the creation of an anemic model, this is the creation of a glorified excel spreadsheet.

像我们上面看到的架构往往会附带一个指南，用以说明如何在系统不同的地方编辑数据以完成复杂的任务。一个典型的案例是单改变受聘者性别的时候，你必须先编辑他们的健康保险情况。这比贫血模型更糟糕😰，这是一个创建Excel表格的光荣任务。

### 伸缩 Scaling

When one looks at the architecture provided above in the context of scaling one will quickly notice that there is a large bottle neck. The bottleneck in terms of scaling is the data storage. When using a RDBMS as 90%+ currently use this becomes even more of a problem most RDBMS are at this point not horizontally scalable and vertically scaling becomes prohibitively expensive very quickly. It is however also extremely important to remember that most systems do not need to scale and as such scalability is really not a grave issue in all cases.

如果想要伸缩资源，大家很快就会注意到在这样的架构下存在一个巨大的瓶颈。瓶颈就是数据存储。当使用关系型数据库管理系统（RDBMS）时（目前90%以上的场景如此），这一问题会更加突出——大多数RDBMS目前仍无法水平扩展，而垂直扩展的成本会迅速攀升至难以承受的程度。然而，很重要的一点是记住大部分系统不需要伸缩，可伸缩性并非在所有的情况下都是一个严重的问题。

> **horizontally scalable（水平扩展）** 和 **vertically scaling（垂直扩展）:**
>
> * **horizontally scalable（水平扩展）：**&#x901A;过增加更多的服务器或节点来分散负载和数据存储，从而提升系统整体容量和性能。也称为“scale-out”。
> * **vertically scaling（垂直扩展）：**&#x901A;过升级单个服务器的硬件资源（如CPU、内存、存储）来提升性能。也称为“scale-up”

## Summary

The DTO up/down architecture employed on many projects is capable of being used for many\
applications and can offer many benefits in terms of simplicity for teams to work with. It cannot\
however be used with a Domain Driven Design based project, to attempt so will bring failure to your\
efforts at applying Domain Driven Design.

很多项目中应用DTO上传/下载的架构可以胜任很多应用，对于简化团队协同也很有帮助。然而，这样的架构与领域驱动设计相斥，如果你想尝试的话一定是以失败告终。

\
This architecture does however make a good baseline and the rest of this document will be focused on\
improving this architecture in incremental steps while attempting to limit or remove cost while adding\
business value at each additional step.

无论如何，这样的架构是一个很好的基线，本文档的剩余部分会聚焦于一步一步的改进架构，在每一步改进中力求控制或消除成本，同时确保每个新增步骤都能带来业务价值。

## 引用 Works Cited

\
Fowler, M. (2003, 11 25). MF Bliki: AnemicDomainModel. Retrieved from Bliki: http://martinfowler.com/bliki/anemicdomainmodel
