i###echo有三种形式 
输入
```bash
$echo hello world
$echo 'hello world'
$echo "hello world"
```
对于上述字符串，三者都可以正确输出

```bash
hello world
``` 
###三者各有特点
####**i** 不加引号：用`分号`隔开时会出问题，因为脚本执行命令时用分号 `;` 来隔开多个命令，`;`后为新一次命令的执行。 
输入
```bash
$echo hello;world
``` 
输出
```bash$
hello 
No command 'world' found, did you mean:
 Command 'tworld' from package 'tworld' (universe)
world: command not found
```  
####**ii** 单引号: 将单引号中的内容原样输出，因此不能在其中使用表达式 
输入
```bash
$echo 'hello $?'
``` 
输出
```bash
hello $?
```
####**iii** 双引号: `!`一般情况下不能在双引号中使用 
输入
```bash
$echo "hello !world"
``` 
输出
```bash
bash: !world: event not found
``` 
 双引号中可以使用转义字符来打印`!`或者使用`set +H`来关闭`!`的功能，将其当作一个普通字符。
 输入
```bash
 $echo "hello \!world"
```
 输出（将转义字符也打印出来了...）
```bash
 hello \!world
```
 输入
 
```bash
$set +H
$echo "hello !world"
``` 
输出
```bash
hello !world
```
 

