## sed

### sed 概要

```shell
    1.stream editor,流编辑器,sed基本上就是玩正则模式匹配
```

### sed 要点
```shell
    1. sed "/g"  /g :表示一行上的替换 所有的匹配
    2. 使用&来当做被匹配的变量
    3.sed  "s/my/Hao Chen's/" pets.txt 没有最后没有 /g 则只会替换第一个匹配的字符串.
    
```

### 参数
```shell
    (1) -i :直接修改文件内容
            例如:
                > sed -i "s/my/Hao Chen's/g" pets.txt
                
    (2) -e script, --expression=script :后面的语句当成脚本命令
    
```
### sed 使用

```shell
    原来的内容 pets.txt:
        This is my cat
          my cat's name is betty
        This is my dog
          my dog's name is frank
          
          
    1.用s命令替换
        > sed "s/匹配的字符串/替换的字符串/g" filename(文件名)
        例如:
            > sed "s/my/Hao Chen's/g" pets.txt
            结果:
                This is Hao Chen's cat
                  Hao Chen's cat's name is betty
                This is Hao Chen's dog
                  Hao Chen's dog's name is frank
                  
    2.在每一行 最前面 加点东西
        > sed "s/^/添加字符串/g" filename(文件名)
        例如:
            > sed "s/^/#/g" pets.txt
            结果:
                #This is my cat
                #    my cat's name is betty
                #This is my dog
                #    my dog's name is frank
                
    3.在每一行 最后面 加点东西
        > sed "s/$/添加字符串/g" filename(文件名)
        例如:
            > sed "s/$/ --- /g" pets.txt
            结果:
                This is my cat --- 
                    my cat's name is betty --- 
                This is my dog --- 
                    my dog's name is frank --- 
                    
    4.替换指定行数的内容
        > sed "start_line,end_line s/匹配的字符串/替换的字符串/g" filename(文件名)
        例如:
            (1) 只替换第3到第4行的文本
                    > sed "3,4s/my/your/g" pets.txt
                    结果:
                        This is my cat
                            my cat's name is betty
                        This is your dog
                            your dog's name is frank
                            
            (2) 只替换第3的文本
                    > sed "3s/my/your/g" pets.txt
                    结果:
                        This is my cat
                            my cat's name is betty
                        This is your dog
                            my dog's name is frank
                            
    5.只替换每一行的某一个符合匹配条件的字符串
        > sed "s/匹配的字符串/替换的字符串/匹配到的序号(需要被替换)" filename(文件名)
        例如:
            (1) 只替换每一行的第二个s
                    >  sed "s/s/S/2" pets.txt 
                    结果:
                        This iS my cat
                            my cat's name iS betty
                        This iS my dog
                            my dog's name iS frank
                            
            (2) 只替换第一行的第3个以后(包括第3个)的所有s：
                    > sed "s/s/S/3g" pets.txt
                    结果:
                        This is my cat  SSS
                            my cat's name is betty  SSS
                        This is my dog  SSS
                            my dog's name is frank  SSS
    6.多种匹配替换
        (1) 把第一行到第三行的my替换为your,第三行到最后一行This替换为That
                > sed '1,3s/my/your/g; 3,$s/This/That/g' pets.txt 
                结果:
                    This is your cat   your cat's name is betty  
                    This is your dog   your dog's name is frank  
                    That is your fish, your fish's name is george
                    That is my goat, my goat's name is adam
                    
    7.使用&来当做被匹配的变量
        > sed 's/my/[&]/g' pets.txt
        结果:
            This is [my] cat   [my] cat's name is betty  
            This is [my] dog   [my] dog's name is frank  
            That is your fish, your fish's name is george
            That is [my] goat, [my] goat's name is adam
            
    8.圆括号匹配
        圆括号括起来的正则表达式所匹配的字符串会可以当成变量来使用，sed中使用的是\1,\2…
        主要要用到"\(" 转义字符
            例如:
                > sed 's/This is my \([^,]*\),.*is \(.*\)/\1:\2/g' pets.txt 
               结果:
                    cat:betty  
                    dog:frank  
                    fish:george
                    goat:adam
                    
    9.N命令
        把下一行的内容纳入当成缓冲区做匹配
        把原文本中的偶数行纳入奇数行匹配，而s只匹配并替换一次
        原来的内容:
            This is my cat, my cat's name is betty  
            This is my dog, my dog's name is frank  
            This is my fish, my fish's name is george
            This is my goat, my goat's name is adam
            
        > sed 'N;s/my/your/' pets.txt 
        结果:
          This is your cat, my cat's name is betty  
          This is my dog, my dog's name is frank  
          This is your fish, my fish's name is george
          This is my goat, my goat's name is adam
          
    10.a命令和i命令
        (1)a命令就是append,在最后一行后追加一行
            >  sed "$ a This is my monkey, my monkey's name is wukong" pets.txt
            
        (2)  i命令就是insert ,第1行前插入一行（insert）
           >  sed "1 i a This is my monkey, my monkey's name is wukong" pets.txt
           
        (3) 用匹配来添加文本,匹配到/fish/后就追加一行(sed a命令)
                >  sed "/fish/a This is my monkey, my monkey's name is wukong" pets.txt
                
    11.c命令(替换匹配行)
       (1) 将第二行进行替换
            > sed "2 c This is my monkey, my monkey's name is wukong" my.txt
            
       (2) 将匹配字符串所对应的行进行替换
            匹配到fish字符串对应的行替换为 "This is my monkey, my monkey's name is wukong"
            > sed "/fish/c This is my monkey, my monkey's name is wukong" my.txt
            
    12.d命令(删除匹配行)
        (1) 将匹配字符串所对应的行进行删除
            >  sed '/fish/d' my.txt
        (2) 删除第二行的数据
            > sed '2d' my.txt
        (3) 删除第二行以后的所有行(包括第二行)
            > sed '2,$d' my.txt
            
    13. p命令(打印输出,当成grep式的命令)
            # 匹配fish并输出，可以看到fish的那一行被打了两遍，
            # 这是因为sed处理时会把处理的信息输出
            $ sed '/fish/p' my.txt
            This is my cat, my cat's name is betty
            This is my dog, my dog's name is frank
            This is my fish, my fish's name is george
            This is my fish, my fish's name is george
            This is my goat, my goat's name is adam
             
            # 使用n参数就好了
            $ sed -n '/fish/p' my.txt
            This is my fish, my fish's name is george
             
            # 从一个模式到另一个模式
            $ sed -n '/dog/,/fish/p' my.txt
            This is my dog, my dog's name is frank
            This is my fish, my fish's name is george
             
            #从第一行打印到匹配fish成功的那一行
            $ sed -n '1,/fish/p' my.txt
            This is my cat, my cat's name is betty
            This is my dog, my dog's name is frank
            This is my fish, my fish's name is george
```

## sed 进阶

```shell
    1.Address
        [address[,address]]{cmd}: 其中cmd 可以是 s命令,a命令,d命令等
        (1) 将匹配到dog后3行进行操作
            > sed '/dog/,+3s/^/# /g' pets.txt
            结果:
                This is my cat, my cat's name is betty  
                # This is my dog, my dog's name is frank  
                # This is my fish, my fish's name is george
                # This is my goat, my goat's name is adam
                
    2.命令打包(cmd可以是多个，它们可以用分号分开，可以用大括号括起来作为嵌套命令)
        (1) 对第3行到第6行，执行命令/This/d  (匹配到This字符所对应的行进行删除)
                > sed '3,6{/This/d}' pets.txt
        (2) 对第3行到第6行，匹配/This/成功后，再匹配/fish/，成功后执行d命令
                > sed '3,6{/This/{/fish/d}}' pets.txt
        
```
