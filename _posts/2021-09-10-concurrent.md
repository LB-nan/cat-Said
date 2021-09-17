---
layout: post
title: Python并发、进程
categories: Python
description: python 并发
keywords: python 并发
---

并发、进程、进程间通信、消费者生产者模型等相关

### 1、并发、并行

- 并发：看起来像同时运行的就可以称之为并发
- 并行：真正的同时执行

所以: 并行一定是并发，并发不一定是并行，单核计算机肯定不能实现并行，但是可以实现并发。

### 2、多道技术

先了解单道：单道就是串行，一串，任务只能有顺序的一个一个来，当前一个任务完全执行完毕之后才可以去执行下一个。任务A读取完成然后去cpu处理，然后任务A输出，然后才可以去读取任务B，然后处理然后输出。

多道技术：在任务A输入完成之后，去cpu执行的时候，可以接着去接收任务B的输入，然后任务A的执行完成之后就可以立刻去执行任务B

```txt

单道：

  任务A输入 =》 cpu任务A处理 =》 任务A输出  =》 任务B输入 =》 任务B处理 =》 任务B输出

多道：

  任务A输入 =》cpu任务A处理 =》任务A输出
           =》任务B输入    =》cpu处理任务B =》 任务B输出

```

可以明显看出来执行效率提高了，时间线缩短了。

多道技术主要就是空间与时间上的复用，

- 当一个程序遇到IO操作的时候，操作系统会剥夺该程序的CPU执行权限。
- 当一个程序长时间占用CPU的时候，操作系统也会剥夺该程序的CPU执行权限

### 3、进程

进程表示程序正在执行的过程，程序就是一堆在硬盘里的代码。

CPU的性能是有限的，每个进程都需要占用CPU资源，想要多个进程交替执行，操作系统必须对进程进行调度，调度的规律有一些基本的算法

1. 先来先服务调度算法
2. 短作业优先调度算法
3. 时间片轮转法+多级反馈队列： 时间片（将固定的时间切分成多份，每一份就表示一个时间片）

时间片轮转法+多级反馈队列：

- 会分多个队列，假设有A/B/C 三个队列，多个进程任务进入A队列，每个分配一个时间片(假设1s)，有的进程1s执行完毕，就执行完毕了，如果没有就多执行几个时间片，如果在A队列执行多次时间片没有执行完毕就把当前的进程放到B队列，B队列执行如A一致，多次没完成就放到C队列，当A队列的进程都执行完了，执行B队列的进程期间，如果A队列进来新进程了，就秒切换A队列执行A队列里的新进程，重复以上操作。
- 队列A\B\C，越往下说明该进程任务所需的时间越长，越往下的任务的执行优先级越低
- 在Linux中可以给任务设置优先级，一次性分配多个时间片

进程的三态转换：

三态：就绪态、运行/执行态、阻塞态

1. 当一个程序创建然后提交会进入就绪态
2. 就绪态会等操作系统进行进程调度，分配时间片，执行完就完了
3. 如果在运行态中，时间片用完还没有执行完，或者进行IO操作，会进入阻塞态，然后IO操作拿到值了，或者其他行为触发了进程，然后重新进入就绪态，然后重新分配时间片，然后重新运行。重复以上过程直到运行完成，进程释放。

### 4、同步异步

同步和异步描述的是任务的提交方式

- 同步：在任务提交之后，原地等待任务的返回结果，等待过程中不做任何其他事，程序会卡住
- 异步：在任务提交之后，不原地等待任务的返回结果，直接去做其他事情，等待任务的返回结果回来了然后进行回调，程序不会卡住

### 5、阻塞非阻塞

描述的是程序的运行状态

### 6、开启进程的方式

在Windows操作系统下，创建进程一定要在`main`内创建，因为在Windows操作系统下创建进程类似于模块导入的方式，会从上往下依次执行代码。在Linux下会直接将代码拷贝一份。

创建进程就是在内存中申请一块内存空间将需要的代码放进去，一个进程对应一块内存空间，多个进程的内存空间互不干扰，进程与进程之间默认情况下无法直接交互，数据也相互隔离(子进程也不会修改主进程的数据)，需要交互可以借助其他第三方模块。

```py
from multiprocessing import Process
import time

# 开启进程的方式一
def task(name):
    print('%s is running'%name)
    time.sleep(3)
    print('%s is over' %name)


if __name__ == '__main__':
    # args：容器类型，容器类型里面哪个只有一个元素也加逗号隔开
    p = Process(target=task, args=('task进程',))
    p.start() # 开启进程
    print('主进程')


# 方式二
class MyProcess(Process):
  def run(self):
    print('running')
    time.sleep(1)
    print('done')


if __name__ == '__main__':
  p = MyProcess()
  p.start()
  print('主进程')

```

### 7、join方法

join方法是让主进程等待子进程代码运行结束之后，再继续执行，不影响其他子进程的执行。相当于把异步改为了同步。

