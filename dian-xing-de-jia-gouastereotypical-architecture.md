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
It is important to note that although Figure 1 is drawn without a data tier one could place a data tier in between the Application Server and the Data Storage. It is also important to note that a “domain” is not necessary to achieve this architecture, one could also use other patterns such as Table Module or Transaction Script. With these only existing as Application Services.

需要指出的是，图1未明确绘制数据层（data tier），但实际可以将数据层置于应用服务（Application Server）与数据存储层（Data Storage）之间。此外，实现这种架构并不强制要求使用"领域模型"（domain），开发者也可以采用其他模式，例如表模块（Table Module）或事务脚本（Transaction Script），这些模式仅以应用服务（Application Services）的形式存在。

> * **表模块（Table Module）**：以数据表为核心组织业务逻辑，适合批量数据处理场景，通过类封装对整张表的操作。
> * **事务脚本（Transaction Script）**：以过程化方式处理单个业务事务，通过脚本直接操作数据库，适用于简单业务逻辑。 这两种模式均可替代领域模型，通过应用服务层实现业务逻辑的封装。

\
Abstracting the “domain” one will find a facade known as Application Services. Application Services provide a simple interface to the domain and underlying data, they also limit coupling between the consumers of the domain and the domain itself.

抽象“domain”的时候会发现应用服务（Application Services）是一个“门面[^1]”。应用服务（Application Services）提供了面向领域和依赖领域的数据之间的简单接口，其也减少了领域和领域消费方的耦合。

\
On the outside of the Application Server sits some type of Remote Facade. This could be many things\
such as SOAP, custom TCP/IP, XML over HTTP, TomCat, or even a person manually typing messages that arrive tied to the legs of pigeons. The Remote Facade may or may not be abstracted away from its\
underlying technology mechanism depending on the situation and tools that are involved.

在应用服务（Application Services）之外有很多类型的远程接口（Remote Facade），它们可能是类似于SOAP、定制化的TCP/IP协议，XML服务，TomCat服务器，甚至是绑在🕊鸽子腿上的某些手动输入的信息。远程接口（Remote Facade）可能已经也可能没有被抽象出来，这取决于具体的情况。

\
The overall usage of an Application Server to abstract away the data storage of a system and to provide\
a centralized location of business logic has become very popular over the years and at the time of this\
writing is in many circumstances considered to be the default architecture applied to many systems.

应用服务（Application Services）的整体用途是抽象出来系统的数据存储层和内聚业务逻辑（business logic）。这个观点在过去几年变得非常流行，在此刻（原作者写作时）这样的架构已然变成了很多系统的默认架构。

## Client Interaction

\
Interacting with the Application Server there is a / are many client(s). The general interaction of the\
clients can be seen in Figure 2.1

会有单个或者多个客户端与应用服务（Application Services）交互。图2.1展示了了一个普遍的客户端交互示意图。



<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption><p>Figure 2 典型的客户端交互</p></figcaption></figure>

The basic interaction of the client can be described as a DTO (Data Transfer Object) up/down interaction. Going through the lifecycle of an operation is the easiest way to show the functioning of the\
API. A user goes to a screen, perhaps to edit a customer. The client sends a request to the remote\
facade for a DTO representing Customer #id. The Remote facade loads up the domain objects required,\
and maps the domain objects to a DTO that is then returned to the client. An example of DTO in XML format can be seen in Figure 3 but the basic explanation is that the DTO in this stereotypical architecture contains the current state of the object in questions.

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

The client will then display the information received from the Remote Facade on the screen allowing the\
user to interact with it. This is very often done utilizing a view model and/or data binding with the view.\
At some point the user will be complete with the editing of the data on the screen and will through\
some action cause the UI to “Save” the data. Generally this is implemented through a “Save Button”\
although some User Interfaces will instead just have you leave the current data which forces a save.

客户端收到远端响应后会在屏幕上展示用户可以操作的信息。2025年04月08日00:16:20 写到这了



The processing of a Save on the client will take the data that has been edited by the user on the screen,\
pack it back into a DTO (usually identical to the DTO it requested from the Remote Façade for displaying\
to the user ). It will then send this DTO back up to the Application Server.\
The Application Server receiving the DTO will then start a transaction/session, map the DTO back to the\
domain objects, allow the domain objects to verify any changes, then save the changes within the\
domain objects back to the data storage likely through the use of something like an Object Relational\
Mapper that has the ability to distinguish what has changed in the domain objects and update the data\
storage accordingly. The Application Server will return to the client either an Acknowledgement (Ack)\
that the change has been persisted or it will return a series of errors as to why it was unable to persist\
the changes.

[^1]: 指的大概是facade pattern： [https://refactoringguru.cn/design-patterns/facade](https://refactoringguru.cn/design-patterns/facade)
