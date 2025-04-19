---
description: Task Based User Interface一节的翻译
icon: '2'
---

# 任务导向的接口设计（Task Based User Interface）

This chapter introduces the concept of a Task Based User Interface and compares it with a CRUD style user interface. It also shows the changes that occur within the Application Server when a more task oriented style is applied to it’s API. 
One of the largest problems seen in “A Stereotypical Architecture” was that the intent of the user was lost. Because the client interacted by posting data-centric DTOs back and forth with the Application 
Server, the domain was unable to have any verbs in it. The domain had become a glorified abstraction of 
the data model. There were no behaviors, the behaviors that existed, existed in the client, on pieces of 
paper, or in the heads of the users of the software. 
Many examples of such applications can be cited. Users have “work flow” information documented for 
them. Go to screen xyz edit foo to bar, then go to this other screen and edit xyz to abc. For many types 
of systems this type of workflow is fine. These systems are also generally low value in terms of the 
business. In an area that is sufficiently complex and high enough ROI in order to use Domain Driven 
Design these types of workflows become unwieldy. 
One reason that is commonly cited for wanting to build a system such as described is that “the business 
logic and work flows can be changed at any time to anything without need of a change to the software”. 
While this may be true it  must be asked at what cost. What happens when someone misses a step in 
the process they have in their head or you have multiple users who do it differently as is commonly the 
case? How do you get any reasonable information out of a system in terms of reporting? 
One way of dealing with this issue is to move away from the DTO up/down architecture that was illustrated in a “Stereotypical Architecture”. Figure 1 shows the client interaction side of a DTO up/down architecture. 

12345tu

The basic explanation of the operation is that the UI will request a DTO, say for Customer 1234 from the 
Application Server. This DTO will be returned to the client and then shown on the screen. The user will 
interact with the DTO in some way (likely either directly or through a View Model). Eventually the client 
will click Save or some other trigger will occur and the client will take the DTO and send it back up to the 
Application Server. The Application Server will then internally map the data back to the domain model 
and save the changes returning a success or failure. 
As discussed the intention of the user is being lost because a DTO is being sent up that just represents 
the current state of the object after the client’s actions are completed. It is possible to bring forward the 
intention of the user; this will allow the Application Server to process behaviors as opposed to saving 
data. Figure shows an interaction capturing intent. 

Capturing intent the client interaction is very similar to the DTO up/down methodology in terms of 
interactions. The client first quests a DTO from the Application Server for instance Customer 1234. The 
Application Server returns a DTO representing the customer that is then shown on the screen for the 
user to interact with usually either directly or through a View Model. The similarities however stop at 
this point. 
Instead of simply sending the same DTO back up when the user is completed with their action the client 
needs to send a message to the Application Server telling it to do something. It could be to “Complete a 
Sale”, “Approve a Purchase Order”, “Submit a Loan Application”. Said simply the client needs to send a 
message to the Application Server to have it complete the task that the user would like to complete. By 
telling the Application Server what the user would like to do, it is possible to know the intention of the 
user.

## Commands





