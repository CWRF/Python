# argparse — 命令行选项、参数和子命令的解析器
###详细的内容见<http://python.usyiyi.cn/python_278/library/argparse.html>
###<font color = orange>在这里只列出比较重要的内容
##1、创建一个解析器
###<font color = black>使用argparse的第一步是创建一个ArgumentParser对象：
```
>>> parser = argparse.ArgumentParser(description='Process some integers.')
```
###ArgumentParser对象会保存把命令行解析成Python数据类型所需要的所有信息。
##2、添加参数
###通过调用add_argument()方法向ArgumentParser添加程序的参数信息。通常情况下，这些信息告诉ArgumentParser如何接收命令行上的字符串并将它们转换成对象。这些信息被保存下来并在调用parse_args()时用到。例如：
```
>>> parser.add_argument('integers', metavar='N', type=int, nargs='+',
...                     help='an integer for the accumulator')
>>> parser.add_argument('--sum', dest='accumulate', action='store_const',
...                     const=sum, default=max,
...                     help='sum the integers (default: find the max)')
```
###接下来，调用parse_args()返回的对象将带有两个属性，integers和accumulate。属性integers将是一个包含一个或多个整数的列表，如果命令行上指定 --sum，那么属性accumulate将是sum()函数，如果没有指定，则是max()函数。

##3、解析参数
###ArgumentParser通过parse_args()方法解析参数。它将检查命令行，把每个参数转换成恰当的类型并采取恰当的动作。在大部分情况下，这意味着将从命令行中解析出来的属性建立一个简单的 Namespace对象。
```
>>> parser.parse_args(['--sum', '7', '-1', '42'])
Namespace(accumulate=<built-in function sum>, integers=[7, -1, 42])

```
###在脚本中，parse_args() 调用一般不带参数，ArgumentParser 将根据sys.argv自动确定命令行参数。
