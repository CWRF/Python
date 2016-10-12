# argparse — 命令行选项、参数和子命令的解析器
###一个很好的链接<http://blog.ixxoo.me/argparse.html>
###python官方文档<http://python.usyiyi.cn/python_278/library/argparse.html>
###<font color = orange>在这里只列出比较重要的内容
##基础
###从一个简单的例子开始，它几乎什么都不做：
```
import argparse
parser = argparse.ArgumentParser()
parser.parse_args()
```
###运行：
```
sunwujiudeMacBook-Air:untitled Sun$ python test.py
sunwujiudeMacBook-Air:untitled Sun$ python test.py --help
usage: test.py [-h]

optional arguments:
  -h, --help  show this help message and exit
sunwujiudeMacBook-Air:untitled Sun$ python test.py -h
usage: test.py [-h]

optional arguments:
  -h, --help  show this help message and exit
sunwujiudeMacBook-Air:untitled Sun$ python test.py --verbose
usage: test.py [-h]
test.py: error: unrecognized arguments: --verbose
sunwujiudeMacBook-Air:untitled Sun$ python test.py foo
usage: test.py [-h]
test.py: error: unrecognized arguments: foo

```
###结果分析：
1、不加任何参数运行，什么也不显示，没什么用

2、第二条展示了`argparse`模块的好处，几乎什么都不做，却得到一个很有用的帮助信息

3、`--help`参数可以简写为`-h`，是唯一预设的（不需要指定）
##定位参数
###例子：
```
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("echo")
args = parser.parse_args()
print args.echo
```
###运行：
```
sunwujiudeMacBook-Air:untitled Sun$ python test.py
usage: test.py [-h] echo
test.py: error: too few arguments
sunwujiudeMacBook-Air:untitled Sun$ python test.py --help
usage: test.py [-h] echo

positional arguments:
  echo

optional arguments:
  -h, --help  show this help message and exit
sunwujiudeMacBook-Air:untitled Sun$ python test.py foo
foo

```
###结果分析：
1、我们用到了方法`add_argument()`，用来指定程序需要接受的命令参数，本例中的`echo`。

2、现在运行程序必须指定一个参数。

3、方法`parse_args()`通过分析指定的参数返回一些数据，如本例中的echo。

4、像魔法一样，`argparse`自动生成这些变量，你可能已经注意到变量`echo`和我们指定的参数相同。

虽然现在帮助信息已经很美观了，但是还不够好。例如我们知道echo是个定位参数，但是却不知道该参数的意思，只能通过猜或者读源码。下面，我们可以让它更有帮助：
```
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("echo",help = "echo the string you use here")
args = parser.parse_args()
print args.echo
```
###运行：
```
sunwujiudeMacBook-Air:untitled Sun$ python test.py -h
usage: test.py [-h] echo

positional arguments:
  echo        echo the string you use here

optional arguments:
  -h, --help  show this help message and exit
```
###再修改：
```
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("square", help="display a square of a given number")
args = parser.parse_args()
print args.square**2
```
###运行：
```
sunwujiudeMacBook-Air:untitled Sun$ python test.py 4
Traceback (most recent call last):
  File "test.py", line 5, in <module>
    print args.square**2
TypeError: unsupported operand type(s) for ** or pow(): 'str' and 'int'
```
运行有点问题，因为如果不指定参数类型，`argparse`默认它是字符串。因此我们需要告诉argparse该参数是整型。
###再再修改
```
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("square", help="display a square of a given number",type = int)
args = parser.parse_args()
print args.square**2
```
###运行：
```
sunwujiudeMacBook-Air:untitled Sun$ python test.py 4
16
sunwujiudeMacBook-Air:untitled Sun$ python test.py four
usage: test.py [-h] square
test.py: error: argument square: invalid int value: 'four'
```
可以运行了，程序能够在执行前过滤一些错误的参数输入。
##可选参数
以上，展示了定位参数的用法。下面让我们来看看如何添加可选参数：
```
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("--verbosity", help="increase output verbosity")
args = parser.parse_args()
if args.verbosity:
    print "verbosity turned on"
```
###运行：
```
sunwujiudeMacBook-Air:untitled Sun$ python test.py --verbosity 1
verbosity turned on
sunwujiudeMacBook-Air:untitled Sun$ python test.py
sunwujiudeMacBook-Air:untitled Sun$ python test.py --help
usage: test.py [-h] [--verbosity VERBOSITY]

optional arguments:
  -h, --help            show this help message and exit
  --verbosity VERBOSITY
                        increase output verbosity
sunwujiudeMacBook-Air:untitled Sun$ python test.py --verbosity
usage: test.py [-h] [--verbosity VERBOSITY]
test.py: error: argument --verbosity: expected one argument
```
###结果分析：
1、当指定`--verbosity`时程序就显示一些东西，没指定的时候就不显示。

