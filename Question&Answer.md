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
*1、竞态条件，即多线程下计算的正确性依赖于恰当的执行时序*<br>
*2、实现复合操作的原子性除了同步，还可以使用java.util.concurrent.atomic提供的原子类*<br>
*3、锁的可重入性表现为，锁是针对线程的，如果某个线程视图获取一个已由它自己所持有的锁，这个请求将成功，即内置锁可重入*<br>
*4、执行时间长的操作或者可能无法正常完成的操作一定不能持有锁*<br>

### Chapter-Three Object Sharing ###
### Question 5:how can we ensure the thread-safety when more than one threads are utilizing the shared object?
### Answer ###
*1、确保共享对象的可见性，多个线程之间可以直接观察到共享变量做出的修改，保证获取的值不为失效数据。可采用加锁或者volatile修饰变量*<br>
### Knowledge InVolved ###
*1、重排序，编译器和处理器可能对操作的执行顺序进行调整，将变量缓存在寄存器或其他地方，以优化操作执行。若使用同步或者volatile修饰变量，编译器将不会将这个变量相关操作进行重排序*<br>
*2、最低安全性，线程在没有同步的情况下获取的变量值，至少是之前某个线程设置的值，而不是一个随机值，这种安全性称为最低安全性。这种安全性适用于绝大多数变量，除了非volitile类型的64位数值变量（double和long），因为JVM将64位数据分成两个32位操作，最低安全性可能导致取得不同值的高位和低位*<br>

### Question 6:what principles should we conform to utilize volatile precisely for describing variables?
### Answer ###
*1、对变量的写入操作不依赖于变量的当前状态，或能保证只有单个线程更新变量的值*<br>
*2、该变量不会与其他状态变量一起纳入不变性条件中*<br>
*3、在访问变量时不需要加锁*<br>

### Qusetion 7:what are the concept about publish and escape?
### Answer ###
*1、发布一个对象是指使对象能够在当前作用域之外的代码中使用*<br>
*2、逸出是某个不该被发布的对象被错误的发布的现象*<br>
### Knowledge Involved ###
*1、常见的逸出现象——this引用逸出，即在构造函数中将this引用逸出，常见形式为在构造函数中创建并启动一个线程，或者调用一个可以改写的实例方法，都会使this引用错误的逸出，直到构造函数返回时才能保证后续的线程安全性*<br>

### Question 8:how can we explain what thread confinement is?And how many methods can we realize the thread confinement?
### Answer ###
*1、线程封闭是实现线程安全性的最简单方式之一，就是不使用共享数据。即仅在单线程内访问数据，避免使用同步*<br>
*2、线程封闭实现有三种方式：*<br>
  *（1）、ad-hoc线程封闭，维护线程封闭的职责完全由程序实现来承担，如只有单线程对volatile变量执行写入操作。此类封闭较脆弱，一般不使用*<br>
  *（2）、栈封闭，简单来说就是方法内的局部变量，只属于线程自己的栈内，其他线程无法访问到*<br>
  *（3）、ThreadLocal封闭，为每个线程建立独立的数据副本，线程只能访问和使用自己的那份数据*<br>
### Knowledge Involved ###
*1、常见的使用线程封闭的例子有Swing和JDBC。Swing将组件和数据模型等封闭在事件分发线程中，其他线程不能访问这些对象；JDBC的连接保存到ThreadLocal对象中，每个线程拥有自己的连接*<br>
*2、ThreadLocal常见的可用于连接管理，session管理*

### Question 9:what is the immutable object?And how can it help us to satisfy the synchronization requirements?
### Answer ###
*1、如果一个对象被创建后，其状态就不能被改变，那么称它为不可变对象，它必定是线程安全的。*<br>
 *（1）、对象创建后其状态就不能被修改*<br>
 *（2）、对象的所有域都是final类型（非必要条件）*<br>
 *（3）、对象是正确创建的，即this引用未逸出*<br>
*2、使用volatile来发布不可变对象可实现一种弱形式的原子性，即对象是不可变的且被volatile修饰，需要进行修改时直接使用重新创建一个新的对象*<br>

### Qusetion 10:how to publish object safely?what class can we utilize to publish our object?
### Answer ###
*1、要安全的发布一个对象，对象的引用和对象的状态必须同时对其他线程可见。一个正确创建的对象可以通过以下方式进行安全地分布：*<br>
 *（1）、在静态初始化函数中初始化一个对象引用*<br>
 *（2）、将对象的引用保存到volatile或者AtomicReference对象中*<br>
 *（3）、将对象的引用保存到某个正确创建的对象的final域中*<br>
 *（4）、将对象的引用保存到由锁保护的域中*<br>
*2、线程安全库中的部分容器类，可用于安全发布*<br>
 *（1）、键-值对类：SynchronizedMap、ConcurrentMap、HashTable*<br>
 *（2）、数组类：Vector、SynchronizedList、SynchronizedSet、CopyOnWriteArrayList、CopyOnWriteArraySet*<br>
 *（3）、队列类：BlockingQueue、ConcurrentLinkedQueue*<br>
