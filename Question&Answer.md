# Concurrency Notes - *Question-and-Answer style*
## Part-One Basic Knowledge

### Chapter-One Concurrency Profile ###
### Question 1:why should we need to use the concurrency in our program?Or can you illustrate the utilize of the concurrency?
### Answer ###
*1、使用线程可以发挥处理器的功能，提升系统吞吐率。表现为提高多处理器的资源利用率，单处理器减少空闲时刻（指IO等待阶段）*<br>
*2、分解复杂且异步的工作流，形成一组简单且同步的工作流，使用框架或其他技术在特定位置进行交互即可*<br>
*3、单线程为了接收多个数据或请求而不受阻塞影响，必须使用非阻塞IO，这将耗费大量资源。而使用多线程则可以使用阻塞IO，其他非阻塞的线程正常接收数据或请求*<br>
*4、线程可帮助实现响应更快的用户图形界面（GUI，Graphic User Interface）。将长时间运行的任务放在单独的执行线程中，让事件线程可以及时处理界面事件。*<br>
### Knowledge Involved ###
*现在主流的GUI框架仍然是单线程子系统的，之所以不使用多线程框架，是因为多线程GUI容易受到资源竞争、死锁等条件导致的稳定性限制，因此一系列开发尝试均告失败。单线程的GUI框架使用事件分发线程（EDT，Event Dispatch Thread）来响应操作的触发。*<br>

### Question 2:what risks should we be careful about utilize threads?
### Answer ###
*1、线程安全性问题，包括线程执行顺序，线程间使用的共享变量，线程可能存在的执行结果*<br>
*2、线程活跃性问题，即线程是否能最终正确执行完成，以免收到死锁，资源竞争等情况的影响*<br>
*3、线程性能问题，线程的调度（即切换）会使得频繁出现保存和恢复执行上下文操作，带来开销。同时使用同步机制来管理共享变量时将增加额外开销*<br>

### Chapter-Two Thread-Safety ###
### Question 3:how can we explain the thread-safety?And what principles we conform can ensure these safety?
### Answer ###
*1、当多个线程访问某个类时，不管运行时环境采用哪种调度方式或者这些线程将如何交替执行，并且在主调代码中不需要任何额外的同步或协同。这个类能表现出正确的行为，那就称这个类是线程安全的*<br>
*2、保证线程安全性的三种方式：不在线程之间共享可变状态变量、将状态变量修改为不可变的变量、在访问状态变量时使用同步。要编写正确的并发程序，关键在于在访问共享的可变状态时需要进行正确的管理*<br>
### Knowledge Involved ###
*有状态对象具备数据存储功能，而无状态对象就是一次操作，没有数据存储功能，因此无状态对象是绝对线程安全的*<br>

### Question 4:what are the characters about thread-safety we should be careful?
### Answer ###
*1、操作的原子性，复合操作和简单操作可能包含多个独立的操作，在多线程环境下，是否按照设想的操作顺序执行将产生竞态条件*<br>
*2、加锁机制，通过锁来保证复合操作的原子性，保护共享变量状态在某刻只有一个线程可以访问修改*<br>
*3、活跃与性能，多线程环境使用同步应注意性能的影响，在必须保证安全性的前提下，权衡简单性和性能*<br>
### Knowledge Involved ###
*竞态条件，即多线程下计算的正确性依赖于恰当的执行时序*<br>
*实现复合操作的原子性除了同步，还可以使用java.util.concurrent.atomic提供的原子类*<br>
*锁的可重入性表现为，锁是针对线程的，如果某个线程视图获取一个已由它自己所持有的锁，这个请求将成功，即内置锁可重入*<br>
*执行时间长的操作或者可能无法正常完成的操作一定不能持有锁*<br>

### Chapter-Three Object Sharing ###
### Question 5:how can we ensure the thread-safety when more than one threads are utilizing the shared object?
### Answer ###
*1、确保共享对象的可见性，多个线程之间可以直接观察到共享变量做出的修改，保证获取的值不为失效数据。可采用加锁或者volatile修饰变量*<br>
### Knowledge InVolved ###
*重排序，编译器和处理器可能对操作的执行顺序进行调整，将变量缓存在寄存器或其他地方，以优化操作执行。若使用同步或者volatile修饰变量，编译器将不会将这个变量相关操作进行重排序*<br>
*最低安全性，线程在没有同步的情况下获取的变量值，至少是之前某个线程设置的值，而不是一个随机值，这种安全性称为最低安全性。这种安全性适用于绝大多数变量，除了非volitile类型的64位数值变量（double和long），因为JVM将64位数据分成两个32位操作，最低安全性可能导致取得不同值的高位和低位*<br>
**

### Question 6:what principles should we conform to utilize volatile precisely for describing variables?
### Answer ###
*1、对变量的写入操作不依赖于变量的当前状态，或能保证只有单个线程更新变量的值*
*2、该变量不会与其他状态变量一起纳入不变性条件中*
*3、在访问变量时不需要加锁*