2、这个参数事实上是可选的，不指定它也不会出错。如果不指定可选的参数，对应的变量就被设置为`None`，比如本例中的`args.verbosity`, 这就是为什么示例中的 `if `没有执行的原因。

3、帮助信息发生了点变化

4、当我们使用可选参数`--verbosity`时，也必须指定一些值。

上面的示例中可选参数--verbosity后面接受任意的整型值，但是对于简单的程序，实际上只需要两种值，True或者False。因此可以修改上面的代码：
```
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("--verbosity", help="increase output verbosity",action = "store_true")
args = parser.parse_args()
if args.verbosity:
    print "verbosity turned on"
```
###运行：
```
sunwujiudeMacBook-Air:untitled Sun$ python test.py --verbosity
verbosity turned on
sunwujiudeMacBook-Air:untitled Sun$ python test.py --verbosity 1
usage: test.py [-h] [--verbosity]
test.py: error: unrecognized arguments: 1
sunwujiudeMacBook-Air:untitled Sun$ python test.py --verbosity -h
usage: test.py [-h] [--verbosity]

optional arguments:
  -h, --help   show this help message and exit
  --verbosity  increase output verbosity
```
###结果分析
1、现在多加一个标志来替代必须给出一些值，并修改了名称来表达我们的意思。注意现在指定了一个新的关键词`action`，并且赋值为store_ture。如果指定了这个可选参数，`args.verbose`就赋值为`True`,否则就为False。

2、多指定了值，它就会发出错误提示。

3、注意帮助文档有什么不同
##简写
如果你很熟悉命令行，你可能已经注意到我在上面已经提到了参数的简写，非常的简单：
```
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("-v","--verbosity", help="increase output verbosity",action = "store_true")
args = parser.parse_args()
if args.verbosity:
    print "verbosity turned on"
```
###运行：
```
sunwujiudeMacBook-Air:untitled Sun$ python test.py -v
verbosity turned on
sunwujiudeMacBook-Air:untitled Sun$ python test.py -h
usage: test.py [-h] [-v]

optional arguments:
  -h, --help       show this help message and exit
  -v, --verbosity  increase output verbosity
```
注意这些帮助信息的变化
##混合使用定位参数和可选参数
再复杂一点：
```
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("square",type=int,help="display a square of a given number")
parser.add_argument("-v","--verbose", help="increase output verbosity",action = "store_true")
args = parser.parse_args()
answer = args.square**2
if args.verbose:
    print "the aquare of {} equals {}".format(args.square,answer)
else:
    print answer
```
###运行
```
sunwujiudeMacBook-Air:untitled Sun$ python test.py
usage: test.py [-h] [-v] square
test.py: error: too few arguments
sunwujiudeMacBook-Air:untitled Sun$ python test.py 4 --verbose 
the aquare of 4 equals 16
sunwujiudeMacBook-Air:untitled Sun$ python test.py --verbose 4
the aquare of 4 equals 16
```
1、为了让程序复杂点，我们重新加上了定位参数。

2、注意到参数的顺序是没有影响的。

