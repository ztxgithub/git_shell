# 脚本

## 脚本运行

```shell
    1.脚本运行的权限
        chmod +x ./demo1   # 使每个人都有执行的权限
        chmod +rx ./demo1  # 使每个人都有读和执行的权限
        chmod u+rx ./demo1 # 仅仅使脚本文件拥有者有读和执行的权限
        chmod u+x ./demo1  # 只有自己可以执行，其它人不能执行
        chmod ug+x ./demo1 # 只有自己以及同一群可以执行，其它人不能执行 
        chmod 555 ./demo1  # 使每个人都有读和执行的权限
        chmod 777 ./demo1 
        
    2.运行脚本的方式
        (1) 直接运行demo1脚本,这个方法是把脚本给系统中所有其他的用户使用,这个时候需要你将demo1脚本移动到目录/usr/local/bin 中
            (必须要有root的权限)，移动到 bin 目录之后，你只需使用 demo1 加回车就能执行脚本了.
             > demo1
             
        (2) 通过sh或者bash命令运行脚本,sh scriptname 运行一个Bash脚本将会禁止所有Bash的扩展特性
                > sh demo1
                > bash demo1
                
        (3) ./demo1
```

## 特殊字符

```shell
    1.注释
        以#开头的行就是注释，会被解释器忽略.echo命令给出的一个转义的#字符并不会开始一个注释．同样地，
        出现在一些参数代换结构和在数值常量表达式中的#字符也同样不会开始一个注释．
        
            echo "这里的 # 不会被注释"
            echo '这里的 # 不会被注释'
            echo 这里的 \# 不会被注释
            echo 这里的 # 会被注释
            
            echo ${PATH#*:}       # 前面的#是参数代换，不是注释.
            echo $(( 2#101011 ))  # 基本转换，不是注释.
        
    2.命令分割符
        分号;命令分割符,分割符允许在同一行里有两个或更多的命令
        
    3.结束符
        双分号;;　case语句分支的结束符
            read Keypress
            case "$Keypress" in
              [[:lower:]]   ) echo "Lowercase letter";;
              [[:upper:]]   ) echo "Uppercase letter";;
              [0-9]         ) echo "Digit";;
              *             ) echo "Punctuation, whitespace, or other";;
            esac      #  允许字符串的范围出现在[]中,
                      #+ 或者POSIX风格的[[中.
            exit 0
            
    4.引号
        引号一个很重要的作用是保护命令行上的一个参数不被shell解释，而把此参数传递给要执行的程序来处理它.
        单引号:
            单引号里的任何字符都会原样输出，单引号字符串中的变量是无效的.
            单引号字串中不能出现单引号（对单引号使用转义符后也不行）
            
        双引号:
            your_name='qinjx'
            str="Hello, I know your are \"$your_name\"!"
            echo $str
                结果：
                    Hello, I know your are "qinjx"!
            
            双引号里可以有变量
            双引号里可以出现转义字符
            引号能改掉echo's不换行的“习惯”
            
            > echo $(ls -al)
                total 24 drwxr-xr-x 5 kacperwang staff 170 1 22 16:47 . drwxr-xr-x 5 kacperwang
                
            > echo "$(ls -al)"
                total 24
                drwxr-xr-x  5 kacperwang  staff  170  1 22 16:47 .
                drwxr-xr-x  5 kacperwang  staff  170  1 22 13:29 ..
                -rwxr-xr-x  1 kacperwang  staff   58  1 22 16:20 demo1
    
    5.命令替换
        命令替换"`"，将会重新分配一个命令甚至是多个命令的输出；它会将命令的输出如实地添加到另一个上下文中
        
            通过这个符号，批量删除文件    
            rm `cat filename`   # "filename" 包含了需要被删除的文件列表
            # 可能会产生"参数列表太长"的错误
            # 更好的方法是              xargs rm -- < filename 
            # ( -- 同时覆盖了那些以"-"开头的文件所产生的特殊情况 )