```py
from multiprocessing import Process
import time

def task(name):
    print('%s is running'%name)
    time.sleep(3)
    print('%s is over' %name)


if __name__ == '__main__':
    # args：容器类型，容器类型里面哪个只有一个元素也加逗号隔开
    p = Process(target=task, args=('task进程',))
    p.start() # 开启进程
    p.join() # 等待子进程执行成功
    print('主进程')

```

当有多个进程的时候，进程执行顺序不是有序的，可能进程1先执行/执行完毕也可能进程3先执行/执行完毕，创建了多个进程只能代表有多个进程，不能确定进程执行的顺序就是创建的顺序。

```py
from multiprocessing import Process
import time

def task(name):
    print('%s is running'%name)
    time.sleep(3)
    print('%s is over' %name)


if __name__ == '__main__':
    # args：容器类型，容器类型里面哪个只有一个元素也加逗号隔开
    p1 = Process(target=task, args=('task进程1',))
    p2 = Process(target=task, args=('task进程2',))
    p3 = Process(target=task, args=('task进程3',))
    p1.start()  # 开启进程
    p2.start()  # 开启进程
    p3.start()  # 开启进程
    start_time = time.time()
    p1.join()
    p2.join()
    p3.join() 

    print('主进程', time.time() - start_time)  # 主进程 3.089634895324707

```

可能会迷惑为什么最后输出3.x秒，因为在等待第一个进程的1s的时候，第二三个进程也同样在执行了。

### 8、进程对象及其他方法

进程id：每个进程都有对应的操作系统分配的pid

- windows：cmd里输入`tasklist`查看，查看具体的进程可以 `tasklist |findstr pid`
- Mac：进入终端之后输入  `ps aux`查看, 查看具体的进程可以`ps aux |grep pid`

在python里面代码查看,有两种方法

```py
from multiprocessing import Process, current_process
import time
import os

def task():
    # 通过current_process().pid查看
    print('pid: %s' % current_process().pid )
    # os.getpid() 查看
    print('pid: %s' % os.getpid())
    print('子进程里获取父进程id: %s' % os.getppid())
    

if __name__ == '__main__':
    # args：容器类型，容器类型里面哪个只有一个元素也加逗号隔开
    p1 = Process(target=task)
    p1.start()  # 开启进程 
    print('主进程:', current_process().pid)
    print('主进程:', os.getpid())
    print('主进程的父进程pid:', os.getppid())

'''
# 输出结果：
主进程: 4639
主进程: 4639
主进程的父进程pid: 4638
pid: 4641
pid: 4641
子进程里获取父进程id: 4639
'''
```

杀死进程

```py
p1.terminate() # 杀死进程
```

判断当前进程是否存活

```py
print(p1.is_alive())
```

### 9、僵尸进程/孤儿进程

僵尸进程：当开设了子进程之后，该进程死后不会立刻释放，因为需要让父进程能够看到它开设的子进程的一些基本信息，如 pid 运行时间等，所有的进程都会进入僵尸进程阶段。

孤儿进程：子进程存活，父进程挂掉，子进程的pid没法回收，操作系统会开设一个功能专门回收孤儿进程。

### 10、守护进程

是生存期长的一种进程，没有控制终端。它们常常在系统引导装入时启动，仅在系统关闭时才终止。在程序里表现为随着主进程开启开启，主进程死亡而死亡的进程。

```py
from multiprocessing import Process
import time

def task():
    print('子进程正常')
    time.sleep(3)
    print('子进程GG')


if __name__ == '__main__':
    p = Process(target=task)
    p.daemon = True  # 设置为守护进程，在开启之前设置才有效
    p.start()  # 开启进程, 因为执行完子进程启动，然后主进程直接死亡，所以子进程也会跟着死亡
    print('主进程GG')

```

### 11、互斥锁

多个进程操作同一份数据的时候，会出现错乱的问题，针对这个问题，解决方案就是加锁，将并发改成串行，牺牲效率保证数据的安全。

```py
from multiprocessing import Process, Lock
import multiprocessing
import time

tickets = 1

def run(i, mutex):
    global tickets
    # 抢锁
    mutex.acquire()
    # 省略买票环节,假设下面有很多买票过程
    time.sleep(2)
    print('tickets:', tickets)
    if(tickets == 1):
        print('%s抢票成功：' % i)
    else :
        print('%s抢票失败：' % i)
    # 释放锁
    mutex.release()


if __name__ == '__main__':
    # 解决Mac的问题
    multiprocessing.set_start_method('fork')
    # 创建锁
    mutex = Lock()
    for i in range(1, 11):
        p = Process(target=run, args=('%s' %i, mutex))
        p.start()
        # 模拟需求，需要在这里操作，因为放到进程里面之后，进程之间数据隔离，不会修改到全局
        tickets = 0

'''
tickets: 1
1抢票成功：
tickets: 0
2抢票失败：
tickets: 0
3抢票失败：
tickets: 0
4抢票失败：
tickets: 0
5抢票失败：
tickets: 0
6抢票失败：
tickets: 0
7抢票失败：
tickets: 0
8抢票失败：
tickets: 0
9抢票失败：
tickets: 0
10抢票失败：
'''

```

