# 2018/9/6
## m4
m4  类似于C语言的预处理器,用来处理宏的。
>m4define(`OS', `operating system')⇒OS⇒operating system

***

##-lpthread 
多线程库pthread，但是编译时总是提示
>“undefined reference to 'pthread_create'”的错误

只要在gcc命令最后面加上-lpthread就可以了,而且在redhat5中-lpthread可以加在-Wall的后面，但是在Ubuntu下必须加在最后才行。

>all:  
    gcc -g -Wall   -I/usr/local/include -o transmitter transmitter.c IniFileRW.c  -lpthread   
>clean:  
    rm -rf *.o transmitter  

***