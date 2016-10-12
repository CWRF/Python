###在编写程序之前需要添加pillow库，以下是python源码
###参考链接<https://www.shiyanlou.com/courses/370>
```
# -*- coding: utf-8 -*

from PIL import Image
import argparse

#命令行输入参数处理
parser = argparse.ArgumentParser()
#add_argument（）方法参数说明：
#type：命令行参数应该被转换成的类型
#default：如果命令行中没有出现该参数时的默认值
parser.add_argument('file')     #输入文件
parser.add_argument('-o', '--output')   #输出文件
parser.add_argument('--width', type = int, default = 80) #输出字符画宽
parser.add_argument('--height', type = int, default = 80) #输出字符画高

#获取参数
args = parser.parse_args()

IMG = args.file
WIDTH = args.width
HEIGHT = args.height
OUTPUT = args.output

ascii_char = list("$@B%8&WM#*oahkbdpqwmZO0QLCJUYXzcvunxrjft/\|()1{}[]?-_+~<>i!lI;:,\"^`'. ")

# 将256灰度映射到70个字符上
def get_char(r,g,b,alpha = 256):
    if alpha == 0:
        return ' '
    length = len(ascii_char)
    gray = int(0.2126 * r + 0.7152 * g + 0.0722 * b)

    unit = (256.0 + 1)/length
    return ascii_char[int(gray/unit)]

if __name__ == '__main__':#可以理解为主函数的入口

    im = Image.open(IMG)
    im = im.resize((WIDTH,HEIGHT), Image.NEAREST)

    txt = ""

    for i in range(HEIGHT):
        for j in range(WIDTH):
            txt += get_char(*im.getpixel((j,i)))#getpixel(self, xy),获取每个坐标像素点的RGB值。参数xy格式为(x,y),返回格式是(R, G, B, A)
        txt += '\n'

    print txt

    #字符画输出到文件
    if OUTPUT:
        with open(OUTPUT,'w') as f:
            f.write(txt)
    else:
        with open("output.txt",'w') as f:
            f.write(txt)
```
