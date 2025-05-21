---
description: Task Based User Interface一节的翻译
icon: '2'
---

# 任务导向的接口设计（Task Based User Interface）

This chapter introduces the concept of a Task Based User Interface and compares it with a CRUD style user interface. It also shows the changes that occur within the Application Server when a more task oriented style is applied to it’s API.

本章介绍任务导向的接口设计的概念，也会将其与CRUD式的接口设计进行比较。本章还会展示当更多的任务导向的接口设计应用起来时，应用服务层的变化，

One of the largest problems seen in “A Stereotypical Architecture” was that the intent of the user was lost. Because the client interacted by posting data-centric DTOs back and forth with the Application Server, the domain was unable to have any verbs in it. The domain had become a glorified abstraction of the data model. There were no behaviors, the behaviors that existed, existed in the client, on pieces of paper, or in the heads of the users of the software.

“典型的架构”中最大的问题之一是用户的意图会丢失。因为客户端与应用服务器的交互方式是来回传递以数据为中心的DTO，因此这样的情况下领域内不包含任何动词。领域变成了一种数据模型的抽象美化，而没有任何行为。行为只存在于客户端，文档中或者是软件使用者的脑海里。

> 狠狠的讽刺呀。

Many examples of such applications can be cited. Users have “work flow” information documented for them. Go to screen xyz edit foo to bar, then go to this other screen and edit xyz to abc. For many types of systems this type of workflow is fine. These systems are also generally low value in terms of the business. In an area that is sufficiently complex and high enough ROI in order to use Domain Driven Design these types of workflows become unwieldy.

可以举例许多这样的案例。用户们有“工作流”文档。在屏幕上将foo编辑成bar，之后去另一个屏幕上将xyz编辑成abc。许多类型的系统中，这样的（分散的）的工作流已经足够了。这些系统普遍上是商业回报低的系统。而在具有足够复杂性且有足够投资回报率（ROI，Return on Investment）的系统中，即适用领域驱动设计的系统中，这样的工作流设计就会变得难以处理。

> 简单的系统不需要引入DDD，简单的设计足矣，而需要长期演进的复杂系统，适合DDD。

One reason that is commonly cited for wanting to build a system such as described is that “the business logic and work flows can be changed at any time to anything without need of a change to the software”. While this may be true it must be asked at what cost. What happens when someone misses a step in the process they have in their head or you have multiple users who do it differently as is commonly the case? How do you get any reasonable information out of a system in terms of reporting?

人们常提到的想要构建一个如上述所描述的系统的一个原因是：“业务逻辑和工作流程可以在任何时候被改变成任何内容，而无需对软件进行修改”。 尽管这可能是真的，但我们必须问问代价是什么，尤其是当某人忘掉流程中的某一步或者各种用户以各种方式使用软件的时候？你怎么保证系统都会得到合理的输出呢？

\
One way of dealing with this issue is to move away from the DTO up/down architecture that was illustrated in a “Stereotypical Architecture”. Figure 1 shows the client interaction side of a DTO up/down architecture.

一个应对该问题的方式是远离“典型的架构”这样的DTO上传和下载的架构，即Figure 1展示的那样。

> 这里再把图1捞出来：<img src=".gitbook/assets/image (1) (1).png" alt="" data-size="original">

<figure><img src=".gitbook/assets/image (2).png" alt=""><figcaption><p>Figure 4 Interaction in a DTO Up/Down Architecture</p></figcaption></figure>

The basic explanation of the operation is that the UI will request a DTO, say for Customer 1234 from the Application Server. This DTO will be returned to the client and then shown on the screen. The user will interact with the DTO in some way (likely either directly or through a View Model). Eventually the client will click Save or some other trigger will occur and the client will take the DTO and send it back up to the Application Server. The Application Server will then internally map the data back to the domain model and save the changes returning a success or failure.

