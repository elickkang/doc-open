-  ***Python2 和 Python3 中的编码***

  - **Python 2 中常量字符串声明，字符串为使用默认编码编码之后的字符串，而如果前面显式带`u`则为unicode字符；**

    1. unicode编码

       ```python
       u'中文'.encode('utf-8')
       
       unicode.encode(u'中文','utf-8')
       ```

    2. 中文解码为unicode

       ```python
       '中文'.decode('utf-8')
       ```

       

  - **python 3中常量字符串声明，字符串为unicode字符未编码**

    1. unicode编码

       ```python
       '中文'.encode('utf-8')
       ```

    2. 中文解码为unicode

       ```python
       '中文'.encode('utf-8').decode('utf-8')
       ```
  
  
  
  ***对比java中对字符串处理***：
  
  ​	java中字符串本质为char数组，每个数组单元是一个字节、占用8个位，并使用补码表示为数值。使用指定编码获取字符串的二进制流，并将补码转变为原码，可以得到字符串的指定编码格式

-  ***pip 仓库全局设置***

  - **顺丰python源为例**
    1. pip config set global.index-url http://yum17.int.sfdc.com.cn/pypi/root/pypi/+simple/
    2. pip config set global.trusted-host yum17.int.sfdc.com.cn

- ***全局变量 global***

  ```shell
  # encoding=utf-8
  import os
  import sys
  
  # 定义全局变量 obj val，该位置不需要global
  obj = {
  "name":u'金融港',
  "age":12
  }
  
  val = 1
  
  def test_reference(ref):
  	ref['name'] = u'光谷软件园'.encode(encoding='utf-8')
  	print 'inner obj is {}'.format(ref['name'])
  
  def test_val(var):
  	var = 2
  	print 'inner val is {}'.format(var)
  	
  def print_val():
  	#def 自定义方法需要使用global引用全局变量
  	global
  	print 'print_val:{}'.format(val)
  	
  if __name__ == '__main__':
  	# main方法中引用全局变量 不需要global
  	
  	test_reference(obj)
  	print 'outter obj is {}'.format(obj['name'])
  	test_val(val)
  	print 'outter val is {}'.format(val)
  ```

  