### Knowledge Involved ###
*1、如果对象从技术上来看是可变的，但其状态在发布后不会再改变，则称之为事实不可变对象。在没有额外的同步的情况下，任何线程都可以安全的使用被安全发布的事实不可变对象*<br>

### Question 11:why we can say that the object publishing requirement is depending on its variability?
### Answer ###
*1、不可变对象可以通过任何机制发布*<br>
*2、事实不可变对象必须通过安全发布*<br>
*3、可变对象除了需要安全发布，还必须是线程安全的或者由某个锁保护起来*

### Chapter-Four Object Combination ###
### Question 12:what is the instance confinement?And can you illustrate the java monitor model?
### Answer
*1、当对象被封装到另一个对象中时，能访问到被封装的对象的所有代码路径都是已知的，称之为实例封闭*<br>
*2、将对象的所有状态都封装起来，并由自己的内置锁来保护的模式称之为Java监视器模式*<br>
### Knowledge Involved
*1、常见的Java监视器模型可以参照车辆追踪，由内部锁来控制视图线程和更新操作的线程*<br>
*2、collections.unmodifableMap(Map<K, V> arg)可以返回一个以arg为参数的不可变的Map副本，相当于返回一个只读的Map（调用map的put/add/remove方法将会出错），当原生Map被修改时也会跟着被修改。但是若值类型是可变类型，那么是可以修改值的。使用unmodifableMap可以实现类似车辆追踪的实时反馈*

### Qusetion 13:what is the thread safety delegation?
### Answer 
*1、线程安全委托即将线程的安全性委托给一些既定已知线程安全的对象状态来负责。*<br>
*2、如果一个类是由多个独立且线程安全的状态变量组成的，并且所有的操作中都不包含无效状态转换，那么可以将线程安全性委托给底层的状态变量。但是如果类中包含多种状态变量的复合操作，则需要另外加锁保证其原子性*<br>

### Question 14:how can we add new functions at existing thread-safety classes?
### Answebrr
*1、除开两种不常使用或难以实现的方法（修改原始的类、继承实现扩展这个类）之外，更常用的为现有线程安全类添加新功能模块的方式是将扩展代码放入一个客户端方法中，客户端对所使用的对象进行加锁，加锁对象应该为所使用的对象，而不是客户端对象本身*<br>
*2、另一个更好的方法是组合，不采用在客户端上给对象加锁的形式，而是将对象封装到另一个类中，在该类中可以轻松实现同步，再提供访问途径，类似synchronizedList*<br>

### Chapter-Five Basic Construction Module
### Question 15:what is the synchronization container class?And what issues should we care for these classes?
### Answer
*1、常见的同步容器类包括Vector和HashTable，以及其他的同样由collections.synchronizedXxx等工厂方法创建的容器类。它们的共同特点是将自己的状态封装起来，对每个公有方法都进行同步，使得每次只有一个线程可以访问容器的状态。值得注意的是，这虽然保证了线程安全性，但是却极大的削减了并发性能*<br>
*2、同步容器类虽然定义了许多同步的原子方法，但进行组合成复合操作时仍然需要进行客户端加锁。其中，最需要注意的是容器类的迭代。当其他线程修改容器时，将会对容器的大小内容进行修改，此时迭代易得到失效值或超出容器下标界限ArrayIndexOutOfBoundsException等，依旧需要采用客户端加锁来保障迭代的正确进行。*<br>
*3、同步容器类在迭代的过程中进行修改将会抛出并发修改错误ConcurrentModificationException。容器类迭代的标准方式都是使用Iterator，在容器类中将使用计数器对容器的修改进行感知和及时报错。一般的处理方式是对容器迭代过程进行加锁，但这种方式在容器内容多的情况下易造成长时间占用或死锁；另一中处理方式是进行复制，在副本上进行迭代，这种方式在容器内容多的情况下复制将存在显著的性能消耗。*<br>
*4、除了常见的修改删除等方式，在同步容器的一些方式中存在着隐藏的迭代过程，如toString()，hashCode()，equals（）等方法，以及containsAll，removeAll，retainAll等方式都会隐式的进行迭代，都有可能抛出ConcurrentModificationException*

### Question 16:what is the concurrent container classes?And what common classes we could utilize to satisfy high concurrency?
### Answer
*1、并发容器是在JDK5.0之后提出的，用于替代同步容器类，提供较好的并发性能的容器，针对多线程并发访问设计。*<br>
*2、常见的并发容器替换同步容器如下：（具体内容等实践过来进行总结）*<br>
 *（1）、ConcurrentHashMap替换Map*<br>
 *（2）、CopyOnWriteArrayList/Set替换List/Set*<br>
 *（3）、ConcurrentSkipListMap/Set替换SortedMap和SortedSet*<br>
 *（4）、新增Queue和BlockingQueue，前者为队列，有ConcurrentLinkedQueue和PriorityQueue（非并发）两种实现方式，后者相较于前者多了可阻塞的获取和插入等操作*

