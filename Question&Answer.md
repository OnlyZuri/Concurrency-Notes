# Concurrency Notes - *Question-and-Answer style*
## Part-One Basic Knowledge

### Question 1:why should we need to use the concurrency in our program?Or can you illustrate the utilize of the concurrency?
### Answer ###
*1、使用线程可以发挥处理器的功能，提升系统吞吐率。表现为提高多处理器的资源利用率，单处理器减少空闲时刻（指IO等待阶段）*
*2、分解复杂且异步的工作流，形成一组简单且同步的工作流，使用框架或其他技术在特定位置进行交互即可*
*3、单线程为了接收多个数据或请求而不受阻塞影响，必须使用非阻塞IO，这将耗费大量资源。而使用多线程则可以使用同步IO，其他非阻塞的线程正常接收数据或请求*
*4、线程可帮助实现响应更快的用户图形界面（GUI，Graphic User Interface）。将长时间运行的任务放在单独的执行线程中，让事件线程可以及时处理界面事件。*
### Knowledge Involved ###
*现在主流的GUI框架仍然是单线程子系统的，之所以不使用多线程框架，是因为多线程GUI容易受到资源竞争、死锁等条件导致的稳定性限制，因此一系列开发尝试均告失败。单线程的GUI框架使用事件分发线程（EDT，Event Dispatch Thread）来响应操作的触发。*

### Question 2:what risks should we be careful about utilize threads?
### Answer ###
*1、线程安全性问题，包括线程执行顺序，线程间使用的共享变量，线程可能存在的执行结果*
*2、线程活跃性问题，即线程是否能最终正确执行完成，以免收到死锁，资源竞争等情况的影响*
*3、线程性能问题，线程的调度（即切换）会使得频繁出现保存和恢复执行上下文操作，带来开销。同时使用同步机制来管理共享变量时将增加额外开销*

### Question 3:how can we explain the thread-safety?And what principles we conform can ensure these safety?
### Answer ###
*1、*