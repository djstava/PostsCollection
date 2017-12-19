### 软硬件环境
* Ubuntu 14.04

### 概述
在类UNIX的多任务操作系统中，守护进程就是运行在后台的计算机程序，而不是运行在前台可以由用户直接控制。典型的守护进程一般都会在进程名后加上字母"d"，表示daemon，如常见的mysqld、telnetd、sshd等。这些进程没有控制终端，不受用户登录、注销、登出的影响，它们一直在运行着。

创建守护进程的一般步骤是这样的，首先fork出一个子进程，父进程退出，然后子进程调用setsid创建新的会话，成为守护进程，接着关闭文件描述符，改变当前目录为根目录，重设文件权限掩码，忽略SIGCHLD信号 ，最后还有必要通过日志系统来记录出错信息，方便日后查询。

最后来看一个实例，注释都写得比较清楚了，就不再赘述了。

### 编写守护进程的实例

	/*daemon.c*/
	#include <stdio.h>
	#include <time.h>
	#include <unistd.h>
	#include <signal.h>
	#include <sys/param.h>
	#include <sys/types.h>
	#include <sys/stat.h>

	void initDaemon(void)
	{
    int pid;
    int i;

    /* terminate parent process */
    if(pid=fork())
        exit(0);

    /* exit when fork failed */
    else if(pid< 0)
        exit(1);

    /* continue only when it it the first child process
    ** run in a new session
    */
    setsid();

    /* seperate from terminator,kill the first child process */
    if(pid=fork())
        exit(0);
    else if(pid< 0)
        exit(1);

    /* close all file descriptors */
    for(i=0;i< NOFILE;++i)
        close(i);
    chdir("/home/djstava");

    /* reset file umask */
    umask(0);
    return;
	}

	void signalUSR1(int signal)
	{
    /*handle SIGUSR1*/
    FILE *fp;
    time_t t;
    if((fp=fopen("log","a")) >=0)
    {
        t=time(0);
        fprintf(fp,"Received signal(%d),at %s\n", signal, asctime(localtime(&t)) );
        fclose(fp);
    }
	}


	int main(int argc, char ** argv)
	{
    FILE *fp;
    time_t t;
    initDaemon();

    /* ignore signal */
    signal(SIGCHLD, SIG_IGN);

    /* handle SIGUSR1 */
    signal(SIGUSR1, signalUSR1);

    while(1)
    {
        sleep(60);
        if((fp=fopen("log","a")) >=0)
        {
            t=time(0);
            fprintf(fp,"djstava at %s\n",asctime(localtime(&t)) );
            fclose(fp);
        }
    }
	}

###编译运行
gcc -o daemon daemon.c
./daemon
ps ax

sudo kill -s SIGUSR1 6942（这里的进程号每台机器不一样，根据实际情况更改）
cat /home/djstava/log

最后在文件/etc/rc.local末尾添加如下语句，使daemon程序在系统开机时就自动启动,最后需要将编译产生的daemon文件copy到/bin目录下
su -djstava -c "/bin/daemon"

最后附上一张UNIX信号表，方便查阅

![](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/linux/daemon/UNIX_signal.png)
