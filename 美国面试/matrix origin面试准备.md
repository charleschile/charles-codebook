### 1. 自己简历


lab要求由woker主动向调度器发起任务请求，Coordinator按照当前的任务分配进度为每个worker分配map或者reduce任务，reduce任务必须在所有的map任务都完成后才能开始启动

lab requires woker to actively send task requests to the scheduler. Coordinator assigns map or reduce tasks to each worker according to the current task assignment progress. reduce tasks can only be started after all map tasks are completed

Coordinator作为一个rpc server,处理所有从worker发送过来的rpc请求，当然在此也顺便学习了一下go的rpc框架，其实还是挺有意思的，非常方便的消息处理框架，首先我们需要设置好双方进行消息交互的rpc消息格式定义，目前定义如下： option请求定义:

Coordinator, as an rpc server, processes all rpc requests sent from workers. Of course, I also learned the rpc framework of go here, which is actually quite interesting and very convenient message processing framework. First, we need to set the definition of rpc message format for message interaction between two parties



Coordinator接受到worker的请求后，根据请求的消息类型进行回应，如果当前的任务已经完成，则直接回应；如果请求的消息为任务请求，则查看是否存在待处理的map任务，如果存在则分发一个map类型的任务，如果map任务都已经下发但是还未全部完成，则通知worker进行等待；如果所有的map任务都已经下发且已经完成，则分配一个reduce类型的任务交给worker进行处理；如果接受的消息为worker通知任务完成，我们会校验该任务的标识，如果校验通过，我们将相应的任务状态设置为已经完成。
最关键的一点处理，每当Coordinator分配一个任务后，就会启动一个定时器任务，该定时器任务会在10s后检查该任务的状态是否已经完成，如果未完成，则将该任务再次进入到待分配列表中。

After receiving a request from a worker, the Coordinator responds according to the message type of the request. If the current task has been completed, the coordinator responds directly. If the requested message is a task request, check whether there are map tasks to be processed. If so, a map task is distributed. If all map tasks have been delivered but not completed, the worker is notified to wait. If all map tasks have been delivered and completed, a reduce task is assigned to the worker for processing. If the received message is worker notifying that the task is completed, we will check the identity of the task. If the check passes, we will set the corresponding task status to completed.
After a Coordinator assigns a task, a timer task is started. The timer checks whether the task is complete 10 seconds later. If the task is not complete, the timer adds the task to the list.


### 2. 项目要求以及mentor工作
go: https://www.turing.com/interview-questions/golang
aws s3: https://medium.com/double-pointer/top-five-most-asked-aws-s3-interview-questions-in-software-engineering-interviews-7c55e5271f37

### 3. 常问问题
What programming languages are you familiar with?


Describe the last project you worked on including, any obstacles and your contributions to its success.

What are your thoughts on declarative vs. imperative paradigms such as functional and object-oriented programming?

What are your most used design patterns and in what contexts do you use them?

What is “Agile” software development and what are your thoughts on it?

What are your thoughts on software testing?

Describe a difficult bug you were tasked with fixing in a large application. How did you debug the issue?

How do you explain technical challenges to stakeholders who do not have technical knowledge or backgrounds?

What aspect of our company, product or team interests you most?

How do you determine a project’s success?

What were your main responsibilities in your previous job?

When was the last time you were in a crunch? What could have prevented the situation and what changed to avoid it in the future?

Why should we hire you as a software engineer on our team?

What are your favorite software engineering books and why?

How do you work independently and as part of a team? Which do you prefer?

Do you prefer “startup" company environments or in a more established atmosphere? Why?

What are your greatest strengths and weaknesses?

Describe a time you overcame a non-technical obstacle at work.