来看看为程序加上处理重复参数的能力会怎么样：
```
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("square", type=int,
                    help="display a square of a given number")
parser.add_argument("-v", "--verbosity", type=int,
                    help="increase output verbosity")
args = parser.parse_args()
answer = args.square**2
if args.verbosity == 2:
    print "the square of {} equals {}".format(args.square, answer)
elif args.verbosity == 1:
    print "{}^2 == {}".format(args.square, answer)
else:
    print answer
```
###运行：
```
sunwujiudeMacBook-Air:untitled Sun$ python test.py 4
16
sunwujiudeMacBook-Air:untitled Sun$ python test.py 4 -v
usage: test.py [-h] [-v VERBOSITY] square
test.py: error: argument -v/--verbosity: expected one argument
sunwujiudeMacBook-Air:untitled Sun$ python test.py 4 -v 1
4^2 == 16
sunwujiudeMacBook-Air:untitled Sun$ python test.py 4 -v 2
the square of 4 equals 16
sunwujiudeMacBook-Air:untitled Sun$ python test.py 4 -v 3
16

```
除了最后一个暴露了一个bug，其他的看起都来运行良好。让我们通过限制`--verbosity`后面跟的值来修正：
```
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("square", type=int,
                    help="display a square of a given number")
parser.add_argument("-v", "--verbosity", type=int,choices = [0,1,2],
                    help="increase output verbosity")
args = parser.parse_args()
answer = args.square**2
if args.verbosity == 2:
    print "the square of {} equals {}".format(args.square, answer)
elif args.verbosity == 1:
    print "{}^2 == {}".format(args.square, answer)
else:
    print answer
```
###运行
```
sunwujiudeMacBook-Air:untitled Sun$ python test.py 4 -v 3
usage: test.py [-h] [-v {0,1,2}] square
test.py: error: argument -v/--verbosity: invalid choice: 3 (choose from 0, 1, 2)
sunwujiudeMacBook-Air:untitled Sun$ python test.py 4 -h
usage: test.py [-h] [-v {0,1,2}] square

positional arguments:
  square                display a square of a given number

optional arguments:
  -h, --help            show this help message and exit
  -v {0,1,2}, --verbosity {0,1,2}
                        increase output verbosity
```
注意帮助信息和错误信息都发生了变化。

现在让我们用一种更常用的方法来处理，类似`CPython`处理自己的参数(参考python --help)：
```
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("square", type=int,
                    help="display the square of a given number")
parser.add_argument("-v", "--verbosity", action="count",
                    help="increase output verbosity")
args = parser.parse_args()
answer = args.square**2
if args.verbosity == 2:
    print "the square of {} equals {}".format(args.square, answer)
elif args.verbosity == 1:
    print "{}^2 == {}".format(args.square, answer)
else:
    print answer
```
我们引入了另一个关键词count来统计可选参数出现的次数：
```
$ python prog.py 4
16
$ python prog.py 4 -v
4^2 == 16
$ python prog.py 4 -vv
the square of 4 equals 16
$ python prog.py 4 --verbosity --verbosity
the square of 4 equals 16
$ python prog.py 4 -v 1
usage: prog.py [-h] [-v] square
prog.py: error: unrecognized arguments: 1
$ python prog.py 4 -h
usage: prog.py [-h] [-v] square

positional arguments:
  square           display a square of a given number

optional arguments:
  -h, --help       show this help message and exit
  -v, --verbosity  increase output verbosity
$ python prog.py 4 -vvv
16
```
1、和前面的版本相比这里多了一个关键词(类似action="store_true")。

2、行为上也类似store_true。

3、count关键词的示范，大家可能在其他地方已经看过了。

4、就像store_ture，如果不指定-v，响应变量就会被设置为None。

5、指定完整的名称和简写效果是一样的。

6、但是不爽的是，帮助信息并没有做出有用的提示，不过可以通过修改help来改善这个问题。

7、最后那个输出又暴露了程序的bug

修改一下：
```
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("square", type=int,
                    help="display a square of a given number")
parser.add_argument("-v", "--verbosity", action="count",
                    help="increase output verbosity")
args = parser.parse_args()
answer = args.square**2

# bugfix: replace == with >=
if args.verbosity >= 2:
    print "the square of {} equals {}".format(args.square, answer)
elif args.verbosity >= 1:
    print "{}^2 == {}".format(args.square, answer)
else:
    print answer
```
###运行
```
$ python prog.py 4 -vvv
the square of 4 equals 16
$ python prog.py 4 -vvvv
the square of 4 equals 16
$ python prog.py 4
Traceback (most recent call last):
  File "prog.py", line 11, in <module>
    if args.verbosity >= 2:
TypeError: unorderable types: NoneType() >= int()
```
1、第一个输出是正确的，修正了上个版本的问题，参数出现次数>=2时都能显示详细的信息。

2、第三个输出还是有问题

