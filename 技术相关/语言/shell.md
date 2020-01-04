- ***按照文件大小排序***

  ```shell
  ll -hSlr
  ```

  

- ***${}与$()的使用***

  - **${}用于变量的取用**

    ```shell
    [dmtsai@study ~]$ echo ${myname}
           <==这里并没有任何资料～因为这个变数尚未被设定！是空的！
    [dmtsai@study ~]$ myname=VBird
    [dmtsai@study ~]$ echo ${myname}
    VBird  <==出现了！因为这个变数已经被设定了！
    ```

  - **$()用于执行特定指令**

    ```shell
    #如何进入到您目前核心的模组目录？
    [dmtsai@study ~]$ cd /lib/modules/`uname -r`/kernel
    [dmtsai@study ~]$ cd /lib/modules/$(uname -r)/kernel  # 以此例较佳！
    ```

  - **$(())用于算数运算**

    ```shell
    [dmtsai@study bin]$ echo $(( 13 % 3 ))
    ```