```

## 操作符

```shell
    1.赋值
        通用的变量赋值操作符，可以用于数值和字符串的赋值,
        "="字符后面不能加空白字符
            category=minerals
            
        不要把"="赋值操作符和=测试操作符搞混
        # = 用于测试操作符
        if [ "$string1" = "$string2" ]
        # if [ "X$string1" = "X$string2" ] 会更安全,
        # 它为了防止其中有一个字符串为空时产生错误信息.
        # (增加的"X"字符可以互相抵消.) 
        then
           command
        fi
        
    2.计算操作符
        ** : 求幂
            # Bash在版本2.02引入了"**"求幂操作符.
            let "z=5**3"    # let 主要用于计算表达式, 而直接 z=5*2,会被直接当成字符串"5*2"
            echo "z = $z"   # z = 125
            
    3.位操作符
        位操作符很少在脚本中使用.他们主要用于操作和测试从端口或sockets中读到的数据.
        # <<=
        # "位左移赋值"
        let "var <<= 2" 结果使var的二进制值左移了二位（相当于乘以4）
        
    4.逗号操作符
        逗号,操作符连接两个或更多的算术操作。所有的操作都被求值(可能会有副作用)，但只返回最后一个操作的结构
        
        let "t1 = ((5 + 3, 7 - 1, 15 - 4))"
        echo "t1 = $t1"               # t1 = 11
        
        let "t2 = ((a = 9, 15 / 3))"  # 初始化"a"并求"t2"的值.
        echo "t2 = $t2    a = $a"     # t2 = 5    a = 9
```

## 变量
    
```shell
    1.变量值
        如果variable1是一个变量的名字，那么$variable1就是引用这个变量的值
        
    2.定义变量
        (1)首个字符必须为字母（a-z，A-Z）。
        (2)中间不能有空格，可以使用下划线（_），等号左右也不能有空格。
        (3)不能使用标点符号。
        (4)不能使用bash里的关键字（可用help命令查看保留关键字）
        
    3.只读变量(readonly)
    
        #!/bin/bash
        github="https://jaywcjlove.github.io"
        readonly github
        github="https://www.github.com"
        
        # 运行脚本，结果如下：
        /bin/sh: NAME: This variable is read only.
        
    4.删除变量unset
        变量被删除后不能再次使用。unset 命令不能删除只读变量
        
    5.变量类型
        不同与许多其他的编程语言,Bash不以"类型"来区分变量.本质上来说,Bash变量是字符串,但是根据环境的不同,
        Bash允许变量有整数计算和比较,其中的决定因素是变量的值是不是只含有数字
        
        (1) 局部变量: 局部变量在脚本或命令中定义,仅在当前shell实例中有效,其他shell启动的程序不能访问局部变量.
        (2) 环境变量: 所有的程序,包括shell启动的程序,都能访问环境变量,有些程序需要环境变量来保证其正常运行.
                     必要的时候shell脚本也可以定义环境变量。
                     
    6.内部变量
        $LINENO：这个变量表示在本shell脚本中该变量出现时所在的行数．它只在脚本中它出现时有意义，它一般可用于调试
        $TZ : 时区
        $PATH：可执行程序文件的搜索路径.一般有/usr/bin/,/usr/local/bin,等等．
        $PWD : 当前路劲(绝对路劲)
        $RANDOM: Bash的一个返回伪随机整数(范围为0 - 32767)的内部函数(而不是一个常量或变量)
        
    7.位置参数
        $#　: 传递到脚本的参数个数
                    echo "num=$#"
                    > ./Variables.sh 1 2
                    结果　num=2
        $*  : 以一个单字符串显示所有向脚本传递的参数, $* 没有用双引号包含,以"$1" "$2" … "$n" 的形式输出所有参数
               如果用双引号包含"$*",会将所有的参数作为一个整体，以"$1 $2 … $n"的形式输出所有参数
                    echo "\$*=$*"
                    > ./Variables.sh 1 2 3
                    结果　$*=1 2 3    # "1" "2" "3"

                    echo "\$*" = "$*"
                    > ./Variables.sh 1 2 3
                    结果　$*=1 2 3    # "1 2 3"
        $$　：脚本运行的当前进程ID号
        $!　：后台运行的最后一个进程的ID号
        $@　：传递给脚本或函数的所有参数, $@加不加双引号,都是以$1" "$2" … "$n" 的形式输出所有参数
                echo "print each param from \"\$*\""
                for var in "$*"
                do
                    echo "$var"
                done
                echo "print each param from \"\$@\""
                for var in "$@"
                do
                    echo "$var"
                done
                
                结果：
                    print each param from "$*"
                    a b c d
                    print each param from "$@"
                    a
                    b
                    c
                    d
                    
        $0　：当前脚本的文件名
        $n　：传递给脚本或函数的参数.n 是一个数字,表示第几个参数.例如,第一个参数是$1,第二个参数是$2
        $-  :显示Shell使用的当前选项，与set命令功能相同
        $?  :显示最后命令的退出状态.0表示没有错误,其他任何值表明有错误
              haproxy 未启动
                > killall -0 haproxy
                   haproxy: no process found
                   
                > echo $?
                  结果： 1   
                  
              haproxy 已启动
                 > killall -0 haproxy
                     
                  > echo $?
                    结果： 0    
