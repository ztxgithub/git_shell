## sed

### sed 概要

```shell
    1.stream editor,流编辑器,sed基本上就是玩正则模式匹配
```

### sed 要点
```shell
    1. sed "/g"  /g :表示一行上的替换 所有的匹配
    2. sed 后面最好用 双引号"s/匹配的字符串/替换的字符串/g" , 不要用 单引号's/匹配的字符串/替换的字符串/g'
    
```

### 参数
```shell
    (1) -i :直接修改文件内容
            例如:
                > sed -i "s/my/Hao Chen's/g" pets.txt
    
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
                
```