在修改一下：
```
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("square", type=int,
                    help="display a square of a given number")
parser.add_argument("-v", "--verbosity", action="count", default=0,
                    help="increase output verbosity")
args = parser.parse_args()
answer = args.square**2
if args.verbosity >= 2:
    print "the square of {} equals {}".format(args.square, answer)
elif args.verbosity >= 1:
    print "{}^2 == {}".format(args.square, answer)
else:
    print answer
```
我们又加入了一个关键词`default`。设置它的默认值为0，这样可以让它兼容其他整型。注意，如果一个可选参数没有指定，它就会被设置成None，None是不能和整型比较的(触发[`TypeError][5]异常`)。
###运行：
```
$ python prog.py 4
16

```
你可以使用我们所学来做很多事情，但是这仅仅是皮毛而已。argparse模块非常强大，下面来看看更多的用法。
##高级用法
如果我们想扩展程序的功能，而不仅仅是求平方：
```
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("x", type=int, help="the base")
parser.add_argument("y", type=int, help="the exponent")
parser.add_argument("-v", "--verbosity", action="count", default=0)
args = parser.parse_args()
answer = args.x**args.y
if args.verbosity >= 2:
    print "{} to the power {} equals {}".format(args.x, args.y, answer)
elif args.verbosity >= 1:
    print "{}^{} == {}".format(args.x, args.y, answer)
else:
    print answer
```
###运行
```
$ python prog.py
usage: prog.py [-h] [-v] x y
prog.py: error: the following arguments are required: x, y
$ python prog.py -h
usage: prog.py [-h] [-v] x y

positional arguments:
  x                the base
  y                the exponent

optional arguments:
  -h, --help       show this help message and exit
  -v, --verbosity
$ python prog.py 4 2 -v
4^2 == 16
```
截止到目前，我们都在利用详细的级别来改变输出，下面的示例演示了利用详细的级别来显示更多输出：
```
import argparse
parser = argparse.ArgumentParser()
parser.add_argument("x", type=int, help="the base")
parser.add_argument("y", type=int, help="the exponent")
parser.add_argument("-v", "--verbosity", action="count", default=0)
args = parser.parse_args()
answer = args.x**args.y
if args.verbosity >= 2:
    print "Running '{}'".format(__file__)
if args.verbosity >= 1:
    print "{}^{} ==".format(args.x, args.y),
print answer
```
###输出
```
$ python prog.py 4 2
16
$ python prog.py 4 2 -v
4^2 == 16
$ python prog.py 4 2 -vv
Running 'prog.py'
4^2 == 16
```
##参数冲突
迄今为止，我们已经使用到了`[argparse.ArgumentParser][6]`的两个方法，来看看他的另一个方法`add_mutually_exclusive_group()`。它可以让我们指定某个参数和其他参数冲突。下面来修改下程序以对这个新方法有更多的了解：我们将加入参数`--quiet`，它和参数`--verbose`冲突，不能同时指定：
```
import argparse

parser = argparse.ArgumentParser()
group = parser.add_mutually_exclusive_group()
group.add_argument("-v", "--verbose", action="store_true")
group.add_argument("-q", "--quiet", action="store_true")
parser.add_argument("x", type=int, help="the base")
parser.add_argument("y", type=int, help="the exponent")
args = parser.parse_args()
answer = args.x**args.y

if args.quiet:
    print answer
elif args.verbose:
    print "{} to the power {} equals {}".format(args.x, args.y, answer)
else:
    print "{}^{} == {}".format(args.x, args.y, answer)
```
程序很简单，为了演示冲突，去掉了其他功能特性展示，运行结果:
```
$ python prog.py 4 2
4^2 == 16
$ python prog.py 4 2 -q
16
$ python prog.py 4 2 -v
4 to the power 2 equals 16
$ python prog.py 4 2 -vq
usage: prog.py [-h] [-v | -q] x y
prog.py: error: argument -q/--quiet: not allowed with argument -v/--verbose
$ python prog.py 4 2 -v --quiet
usage: prog.py [-h] [-v | -q] x y
prog.py: error: argument -q/--quiet: not allowed with argument -v/--verbose
```
很好理解，我们添加最后那个输出是为了展示灵活性，比如，指定参数时可以同时混用参数全称和简写。

通过前面的学习，为了以防万一，你可能想通过帮助信息来告诉用户如何使用你的程序：
```
import argparse

parser = argparse.ArgumentParser(description="calculate X to the power of Y")
group = parser.add_mutually_exclusive_group()
group.add_argument("-v", "--verbose", action="store_true")
group.add_argument("-q", "--quiet", action="store_true")
parser.add_argument("x", type=int, help="the base")
parser.add_argument("y", type=int, help="the exponent")
args = parser.parse_args()
answer = args.x**args.y

if args.quiet:
    print answer
elif args.verbose:
    print "{} to the power {} equals {}".format(args.x, args.y, answer)
else:
    print "{}^{} == {}".format(args.x, args.y, answer)

```
注意下面的帮助信息，`[-v | -q]`表明了可以使用`-v`或者`-q`，但是不能同时使用。
#官方文档内容
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
