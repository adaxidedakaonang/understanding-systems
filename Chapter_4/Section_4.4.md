## 死锁

### 死锁的概念

死锁只有同时满足一下四个条件才会发生：
1. 互斥条件
2. 持有并等待条件
3. 不可剥夺条件
4. 环路等待条件

#### 互斥条件

>多个线程不能同时使用同一个资源

#### 持有并等待条件

> 等待获取资源2的同时没有释放自己已经持有的资源1

#### 不可剥夺条件

> 在自己使用完之前不能被其他线程获取

#### 环路等待条件

> 两个线程获取资源的额顺序构成了环形链


### 排查死锁问题

1. pstack thread_number
2. ```C++ 
    gdb -p 87746
    (gdb) info thread
    (gdb) thread 2
    (gdb) bt
    (gdb) frame 3
    (gdb) p mutex_A
    (gdb) p mutex_B
    ```

### 避免死锁问题的发生

1. 使用资源有序分配法，破坏环路等待条件
   >保持相同的顺序获取资源