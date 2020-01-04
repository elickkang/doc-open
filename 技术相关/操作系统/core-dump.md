***ulimit***

- -H 设置硬件资源限制. 
- -S 设置软件资源限制. 
- -a 显示当前所有的资源限制.
-  -c size:设置core文件的最大值.单位:blocks
-  -d size:设置数据段的最大值.单位:kbytes
-  -f size:设置创建文件的最大值.单位:blocks
-  -l size:设置在内存中锁定进程的最大值.单位:kbytes
-  -m size:设置可以使用的常驻内存的最大值.单位:kbytes 
-  -n size:设置内核可以同时打开的文件描述符的最大值.单位:n 
-  -p size:设置管道缓冲区的最大值.单位:kbytes 
-  -s size:设置堆栈的最大值.单位:kbytes 
-  -t size:设置CPU使用时间的最大上限.单位:seconds 
-  -v size:设置虚拟内存的最大值.单位:kbytes

ulimit -c unlimited

ulimit -n 65536



***coredump***

​	*程序运行过程中遇到异常导致异常退出或者中止，并在满足一定条件的情况下会产生一个叫做core的文件*

- **查看core文件是否打开**

  ```shell
  ulimit -c 
  ```

- **打开core文件生成**

  ```shell
  ulimit -c 1024 #1024个blocks，一般1block=512byte
  ulimit -c ulimited #取消大小限制
  ```

- **检查core文件的选项是否·打开**

  ```shell
  ulimit -a  # 显示当前所有limit信息
  ```

- **永久配置core**

  1. 设置环境变量/etc/profile 或者 ./bashrc ./bash_profile

     ```
     echo 'limit -c unlimited' > /etc/profile
     ```

  2. 修改 /etc/security/limits.conf 设置

     ```shell
     #vim /etc/security/limits.conf
     <domain>    <type>    <item>        <value>
      
     *          soft       core         unlimited
     ```

     

- **core文件存储位置**

  ​	core文件默认存储在对应的可执行程序的同一目录下，如要设置core文件存储位置，可通过如下指令：

  ```shell
  cat  /proc/sys/kernel/core_pattern  # 缺省值是|/usr/share/apport/apport %p %s %c %P
  
  echo “/data/coredump/core”> /proc/sys/kernel/core_pattern  # 把core文件生成到/data/coredump/core目录下
  ```

- **core文件的命名**

  ​	默认情况下core文件命名为***core***,但是有必要对core进行重命名，防止文件覆盖

  -  **/proc/sys/kernel/core_uses_pid**可以控制core文件的文件名中是否添加pid作为扩展 

    ```shell
    echo "1" > /proc/sys/kernel/core_uses_pid
    ```

  -  **proc/sys/kernel/core_pattern**可以控制core文件保存位置和文件名格式 

    ```shell
    echo "/corefile/core-%e-%p-%t" > core_pattern 
    # 可以将core文件统一生成到/corefile目录下，产生的文件名为core-命令名-pid-时间戳
    ```

    ```shell
    %% - 单个%字符
    %p - 添加pid
    %u - 添加当前uid
    %g - 添加当前gid
    %s - 添加导致产生core的信号
    %t - 添加core文件生成时的unix时间
    %h - 添加主机名
    %e - 添加程序文件名 
    ```

    

- **造成core的原因**

  -  内存访问越界
  -  多线程程序使用了线程不安全的函数
  -  多线程读写的数据未加锁保护
  -  非法指针

- **调试coredump**

  -  使用gdb调试core文件来查找程序中出现段错误的位置时，要注意的是可执行程序在编译的时候需要加上**-g**编译命令选项 

    ```shell
    g++ -g core.cpp -o test 
    ```

    

  -  **判断是否为core文件** 

    ```shell
    file core.xxxxx
     
    输出：
    core.11691: ELF 64-bit LSB core file x86-64, version 1 (SYSV), SVR4-style, from
    ```

    

  - 第一种方式

    1.  启动gdb，进入core文件，命令格式：gdb [exec file] [core file] 

       ```shell
       gdb ./test core.xxxxx
       ```

       

    2.  在进入gdb后，查找段错误位置：where或者bt，用法示例 

       ```shell
       bt
       #0  0x00007f205b7afde5 in _IO_vfscanf_internal (s=<optimized out>, format=<optimized out>, argptr=argptr@entry=0x7ffdf417be88, 
           errp=errp@entry=0x0) at vfscanf.c:1902
       #1  0x00007f205b7ba87b in __scanf (format=<optimized out>) at scanf.c:33
       #2  0x0000000000400589 in core_test1 () at core.cpp:5
       #3  0x00000000004005bf in main () at core.cpp:15
       ```

       

  - 第二种方式

    1.  启动gdb，进入core文件，命令格式 

       ```shell
       gdb -c [core file]  //或 gdb --core=[core file]
       ```

    2.  在进入gdb后，指定core文件对应的符号表，命令格式 

       ```shell
       (gdb) file [exec file]
       ```

    3.  查找段错误位置：where或者bt。用法示例： 

       ```shell
       bt
       ```

       

