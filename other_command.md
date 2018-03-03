# 其他命令

## awk

### awk概要

```shell
    awk就是把文件逐行的读入，以空格为默认分隔符将每行切片，切开的部分再进行各种分析处理
    使用方法：
            awk '{pattern + action}' {filenames}
            
            pattern:awk在数据中查找的内容
            action: 是在找到匹配内容时所执行的一系列命令,如果action省略，则默认是{ print }
            
    内建变量：
            FS:输入字段分隔符(默认是空格或Tab)
            RS： 输入的记录分隔符， 默认为换行符
            OFS：输出字段分隔符， 默认也是空格
            ORS：输出的记录分隔符，默认为换行符
            $0 : 代表整行的内容
            $1~$n: 第n个字段(第几列)，字段间由FS分隔
            NF：当前记录中的字段个数（有多少列）
            NR： 已经读出的记录数，就是行号，从1开始，如果有多个文件话，这个值也是不断累加中。
            FNR：当前记录数，与NR不同的是，这个值会是各个文件自己的行号
            FILENAME：当前输入文件的名字
        
```

### awk命令

```shell
    > netstat -ntulp | awk '{print $1,$4}'
    (1) 单引号中的被大括号括着的就是awk的语句，注意，其只能被单引号包含
    (2) 其中的$1..$n表示第几列。注：$0表示输出所有列
    结果:
        tcp 0.0.0.0:139
        tcp 127.0.0.1:63342
        tcp 127.0.0.1:41521
        tcp 127.0.1.1:53
        tcp 0.0.0.0:22
        tcp 0.0.0.0:1883
        tcp 0.0.0.0:445
        tcp 127.0.0.1:8125
        
    格式化输出:和C语言的printf一样
        > netstat -ntulp | awk '{printf "%-8s %-8s %-8s %-18s %-22s %-15s\n",$1,$2,$3,$4,$5,$6}'
        
        
    过滤：
        1.筛选出第三列的值为0 && 第六列字符串的值为 "LISTEN"的行
            > netstat -ntulp | awk '$3==0 && $6=="LISTEN"'
            (1) 其中的“==”为比较运算符。其他比较运算符：!=, >, <, >=, <=
            结果：
                tcp        0      0 0.0.0.0:139             0.0.0.0:*               LISTEN      -               
                tcp        0      0 127.0.0.1:63342         0.0.0.0:*               LISTEN      3530/java       
                tcp        0      0 127.0.0.1:41521         0.0.0.0:*               LISTEN      3946/python2    
                tcp        0      0 127.0.1.1:53            0.0.0.0:*               LISTEN      -               
                tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -               
                tcp        0      0 0.0.0.0:1883            0.0.0.0:*               LISTEN      -   
                
        2.筛选出Send-Q(第三列)大于0,并打印出所有列信息 的行
            > netstat -ntulp | awk '$3>0 {print $0}'
            
        3.筛选出Send-Q(第三列)大于0,并打印出所有列信息 的行以及表头
                    > netstat -ntulp | awk '$3>0 || NR==1 {print $0}'
                    
    输出行号：
        > netstat -ntulp | awk '$3==0 && $6=="LISTEN" {printf "%-8s %-8s %-8s %-18s %-22s %-15s\n",NF,NR,FNR,$4,$5,$6}'
        结果：
            7        3        3        0.0.0.0:139        0.0.0.0:*              LISTEN         
            7        4        4        127.0.0.1:63342    0.0.0.0:*              LISTEN         
            7        5        5        127.0.1.1:53       0.0.0.0:*              LISTEN         
            7        6        6        0.0.0.0:22         0.0.0.0:*              LISTEN         
            7        7        7        0.0.0.0:1883       0.0.0.0:*              LISTEN         
            7        8        8        0.0.0.0:445        0.0.0.0:*              LISTEN         
            7        9        9        127.0.0.1:8125     0.0.0.0:*              LISTEN         
            7        10       10       127.0.0.1:8125     0.0.0.0:*              LISTEN         
            7        11       11       127.0.0.1:6942     0.0.0.0:*              LISTEN         
            7        12       12       0.0.0.0:19999      0.0.0.0:*              LISTEN         
            7        13       13       :::3306            :::*                   LISTEN         
            7        14       14       :::139             :::*                   LISTEN         
            7        15       15       :::22              :::*                   LISTEN         
            7        16       16       :::1883            :::*                   LISTEN         
            7        17       17       :::445             :::*                   LISTEN         
            7        18       18       ::1:8125           :::*                   LISTEN         
            7        19       19       :::19999           :::*                   LISTEN 
            
    指定分隔符：
    
          (1)  以 “：” 为分隔符，分割每一行
            > awk 'BEGIN{FS=":"} {print $1,$3,$6}' /etc/passwd
            等同于
            > awk -F : '{print $1,$3,$6}' /etc/passwd
            
            结果：
                root 0 /root
                daemon 1 /usr/sbin
                bin 2 /bin
                sys 3 /dev
                sync 4 /bin
                games 5 /usr/games
                
          (2) 根据输入分隔符(FS=":")对输入行(文件里的每一行)进行分割，要进行按规定输出内容(输出分隔符为OFS="|")
                > awk -F : '{print $1,$3,$6}' OFS="|" /etc/passwd
                
                    结果:
                        root|0|/root
                        daemon|1|/usr/sbin
                        bin|2|/bin
                        sys|3|/dev
                        sync|4|/bin
                        games|5|/usr/games
                        man|6|/var/cache/man
                        lp|7|/var/spool/lpd
                        mail|8|/var/mail
                        
          (3) 指定多个分隔符
                >  awk -F '[:;]' '{print $0,$1,$2,$3,$4}' OFS="|" ./awk_example
                结果：
                    aaa;bbb:ccc:ddd;eee|aaa|bbb|ccc|ddd
                    
                awk_example文件内容:
                    aaa;bbb:ccc:ddd;eee
```

