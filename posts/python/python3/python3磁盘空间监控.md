### 软硬件环境

* python3
* apscheduler

### 前言

在做频繁操作磁盘的python项目时，经常会碰到磁盘空间不足的情况，这个时候，工程应该要有自己的处理模块，当磁盘利用率到达某个点时，发出警告并停止程序的运行。本文就利用Python3中的apscheduler模块来处理这样的问题。

### 代码实践

```
import os
import sys
import signal
import logging

from apscheduler.schedulers.background import BackgroundScheduler
from apscheduler.triggers.interval import IntervalTrigger

# 开启磁盘空间检测
sched = BackgroundScheduler()

# 间隔5分钟开启一个检查
intervalTrigger = IntervalTrigger(minutes=5)

# 给检查任务设个id,方便任务的取消
sched.add_job(spaceMonitorJob, trigger=intervalTrigger, id='id_space_monitor')
sched.start()

# 禁止apscheduler相关信息屏幕输出
logging.getLogger('apscheduler.executors.default').propagate = False
```

方法spaceMonitorJob代码如下

```
def spaceMonitorJob():
    '''
    当磁盘(切片存储的目录)利用率超过90%,程序退出
    :return:
    '''

    try:
        st = os.statvfs('/')
        total = st.f_blocks * st.f_frsize
        used = (st.f_blocks - st.f_bfree) * st.f_frsize
    except FileNotFoundError:
        print('check webroot space error.')
        logger.error('check webroot space error.')
        
        # 移除任务，病关闭sched任务
        sched.remove_job(job_id='id_space_monitor')
        sched.shutdown(wait=False)
        sys.exit(-3)
	
    if used / total > 0.9:
        print('No enough space.')
        logger.debug('No enough space.')
        sched.remove_job(job_id='id_space_monitor')
        sched.shutdown(wait=False)

        # 杀掉进程
        os.killpg(os.getpgid(os.getpid()), signal.SIGKILL)
		
		# 退出
        exit(-3)
```