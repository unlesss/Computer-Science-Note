 bourne shell：$
 c shell：%

以指令 “#！” 开头，执行时调用bin/bash

# Shell 变量
	可以包含只有字母（a到z或A到Z），数字（0〜9）或下划线（_）
	一般使用大写字母

read 从键盘读取输入
readonly修饰变量为只读
unset 取消跟踪变量值

## 三种类型
1. 局部
2. 环境
3. shell 

### 特殊变量
![[Pasted image 20240606111308.png]]

$0 脚本名
$n 位置参数
$# 参数个数
$* 和 $@ 以空格和列表的形式读取所有参数
$? 表示前一个命令的退出状态

# 数组
```shell
array_name=(value1 value2 ...)
${array_name[index]}

${array_name[*]} #所有项目
${array_name[@]}
```

# 基本运算符
使用倒逗号来包括表达式
表达式中，符号两边要有空格


```
`expr 2 + 2` 
+ - * / % == ！= #算数运算符

```
```shell
-eq  相等
-ne  不相等
-gt  大于
-lt 小于
-ge 大于等于
-le 小于等于
[ $a -eq $b ] 必须有空格
```

```shell
！
-o or
-a and
```

```shell
=
！=
-z 长度为0  [ -z $a ] 
-n 长度不为0  	[ -n "$a" ] 
$ 不为空
```


# echo命令

1. 普通字符串
2. 转义字符    在引号中打出需要转义的字符
3. 变量 read命令读取键盘输入
4. 换行 不换行
5. 结果重定向到文件 >
6. 原样输出  单引号
7. 命令执行结果 反引号

# printf命令
	printf  format-string  [arguments...]

%s：字符串
%d：十进制整数
%f：浮点数
%c：字符
%x：十六进制数
%o：八进制数
%b：二进制数
%e：科学计数法表示的浮点数

# test命令

```shell
num1=100
num2=100
if test $[num1] -eq $[num2]
then
    echo '两个数相等！'
else
    echo '两个数不相等！'
fi

#!/bin/bash

a=5
b=6

result=$[a+b] # 注意等号两边不能有空格
echo "result 为： $result"
```

# shell流程控制

## if
```shell
if condition
then
    command1 
    command2
    ...
    commandN 
fi

if [ $(ps -ef | grep -c "ssh") -gt 1 ]; then echo "true"; fi


if condition1
then
    command1
elif condition2 
then 
    command2
else
    commandN
fi


if (( a > b )); then
    ...
fi
```

## for
```shell
for var in item1 item2 ... itemN
do
    command1
    command2
    ...
    commandN
done

for item in {1..10..2} 设定输出间隔
do
    echo $item
done
运行结果：
1
3
5
7
9
 
实现四： 输出当前目录下所有的文件和文件夹
for file in $(ls)
do
    echo $file
done
 
for file in *
do
    echo $file
done
```

## while
```shell
while condition
do
    command
done

```
## util
```shell
until condition
do
    command
done

```

## case

```shell
case 值 in
模式1)
    command1
    command2
    ...
    commandN
    ;;
模式2)
    command1
    command2
    ...
    commandN
    ;;
模式*) # 表示其他输入
esac

```

## break continue
```shell
#!/bin/bash
while :
do
    echo -n "输入 1 到 5 之间的数字: "
    read aNum
    case $aNum in
        1|2|3|4|5) echo "你输入的数字为 $aNum!"
        ;;
        *) echo "你输入的数字不是 1 到 5 之间的!"
            continue
            echo "游戏结束"
        ;;
    esac
done
```
# shell函数

```shell
[ function ] funname [()]

{

    action;

    [return int;]

}
```
	参数返回，可以显示加：return 返回，如果不加，将以最后一条命令运行结果，作为返回值。 return 后跟数值 n(0-255).

$n 表示不同位置的参数
 $ 10  不能获取第十个参数，获取第十个参数需要 
 $ {10}。当n>=10时，需要使用${n}来获取参数。

```shell
$$ 进程id
$! 后台运行的最后一个进程的ID号

```

# 重定向

## 输出重定向

```shell
command1 > file1
```
上面这个命令执行command1然后将输出的内容存入file1。

注意任何file1内的已经存在的内容将被新内容替代。如果要将新内容添加在文件末尾，请使用>>操作符。

## 输入重定向

本来需要从键盘获取输入的命令会转移到文件读取内容。

**重定向原理**

执行命令时打开三个文件
1. stdin      0
2. stdout     1
3. stderr        2

command 2>file 控制stderr文件的输出重定向
command > file 2>&1 stdout和stderr 合并后重定向
command < file1 >file2 stdin和stdout 都重定向


## here document

command << delimiter
    document
delimiter

将两个分隔符之间的内容传递给command
结尾的分隔符要顶格写

## /dev/null
	希望执行某个命令，但又不希望在屏幕上显示输出结果，那么可以将输出重定向到 /dev/null
	
/dev/null 是特殊的文件，写入到它的内容都会被丢弃；如果尝试从该文件读取内容，那么什么也读不到。但是 /dev/null 文件非常有用，将命令的输出重定向到它，会起到"禁止输出"的效果。
$ command > /dev/null 2>&1


# shell文件包含

	导入外部脚本
. filename   # 注意点号(.)和文件名中间有一空格

或

source filename