这样的架构的基本解释是UI需要DTO，比如从应用服务器获取顾客1234，DTO就会被返回给客户端，之后展示在屏幕上。用户和DTO以某种方式进行交互（比如直接或间接的操作视图模型（View Model））。最终客户端会点击保存按钮或者使用其他的触发方式，使得客户端将DTO对象返回给应用服务器。应用服务器随后将数据内部映射回域模型并保存更改，且返回成功或失败的结果。

\
As discussed the intention of the user is being lost because a DTO is being sent up that just represents the current state of the object after the client’s actions are completed. It is possible to bring forward the intention of the user; this will allow the Application Server to process behaviors as opposed to saving data. Figure shows an interaction capturing intent.

之前已经讨论过用户意图丢失的问题，其原因在于DTO对象只能代表客户端行为完成后的状态。我们可以提前获取用户意图，这使得应用服务器可以处理行为而非直接保存数据。图片展示了获取用户意图的交互过程。



<figure><img src=".gitbook/assets/image (1).png" alt=""><figcaption><p>Figure 5 Behavioral Interface</p></figcaption></figure>

Capturing intent the client interaction is very similar to the DTO up/down methodology in terms of interactions. The client first quests a DTO from the Application Server for instance Customer 1234. The Application Server returns a DTO representing the customer that is then shown on the screen for the user to interact with usually either directly or through a View Model. The similarities however stop at this point.

就交互方式而言，获取用户意图与直接DTO上传下载的方法很相似。客户端首先从应用服务器为顾客1234获取一个DTO对象，应用服务器返回一个DTO供用户直接或间接的通过视图模型交互。之后的过程就不相同了。

\
Instead of simply sending the same DTO back up when the user is completed with their action the client needs to send a message to the Application Server telling it to **do something**. It could be to “Complete a Sale”, “Approve a Purchase Order”, “Submit a Loan Application”. Said simply the client needs to send a message to the Application Server to have it complete the task that the user would like to complete. By telling the Application Server what the user would like to do, it is possible to know the intention of the user.

当用户完成某些行为时，客户端需要向应用服务器发送消息让应用服务器**做某些事情**，而不是简单的发送相同的DTO回去，比如说：“完成交易”，“同意购买的订单”，“提交贷款申请”。说的简单点，客户端需要发送给应用服务器的消息包含用户想要完成的任务。通过告诉应用服务器用户想要干什么，才可能知道用户的意图。

## 命令 Commands

The method through which the Application Server will be told what to do is through the use of a Command. A command is a simple object with a name of an operation and the data required to perform that operation. Many think of Commands as being Serializable Method Calls. Listing 1 includes the code of a basic command.

告知应用服务器需要干什么是通过命令（Command）。命令是一个简单的拥有操作名和执行操作所需数据的对象。许多人认为命令是可序列化的方法调用。下面的List 1是一个基础命令的代码。

{% code title="Listing 1 A Simple Command" lineNumbers="true" fullWidth="true" %}
```java
public class DeactivateInventoryItemCommand {
 public readonly Guid InventoryItemId;
 public readononly string Comment;
 public DeactivateInventoryItemCommand (Guid id, string comment) {
 InventoryItemId = id;
 Comment = comment;
}
```
{% endcode %}

As a side note the example in Listing 1 includes the pattern name after the name of the Command. This is a decision that has many positives and negatives both linguistically and operationally. The choice over whether to use a pattern name in a class name is one that should not be taken lightly by a development team.

顺便提一下，在Listing 1中，在命令名称（Command）后包含了模式名称。这样的做法在语言和操作层面上均有正面或者负面的影响。是否在类中使用模式名称对开发团队来说不应该轻率的做出选择。

> 个人理解：命令名称指的是具体操作，比如：`DeactivateInventoryItem` ，模式名称指的是`Command` 。`Command`是模式名称的原因是一共有两种模式C（Command）和Q（Query）。



One important aspect of Commands is that they are always in the imperative tense; that is they are telling the Application Server to do something. The linguistics with Commands are important. A situation could for with a disconnected client where something has already happened such as a sale and could want to send up a “SaleOccurred” Command object. When analyzing this, is the domain allowed to say no that this thing did not happen? Placing Commands in the imperative tense linguistically shows that the Application Server is allowed to reject the Command, if it were not allowed to, it would be an Event for more information on this see “Events”.

