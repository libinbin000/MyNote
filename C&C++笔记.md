# 2018/9/6
## m4
m4  类似于C语言的预处理器,用来处理宏的。
>m4define(`OS', `operating system')⇒OS⇒operating system

***

## -lpthread 
多线程库pthread，但是编译时总是提示
>“undefined reference to 'pthread_create'”的错误

只要在gcc命令最后面加上-lpthread就可以了,而且在redhat5中-lpthread可以加在-Wall的后面，但是在Ubuntu下必须加在最后才行。

>all:  
    gcc -g -Wall   -I/usr/local/include -o transmitter transmitter.c IniFileRW.c  -lpthread   
>clean:  
    rm -rf *.o transmitter  

***
## 记录一个makefile
用于编译libev项目的，一直不知道如何连接动态库.so

<code>
CC=gcc

TARGET=test

CFLAGS= -Wall

SRCS=main.c\

OBJS=$(SRCS:.c=.o)

INCLUDES=-I./libev\

LIB_PATHS=-L./libev/.libs

LIBS=-lev

>在gcc末尾加入以下代码

SO=-Wl,-rpath=./libev/.libs

$(TARGET):$(OBJS)
	$(info >>>>>>>>>>>>>>>>>>>>>>>)
	$(CC) $^ -o $@	$(CFLAGS) $(INCLUDES) $(LIB_PATHS) $(LIBS) $(SO)

%.o : %.c
	$(info >>>>>>>>>>>>>>>>>>>>>>>%.o)
	$(CC) -c $< $(CFLAGS) $(INCLUDES) $(LIB_PATHS) $(LIBS)

clean:
	rm -rf *.out *.o    #清除中间文件及生成文件

.PHONY:clean

</code>

***