其他还有各种锁，比如行锁，表锁....都是将并发改成串行

### 12、进程通信/消息队列

队列`Queue`模块

```py
import queue

q = queue.Queue(5) # 创建队列，可以传数字，代表队列的最大数据量，默认32767

#  存数据,如果数据存满了，后续还有数据要存放进来，程序会阻塞卡住
q.put(111)
q.put(222)

# 判断当前队列是否满了
print(q.full())

# 取
v1 = q.get()
v2 = q.get()
# 当取的时候，队列里没有数据了，程序会原地阻塞 卡住
# v3 = q.get()
# get_nowait()方法如果取不到数据会直接报错：_queue.Empty
v3 = q.get_nowait()

# 判断当前队列是否空了
print(q.empty())

# 取数据的时候，如果没有，原地等待3s，还没就报错
v4 = q.get(timeout=3)

print(v1)
print(v2)
# print(v3)

```

ps: `q.full() q.empty() q.get_nowait()`在多进程的情况下是不精确的

ps: Mac进程需要设置这个`multiprocessing.set_start_method('fork')`的问题参考：https://blog.csdn.net/wujiaqi0921/article/details/78192879

### 13、IPC机制

进程与进程之间通信

```py
# 主进程和子进程通信
from multiprocessing import Process, Queue

def producer(q):
    q.put('一条数据')
    print('hello')

if __name__ == '__main__':
  q = Queue()
  p = Process(target=producer, args=(q,))
  p.start()
  q.get()

# 进程与进程通信
from multiprocessing import Process, Queue
import time
import multiprocessing

def producer(q):
    q.put('一条数据')


def consumer(q):
    print(q.get())

if __name__ == '__main__':
    # Mac的环境有问题需要加这个
    multiprocessing.set_start_method('fork')
    q = Queue()
    p = Process(target=producer, args=(q,))
    p1 = Process(target=consumer, args=(q,))
    p.start()
    p1.start()
```

### 14、生产者消费者模型

生产者：生产/制造东西的；消费者：消费/处理东西的

生产者和消费者不是直接做交互的，需要借助媒介(消息队列)进行交互。

```py
from multiprocessing import Process, Queue
import time
import random
import multiprocessing


def producer(name, food, q):
    for i in range(5):
        data = '%s生产了%s,%s个'%(name, food, i)
        time.sleep(random.randint(1,3))
        print(data)
        q.put(data)


def consumer(name, q):
    while True:
      food = q.get()
      if food is None: break
      time.sleep(random.randint(1,3))
      print('%s吃了%s' %(name, food))

if __name__ == '__main__':
    multiprocessing.set_start_method('fork')
    q = Queue()
    p1 = Process(target=producer, args=('生产者1', '包子', q))
    p2 = Process(target=producer, args=('生产者2', '饺子', q))
    c1 = Process(target=consumer, args=('消费者1', q))
    c2 = Process(target=consumer, args=('消费者2', q))
    p1.start()
    p2.start()
    c1.start()
    c2.start()
    p1.join()
    q.put(None)
    p2.join()
    q.put(None)

```

这样的可以实现这个问题，但是需要根据生产者的数量来put一个结束标识，可以使用`JoinableQueue`队列来完成

- JoinableQueue：每当队列里存入一个数据的时候，内部会有一个计数器+1，每当调用`task_done()`的时候，计数器-1，当计数器为0的时候执行`q.join()`

把上面的代码优化一下

```py
from multiprocessing import Process, Queue, JoinableQueue
import time
import random
import multiprocessing


def producer(name, food, q):
    for i in range(5):
        data = '%s生产了%s,%s个'%(name, food, i)
        time.sleep(random.randint(1,3))
        print(data)
        q.put(data)


def consumer(name, q):
    while True:
      food = q.get()
      time.sleep(random.randint(1,3))
      print('%s吃了%s' %(name, food))
      # 告诉队列 已经取出来了一个数据并且处理完毕
      q.task_done()


if __name__ == '__main__':
    multiprocessing.set_start_method('fork')
    q = JoinableQueue()
    p1 = Process(target=producer, args=('生产者1', '包子', q))
    p2 = Process(target=producer, args=('生产者2', '饺子', q))
    c1 = Process(target=consumer, args=('消费者1', q))
    c2 = Process(target=consumer, args=('消费者2', q))
    p1.start()
    p2.start()
    # 将消费者子进程设置为守护进程，当主进程执行完毕之后，杀死子进程
    c1.daemon = True
    c2.daemon = True
    c1.start()
    c2.start()
    p1.join()
    p2.join()
    # 等待队列中所有的数据被取完然后执行数据
    q.join() 

```