Commands的一个重要的方面是它们经常是祈使语气，即它们告诉、要求应用服务器完成某些事情。命令的语言非常重要。存在这么一种场景，断连的服务器在某些事比如售卖已经发生的时候向发送“售卖”（SaleOccurred）命令。领域（domain）是否允许拒绝没有发生的事情呢？命令（Commands）使用祈使语气表明应用服务器可以拒绝命令（Commands），在不允许执行的情况下，则其就是一个事件（Event），更多信息可以参考“Events”。



Occasionally there exist funny examples of language in English. A perfect example of this would be\
“Purchase” which can be used either as a verb in the imperative or as a noun describing the result of its usage in the imperative. When dealing with these situations, ensure that the concept being pushed forward represents the imperative of the verb and not the noun. As an example a purchase should be including what to purchase and expecting the domain to possibly fill in some information like when the item was purchased as opposed to sending up a purchase DTO that fully describes the purchase.

英语中存在一些有趣的语言例子。一个完美的例子是“Purchase”，这个单次可以用作祈使语句的动词，也可以用作描述结果的名词。当处理这种既有动词语义也有名词语义的场景时，保证传递的是动词的语义而非名词的语义。还是刚才那个例子，一次”purchase“应该明确要购买的东西的信息并期望领域填充的部分可能的信息，比如商品购买的时间，而不是发送详细描述购买过程的DTO（名词形式的DTO）。

> 作者强调在软件设计中应遵循"**动词优先于名词**"的原则、且**避免过度封装（**&#x5BA2;户端只需提供必要参数（如商品信息），系统自动生成元数据（如时间戳））。

\
The simple Command in Listing 1 includes two data properties. It includes an Id which represents the InventoryItem it will apply to and it includes a comment as to why the item is being deactivated. The comment is quite typical of an attribute associated with a Command, it is a piece of data that is required in order to process the behavior. There should only exist on a Command data points that are required to process the given behavior. This contrasts greatly with the typical architecture where the entire data of the object is passed back to the Application Server.

Listing 1 中的A Simple Command包含两个数据属性：包含一个id，用于代表其操作的目标，包含一个评论（comments）属性，用于表明为什么目标要被这么操作。评论是一个典型的与Command关联的属性，其是处理相关行为所需的一块数据。在一个命令中，应该只存在处理给定行为所需要的数据。这与典型的架构有很大的不同，在典型架构中，对象的全部数据都会被传递回应用服务器。

Most importantly of the data is the Id of the associated inventory item. At least one Id must exist for all\
commands that are updating state in some way, as all commands are intended to be routed to an\
object. When issuing a Create Command it is not necessary though valuable to include an Id. Having the\
client originate Ids normally in the form of UUIDs is extremely valuable in distributed systems.

数据中最重要的是其关联的事项的ID。所有以某种方式更新状态的命令都必须至少有一个 ID，因为所有命令都旨在路由到某个对象。当处理创建命令的时候，ID不是必须的。在分布式系统中，客户端正常的以UUID的形式发起命令是非常有价值的。

It is quite common for developers to learn about Commands and to very quickly start creating Commands using vocabulary familiar to them such as “ChangeAddress”, “CreateUser”, or “DeleteClass”. This should be avoided as a default. Instead a team should be focused on what the use case really is.

开发人员学习命令后，通常会很快地使用他们熟悉的词汇（例如“修改地址（ChangeAddress）”、“CreateUser”或“DeleteClass”）来创建命令。应该避免这种情况，相反，团队应该专注于用例的真正含义。

\
Is it “ChangeAddress”? Is there a difference between “Correcting an Address” and “Relocating the\
Customer”? It likely will be if the domain in question is for a telephone company that sends the yellow\
pages to a customer when they move to a new location.