```

## 参数变量

```shell
    1.${parameter-default}：当变量没有定义声明时,parameter=default
        echo "username=${username-whoami}"
            结果：
                username=whoami
                
    2.${variablename?}  :结构也能检查一个脚本中变量的设置情况,如果variablename没有定义声明,或则值为null,则shell执行终止退出.
    
    3.${variablename?"ERROR MESSAGE"} :如果variablename没有定义声明,或则值为null,则shell执行终止退出,并打印"ERROR MESSAGE"
    
    4.${#var}：字符串长度（即变量$var的字符个数）.对于数组来说，${#array}是数组的第一个元素的升序.
      ${#*}和${#@} 表示传递给参数的个数.对于一个数组来说，${#array[*]}和${#array[@]}表示数组中元素的个数
      
    5.${var#Pattern} :删除从$var前端开始的最短匹配$Pattern的字符串(一般Pattern是正则表达式),
      ${var##Pattern}：删除从$var前端开始最长匹配$Pattern的字符串(一般Pattern是正则表达式)
            echo `basename $0`          # 取基本的脚本名(不包含全路径).
            echo "${0##*/}"             # 脚本名(不包含全路径)
            
            filename=test.data
            echo "${filename##*.}"      # data
                                        # 文件的扩展名.
                                        
    6.${parameter}：和$parameter是相同的，都是表示变量parameter的值
                            
```

## declare

```shell
    1. -f :会打印出　declare -f 前面的所有定义的函数
    2. -i :将变量申明为整数
            declare -i var1   # var1是一个整数.
            var1=2367
            echo "var1 declared as $var1"
            var1=var1+1       # 整数声明后，不需要使用'let'.
            
    3.-r : 将变量申明为只读
            declare -r var2=13.36         # 'declare'允许设置变量的属性，同时也给变量赋值.
            var2=13.37                    # 试图更改只读变量的值引起错误，并且从脚本退出. 
                                       
    4.-a : 声明为数组
                declare -a indices
```

## 双括号结构

```shell
    用((...))结构来使用C风格操作符来处理变量
    
    (( a = 23 ))  # 以C风格来设置一个值，在"="两边可以有空格.
    echo "a (initial value) = $a"
    
    (( a++ ))     # C风格的计算后自增.
    echo "a (after a++) = $a"
```

## shell脚本编程命令

```shell
    1. basename:用于打印目录或者文件的基本名称
        显示当前目录名(只含一个路劲名,不是绝对路劲)
            > basename `pwd`
            
    2.dirname：用于截取目录
    
    3.eval
        eval 命令将会首先扫描命令行进行所有的置换,然后再执行该命令.该命令适用于那些一次扫描无法实现其功能的变量,
        该命令对变量进行两次扫描.这些需要进行两次扫描的变量有时被称为复杂变量.
        eval命令即可以用于回显简单变量，也可以用于回显复杂变量.
            a='my'
            b='site'
            my_site='my site www.361way.com'
            eval echo '$'"$a"_"$b"
            eval echo '$'{"$a"_"$b"}
            
    4.echo 
        选项：
            -e : 打印出转义字符串
            -n : 不产生新行
        
                            
```

### 文件测试操作符

如果下面的条件成立返回真。[demo15](./example/demo15)

| 操作符 | 描述 |
| ---- | ---- |
| -e | 文件存在 |
| -r | 文件是否可读 (指运行这个测试命令的用户的读权限) |
| -w | 文件是否可写 (指运行这个测试命令的用户的读权限) |
| -x | 文件是否可执行 (指运行这个测试命令的用户的读权限) |
| -f | 文件是一个普通文件(不是一个目录或是一个设备文件) |
| -s | 文件大小不为零 |
| -d | 文件是一个目录 |
| -b | 文件是一个块设备(软盘，光驱，等等。) |
| -c | 文件是一个字符设备(键盘，调制解调器，声卡，等等。) |
| -p | 文件是一个管道 |
| -h | 文件是一个符号链接 |
| -L | 文件是一个符号链接 |
| -S | 文件是一个socket |
| -t | 文件(描述符)与一个终端设备相关。|
| -g | 文件或目录的设置-组-ID(sgid)标记被设置。 |
| -u | 文件的设置-用户-ID(suid)标志被设置 |
| -k | 粘住位设置 |
| -N | 文件最后一次读后被修改 |
| f1 -nt f2 | 文件f1比f2新 |
| f1 -ot f2 | 文件f1比f2旧 |
| f1 -ef f2 | 文件f1和f2 是相同文件的硬链接 |
| ! | "非" -- 反转上面所有测试的结果(如果没有给出条件则返回真)。|

例子：
    file="demo15"
    if [ -r $file ]
    then
       echo "文件可读"
    else
       echo "文件不可读"

**注意⚠️**

1. `-t` 这个测试选项可以用于检查脚本中是否标准输入 ([ -t 0 ])或标准输出([ -t 1 ])是一个终端。
1. `-g` 如果一个目录的sgid标志被设置，在这个目录下创建的文件都属于拥有此目录的用户组，而不必是创建文件的用户所属的组。
    这个特性对在一个工作组里的同享目录很有用处.
    
### 比较操作符

二元比较操作符比较两个变量或是数值。注意整数和字符串比较的分别。

**整数比较**

[demo16](./example/demo16)

| 比较操作符 | 描述 | 例子 |
| ---- | ---- | ---- |
| `-eq` | 等于 | `if [ "$a" -eq "$b" ]` |
| `-ne` | 不等于 | `if [ "$a" -ne "$b" ]` | 
| `-gt` | 大于 | `if [ "$a" -gt "$b" ]` |
| `-ge` | 大于等于 | `if [ "$a" -ge "$b" ]` |
| `-lt` | 小于 | `if [ "$a" -lt "$b" ]` |
| `-le` | 小于等于 | `if [ "$a" -le "$b" ]` |
| `<` | 小于(在双括号里使用) | `(("$a" < "$b"))` |
| `<=` | 小于等于 (在双括号里使用) | `(("$a" <= "$b"))` |
| `>` | 大于 (在双括号里使用) | `(("$a" > "$b"))` |
| `>=` | 大于等于(在双括号里使用) | `(("$a" >= "$b"))` |

**字符串比较**

| 比较操作符 | 描述 | 例子 |
| ---- | ---- | ---- |
| = | 等于 | `if [ "$a" = "$b" ]` |
| == | 等于，它和=是同义词。 | `if [ "$a" == "$b" ]` |
| != | 不相等，操作符在[[ ... ]]结构里使用模式匹配. | `if [ "$a" != "$b" ]` |
| < | 小于，依照ASCII字符排列顺序，注意"<"字符在[ ] 结构里需要转义 | `if [[ "$a" < "$b" ]]` `if [ "$a" \< "$b" ]` |
| > | 大于，依照ASCII字符排列顺序，注意">"字符在[ ] 结构里需要转义. | `if [[ "$a" > "$b" ]]` `if [ "$a" \> "$b" ]`| 
| -z | 字符串为"null"，即是指字符串长度为零。 | - |
| -n | 字符串不为"null"，即长度不为零。 | - |

**混合比较**

| 比较操作符 | 描述 | 例子 |
| ---- | ---- | ---- |
| -a | 逻辑与，如果exp1和exp2都为真，则exp1 -a exp2返回真。 | `if [ "$exp1" -a "$exp2" ]` |
| -o | 逻辑或，只要exp1和exp2任何一个为真，则exp1 -o exp2 返回真。 | `if [ "$exp1" -o "$exp2" ]` |

在一个混合测试中，把一个字符串变量引号引起来可能还不够。如果$string变量是空的话，表达式`[ -n "$string" -o "$a" = "$b" ]`
在一些Bash版本中可能会引起错误。安全的办法是附加一个外部的字符串给可能有空字符串变量比较的所有变量，
`[ "x$string" != x -o "x$a" = "x$b" ]` (x字符可以互相抵消)

## 操作字符串

```shell
    1. 索引
        expr index $string $substring 在字符串$string中$substring第一次出现的数字位置
            String=abcABC123ABCabc
            echo `expr index "$String" C12`             # 6 , C 字符的位置.
                                                            
    2. 字串提取
        ${string:position} 把$string中从第$postion个字符(postion从0开始)开始字符串提取出来。
            如果$string是"*"或"@"，则表示从位置参数中提取第$postion后面的字符串。
        ${string:position:length} 把$string中$postion个字符后面的长度为$length的字符串提取出来
        
              # 字串提取
              String=abcABC123ABCabc
              #       0123456789.....
              #       以0开始计算. 
              echo ${String:0}                            # abcABC123ABCabc
              
              ./escaped.sh 1 2 3
              echo ${*:1}      # 1 2 3
              
              ./escaped.sh 1 2 3
              echo ${*:0}      ./escaped.sh 1 2 3

              
              从字符串的右边结尾处提取
                echo ${String:(-4)}                         # Cabc 
                
    3.字串移动
      
          ${string#substring}从$string左边开始，剥去最短匹配$substring子串。
          ${string##substring}从$string左边开始，剥去最长匹配$substring子串。
          ${string%substring} 从$string结尾开始，剥去最短匹配$substring子串。
          ${string%%substring}从$string结尾开始，剥去最长匹配$substring子串。
          
          String=abcABC123ABCabc
          #       ├----┘     ┆
          #       └----------┘
          
          echo ${String#a*C}      # 123ABCabc
          # 剥去匹配'a'到'C'之间最短的字符串.
          
          echo ${String##a*C}     # abc
          # 剥去匹配'a'到'C'之间最长的字符串.
          
          
          String=abcABC123ABCabc
          #       ┆           ||
          #       └------------┘
          
          echo ${String%b*c}      # abcABC123ABCa
          # 从$String后面尾部开始，剥去匹配'a'到'C'之间最短的字符串.
          
          echo ${String%%b*c}     # a
          # 从$String后面尾部开始，剥去匹配'a'到'C'之间最长的字符串.
                                 
```

## shell 流程控制

```shell
    1.for 循环
        for循环一般格式为：
            for var in item1 item2 ... itemN
            do
                command1
                command2
                ...
                commandN
            done
            
        例子:
            for loop in 1 2 3 4 5
            do
                echo "The value is: $loop"
            done
            
            结果:
                The value is: 1
                The value is: 2
                The value is: 3
                The value is: 4
                The value is: 5
                
    2.while语句
        while condition
        do
            command
        done
        
        例子:
           var0=0
           LIMIT=10
           
           while [ "$var0" -lt "$LIMIT" ]
           do
             echo -n "$var0 "        # -n 将会阻止产生新行。
             var0=`expr $var0 + 1`   # var0=$(($var0+1))  也可以。
                                     # var0=$((var0 + 1)) 也可以。
                                     # let "var0 += 1"    也可以。
           done                      # 使用其他的方法也行。
            
        结果:
                1 2 3 4 5 6 7 8 9
                
            多条件判断
                    while ( [ "$var1" == end ] || [ "$var1" == begin ] )  #"$var1" 与 "[" 要有空格,"$var1" 与 "==" 要有空格
                    do
                    echo "Input variable #1 (end to exit) "
                        read var1        #从键盘上读入
                        echo "variable #1 = $var1"
                    done
                    exit 0          
                          
                          
        无限循环:
                while :
                do
                    command
                done
                或者
                
                while true
                do
                    command
                done
                
    3.until 循环
    
        until循环执行一系列命令直至条件为真时停止,until循环与while循环在处理方式上刚好相反,
        条件可为任意测试条件,测试发生在循环末尾，因此循环至少执行一次
            until condition
            do
                command
            done
            
    4.case
    
         case 值 in
         模式1)
             command1
             ;;
         模式2）
             commandN
             ;;
          *)  
            command1
             ;;
         esac
            
            while :
            do
                echo -n "Input a number between 1 to 5: "
                read aNum
                case $aNum in
                    1|2|3|4|5) echo "Your number is $aNum!"
                    ;;
                    *) echo "You do not select a number between 1 to 5, game is over!"
                        break   # 退出while循环
                    ;;
                esac
            done
        
```

## 函数

```shell
    
    shell中函数的定义格式如下：
        [ function ] funname [()]
        
        {
        
            action;
        
            [return int;]
        
        }
        
        1、可以带function fun() 定义,也可以直接fun() 定义,不带任何参数。
        2、参数返回，可以显示加：return 返回，如果不加，将以最后一条命令运行结果，作为返回值。
        
        例子:
            funWithReturn(){
                aNum=1
                anotherNum=2
                return $(($aNum+$anotherNum))
            }
            funWithReturn
            echo "输入的两个数字之和为 $? !"
            
        函数返回值在调用该函数后通过 $? 来获得.
        注意：所有函数在使用前必须定义。这意味着必须将函数放在脚本开始部分，直至shell解释器首次发现它时，才可以使用。
        调用函数仅使用其函数名即可。
        
        在Shell中，调用函数时可以向其传递参数。在函数体内部，通过 $n 的形式来获取参数的值，例如，$1表示第一个参数，$2表示,
        当n>=10时，需要使用${n}来获取参数
        
            funWithParam(){
                echo "第一个参数为 $1 !"
                echo "第二个参数为 $2 !"
                echo "第十个参数为 $10 !"
                echo "第十个参数为 ${10} !"
                echo "第十一个参数为 ${11} !"
                echo "参数总数有 $# 个!"
                echo "作为一个字符串输出所有参数 $* !"
            }
            funWithParam 1 2 3 4 5 6 7 8 9 34 73
            
        局部变量:
            如果变量用local来声明，那么它只能在该变量声明的代码块(block of code)中可见，这个代码块就是局部"范围"
        
                            
```

# shell 注意事项

```shell
    1. =号的左右两边都不能有空白符
            # 如果有一个空白符会怎么样？
            #
            #  如果用 "VARIABLE =value",
            #              ^
            #+ 脚本会以为"VARIABLE"是一个命令并且此命令带了一个参数"=value"。
            # 
            #  如果用 "VARIABLE= value",
            #               　　^
            #+ 脚本会以为"value"是一个命令，
            #+ 并且把环境变量"VARIABLE"赋为空值：""。
            
    2.　echo $hello和echo "$hello"产生不同的输出,把变量引起来会保留空白字符.
    
        hello="A B  C   D"
        echo $hello   # A B C D
        echo "$hello" # A B  C   D
 
        
    3.　在单引号中的变量引用会被禁止,字符"$"会仅仅被认为是一个普通的字符，而不是变量的前缀.
        echo '$hello'  # $hello
        
    4.　在同一行里用空白字符隔开为多个变量赋值是可以的,
            警告：这可能减少可读性，并且可能是不可移植的,在老版本的sh中这可能会引起问题
        
        var1=21  var2=22  var3=$V3
        echo
        echo "var1=$var1   var2=$var2   var3=$var3"

    5. 如果给变量赋的值中有空白字符，引号是必须的
            other_numbers="1 2 3"
            echo "other_numbers = $other_numbers"   # other_numbers = 1 2 3
            
    6.
        uninitialized_variable=23       # 赋值
        unset uninitialized_variable    # 销毁变量.
        echo "uninitialized_variable = $uninitialized_variable"
                                        # uninitialized_variable =
                                          结果仍然是null值.
                                          
    7.如果要进行shell中变量的加减法,必须每次计算时都要加上 let ,或则可以 declare -i 声明为一个整数,以后计算时就不需要加上 let
    
    8. suffix=$(date +%s)  和　suffix=`date +%s` 　效果一样
    
    9.判断语句 [ "$var1" == end ]  
      "$var1" 与 "[" 要有空格,"$var1" 与 "==" 要有空格

```
