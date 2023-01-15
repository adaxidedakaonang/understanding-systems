## 1.2 存储器金字塔

速度：**CPU内部**(*寄存器>L1>L2>L3*)>内存>SSD>HDD

### 寄存器
32位CPU中寄存器可存储**4**个字节  
64位CPU中寄存器可存储**8**个字节  

### CPU Cache
*Static Random-Access Memory*

#### *L1*  
2~4个时钟周期，KB级
分为指令缓存和数据缓存

#### *L2*  
10~20个时钟周期，KB~MB

#### *L3*
多个CPU核心共用，MB级  
20~60个时钟周期

### 内存
*Dynamic Random Access Memory*  

**存储器只和相邻一层的存储设备打交道**  

**CPU查询顺序：寄存器->L1->L2->L3->内存**  

---
## 1.3

Cache Line(缓存块)：CPU从内存读取到cache的最小单位。

---
## 1.4 缓存一致性

* 写直达 (Write Through)
  >数据同时写入内存和Cache中
* 写回 （Write Back）
  >数据写入Cache，同时标记其为脏，即与内存数据不一致。下次写入如果Cache为脏，则先写入内存，再写入Cache。否则标记为干净。

* 写传播  
  >CPU核心内的cache更新时，必须传播到其他核心的cache

* 事务的串行化  
  >CPU核心内对数据的操作顺序，必须在其他核心看起来顺序是一样的  
    * CPU核心对cache的操作需要同步给其他CPU核心；
    * **锁🔒**：拿到了锁才能进行数据更新。

### 总线嗅探（MESI协议）

* Modified，已修改
  >脏标记
* Exclusive，独占
  > 数据只存储在一个CPU核心里，如有其他CPU从内存中读进了相同数据，则变为共享
* Shared，共享
  > 
* Invalidated，已失效

### 总结

CPU读写数据都是在自己的cache中进行的

## 1.5 CPU是如何执行任务的

### CPU如何读写数据

**Cache伪共享**  
>多个线程同时读写同一个cache line中的不同变量时，而导致CPU cache失效的现象。

*结构体里的成员变量在物理内存地址上是连续的，因此可能**位于同一个cache line***

```C++
struct test {
    int a;
    int b;
}
```
```C++
struct test {
    int a;
    int b __cacheline_aligned_in_smp;
}
```

*避免伪共享*  
    1.空间换时间   
    2.字节填充(前七后七)+继承  

### CPU如何选择线程的  

* 实时任务：优先级0-99  （调度类：*Deadline* *Realtime*）
* 普通任务：优先级100-139  （调度类：*Fair*）

#### 实时任务 调度策略
* *SCHED_DEADLINE*: 优先调度deadline任务  
*  *SCHED_FIFO*：先进先出，高优先级可以插队  
*  *SCHED_RR*：同优先级任务轮流执行，高优先级可以插队  
  
#### 普通任务 调度策略  
* *SCHED_NORMAL*：普通任务的调度策略
* *SCHED_BATCH*：后台任务，低优先级。  

#### 完全公平调度（*Completely Fair Scheduling*）  

CFS算法调度会优先选择vrtime少的任务。  

虚拟运行时间 += 实际运行时间 * 权重  
vruntime += delta_exec * NICE_0_LOAD  

#### 调整优先级  

nice值：-20~19  

```python
$ nice -n -3 /usr/sbin/mysqld
$ renice -10 -p <正在运行的进程pid>
# nice值只能调整普通任务的优先级
```  

```python
# 修改调度策略为 SCHED_FIFO, 且优先级为1
$ chrt -f 1 -p 1996
```

## 1.6 软中断

**中断：系统用来响应硬件设备请求的一种机制，操作系统收到硬件的中断请求，会打断正在执行的进程，然后调用内核中的中断处理程序来响应请求。**  
**中断处理程序，要尽可能快的执行完，尽可能减少对正常进程运行调度地影响。**  

* 硬中断：硬件触发中断，用来快速处理中断；  
* 软中断：内核触发中断，用来异步处理上半部未完成的工作。

## 1.7 0.1 + 0.2 != 0.3  
















