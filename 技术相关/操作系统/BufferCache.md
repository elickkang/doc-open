- ***buffer 与 cache的区别***
  - cache缓存的是file，很重要；buffer缓存的是inode、dentry等内容，相对不那么重要
  - cache***缓存***了file，以优化文件的I/O；buffer***缓冲***了磁盘block，以优化磁盘I/O

- ***buffer与cache的统一***

  ​	在linux kernel 2.4之前，两个cache是有区别的，文件存放在cache中，磁盘块存储在buffer中,由于大部分磁盘中的文件在呈现到文件系统的过程中，数据会呈现两次（buffer中缓冲了磁盘块，cache缓存了文件数据），很多unix系统都是类似模式。这很容易实现，然而不够优雅高效。在kernel2.4开始，两个cache的内容走向统一。如果数据既以文件又以块的形式缓存，那么buffer 将简单的指向cache，这样就仅有一个数据的实例存储在内存中。但是有一点，因为file数据是用块数据呈现的，大部分buffer呈现为cache，一小部分数据不是文件备份，如：metadata、raw block I/O，这些数据用buffer存储（inode cache、dentry cache）

- ***buffer cache的释放***

  ```shell
  # 释放page cache
  echo 1> /proc/sys/vm/drop_caches 
  
  # 释放 inode cache和dentry cache
  echo 2> /proc/sys/vm/drop_caches 
  
  # 释放全部
  echo 3> /proc/sys/vm/drop_caches
  ```

  



