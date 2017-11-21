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

```