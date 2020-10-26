Python中，`GIL(Global Interpreter Lock)`的存在，`线程`无法利用多核系统，因为GIL在任何指定的时间只允许`单个Python操作`执行。

但是尽管GIL存在，线程依然适合`I/O操作`的并行执行。

#### ThreadPool

线程最大的坏处之一就是`创建`它们的代价，这就是为什么`协程`是一个更好的解决方案。如果你需要线程的话，`ThreadPool`可以避免这种代价。



```
from multiprocessing.pool import ThreadPool

def fetch_url(url):
    import urllib.request
    res = urllib.request.urlopen(url)
    return res.read()

def wait_until(predicate):
    import time
    seconds = 0
    while not predicate():
        print('waiting...')
        time.sleep(1.0)
        seconds += 1
    print('Done')
    return seconds

pool = ThreadPool(4)
t1 = pool.apply_async(fetch_url, args=('https://httpbin.org/delay/3',))
t2 = pool.apply_async(wait_until, args=(t1.ready,))
pool.close() # 告诉线程池没有其它任务可做，一旦完成任务，就可停止
pool.join() # 阻塞主进程，等待线程池停止(即等待所有函数执行完成)
print('Total Time : ', t2.get())
print('Content : ', t1.get())
```



ThreadPool提供了map方法，将一个函数应用到参数列表上。

```
from multiprocessing.pool import ThreadPool

def fetch_url(url):
    import urllib.request
    res = urllib.request.urlopen(url)
    return res.read()

urls = [
    "https://httpbin.org/delay/1",
    "https://httpbin.org/delay/2",
    "https://httpbin.org/delay/3",
    "https://httpbin.org/delay/4",
]


def map_thread_local():
    pool = ThreadPool(4)
    return pool.map(fetch_url, urls)

import timeit
total = timeit.timeit(map_thread_local, number=1)
print(total)
```



#### Coroutine

```
Functions whose execution can be interleaved by suspending and resuming them are called coroutines.
```

如果多个耗费时间长的I/O执行，很容易造成大量的操作并行执行，这些任务只是在等待数据从网络或磁盘上返回，这种是`阻塞式I/O`。

在上述情况下，`异步I/O`是更好的方式。`阻塞式I/O`中，代码停止，只是等待读写任务完成；`异步I/O`中，代码可以切换去做其它事情，一旦数据获得，再返回，从原来的地方继续执行。

在Python中，协程通过`async def` 语法实现，通过`asyncio`事件循环来执行。



```
import asyncio

async def countdown(identifier, n):
    while n > 0:
        print('left: ', n, ' ({})'.format(identifier))
        await  asyncio.sleep(1)
        n -= 1

async def main():
    await asyncio.wait([
        countdown('A', 2),
        countdown('B', 3)
    ])

loop = asyncio.get_event_loop()
loop.run_until_complete(main()) # 要求事件循环等待，直到协程完成
loop.close()
```

`asyncio.wait` 函数负责等待一些协程完成；除非显式声明`await`，否则代码会立即往前执行。



`countdown`函数中，为什么使用`asyncio.sleep`，而不是`time.sleep`。原因是在处理协程时候，确保`阻塞的函数`也是一个协程。



#### Process

多进程会产生`多个独立`的Python解释器，

注意：在Windows平台下，`multiprocessing`的多进程必须在`if __name__ == '__main__'` 下使用，并且添加
`multiprocessing.freeze_support()`代码。

```
import os
import multiprocessing
from multiprocessing import Pool

def fib(n: int, seen: set):
    if n not in seen and n % 5 == 0:
        print(os.getpid(), '->', n)
        seen.add(n)
    if n < 2:
        return n
    return fib(n - 2, seen) + fib(n - 1, seen)

if __name__ == '__main__':
    multiprocessing.freeze_support()
    pool = Pool()
    t1 = pool.apply_async(fib, args=(20, set()))
    t2 = pool.apply_async(fib, args=(22, set()))

    pool.close()
    pool.join()

    print(t1.get())
    print(t2.get())

```



`multiprocessing.Pool`底层使用线程，而`multiprocessing.pool.ThreadPool`底层使用子进程。





  





