### Part-Two StructuredConcurrentProgram
### Chapter-Six MissionExecution
### Question 1:what principles should we care for when mission was executed at thread?
### Answer
*1、应用程序希望能支持尽可能多的用户，而用户希望能获得尽快的响应。且当服务其负荷过载时，应用程序的性能应该是逐渐降低而不是直接失败的。为了实现以上的目标，就必须要有一个清晰的任务边界和合理的任务执行策略。以下为常见的任务执行策略。*<br>
*2、串行地执行任务，即在单个线程中串行执行任务，其他请求会长时间等待直到线程处理完任务，服务器响应糟糕*<br>
*3、为每个任务创建一个线程，仅适用于请求速率小于服务器的处理速率时，能带来更快的响应和更高的吞吐量。但是有几个很明显的缺陷：*<br>
  *（1）、线程生命周期的开销非常高，包括线程的创建与销毁都需要一定的代价，消耗一定的计算机资源。当请求到达率很高且处理过程是轻量级的时候，会很快消耗完计算机资源。*<br>
  *（2）、消耗计算机资源，活跃的线程对于计算机资源尤其是内存的消耗极大。如果可运行的线程数量大于可用的处理器数量，那么部分线程将会被闲置，进一步导致大量空闲的线程得不到运行但又占用大量内存。除此之外，垃圾回收器以及竞争CPU资源等都会带来额外的资源消耗。*<br>
  *（3）、稳定性，可创建线程的数量是受到一定的限制的，包括JVM的启动参数、Thread构造函数请求栈的大小以及底层操作系统对线程的限制等。破除这些限制将导致OutOfMemoryError。*
  
### Question 2:how to utilize the Executor framework?
### Answer
*1、为每个任务分配一个线程是不实际的，因此Java提供了一种解决方案——Executor框架。将任务执行抽象不定为Thread，而是Executor来进行管理。它可支持多种任务执行策略，能将任务提交和任务执行过程有效解耦，并提供对于线程生命周期管理、统计信息手机、性能监视等机制。Executor框架基于生产者-消费者模式，提交任务的操作相当于生产者，执行人物的线程相当于消费者。*
```java
public interface Executor{
  void execute(Runnable command);
}
```
*2、Executor可按多种任务执行策略进行管理任务的执行，如下：*<br>
```java
// 基于线程池的有界任务线程
private final Executor exec = Executor.newFixedThreadPool(100);
ServerSocket socket = new ServerSocket(80);
while(true){
  final Socket connncetion = socket.accept();
  Runnable task = new Runnable(){ //run（）方法实现 };
  exec.execute(task);
}

// 为每个任务都分配线程
public class PerTaskExecutor implements Executor{
  public void execute(Runnable r){
    new Thread(r).start();
  }
}

// 串行执行任务
public class SerialExecutor implements Executor{
  public void execute(Runnable r){
    r.run();
  }
}
```

### Question 3:what thread-pool should we know and helping finish concurrent program?
### Answer
*1、线程池通过重用已有线程，减少了创建新线程所带来的资源消耗，任务提交也无须等待新线程创建完毕，提高了响应，避免了内存耗尽。*<br>
*2、Executor的工厂法提供了多种线程池的创建，其特征如下:*<br>
  *（1）、newFixedThreadPool，创建固定数量的线程池，若某个线程因为未预期的Exception而结束，则会补充一个新的线程*<br>
  *（2）、newCacheThreadPool，创建可缓存的线程池，当处理需求规模小于线程规模时，将回收空闲线程，当需求大于线程规模时，将创建新线程，线程池规模不存在任何限制（小心内存耗尽）*<br>
  *（3）、newSingleThreadExecutor，创建单线程池的Executor，即串行执行任务，可保证按照顺序来执行（FIFO，优先级等限制）。当前线程异常结束时会生成新线程替代。*<br>
  *（4）、newScheduledThreadPool，创建固定数量的线程池，但可以用延迟或定时的方式来执行任务*<br>
  