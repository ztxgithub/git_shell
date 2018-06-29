# 其他命令

## awk

### awk概要

```shell
    awk就是把文件逐行的读入，以空格为默认分隔符将每行切片，切开的部分再进行各种分析处理
    使用方法：
            awk 'pattern {action}' filenames
            
            'program text'
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
            length:代表每一行的长度
        
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
                        
          (3) 指定多个分隔符(或|)
                >  awk -F '[:;]' '{print $0,$1,$2,$3,$4}' OFS="|" ./awk_example
                结果：
                    aaa;bbb:ccc:ddd;eee|aaa|bbb|ccc|ddd
                    
                awk_example文件内容:
                    aaa;bbb:ccc:ddd;eee
                    
    字符串匹配:
            ~ :表示模式开始
            /pattern/ :pattern是模式,可以是正则表达式
            
            awk_example文件内容:
                    aaa:bbb:ccc:ddd:eee
                    aaa:bbb:ccc:ddd:eee
                    aaa:bbb:rcc:ddd:eee
                    aaa:bbb:crc:ddd:eee
                    aaa:bbb:ccc:ddd:eee
                    
         (1) 根据分隔符FS(:),匹配第三列为“ccc”
            > awk -F : '$3 ~ /ccc/ {print NR,$1,$2,$3}' ./awk_example
            结果：
                1 aaa bbb ccc
                2 aaa bbb ccc
                5 aaa bbb ccc
                
        (2) 根据分隔符FS(:),检测每一行匹配为“ccc”
         > awk -F : '/ccc/ {print NR,$1,$2,$3}' ./awk_example
         结果：
             1 aaa bbb ccc
             2 aaa bbb ccc
             3 aaa bbb rcc
             4 aaa bbb crc
             5 aaa bbb ccc
             
         (3) 根据分隔符FS(:),检测每一行匹配为“ccc”或则"rcc"
                  > awk -F : '$3 ~ /ccc|rcc/ {print NR,$1,$2,$3}' ./awk_example
                  结果：
                     1 aaa bbb ccc
                     2 aaa bbb ccc
                     3 aaa bbb rcc
                     5 aaa bbb ccc
                     
         (4) 模式取反，根据分隔符FS(:),检测每一行匹配非“ccc”
                           > awk -F : '$3 !~ /ccc/ {print NR,$1,$2,$3}' ./awk_example    (注意"!~" 之间没有空格)
                           结果：
                              1 aaa bbb ccc
                              2 aaa bbb ccc
                              3 aaa bbb rcc
                              5 aaa bbb ccc
                              
    拆分文件:
         (1) 根据分隔符FS(:)，指定某一列，将该列下值相同的行保存在 以该值为文件名中。
                    > awk -F : '{print > $3}' ./awk_example
                    结果：
                    > ls
                    ccc  crc  rcc
                    
                    ccc中文件的内容为：
                        aaa:bbb:ccc:ddd:eee
                        aaa:bbb:ccc:ddd:eee
                        aaa:bbb:ccc:ddd:eee
                        
         (2) 根据分隔符FS(:)，指定某一列，将该列下值相同的某几列保存在 以该值为文件名中。
                    > awk -F : '{print $1,$2,$3 > $3}' ./awk_example  
                    结果：
                    > ls
                    ccc  crc  rcc
                    
                    ccc中文件的内容为：
                        aaa bbb ccc
                        aaa bbb ccc
                        aaa bbb ccc
                        
         (3) awk 'NR!=1{if($6 ~ /TIME|ESTABLISHED/) print > "1.txt";
             else if($6 ~ /LISTEN/) print > "2.txt";
             else print > "3.txt" }' netstat.txt
             
    统计:
        (1) 计算当前目录下所有文件大小总和
                > ls -l | awk '{my_sum+=$5} END {print my_sum}'
                结果：
                    160
                    
                > ls -l
                结果：
                    -rw-rw-r-- 1 jame jame 100 Mar  6 19:45 awk_example
                    -rw-rw-r-- 1 jame jame  36 Mar  6 20:18 ccc
                    -rw-rw-r-- 1 jame jame  12 Mar  6 20:18 crc
                    -rw-rw-r-- 1 jame jame  12 Mar  6 20:18 rcc
                    
        (2) 统计某一列值相同的个数
                > awk -F : 'NR!=1{a[$3]++} END {for(i in a) print i "->" a[i]};' ./awk_example 
                结果:
                    rcc->1
                    crc->1
                    ccc->2
                    
                >  awk 'NR!=1{a[$6]++;} END {for (i in a) print i ", " a[i];}' netstat.txt
                结果:
                    TIME_WAIT, 3
                    FIN_WAIT1, 1
                    ESTABLISHED, 6
                    FIN_WAIT2, 3
                    LAST_ACK, 1
                    LISTEN, 4
                    
                查看每一个用户占多少内存
                >  ps aux | awk 'NR!=1{a[$1]+=$6;} END {for(i in a) print i "->" a[i]"KB";}'
                结果:
                    jame->2302408KB
                    syslog->3396KB
                    
    awk脚本:
        BEGIN{ 这里面放的是执行前的语句 }
        END {这里面放的是处理完所有的行后要执行的语句 }
        {这里面放的是处理每一行时要执行的语句}
        
        实例:
            score.txt 文件内容
                Marry   2143 78 84 77
                Jack    2321 66 78 45
                Tom     2122 48 77 71
                Mike    2537 87 97 95
                Bob     2415 40 57 62
                
            cal.awk 内容
                  #!/usr/bin/awk -f    （绝对路径很关键）
                    #运行前
                    BEGIN {
                            math = 0
                            english = 0
                            computer = 0
                          
                            printf"NAME    NO   MATH  ENGLISH  COMPUTER  ToTAL\n"
                            printf"-------------------------------------------\n"
                            
                            
                          }
                    
                    #运行中
                    {
                        math+=$3
                        english+=$4
                        computer+=$5
                        printf "%-6s %-6s %4d %8d %8d %8d\n", $1, $2, $3,$4,$5, $3+$4+$5
                    
                    }
                    
                    #运行后
                    END{
                            printf "---------------------------------------------\n"
                            printf "  TOTAL:%10d %8d %8d \n", math, english, computer
                            printf "AVERAGE:%10.2f %8.2f %8.2f\n", math/NR, english/NR, computer/NR
                       }
                    
                    
            > awk -f cal.awk ./score.txt    
            或则
            > ./cal.awk ./score.txt         (其中cal.awk的内容 #!很关键)
            
            
    环境变量：
            如何与环境变量进行交互（如何读取环境变量）
            (1) 使用-v (-v var=value   assigns value to program variable var)
            (2) 使用ENVIRON(前提条件是在shell终端中要 export该变量)
            
            实例:
                > x=10 y=5
                > export y
                >echo $x $y
                结果 10 5
                > awk -v val=$x '{print $1,$2,$3,val,ENVIRON["y"]}' ./score.txt 
                结果:
                    Marry 2143 78 10 5
                    Jack 2321 66 10 5
                    Tom 2122 48 10 5
                    Mike 2537 87 10 5
                    Bob 2415 40 10 5
        
          
                
```

