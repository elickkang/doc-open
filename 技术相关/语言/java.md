- **字符串的转义**

  ```java
  //分析如下代码中为什么要这样写 deStr.split("\\(")
  public static void main(String[] args) {
          String deStr = "我是中国人我爱中国(中华人民共和国)";
          List<String> deRst = Arrays.asList(deStr.split("\\("));
          System.out.println(deRst);
  }
  ```

  - 目的

    本例子是用split方法对字符串进行分割，分割符号为***（***

  - 分析

    1. split形参是正则表达式，***(***在正则表达式中是特殊字符(*用于分组*)，所以需要进行转移 ***\（***

    2. 然而***\\***在java代码中是特殊字符(*用于转义*)，所以也需要加***\\***

    3. 最后就需要如下写法

       deStr.split("\\(")

- **java 命令执行(javac 类似)**

  - ***指定 class文件的路径***

    java -cp ./target/ Test

  - ***指定jar包的路径***

    java -cp ./target/* Test

  - ***注意 Windows及UNIX的多个路径的分隔符***

    - *windows(逗号)*

      java -cp xxx.jar;yyy.jar x.y.c.startClass

    - *linux(冒号)*

      java -cp xxx.jar:yyy.jar x.y.c.startClass 