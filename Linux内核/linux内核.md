# 1. 内核模块 Linux最简单的内核模块，入门内核编程和设备驱动开发
```bash
1.vim hello.c    
2.make  
3.sudo insmod hello.ko  
4.lsmod  
5.dmesg  
6.sudo rmmod hello  

linux 指令  
1.sudo insmod hello.ko  
2.cat /proc/modeles  


1.ls /sys/module/hello  
```

[教学视频](https://www.youtube.com/watch?v=triv8bcVLSQ&list=PLHpfx416EzLP2ns3uCecrL1EaDucr33ow)


# 2. linux内核模块参数和导出符号
module_param();
module_param_array();


ls /sys/module/hello/parameters

符号导出：
EXPORT_SYMBOL(hi)
EXPORT_SYMBOL(prt)  //func

外部使用
extern char *hi
extern void ptr(void);

注意编译释放顺序

查看导出符号
1. vim module.symvers
1. cat /proc/kallsyms | grep hello (proc文件系统)

查看依赖(判断能否正常卸载)
ls /sys/module/hello/holders

# 3. Linux分配设备号，字符设备&块设备
查看设备：cat /proc/devices  

## 字符设备
```c {.4}
head:
#include <include/fs.h>
dev_t 类型数据：高12位是主设备号，低20位是次设备号
func:
1.register_chrdev_region() // 已知设备号，完成注册
2.alloc_chrdev_region()    // 由内核分配(动态)
3.unregister_chrdev_region()  // 设备号注销
macro:
1.MAJOR()      // 提取主设备号
2.MINOR()      // 提取次设备号
3.MKDEV()      // 将主次设备号合成一个 dev_t 数据
```
## 块设备
```c
func:
1.regiset_blkdev()  // 输入参数为零 返回值为设备号， 输入参数不为0 ,返回值为0表示成功
2.unregister_blskdev()
```
# 4. 写一个简单的字符设备驱动
面向对象
**字符设备<->设备文件 : 设备结构体 + 操作函数**
```c
涉及函数：
1. kzalloc()/kmalloc()
2. kfree()
3. cdev_init()  // 初始化字符设备
4. cdev_add()   // 将字符设备添加到内核中
5. cdev_del()   // 删除内核字符设备
6. struct file_operations    // 保存操作字符设备的函数

head：
#include <linux/cdev.h>      // 字符设备
#include <linux/slab.h>      // 分配空间头文件

struct hello_char_dev {    // 实际的字符设备结构，类似于面向对象的继承
  struct cdev cdev;
  char c;      // 自己定义的一些属性
}

struct hello_char_dev *hc_devp;

func：
hc_open  hc_read  hc_write  hc_release

struct file_operations hc_fops = {    // 字符设备的操作函数
  .owner = THIS_MODULE,
  .open = hc_open,
  .read = hc_read,
  .write = hc_write,
  .release = hc_release,
};
```
**workflow:**

1. 分配设备号(a. 指定设备号 b. 动态分配设备号)

2. 给字符设备分配空间(kzallc，分配后初始化为0)

3. 初始化字符设备结构(cdev_init(&hc_devp.cdev, &hc_flops))

4. 添加字符设备到系统中(cdev_add(&hc_devp.cdev, ...))

**设备驱动程序可以工作，然而系统不会自动生成device下的设备文件，手动添加设备文件**
```bash
  sudo mknod /dev/hc_dev0 c 240 0
  sudo mknod /dev/hc_dev1 c 240 1
c：字符设备  240: 主设备号   0/1: 次设备号
```
`test1 :
1. cat /dev/hc_dev0(读取设备)
2. dmesg
Phenomenon：open + read + release

test2 :
1. echo 1 > /dev/hcdev0(写设备)
2. dmesg
Phenomenon：open + write + release`


# 5.模块加载后自动设备节点(udev后台进程自动生成)
```c
func：
1. class_creat()      // 创建类  在 在/sys/class/下创建文件夹
   clasee_destory()
2. device_create()    // 在创建的类下创建设备 有了这步会在/dev生成设备节点
   device_destory()

head:
#include <linux/device.h>

struct data:
struct class *hc_cls;
```
workflow:   
4. ...   
5. 创建类(hc_cls = class_create(, "hc_dev");)   
6. 创建具体的设备(device_create(hc_cls,...., "hc_dev%d", i));   

设备读写权限不足：修改规则:   
查看规则 `ls /etc/udev/rules.d/*.rules`  
方式：
1. 需要用户组

  SUBSYSTEM == "hc_dev", GROUP = "g"

2. 修改权限

  SUBSYSTEM == "hc_dev", MODE = "0666"
  
怎么查看设备信息：

  udevadm info -a -n /dev/hc_dev0

# 6.Linux实现具体的设备读写功能，如何使用container of、copy to user、copy from user
```c
func：
1. container_of()     // 通过结构体成员地址得到结构本身的地址
2. copy_to_user()     // read 函数用，从内核到用户
3. copy_from_user()   // write 函数用，从用户到内核
4. struct inode       // 表示文件，用到其成员i_cdev
5. struct file        // 表示打开的文件描述符，用到成员private_data

head:
#include <linux/uaccess.h>
```
[Linux 驱动设备的读写实现](https://www.youtube.com/watch?v=f1pB5XNnf9E&list=PLHpfx416EzLP2ns3uCecrL1EaDucr33ow&index=6)

# 7.Linux实现同步和互斥
  信号量，互斥锁 【读写锁、自旋锁、seqlock、原子变量......】
```c
func:
1. sema_init()
2. down_interruptible()
3. up()
4. mutex_init()
5. mutex_lock_interruptible()
6. mutex_unlock()

head:
#include <linux/semaphore.h>
#include <linux/mutex.h>

struct hello_char_dev {
  struct cdev cdev;
  char *c;
  int n;
  struct semaphore sema;
  struct mutex mtx;
}
```
workflow:
1. init
  sema_inti(.sema, 1);     // 初始化信号量
  mutex_init(.mtx);        // 初始化互斥量

2. 使用
  if (down_interruptible(&sema))    // 为0 则成功下面的流程 为 -1 就休眠 等待
      return ERSETARTSYS;
  if (mutex_lock_interruptible(&mtx))
      return ERSETARTSYS;

3. 释放
  up(&sema);
  mutex_unlock(&mtx);

[linux 同步与互斥](https://www.youtube.com/watch?v=f1pB5XNnf9E&list=PLHpfx416EzLP2ns3uCecrL1EaDucr33ow&index=7)

# 8.Linux实现设备驱动的ioctl函数
  ioctl执行硬件控制，除了读写外的其他操作，比如锁门、弹出介质、设置波特率、设置比特位等。   

`long (*unlocked_ioctrl)(struct file *filp, unsigned int cmd. unsigned long arg);`

命令构成：

| direction(方向)  | size(数据大小) | type(幻数) | number(序数) |
| --------------  | ------------   | ---------   | ------------  |  
|    2bits         |      14bits    |  8bits      |  8bits         |  
```c
宏：nr表示指令号
  _IO(type,nr)  _IOR(type, nr, size)  _IOW(type, nr, size)  _IOWR(type, nr, size)   // 构造命令  
  _IOC_DIR(nr)  _IOC_TYPE(nr)    _IOC_NR(nr)    _IOC_SIZE(nr)      // 提取命令字段  

example:
#define HC_IOC_MAGIC 0x81
#define HC_IOC_RESET        _IO(HC_IOC_MAGIC, 0)        // 清空空间
#define HC_IOCP_GET_LENS    _IOR(HC_IOC_MAGIC, 1, int)  // 通过指针返回
#define HC_IOCV_GET_LENS    _IO(HC_IOC_MAGIC, 2)        // 通过返回值返回，正常情况不会混用两种取值方式
查看可用幻数：
 /Documentation/userspace-api/ioctl/ioctl/ioctl-numberr.rst
func:
  access_ok()                // 检查用户空间地址是否OK
  put_user()  _put_user()    // 向用户空间写数据  (少量数据)
  get_user()  _get_user()    // 从用户空间接收数据
  capable()                  // 检查进程是否有权限

add func:
long hc_ioctl(struct file *filp, unsigned int cmd. unsigned long arg)

update struct:
struct file_operations hc_fops = {    // 字符设备的操作函数
  .owner = THIS_MODULE,
  .open = hc_open,
  .read = hc_read,
  .write = hc_write,
  .release = hc_release,
  .unlocked_ioctl = hc_ioctl,         // 还有一个compat_ioctl，用于32位程序在64位系统运行
};
```

[linux ioctl函数实现](https://www.youtube.com/watch?v=7mEk3Xw9osQ&list=PLHpfx416EzLP2ns3uCecrL1EaDucr33ow&index=8)

# 9.Linux实现进程简单休眠 把CPU让给其他进程
放入休眠等待队列
```c
func:
  初始化：
  1. DECLARE_WAIT_QUEUE_HEAD(wq);  // example
  2. wait_queue_head_t wq;
  3. init_waitqueue_head()
  休眠：
  1. wait_event();                 // 不可中断进程(死等)
  2. wait_event_interruptible()    // 可中断进程
  唤醒：
  1.wake_up()
  2.wake_up_interruptible()

head:
#include <linux/wait.h>
```

[linux 休眠实现](https://www.youtube.com/watch?v=qQdjhuXfjXw&list=PLHpfx416EzLP2ns3uCecrL1EaDucr33ow&index=9)


# 10.Linux内核如何表示时间，如何实现延时
```c
func：
HZ：100 - 1000之间
jiffies: 系统时钟中断计数器
使用jiffies可以使用下面的比较宏，避免32位溢出
time_after(a,b)         a>b?    // a比b大返回1
time_before(a,b)        a>b?    // a比b大返回0
time_after_eq(a,b)      a>=b?
time_before_eq(a,b)     a>=b?

jiffies与常用时间的转换:
jiffies_to_msece();
jiffies_to_usece();
msece_to_jiffies();
usece_to_jiffies();
jiffies_to_timespec64();
timespec64_to_jiffies();

延时：
wait_event_timeout();
wait_event_interruptible_timeout();
set_current_state();     // 改变进程状态
schedule_timeout();      // 与set...一起使用

ndelay();      // 忙等待 CPU死循环  短时用
udelay();
mdelay();      // 考虑使用 msleep

休眠延时：
usleep_rang(); // (10us,20ms) 可选范围 系统自定
msleep();
msleep_interruptible();
ssleep();

head:
#include <linux/jiffies.h>
#include <linux/seched.h>
#include <linux/delay.h>
```

# 11.Linux内核实现延缓操作

supplement：
在 Linux 内核中，`内核定时器(Kernel Timer)`、`Tasklet` 和 `Workqueue` 是用于处理异步任务和延迟执行的机制。

**内核定时器(Kernel Timer)**：内核定时器是一种在特定时间后执行特定函数的机制。它们通常用于实现如超时、定时更新等功能。
内核定时器只能在进程上下文中运行，不能在中断上下文中运行。

**Tasklet**：Tasklet 是一种**底半部（Bottom Half）机制**，用于在中断处理结束后延迟执行一些任务。Tasklet 可以在中断上下文中运行，
它们在所有 CPU 上共享一个队列，因此同一时间只有一个 Tasklet 可以运行，避免了并发问题。但是，不同的 Tasklet 可以在不同的 CPU 上并行运行。

**Workqueue**：Workqueue 是一种更灵活的**底半部机制**，它允许在进程上下文中延迟执行任务。
与 Tasklet 不同，`Workqueue 可以睡眠`，因此它们可以执行可能会阻塞的操作，如内存分配、文件 I/O 等。
Workqueue 也支持并发执行，每个 Workqueue 都有自己的队列，可以在多个 CPU 上并行运行。

```c
1. Kernel Timer                    // 中断 0号进程：swapper
   struct timer_list
   timer_setup();
   mod_timer();
   del_timer();
2. tasklet                      // 软中断 ksoftirqd
  struct tasklet_struct
  tasklet_init();
  tasklet_hi_schedule();
  tasklet_schedule();
  tasklet_kill();
3. workqueue                  // 内核线程的一部分 kworker
  alloc_workqueue();
  destory_workqueue();
  struct work_struct
  INIT_WORK();

  struct delayed_work
  INIT_DELAYED_WORK();
  queue_delayed_work();
4. others
  in_interrupt();            // 判断是否在中断中
  smp_processor_id();        // 给出程序运行的核ID

head:
#include <linux/interrupt.h>    // timer  tasklet  workqueue
#include <linux/timer.h>        // timer
#include <linux/workqueue.h>    // workqueue

update(add member) struct:
struct hello_char_dev {
  struct cdev cdev;
  char *buffer;
  int loops,
  int tdelay;

  struct timer_list t1;
  struct tasklet_struct tsklt;
  struct work_struct work;
  struct delayed_work dwork;
}

```


# 12.proc文件系统 Linux创建proc文件系统接口，揭露proc文件秘密，调试内核更加方便

**Linux 的 /proc 文件系统（通常被称为 procfs）是一个虚拟文件系统，它创建了一个内核与用户空间之间的接口。通过这个接口，用户空间的程序可以读取内核的数据，也可以修改内核的某些设置。**

/proc 文件系统包含了大量的文件和目录，这些文件和目录对应了内核中的各种数据结构。例如：

/proc/[pid]：每个正在运行的进程都有一个对应的目录，目录名是进程的 PID。这个目录包含了关于这个进程的各种信息，如状态、内存使用量、打开的文件等。

/proc/meminfo：这个文件包含了关于系统内存使用的信息。

/proc/cpuinfo：这个文件包含了关于 CPU 的信息。

/proc/sys：这个目录包含了可以用来修改内核设置的文件。

需要注意的是，`/proc 文件系统中的文件并不对应实际的磁盘文件，它们是内核的内部数据结构的映射`。当你读取或写入这些文件时，你实际上是在读取或修改内核的内部数据。
```c
func：
1. 传统
  struct proc_ops
  proc_create();
  head:
  #include <linux/proc_fs.h>
2. seq
  struct seq_operations
  proc_create_seq();
  remove_proc_entry();
  head:
  #include <linux/seq_file.h>

struct proc_opsc hp_ops = {
  .proc_open = hp_open,
  .proc_read = hp_read,
}

const struct seq_operations seq_ops = {
  .start= hp_seq_start,
  .stop = hp_seq_stop,
  .next = hp_seq_next,
  .show = hp_seq_show,
}
```

# 13.linux内存分配函数
```c
func：
1. 小字节(<1000)
  kmalloc();
  kzalloc();
  kfree();
2. slab分配器/专用高速缓存/速度快/利用率高/针对频繁申请释放
  struct kmem_cache
  kmem_cache_creat();
  kmem_cache_alloc();
  kmem_cache_free();
  kmem_cache_destroy();
3. 大块内存，按页分配
  __get_free_page();    // 分配一页
  __get_free_pages();   // 分配多页
  get_zeroed_page();
  free_page();
4.分配大块连续地址(虚拟地址连续/物理地址不连续/效率不高/>16pages)
  vmalloc();
  vfree();
```

# 14.数据类型和对齐 Linux内核基础数据类型，移植性核数据对齐
不同的架构，基础类型大小可能不同，主要区别是在long 和 ptr上:  
| arch   | size: | char | short | int | `long` | `ptr` | long-long | u8 | u16 | u32 | u64 | pid_t |   
| ------ | ----- | ---- | ----- | --- | ------ | ----- | --------- | -- | --- | --- | --- | ----- |
| x86_64 |       |  1   |   2   |  4  |  `8`   |  `8`  |    8      | 1  |  2  |  4  |  8  |   4   |   
| armv7  |       |  1   |   2   |  4  |  `4`   |  `4`  |    8      | 1  |  2  |  4  |  8  |   4   |   

**小端**：低位在低地址，高位在高地址 0x1234abcd -->> 1234abcd  
**大端**：低位在高地址，高位在低地址 0x1234abcd -->> cdab3412  
1. cpu_to_le32()    le32_to_cpu()    // cpu2小端； 小端2cpu     
2. cpu_to_be32()    be32_to_cpu()    // cpu2大端； 大端2cpu  

3. htonl()          ntohl()          // host2net 长整型； net2host 长整型  
4. htons()          ntohs()          // host2net 短整型； net2host 短整型  

`主机是小端、网络大端`   

| arch   |  size: |  2le32   |  2be32   |  htonl   |  ntohl(0x1234abcd) |
| ------ | ------ | -------- | -------- | -------- | ------------- |
| x86_64 |        | 1234abcd | cdab3412 | cdab3412 | cdab3412 |
| armv7  |        | 1234abcd | cdab3412 | cdab3412 | cdab3412 |

数据对齐原则：数据存放的地址必须可以被的类型大小整除。[Linux 数据对齐，测试](https://www.youtube.com/watch?v=XpHI3bS5JE8&list=PLHpfx416EzLP2ns3uCecrL1EaDucr33ow&index=14)


# 15.Linux内核中断的使用

在 Linux 内核中，中断处理被分为两部分：`顶半部(Top Half)`和`底半部(Bottom Half)`。    

**顶半部**：当硬件中断发生时，内核会立即响应并执行一个预定义的中断处理程序，这部分被称为顶半部。
顶半部的主要任务是响应中断，并进行必要的最小处理，如读取硬件状态，清除中断标志等。
由于顶半部需要快速响应并返回，因此它不能执行可能会阻塞的操作，如内存分配、文件 I/O 等。

**底半部**：底半部是顶半部延迟执行的部分，它负责执行`可能会阻塞`的操作，如内存分配、文件 I/O 等。
`底半部可以被中断，也可以在多个 CPU 上并行执行。`
Linux 内核提供了多种底半部机制，如软中断（Softirq）、Tasklet、Workqueue 等。

这种分离的设计可以确保内核能快速响应中断，同时也能处理复杂的任务。**顶半部处理紧急的任务，底半部处理可以延迟的任务。**

三种底半部有什么区别：
软中断（Softirq）、Tasklet 和 Workqueue 都是 Linux 内核中用于处理异步任务的机制，但它们在运行环境、调度方式和使用场景上有所不同：

1. 软中断（Softirq）：运行在中断上下文中，不能被阻塞，也不能睡眠。软中断在同一时间可以在多个 CPU 上并行运行，即使是同一个软中断也可以并行。软中断的调度相对复杂，需要手动调度。通常用于需要高并发和高性能的场景，如网络和磁盘子系统。

2. Tasklet：基于软中断的一种简化机制，运行在中断上下文中，不能被阻塞，也不能睡眠。同一个 Tasklet 在同一时间只能在一个 CPU 上运行，但不同的 Tasklet 可以在不同的 CPU 上并行运行。Tasklet 的调度相对简单，内核会自动调度。更适合处理简单的异步任务。

3. Workqueue：运行在进程上下文中，可以被阻塞，也可以睡眠。工作队列可以创建多个工作项，这些工作项可以在多个 CPU 上并行运行。由于运行在进程上下文中，工作队列可以执行可能会阻塞的操作，如内存分配、文件 I/O 等。

# 16. Linux通过IO内存访问外设
外设将自己的寄存器映射到系统的物理内存空间中(这部分区域就是IO内存区域)，可以通过读写这些内存地址来操作设备。  
```c
func:
request_mem_region();    // 外设将自己的寄存器防止到系统的内存空间中，这一步通常不是软件做的
release_mem_region();
ioremap();               // 创建一个新的内存映射，将物理地址转化为内核可以访问的虚拟地址
iounmap();
ioread8()/ioread16()/ioread32()      // 读写虚拟内存func
iowrite8()/iowrite16()/iowrite32()
```


# 17. Linux设备驱动PCI的驱动如何写

```c
struct pci_device_id    // PCI驱动支持的设备,用于描述 PCI 驱动支持的设备。它包含了设备的供应商 ID、设备 ID、类代码等信息。
PCI_DEVICE();
PCI_DEVIDE_CLASS();
MODULE_DEVICE_TABLE();  // 导出pci_device_id结构体到用户空间，模块与硬件设备的对应关系

struct pci_driver
pci_regitster_driver();  // 注册pci驱动到内核
pci_unregiset_driver();  // 注销

pci_enable_device();     // 激活/初始化PCI设备，比如唤醒设备、读写配置信息等
pci_disable_device();

pci_read_config_byte();
pci_read_config_word();
pci_read_config_dword();
pci_resource_start();   // 获取区域信息(bar info) pci支持6个区域(io端口/内存) BAR（Base Address Register）的起始地址。
pci_resource_end();     // 这个函数用于获取 PCI 设备的某个 BAR 的结束地址
pci_resource_flags();   // 这个函数用于获取 PCI 设备的某个 BAR 的标志

pci_request_regions();  // 跟request_mem_region()一样
pci_release_regions();

pci_ioremap_bar();      // 用于映射 PCI 设备，跟ioremap 一样，做了必要检查

pci_set_drvdata();      // 设置驱动私有数据
pci_get_drvdata();      // 设置驱动私有数据

head:
#include <linux/pci.h>
```

![设备单](/image/Snipaste_2024-09-23_16-52-59.png)







