- ***变量声明前置***

  ```javascript
  1 var a = 1;
  2 function main() {
  3     console.log(a);
  4     var a = 2;
  5 }
  6 main()//输出undefined
  ```

  等同于:

  ```shell
  1 var a = 1;
  2 function main() {
  3     var a;
  4     console.log(a);
  5     a = 2;
  6 }
  ```

  在一个作用域块中，所有的变量都被放置在块的开始进行声明

