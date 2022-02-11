1. 变量

```shell
num=18 # 等号左右都不能有空格
readonly num # 将变量变成只读，之后不可修改也不可删除
name="qa"
unset name # 删除变量
echo "my age is ${num}" # my age is 18
```

2. 字符串

```shell
name='rose'
str='i love $name'
echo $str # i love $name
# 单引号里啥都是原样输出
# 单引号里不能出现单独的一个单引号，对单引号使用转义符号也不行

str2="i love $name"
echo $str2 # i love rose
# 双引号内可以有变量，可以有转义字符

skill='java'
echo ${skill} # java
echo ${#skill} # 4 # 字符串长度

str3="i like hadoop"
echo ${str:2} # substring(2)
echo ${str:2:2} # substring(2, 2)

str4="hadoop is so easy"
echo `expr index "$str4" o` # linux 下运行为 4
```

3. 运算符

```shell
1+2 是不对的，必须写成 1 + 2
完整表达式要被反引号 `` 包裹
a=10
b=20
```

| 运算符 | 说明                                  | 举例                       |
| ------ | ------------------------------------- | -------------------------- |
| +      | 加法                                  | `expr $a + $b`             |
| -      | 减法                                  | `expr $a + $b` 为 -10      |
| *      | 乘法                                  | `expr $a \* $b` 结果为 200 |
| /      | 除法                                  | `expr $a / $b` 结果为 2    |
| %      | 取余                                  | `expr $a % $b` 结果为 0    |
| =      | 赋值                                  | `a=$b` 返回 false          |
| ==     | 相等。用于比较两个数字，相同则为 true | `[ $a == $b ]` 返回 false  |
| !=     | 不相等                                | `[ $a == $b ]` 返回 true   |

注：条件表达式要放在方括号之间，并且要有空格，`[$a==$b]` 是错的

4. 关系运算符

关系运算符只支持数字,不支持字符串,除非字符串的值是数字。下表列出了常用的关系运算符,假定变量a为10 ,变量b为20 :

| 运算符 | 说明                              | 英文                     | 举例                      |
| ------ | --------------------------------- | ------------------------ | ------------------------- |
| -eq    | 检测两个数是否相等，相等返回 true | equal                    | [ \$a -eq \$b] 返回 false |
| -ne    |                                   | Not equal                |                           |
| -gt    |                                   | greater than             |                           |
| -lt    |                                   | less than                |                           |
| -ge    |                                   | Greater then or equal to |                           |
| -le    |                                   | less than or equal to    |                           |

5. 流程控制

条件

```bash
if condition1
then
	command1
	command2
	...
	commandN
elif condition2 # 可选
	command3
else
	command # 可选
fi
```

循环

```bash
# 1
for i in {1..100..2}; do # 1 到 100 的奇数
	echo $i;
done

# 2
for f in `ls /`; do
	echo $f
done

# 3
sum=0
i=1
while [ $i -le 100 ] ; do
	sum=$[ sum + i ]
	i=$[ i + 1 ]
done
echo $sum

# 无限循环
for ((;;))
do
	command
done

while true
do
	command
done
#例子
while true ;
do
	echo `date +"%Y-%m-%d %H:%M:%S"`
	sleep 1
done
```

case

```bash
case 值 in
	模式1)
		command1
		...
		command2
		;;
	模式2)
		command3
		;;
esac
```

循环里也有 break 和 continue

函数

```bash
[ function ] funname() 
{
	action;
	$1 #第一个入参
	$2 #第二个入参
	$* #所有的参数
	$@ #所有的参数
	[return int;]
}

fun1() {} # 定义函数

fun1 aa bb # 调用函数
```



sed 命令中 s表示替换，\1表示用第一个括号里面的内容替换整个字符串，sed支持*，不支持?、+，不能用\d之类，正则支持有限。