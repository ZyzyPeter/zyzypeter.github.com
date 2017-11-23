---
layout: post
title: Python 多线程与多进程
categories: Python
description: Python的安装
keywords: Python，freshman
---

资料来源参考：

主要：
[廖雪峰 进程和线程](https://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/0013868322563729e03f6905ea94f0195528e3647887415000)

[廖雪峰 协程](https://www.liaoxuefeng.com/wiki/001374738125095c955c1e6d8bb493182103fac9270762a000/0013868328689835ecd883d910145dfa8227b539725e5ed000)

[Python中yield的解释 - Python - 伯乐在线](http://python.jobbole.com/83610/)

[youxin 博客园 Monkey patch](https://www.cnblogs.com/youxin/p/3805706.html)

[Vamei CSDN](https://www.cnblogs.com/vamei/archive/2012/10/12/2721484.html)

## 进程与线程简介

**进程（Process）**实际上表示的就是计算机正在进行的一个任务，比如，打开一个浏览器便是启动一个浏览器进程，打开一个记事本便是启动一个记事本进程。

但是，一个进程未必只能进行一件事，就像一个Word进程，在打字的同时还会有拼写检查，这些在进程内部同时进行的多个“子任务”，就称为**线程（Thread）**。

在以往的单核CPU上，系统执行多进程的方式是通过不断的在多个进程中切换——例如任务1执行0.01秒，切到任务2执行0.01秒再切到任务3......以此类推，而在多核CPU出现后，真正的并行执行多任务才真正的得以实现，但绕是如此，一台计算机同时进行的进程是非常之多的，远远大于CPU的核心数量，因此，操作系统依然会将这些任务轮流调度到每个核心上运行。

多线程的执行方式类似于多进程，也是通过快速切换来达到看起来“同时运行”的目的。

而对于我们之前的经历而言，写好的一个程序（进程）往往只能同时进行一个线程，因此，若要同时进行多个任务，我们有以下三种方案：

1. 写多个程序，然后同时运行
2. 在一个程序中运行多个线程
3. 多进程+多线程

Python既支持多进程，也支持多线程，接下来我们将讨论如何用Python实现这类任务。

## Python多进程

### multiprocessing

#### Process

Unix/Linux系统提供一个fork函数供python在该类型系统上进行多进程的调用，但是Windows系统不一样，介于我们现在使用的还是Windows系统，Unix系统使用多进程的方式我们留到以后改换成Unix系统之后在补全。

python提供了一个`multiprocessing`模块来供跨平台版本的python使用多进程，这个模块提供了一个`Process`类来代表一个进程对象。

下面是一个启动子进程并等待其结束的例子：

```python
from multiprocessing import Process
import os

def run(name):
    print 'Child process',name,os.getpid()

if __name__ == '__main__':
    print 'Parent process',os.getpid()
    p=Process(target=run,args=('child_process',))
    p.start()
    p.join()
    print 'terminate'
```

```
Parent process 5280
Child process child_process 14920
terminate
```

模块`os`提供的方法`getpid()`可以让我们查看当前运行的进程的id。创建子进程时，只需要传入一个执行函数和函数的参数（注意参数`args=('child_process',)`后面的那个逗号`,`，因为传参的方式是传入元祖，所以如果不加逗号，函数会认为要把`'child_process'`的每个字符都当作参数传入），创建一个`Process`实例，然后用`start()`方法启动。`join()`方法表示等待子进程结束后再继续往下运行，常用于进程间的同步。有意思的是，如果我们传参数不按照上述代码`args=()`这样的形式来传参而是直接类似于`target=run('a')`这样的形式传入参数，就会发现，Python会直接将其作为一个函数放在主进程内运行，而不会再作为子进程单独运行。

```python
from multiprocessing import Process
import os

def run(name):
    print 'Child process',name,os.getpid()

if __name__ == '__main__':
    print 'Parent process',os.getpid()
    p=Process(target=run,args=('child_process',))
    p.start()
    print 'test'
    print 'terminate'
```

```
Parent process 14128
test
terminate
Child process child_process 14892
```

可以看到，若是没有`join()`那么子进程的运行时间就不会和我们预想的同步。

#### Pool

如果我们要创建大量的子进程，可以利用进程池的方式来批量创建子进程。

进程池类`Pool`同样是由模块`multiprocess`导出

```python
from multiprocessing import Pool
import os,time,random

def run(name):
    print 'Run task %s (%s)...' % (name, os.getpid())
    start = time.time()
    time.sleep(random.random() * 3)
    end = time.time()
    print 'Task %s runs %0.2f seconds.' % (name, (end - start))

if __name__=='__main__':
    print 'Parent process',os.getpid()
    p=Pool(5)
    for i in range(5):
        p.apply_async(run,args=(i,))
    print 'Waiting for all subprocess done'
    p.close()
    p.join()
    print 'End'
```

```
Parent process 13192
Waiting for all subprocess done
Run task 0 (14936)...
Run task 1 (4216)...
Run task 2 (4684)...
Run task 3 (4552)...
Run task 4 (8484)...
Task 2 runs 0.39 seconds.
Task 4 runs 0.91 seconds.
Task 1 runs 2.71 seconds.
Task 0 runs 2.71 seconds.
Task 3 runs 2.73 seconds.
End
```

对于`Pool`对象，若要调用`join()`则必须提前调用`close()`，一旦调用`close()`则无法再添加新的子进程。

#### 进程间通信

python模块`multiprcess`提供`Queue`和`Pipe`类来进行进程间的通信，另外还有很多方式，这里我们先介绍提出的这两种。

##### Queue

我们创建两个进程，一个`inputer()`用于往`Queue`里写数据，一个`reader()`用于从`Queue`里读数据

```python
import random
from multiprocessing import Process
from multiprocessing import Queue
import time


def inputer(q):
    a=[1,2,3]
    b=['a','b','c']
    c=[a,b]
    for l in c:
        print 'Put list',id(l),'to Queue'
        q.put(l)
        time.sleep(random.random())

def reader(q):
    while True:
        value=q.get(True)
        print 'Get list content from Queue:'
        print value

if __name__=='__main__':
    q=Queue()
    i=Process(target=inputer,args=(q,))
    r=Process(target=reader,args=(q,))
    i.start()
    r.start()
    i.join()
    r.terminate()
```

```
Put list 46446472 to Queue
Get list content from Queue:
[1, 2, 3]
Put list 46336040 to Queue
Get list content from Queue:
['a', 'b', 'c']
```

类构造方法`Queue(maxsize)`可以接受一个参数`maxsize`来限制`Queue`中最多可以存放的对象数量。

##### Pipe

`Pipe`对象分两种，一种为**单向管道（half-duplex）**，一种为**双向管道（duplex）**，我们可以通过构造方法`Pipe(duplex=False)`来创建单向管道（默认为双向管道）。

`Pipe`执行任务的方式是，一个进程从`Pipe`的一端输入对象，然后一个进程从`Pipe`的另一端接收对象，单向管道只允许管道一端的进程输入，而双向管道则允许从两端输入。

下面是一个例子

```python
from multiprocessing import Pipe
from multiprocessing import Process


def sender(p):
    g={'a':'hello'}
    print p
    p[0].send(g)
    print 'process1 recv',p[1].recv()['a']

def recver(p):
    print p[1]
    g={'a':'nice to meet you'}
    p[1].send(g)
    print 'process2 recv',p[0].recv()['a']

if __name__=='__main__':
    pipe=Pipe()
    p1=Process(target=sender,args=(pipe,))
    p2=Process(target=recver,args=(pipe,))
    p1.start()
    p2.start()
    p1.join()
    p2.join()
```

```
(< read-write PipeConnection, handle 8 >, < read-write PipeConnection, handle 12 >)
process1 recv hello
< read-write PipeConnection, handle 12 >
process2 recv nice to meet you
```

调用构造方法`Pipe()`创建了一个双向管道，实际上是创建了一个由两个单向管道组成的二元组，若是一个进程调用了一个单向管道的`send`方法，那么另外一个进程就不能再调用这个管道的`send`方法，我们可以从例子中看到，进程`sender`用了二元组第一个管道的`send`，进程`recver`用了第二个。

## Python多线程

一个进程由若干个线程组成，在Python标准库中，有两个模块`thread`和`threading`提供调度线程的接口。介于`thread`是低级模块，很多功能还不完善，我们一般只会用到`threading`这个比较完善的高级模块，因此这里我们只讨论`threading`模块的使用。

### threading

要启动一个线程，我们只需要把一个函数传入`Thread`实例，然后调用`start()`运行，这个我们之前操作进程调用`Process`实例的方式如出一辙。

```python
import random
from threading import Thread
import threading
import time

def run():
    print 'tread',threading.current_thread().name,'is running...'
    for i in range(5):
        print 'thread',threading.current_thread().name,'number',i
        start=time.time()
        time.sleep(random.random()*2)
        end=time.time()
        print 'thread',threading.current_thread().name,'number',i,'run for',end-start,'seconds'

if __name__=='__main__':
    print 'thread',threading.current_thread().name,'is running...'
    t=Thread(target=run,name='func_run')
    t.start()
    t.join()
    print 'thread',threading.current_thread().name,'is end'
```

```
thread MainThread is running...
tread func_run is running...
thread func_run number 0
thread func_run number 0 run for 1.78100013733 seconds
thread func_run number 1
thread func_run number 1 run for 1.5110001564 seconds
thread func_run number 2
thread func_run number 2 run for 0.77899980545 seconds
thread func_run number 3
thread func_run number 3 run for 0.962000131607 seconds
thread func_run number 4
thread func_run number 4 run for 0.118000030518 seconds
thread MainThread is end
```

`current_thread()`函数用于返回当前线程的实例，主线程实例的名字为`MainThread`，子线程的名字可以在创建时给予，或者被默认给予`Thread-1`，`Thread-2`这样的名字。

### Lock

多进程和多线程最大的区别就在于，对于多进程，同一个变量各自有一份拷贝存在于每个进程，互不影响，而多线程不然，所有的线程共用所有的变量，因此，任何一个变量都可以被任意的一个线程修改。为了避免多个线程同时修改同一个变量这种危险情况的出现。

首先我们需要理解，多个线程同时修改一个变量这种情况是怎么出现的。

```python
from threading import Thread

a=0
def change():
    global a
    a=a+1
    a=a-1

def loop_change():
    for i in range(10000):
        change()

t1=Thread(target=loop_change,args=())
t2=Thread(target=loop_change,args=())
t1.start()
t2.start()
t1.join()
t2.join()
print a
```

```
-97
```

理论上来说，不论我们如何调用函数`change()`，共享变量`a`的值都应该为0，但实际上，因为两个线程`t1`，`t2`之间交替运行的次数过多，导致`a`的结果未必就是0了。

要理解这种情况首先要简单的了解一下CPU执行代码时的底层工作原理：

实际上，在编程语言中，一行代码在底层运行的情况未必就是作为一行来完成的，例如上面的代码`a=a+1`，CPU在处理时实际上的运行方式是先用一个临时变量存储`a+1`的值，再把这个临时变量的值赋给`a`，若是学习过arm开发，就可以理解到，CPU在工作时的情况实际上是先将值`a`和`1`分别存入两个寄存器，然后将两个寄存器的值进行加法运算并将结果存入第三个寄存器，之后再将第三个寄存器的值存入并覆盖原本保存`a`的值的寄存器内。用代码语言可以作如下理解：

```
c1=0
c2=1
c3=c1+c2
c1=c3
```

正因此，因为两个线程都调用了各自的寄存器，或者说都有各自的临时变量`c3`，那么当`t1`和`t2`交替运行时，就可能出现下述代码所描述的情况:

```python
mt:c1=0 #主线程赋值给寄存器c1

t1:c2=1
t1:c3=c1+c2 #c3=1

t2:c4=1
t2:c5=c4+c1 #c5=1

t1:c1=c3 #c1=1
t1:c3=c1-c2 #c3=0
t1:c1=c3 #c1=0

t2:c5=c1-c4 #c5=-1
t2:c1=c5 #c1=-1

最终结果：c1=-1
```

为了避免这种情况的发生，我们就需要提供线程锁来确保：当一个线程获得了`change()`的调用权时，另一个线程就不能在同时执行`change()`方法，直到锁被释放之后，获得了该锁才能继续进行修改。

要创建一个线程锁，我们用`threading.lock()`方法来实现

```python
lock=threading.Lock()
a=0
def change():
    global a
    a=a+1
    a=a-1

def loop_change():
    for i in range(100000):
        lock.acquire() #获得锁
        try:
            change()
        finally:
            lock.release() #释放锁
t1=Thread(target=loop_change,args=())
t2=Thread(target=loop_change,args=())
t1.start()
t2.start()
t1.join()
t2.join()
print a
```

```
0
```

这样子，无论如何运行，结果都将是我们预期的0。

当多个线程同时执行`lock.acquire()`时，只有一个线程能够成功地获得线程锁，然后继续执行代码，其它线程只能等待锁的释放。

获得锁的线程则一定要记得释放，否则会成为死线程。因此我们会用`try...finally...`来确保锁的释放。

然而，锁的问题就是一方面让原本多线程的任务实际上又变成了单线程的运行方式（尽管对于Python的伪多线程而言，这并不会造成什么性能的下降），另外，又由于可以存在多个锁，对于不同的线程可能会持有不同的锁并且试图获取对方的锁时，可能会造成死锁，导致多个线程全部挂起，这时只能通过操作系统来强行终止。

### Python的GIL锁

对于一个多核CPU，它可以同时执行多个线程。

我们可以通过Windows提供的任务管理器看见CPU的资源占用率，因此，当我们提供一个无限循环的死线程时，CPU一核的占用率就会提升到100%，若是提供两个，就又会有一核的占用率到100%。如果在java或者C中这么做，那么确实会发生这种情况，但是，如果我们在Python中这样尝试的话

```python
def loop():
    x = 0
    while True:
        x = x ^ 1

for i in range(multiprocessing.cpu_count()):
    print multiprocessing.cpu_count()
    t = threading.Thread(target=loop)
    t.start()
```

```
4
4
4
4
```

可以看到，执行上面的代码会从`multiprocessing.cpu_count()`得知我们有4个CPU，然后打印了4行说明已经执行了4个线程，这个时候我们的CPU占用率应该是满的，但实际上

![](\images\blog\py_thread1.png)

我们从红框中看到，情况并非如此。

实际上哪怕我们启用再多的线程，CPU的占用率也不会提高多少。这是因为尽管Python使用的是真正的线程，但Python的解释器在执行代码时有一个**GIL锁（Gloabal Interpreter Lock）**，不论是什么Python代码，一旦执行必然会获得GIL锁，然后每执行100行代码就会释放GIL锁使得其它线程有机会执行。GIL锁实际上就给一个Python进程的所有线程都上了锁，因此哪怕是再多的线程，在一个Python进程中也只能交替执行，也即是只能使用一个核。

若是要用到多核，我们可以使用多进程来实现多核任务，因为尽管有GIL锁，但是不同的Python进程用的是不同的GIL锁，互不影响。

### ThreadLocal

既然我们已经知道，一个全局变量会受到所有线程的影响，那么，我们应该如何构建一个独属于这个线程的“全局变量”？换言之，我们既希望这个变量在这个线程中拥有类似于全局变量的功能，又不希望其它线程能够调用它，以防止出现上面所述的问题，该怎么做？

```python
def do_task1(a):
    pass

def do_task2(a):
    pass

def run_thread():
    a=1
    do_task1(a)
    do_task2(a)

t=Thread(target=run_thread,name='thread_test')
```

可以看到，在这个子线程中，如果我们希望函数`do_task1()`和`do_task2()`能用到变量`a`，则必须将它作为参数传进去。

为了避免这种过于繁琐的操作，`ThreadLocal`对象便是Python用于解决这个问题的方法，它由`threading.local()`方法创建：

```python
local_varient=threading.local()

def varient_print():
    print 'Thread',threading.current_thread().name,'\'s varient is',local_varient.a

def thread_run(a):
    local_varient.a=a
    varient_print()

t1=Thread(target=thread_run,args=(5,))
t2=Thread(target=thread_run,args=(6,))
t1.start()
t2.start()
t1.join()
t2.join()
```

```
Thread Thread-1 's varient is 5
Thread Thread-2 's varient is 6
```

我们可以认为`ThreadLocal`的原理类似于创建了一个词典，当我们创建一个变量`local_varient.a`的时候实际上是在`local_varient`这个词典里面创建了数个以当前线程为关键字（`threading.current_thread()`），不同线程中的`a`为值的键值对组成的`dict`，可以参照下面这个例程：

```python
def varient_print():
    print 'Thread',threading.current_thread().name,'\'s varient is',local_varient[threading.current_thread()]

def thread_run(a):
    local_varient[threading.current_thread()]=a
    varient_print()

t1=Thread(target=thread_run,args=(5,))
t2=Thread(target=thread_run,args=(6,))
t1.start()
t2.start()
t1.join()
t2.join()
```

结果与上面用`ThreadLocal`的例程是一样的。当然，我在这里只是试图简单的描述一下`ThreadLocal`的工作原理，因为实际上它的工作原理和我们上面利用`dict`的例程并不是完全一样的，因为`ThreadLocal`对象可供传给的变量完全不只一个：

```python
def thread_run(a):
    local_varient.a=a
    local_varient.b=a+1
    varient_print()
```

甚至`local_varient.c`、`local_varient.d`...都可以，没有一定的数量限制。而`dict`中能用`threading.current_thread()`做关键字的键值对都只能有一个不是吗。

## 进程和线程的比较

在初步了解进程和线程以及它们在Python中的运用方式之后，我们现在来讨论一下二者的区别与利弊。

首先，我们简单了解一下多任务的工作模式：通常我们会将其设计为Master-Worker 模式，Master负责分配任务，Worker负责执行任务，多任务环境下通常是一个Master对应多个Worker。

那么多进程任务实现Master-Worker，主进程就是Master，其它进程是Worker

多线程任务，主线程Master，子线程Worker。

先来说说多进程，多进程的优点就在于，它的稳定性高。因为一个子进程的崩溃不会影响到其它子进程和主进程（主进程挂了还是会全崩的）。但多进程的问题就在于，其创建进程的开销过大，特别是Windows系统，其多进程的开销要比使用`fork()`的Unix/Linux系统大的多得多。并且，对于一个操作系统本身而言，它能够同时运行的进程数也是有限的。

多线程模式占用的资源消耗没有多进程那么大，因此它也往往会更快一些（但似乎也不会快太多？但至少在Windows下多线程的效率往往要比多进程要高），而且，多线程模式与多进程模式正好相反，一个线程挂掉会直接让进程内包括主线程的所有的线程都崩溃，因为所有线程共享进程的内存。在Windows系统中，如果我们看到了这样的提示“该程序执行了非法操作，即将关闭”，那往往就是因为某个线程出现问题导致整个进程的崩溃。

### 切换

不论是进程还是线程，如果数量太多，那么效率是肯定上不去的。

因为操作系统在切换进程和线程时，需要先保存当前执行的现场环境（包括CPU寄存器的状态，内存页等），然后再准备另一个任务的执行环境（恢复上次的寄存器状态，切换内存页等），才能开始执行新任务。这个过程虽然很快，但再快也是需要耗时的，因此一旦任务数量过于庞大，那么浪费在准备环境的时间就也会非常巨大。

### 计算密集型和IO密集型

考虑多任务的类型也是我们判断如何构建工作模式的一个重要点。我们可以将任务简单的分为两类：**计算密集型**和**IO密集型**

#### 计算密集型

计算密集型任务的特点是要进行大量的运算，消耗CPU资源，例如一些复杂的数学运算，或者是一些视频的高清解码运算等等，纯靠CPU的计算能力来执行的任务。这种任务虽然也可以用多任务模式来完成，但任务之间切换的消耗往往比较大，因此若是要高效的进行这类任务的运算，计算密集型任务同时进行的数量最好不要超过CPU的核心数。

而对于语言而言，代码运行的效率对于计算密集型任务也是至关重要，因此，类似于Python这样的高级语言往往不适合，而像C这样的底层语言的效率就会更高。好在Python处理这类任务时用的往往是用C编写的库，但若是要自己实现这类任务的底层计算功能，还是以C为主比较好。

#### IO密集型

IO密集型的特点则是要进行大量的输入输出，涉及到网络、磁盘IO的任务往往都是IO密集型任务，这类任务消耗CPU的资源并不高，往往时间都是花在等待IO操作完成，因为IO操作的速度往往都比CPU和内存运行的速度要慢很多。对于IO密集型任务，多任务执行提升的效率就会很高，但当然，任务数量还是有一个限度的。

而对于这类任务使用的编程语言，Python这类开发效率高的语言就会更适合，因为能减少代码量，而C语言效果就很差，因为写起来很麻烦。

### 异步IO

现代操作系统对IO操作进行了巨大的改进，其提供了异步IO的操作来实现单进程单线程执行多任务的方式，它在单核CPU上采用单进程模型可以高效地支持多任务。而在多核CPU上也可以运行多个进程（数量与CPU核心数相同）来充分地利用多核CPU。通过异步IO编程模型来实现多任务是目前的主流趋势。

而在Python中，单进程的异步编程模型称为协程。

## 协程

**协程（Coroutine）**又称微程，纤程。

子程序（又名“函数”）在所有语言中都是“层级调用”，换言之，就是A调用B，B调用C，那么就要等待C执行完成后返回到B，B完成后返回到A，然后A执行完成。正如我们所知的，这种运行模式是通过栈来实现的。

协程看上去也像是函数，但是在执行过程中，协程在子程序的内部可以中断，然后转而执行别的子程序，在某个恰当的时间点又转回来执行这个子程序。

协程看起来更像是多线程模式的运行，但实际上它只用了一个线程，因此相比多线程，协程拥有极高的运行效率，因为不用切换；并且协程还不需要使用多线程的锁机制，因为只有一个线程就不存在变量冲突，在协程中控制共享资源只需要判断状态即可，因此也没有死锁的风险。

如果我们要利用多核CPU，就可以通过多进程+协程的方式来进行，既充分地了利用了多核，又充分地发挥了协程地高效率，可以获得极高地性能。

### yield

Python用于`generator`的`yield`可以一定程度上实现协程的功效。我们来看下面这个例子：

假设我们需要同时执行两个任务，一个任务用于生成一个数据，一个任务用于接收这个数据并打印出来，那么如果用多线程模式进行编程的话，我们就需要两个线程，一个线程写数据一个线程拿数据，并且通过锁机制和队列控制等待。

如果用`yield`的话，则可以这么做

```python
def outputter():
    r=''
    while True:
        n= yield r
        if not n:
            return
        print 'Output',n
        time.sleep(1)
        r='ok'

def producer(c):
    c.next()
    i=0
    while i<3:
        i+=1
        print 'Producing',i
        r=c.send(i)
        print 'Outputter return',r
    c.close()

c=outputter()
producer(c)
```

```
Producing 1
Output 1
Outputter return ok
Producing 2
Output 2
Outputter return ok
Producing 3
Output 3
Outputter return ok
```

这段代码与我们之前常用的写法似乎有些区别，因为我们看到子程序`outputter`压根没有跟在`return`后面的返回值，但是不论是在主程序中，还是在子程序`producer`中，都接收了`outputter`的返回值并且二者接收的内容显然还是不一样的。

要理解这种情况，我们首先要明白`yield`是如何返回一个**生成器（generator）**的。

我们直到，代码`for ... in ...`可以将`in`后面的对象的内容迭代出来，这个过程是可以重复的，简单的说，像是执行了代码`for x in listA`一次，后面我们依然可以用`for...in...`语句来迭代`listA`，`listA`依然能够被迭代，因为列表`listA`是一个可迭代对象。

但是，如果我们有大量的数据要迭代，并且不希望把它们存在一个类似于列表这样的可迭代对象里面，因为这样会很占内存，那么我们就可以考虑使用生成器来完成这个工作。

生成器也是一种迭代器，但它只能被迭代一次，因为它实时地生成这些值，而这些值一旦被迭代后就会被销毁，不再是先都存放内存当中然后一个一个迭代了。我们可以参考下面这个例程

```python
a=(i for i in range(5))
for i in a:
    print i
for i in a:
    print i
```

```
0
1
2
3
4
```

尽管我们运行了两次`for...in...`语句，但成功的只有第一次，因为`a`就是一个简单的生成器，它只能被迭代一次

```python
print a
```

```
<generator object <genexpr> at 0x0306BE40>
```

`yield`就是这么一个生成generator的关键词，它类似于`return`，在一个子程序中调用`yield`的时候，这个子程序的第一次运行仅会运行到有`yield`的位置然后直接结束

```python
def test_func():
    while True:
        n=yield
        if not n:
            return
        print n

def activate(b):
    b.next()
    b.send(2)
    b.send(3)
    b.close()

b=test_func()
activate(b)
```

```
2
3
```

我们可以看到，这就是为什么主程序中变量`b`接收到的内容是一个生成器。而每当我们调用一次函数`next()`（也可以这样调用`next(b)`，这个函数在`generator`对象内是通过实例方法`__next__`来编写的）时，就会执行一次生成器函数`test_func`内的循环，又因为我们添加的条件语句，使得需要`n`内有值才会继续运行，否则会`return`，因此我们还需要通过`send()`方法往生成器里面传输内容。

到此，我们就简单地了解到了`yield`实现协程工作方式的原理。

### gevent

但是`yield`在Python中算式比较低级的对协程模式的支持，它还有很多不完善的内容。

第三方模块`gevent`就是一个提供了比较完善的协程支持的模块。

`gevent`通过greenlet实现协程：

当一个greenlet遇到IO操作时，它会自动切换到其他的greenlet执行任务，等到IO操作完成时又在适当的时候切换回来运行。我们直到IO操作通常比较耗时，因此有这种自动切换机制就可以让我们的任务在等待IO操作时可以“顺便”做点儿其他的事。

`gevent`需要修改Python自带的一些标准库，以便于能够在IO操作时自动完成切换，这一过程通过`monkey patch`来完成：

```python
from gevent import monkey;monkey.patch_socket()
import gevent

def test(n):
    for i in range(n):
        print gevent.getcurrent(),i

g1=gevent.spawn(test,5)
g2=gevent.spawn(test,3)
g3=gevent.spawn(test,4)
g1.join()
g2.join()
g3.join()
```

```
<Greenlet at 0x2bee7b0: test(5)> 0
<Greenlet at 0x2bee7b0: test(5)> 1
<Greenlet at 0x2bee7b0: test(5)> 2
<Greenlet at 0x2bee7b0: test(5)> 3
<Greenlet at 0x2bee7b0: test(5)> 4
<Greenlet at 0x2bee8f0: test(3)> 0
<Greenlet at 0x2bee8f0: test(3)> 1
<Greenlet at 0x2bee8f0: test(3)> 2
<Greenlet at 0x2bee940: test(4)> 0
<Greenlet at 0x2bee940: test(4)> 1
<Greenlet at 0x2bee940: test(4)> 2
<Greenlet at 0x2bee940: test(4)> 3
```

如果这样子使用的话，3个greenlet会依次运行。

要让greenlet交替运行，可以通过`gevent.sleep()`交出控制权：

```python
def test(n):
    for i in range(n):
        print gevent.getcurrent(),i
        gevent.sleep(0)
```

```
<Greenlet at 0x333c800: test(5)> 0
<Greenlet at 0x333c940: test(3)> 0
<Greenlet at 0x333c990: test(4)> 0
<Greenlet at 0x333c800: test(5)> 1
<Greenlet at 0x333c940: test(3)> 1
<Greenlet at 0x333c990: test(4)> 1
<Greenlet at 0x333c800: test(5)> 2
<Greenlet at 0x333c940: test(3)> 2
<Greenlet at 0x333c990: test(4)> 2
<Greenlet at 0x333c800: test(5)> 3
<Greenlet at 0x333c990: test(4)> 3
<Greenlet at 0x333c800: test(5)> 4
```

发现有交替运行的效果了。

不过实际上我们在使用`gevent`的时候是当然不会去用`gevent.sleep()`去切换协程的，因为greenlet在执行到IO操作时，是会自动切换的：

```python
import urllib2
from gevent import monkey;monkey.patch_all()
import gevent

def test(n):
    print 'start getting',n
    response=urllib2.urlopen(n)
    data=response.read()
    print len(data)

gevent.joinall([gevent.spawn(test,'https://www.baidu.com/'),
gevent.spawn(test,'https://www.yahoo.com/'),
gevent.spawn(test,'http://cn.bing.com/')])
```

```
start getting https://www.baidu.com/
start getting https://www.yahoo.com/
start getting http://cn.bing.com/
227
131995
515119
```

可以看到，greenlet“start”后在IO操作期间会切换到其它greenlet运行。另外要注意，这里`monkey`调用的方法是`monkey.patch_all()`，和上一个例程不一样。

## 分布式进程

### 分布式系统

我们知道，相比多线程，多进程的效果往往要更好，但是如果任务数量实在庞大，在一台机器上进行有限的多进程工作模式也往往很难吃的消。这个时候，我们就需要把多进程分配到多台机器上通过网络互相通信进行协同工作，相比最多只能分布到多个CPU的线程，这种工作模式的效率会有非常大的提升。

这种建立在网络之上的软件系统，就称为**分布式系统（distributed system）**

分布式系统有两大特点：内聚性和透明性

* 内聚性：每一个数据库节点高度自治，有本地的数据库管理系统。

* 透明性：每一个数据库分布节点对于用户的应用来说都是透明的，是无法区分本地还是远程的。

现在我们来了解一下，如何使用Python搭建分布式进程工作模式。

### managers

Python中`multiprocessing`的子模块`managers`支持把多进程分布到多台机器上，一个服务进程可以作为调度者来将任务分布到其它的多个进程当中，并依靠网络进行互相通信。由于`managers`的模块封装好了，所以在Python中我们调用它时可以不需要了解网络通信的底层细节，就可以直接进行分布式多进程程序的编写：

我们现在假设我们有3个数据要传到另一台机器上，希望另一台机器将这3个数据进行加密（例如把它们都加上10）后返回给原来的这台机器，我们可以通过传输队列对象`Queue`来进行这个任务的实现:

```python
# coding=utf-8
import time,sys,Queue
from multiprocessing.managers import BaseManager

# 用于发送数据的队列
data_queue=Queue.Queue()
# 用于接收结果的队列
result_queue=Queue.Queue()

class QueueManager(BaseManager):
    pass

# 下面两个函数在Unix系统不需要
def return_data_queue():
    global data_queue
    return data_queue

def return_result_queue():
    global result_queue
    return result_queue

def test():
    # 将两个Queue注册到网络上，callable参数用于关联Queue对象
    # 若是Unix系统，则在callable参数中可以直接调用data_queue和result_queue，不需要特意用函数封装
    QueueManager.register('get_data_queue',callable=return_data_queue)
    QueueManager.register('get_result_queue',callable=return_result_queue)
    # 绑定到本地端口5000并设置验证码‘123’
    # Unix系统不需要填127.0.0.1
    manager=QueueManager(address=('127.0.0.1',5000),authkey='123')
    #启动Queue
    manager.start()
    # 获得通过网络访问的Queue对象，因为我们不能直接对原始对象（例如data_queue）进行操作，那样子会绕过QueueManager的封装
    data=manager.get_data_queue()
    result=manager.get_result_queue()
    # 传入3个数据
    data.put(1)
    data.put(2)
    data.put(3)
    print 'Putting data...1,2,3'
    # 从result队列读取加密后的结果
    for i in range(3):
        print result.get(timeout=10)

# Unix系统不需要下面这个条件语句，直接运行test()即可
if __name__=='__main__':
    test()

```

这是作为第一台机器调度加密任务并接受加密后数据信息的例程`manager.py`，我当前写的是适用于Windows系统的代码，来源参考[Python初学分布式的疑问 CSDN](http://bbs.csdn.net/topics/390869705/)

```python
# coding=utf-8
import time,sys,Queue
from multiprocessing.managers import BaseManager

class QueueManager(BaseManager):
    pass

# 这里由于是直接使用网络上的Queue，所以只提供注册的名字
QueueManager.register('get_data_queue')
QueueManager.register('get_result_queue')
# 连接到运行manager.py的服务器，这里用的ip是本机
server_addr='127.0.0.1'
print 'Connecting',server_addr
# 连接到我们在manager.py中指定的端口，并提供指定的验证码
manager=QueueManager(address=(server_addr,5000),authkey='123')
manager.connect()
data=manager.get_data_queue()
result=manager.get_result_queue()
# 进行加密
for i in range(3):
    try:
        n=data.get(timeout=1)
        n=n+10
        time.sleep(1)
        result.put(n)
    except Queue.Empty:
        print 'data queue is empty'
# 处理完毕
print 'over'
```

上面是子进程运行代码`worker.py`索性子进程执行的代码Windows系统不需要作出什么改动。

我们先启动`manager.py`，可以看到

```
Putting data...1,2,3
```

说明主进程开始运行，并且在等待结果。然后我们运行子进程代码`worker.py`

```
Connecting 127.0.0.1
over
```

成功连接到主进程，并获取了数据进行处理，然后传输回主进程

```
11
12
13
```

主进程得到加密后的数据结果，并将其打印出来。

这便是一个简单的分布式运算，其中，`Queue`对象就存储在主进程`manager.py`中，`worker.py`没有创建`Queue`对象而是直接对`manager.py`中的`Queue`对象做出修改。

