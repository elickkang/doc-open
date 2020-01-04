- ***jvm内存模型***

![](..\../IMG/java内存模型.png)

- ***jvm常用参数***

![](..\../IMG/jvm参数.png)

- ***其他参数***

  - **-Xmn256m**

    ​	设置新生代大小，总堆内存减去新生代内存就是老年代内存， -Xmx减去-Xmn 

  - **-XX:+DisableExplicitGC**

    ​	禁止显示执行system.gc(),代码中调用gc，jvm永远忽略不执行

  - **-XX:MaxDirectMemorySize=40M **

    ​	最大直接内存，绕过jvm直接向操作系统申请内存，又被称为堆外内存，是在jdk4及其之后的nio中引入的，目的是为了提高文件/网络流数据的读写效率，当进行大数量的读写时，考虑堆外内存。

    - *UnSafe*

       使用Unsafe获取的堆外内存，必须由程序显示的释放，JVM不会帮助我们做这件事情

      ```java
      import sun.misc.Unsafe;
      
      public class RevisedObjectInHeap
      {
      	private long address = 0;
      
      	private Unsafe unsafe = GetUsafeInstance.getUnsafeInstance();
      
      	// 让对象占用堆内存,触发[Full GC
      	private byte[] bytes = null;
      
      	public RevisedObjectInHeap()
      	{
      		address = unsafe.allocateMemory(2 * 1024 * 1024);
      		bytes = new byte[1024 * 1024];
      	}
      
      	@Override
      	protected void finalize() throws Throwable
      	{
      		super.finalize();
      		System.out.println("finalize." + bytes.length);
              
      		//显示释放unsafe堆外内存
      		unsafe.freeMemory(address);
      	}
      
      	public static void main(String[] args)
      	{
      		while (true)
      		{
      			RevisedObjectInHeap heap = new RevisedObjectInHeap();
      			System.out.println("memory address=" + heap.address);
      		}
      	}
      
      }
      ```

      ​	我们**覆盖了finalize方法，手动释放分配的堆外内存**。如果堆中的对象被回收，那么相应的也会释放占用的堆外内存。**这里有一点需要注意下**：

      ```
      // 让对象占用堆内存,触发[Full GC
      private byte[] bytes = null;
      ```

      ​	这行代码主要目的是为了触发堆内存的垃圾回收行为，顺带执行对象的finalize释放堆外内存。**如果没有这行代码或者是分配的字节数组比较小，程序运行一段时间后还是会报OutOfMemoryError**。这是因为每当创建1个RevisedObjectInHeap对象的时候，占用的堆内存很小（就几十个字节左右），但是却需要占用2M的堆外内存。这样堆内存还很充足（这种情况下不会执行堆内存的垃圾回收），但是堆外内存已经不足。

    - *Nio ByteBuffer*

      ​	**使用这种堆外内存注意-XX:+DisableExplicitGC的使用可能导致堆外内存溢出**

      ​	Direct Memory是受GC控制的，例如

      ```java
      ByteBuffer bb = ByteBuffer.allocateDirect(1024)
      ```

      ​	这段代码的执行会在堆外占用1k的内存，Java堆内只会占用一个对象的指针引用的大小，**堆外的这1k的空间只有当bb对象被回收时，才会被回收**，这里会发现一个明显的不对称现象，就是堆外可能占用了很多，而堆内没占用多少，导致还没触发GC，那就很容易出现Direct Memory造成物理内存耗光。Direct ByteBuffer分配出去的内存其实也是由GC负责回收的，而不像Unsafe是完全自行管理的，Hotspot在GC时会扫描Direct ByteBuffer对象是否有引用，如没有则同时也会回收其占用的堆外内存。  

  - **-XX:+UseConcMarkSweepGC**

    启用CMS并发收集器

  - **XX:+UseCMSCompactAtFullCollection**

     CMS是不会整理堆碎片的，因此为了防止堆碎片引起full gc，通过会开启CMS阶段进行合并碎片选项：XX:+UseCMSCompactAtFullCollection

  - **XX:+UseCMSCompactAtFullCollection**

    参加 jvm-大内存分页.md

  - **-XX:-UseFastAccessorMethods **

     开启此设置后可以跳过对该类方法的编译 

  - **XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=70**

    ​	 这两个设置一般配合使用,一般用于『降低CMS GC频率或者增加频率、减少GC时长』的需求

    - *-XX:CMSInitiatingOccupancyFraction=70*

      ​	是指设定CMS在对内存占用率达到70%的时候开始GC(因为CMS会有浮动垃圾,所以一般都较早启动GC);

    - *-XX:+UseCMSInitiatingOccupancyOnly*

       	只是用设定的回收阈值(上面指定的70%),如果不指定,JVM仅在第一次使用设定值,后续则自动调整.

  