确定是“修改地址（ChangeAddress）”吗？“更正地址（Correcting an Address）” 和 “重定位用户（Relocating the Customer）”之间是否有区别？当问题的领域涉及到电话销售公司时，两者确实存在区别：当顾客搬家到新位置时，电话销售公司会向顾客发送新的黄页。

\
Is it “CreateUser” or is it “RegisterUser”? “DeleteClass” or “DeregisterStudent”. This process in naming\
can lead to great amounts of domain insight. To begin defining Commands, the best place to begin is in\
defining use cases, as generally a Command and a use case align.

是“创建用户”还是“登记用户”？“删除班级”还是“注销学生”。其处理过程的名字会带来巨量的领域洞察。在开始定义Commands之前，最好的方式是定义用例，因为Command通常和用例是保持一致的。

It is also important to note that sometimes the only use case that exists for a portion of data is to\
“create”, “edit”, “update”, “change”, or “delete” it. All applications carry information that is simply\
supporting information. It is important though to not fall into the trap of mistaking places where there\
are use cases associated with intent for these CRUD only places.

2025年05月21日13:53:33 到这里了

\
Commands as a concept are not difficult but are different for many developers. Many developers see\
the creation of the Commands as a lot of work. If the creation of Commands is a bottleneck in the\
workflow, many of the ideas being discussed are likely being applied in an incorrect location.

\


## User Interface



In order to build up Commands the User Interface will generally work a bit differently than in a DTO\
up/down system. Because the UI must build Command objects it needs to be designed in such a way\
that the user intent can be derived from the actions of the user.



\
The way to solve this is to lean more towards a “Task Based User Interface” also known as an “Inductive\
User Interface” in the Microsoft world. This style of UI is not by any means new and offers a quite\
different perspective on the design of user interfaces. Microsoft identified three major problems with\
Deductive UIs when researching Inductive UIs.





**Users don't seem to construct an adequate mental model of the product.** The interface design for most\
current software products assumes that users will understand a conceptual model that the designers\
carefully crafted. Unfortunately, most users don't seem to ever acquire a mental model that is thorough\
and accurate enough to guide their navigation. These users aren't dumb — they are just very busy and\
overloaded with information. They do not have the time, energy, or desire to wonder about a conceptual\
model for their software.

\
**Even many long-time users never master common procedures.** Designers know that new users may\
have trouble at first, but expect these problems to vanish as users learn common tasks. Usability data\
indicates this often doesn't happen. In one study, researchers set up automated equipment to videotape\
users at home. The tapes showed that users focusing on the task at hand do not necessarily notice the\
procedure they are following and do not learn from the experience. The next time users perform the\
same operation, they may stumble through it in exactly the same way.



**Users must work hard to figure out each feature or screen.** Most software products are designed for\
(the few) users who understand its conceptual model and have mastered common procedures. For the\
majority of customers, each feature or procedure is a frustrating, unwanted puzzle. Users might assume\
these puzzles are an unavoidable cost of using computers, but they would certainly be happier without\
this burden. (Microsoft Corporation, 2001)



The basic idea behind a Task Based or Inductive UI is that its important to figure out how the users want\
to use the software and to make it guide them through those processes.

\
Many commercial software applications include user interfaces in which a screen presents a set of\
controls, but leaves it to the user to deduce the page's purpose and how to use the controls to\
accomplish that purpose. (Microsoft Corporation, 2001)

\
The goal is to guide the user through the process. An example of the differences can be seen in the\
DeactivateInventoryItem example previously shown. A typical deductive UI might have an editable data\
grid containing all of the inventory items. It would have editable fields for various data and perhaps a\
drop down for the status of the inventory item, deactivated being one of them. In order to deactivate an\
inventory item the user would have to go to the item in the grid, type in a comment as to why they were\
deactivating it and then change the drop down to the status of deactivated. A similar example could be\
where you click to a screen to edit an inventory item but go through the same process as seen in Figure\
3\.

<figure><img src=".gitbook/assets/image.png" alt=""><figcaption><p>Figure 6 A CRUD screen for an Inventory Item</p></figcaption></figure>