### Question 17:how the concurrentHashMap and copyOnWriteArrayList help us to satisfy high concurrency?
### Answer
*1、ConcurrentHashMap不采用给所有方法加同步锁，而是采用更细的加锁机制，即分段锁来实现更大程度的共享。在这种机制下，执行读取操作的线程和执行写入操作的线程可以并发的访问Map，并且一定数量的写入线程可以并发的修改Map。*<br>
*2、ConcurrentHashMap和其他并发容器一样，提供弱一致性的迭代器。迭代器在创建时会遍历已有元素，在迭代过程中不会抛出ConcurrentModificationException，在迭代完成再将修改提交给容器。这种机制提高了并发性能，但也使size（）和isEmpty（）等方法变成不准确*<br>
*3、因为其特性，ConcurrentHashMap不支持客户端加锁独占访问，因此独占的情况下考虑使用其他类。ConcurrentHashMap中提供了诸如putIfAbsent（）等复合操作，若需要实现新的功能，可以考虑使用其接口ConcurrentMap*<br>
*4、CopyOnWrite类容器是读写分离的，并发的读不受限制，在写入操作时会生成一个新的副本，修改完成之后容器指向副本元素地址。与ConcurrentHashMap相同的是迭代过程中将直接使用原容器，因此迭代过程进行修改并不会抛出ConcurrentModificationException*

### Question 18:how can we utilize BlockingQueue by producer-consumer model?
### Answer 
*1、阻塞队列BlockingQueue提供了可阻塞的put和take方法，以及支持定时可返回失败状态的offer和poll方法。通过设计生产者与消费者线程的比例来实现更高的资源使用率，灵活使用有界队列来防止生产者耗尽内存，采用非阻塞持久等待的offer等方法来处理负荷过载的情况*<br>
*2、阻塞队列能够更好的构建资源管理机制，其实现方式有LinkedBlockingQueue和ArrayBlockingQueue，均为FIFO队列，与linkedList和ArrayList相类似但拥有更好的并发性能。另一种实现方式是PriorityBlockingQueue，按优先级排序的队列。*<br>
*3、最后一种是特殊的实现方式，同步队列SynchronousQueue。它本质上并不属于一个真正的队列，它并没有为元素维护存储空间，而是维护一组线程，由这组线程来直接将元素移入或移出，减去了存于数组再从数组中取出的过程。因为它没有存储功能，因此put和take方法将一直阻塞，直到有另一个线程准备好参与交付过程。仅当有足够多的消费者，且始终有一个消费者准备好获取交付的工作时才适合用同步队列*
### Knowledge Involved
*1、生产者消费者模式能简化开发过程，将消除两者的代码依赖性，使生产数据的过程和处理数据的过程解耦以简化工作负载（这两个过程处理速率不一致）*

### Question 19:how can we know about serial thread confinement?
### Answer
*1、线程封闭对象只能为单个线程所占有，但可以通过安全地发布该对象来转移所有权。在转移后，只有一个线程能获得此对象，之前的线程不再具有访问权，即通过串行的方式传递线程封闭对象。*<br>
*2、线程池也利用了串行线程封闭技术，在线程间安全地传递封闭对象*

### Question 20:what about the deque?And what model it worked at?
### Answer
*1、JAVA 6新增了双端队列的两种容器类，Deque和BlockingDeque，用于实现在队首队尾的高效率插入移除。双端队列能更好的应用于工作密取模式（work stealing）*<br>
*2、工作密取类似但又区别于生产者-消费者模式。在此模式中，每个消费者都拥有自己的工作队列（生-消模式下消费者共享一个工作队列）。当消费者完成自己工作队列上的所有任务时，会从其他消费者工作队列的尾端获取新工作，以保障每个消费者都处于忙碌状态*

### Question 21:how to understand the block and interrupt?
### Answer
*1、线程可以被阻塞或暂停执行，当线程被阻塞时，会被挂为阻塞状态（BLOCKED，WAITING，TIMED_WAITING）。与之相联系的是InterruptException中断异常，当方法会抛出中断异常时，则表明该方法是一个阻塞方法，当其被中断时，它将努力提前结束阻塞状态*<br>
*2、Thread类提供了interrupt方法，用于中断线程或查询线程是否已经被中断。线程利用一个布尔值来表示其中断状态。需要注意的是，中断是一种协作机制，一个线程并不能强制要求另一个线程中断其正在执行的某个操作。当A中断B时，A仅仅是要求B在执行到可暂停的位置去停止正在执行的操作，前提还是B同意被暂停。*<br>
*3、当方法会抛出InterruptException异常时，则说明其为阻塞方法，则必须处理对中断的响应。常见的方式有以下几种：*
 *（1）、传递中断异常InterruptException，将此异常传递给方法的调用者去处理。实现形式为不捕获此异常，或捕获后通过某种方式再次抛出此异常。*<br>
 *（2）、恢复中断，一些情况下不允许抛出异常，则必须捕获此异常，对当前线程调用interrupt方法恢复中断状态，使调用栈的更高层代码能够看到引发了一个中断*<br>
 *（3）、在对Thread进行扩展，并且能控制调用栈上所有更高层代码的时候，可考虑屏蔽中断，如捕获异常但不做出反应*
