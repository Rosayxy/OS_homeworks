# OS 第八次作业
于新雨 计25 2022010841
## 学习文档
参考[linux docs](https://docs.kernel.org/scheduler/sched-eevdf.html)    
EEVDF 的调度算法目的是将 cpu time 平等地分配给具有相同优先级的所有可运行任务   
它为每个任务分配一个虚拟运行时间，创建一个 lag 值，用于确定任务是否获得了公平份额的CPU时间。这样，具有正 lag 值的任务欠缺CPU时间，而负滞后值意味着任务已超出其份额    

EEVDF选择滞后值大于或等于零的任务，并为每个任务计算虚拟截止时间(VD)，选择具有最早VD的任务执行这允许对具有较短时间片的 latency-sensitive 任务进行优先处理，提高它们的响应性，这个设计的合理性在于部分任务会对延迟有较高的要求，比如是一些和用户交互的进程。以及如果任务的VD较早，它可以抢占其他任务，并且任务可以使用新的 sched_setattr() 系统调用请求特定的时间片，这进一步方便了 latency-sensitive 应用程序的工作。   

以及当任务睡眠时，它仍保留在运行队列中但标记为 "deferred dequeue" ，允许其 lag 随VRT衰减。因此，长时间 sleep 的任务的 lag 会被 reset 掉，以及需要防止任务通过短暂的 sleep 来 reset lag    
