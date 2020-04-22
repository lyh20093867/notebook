### cpu使用率
单位时间内cpu使用情况的统计，以百分比的方式展示； 通常说的cpu使用率，就是除了空闲时间外的其他时间占cpu总时间的百分比。  
为了维护 CPU 时间，Linux 通过事先定义的节拍率(内核中表示为 HZ)，触发时间中断，并使用全局变量 Jiffies 记录了开机以来的节拍数。每发生一次时间中断，Jiffies 的值 就加 1。   
可以通过/boot/下面的config配置查看节拍率,表示每秒钟触发多少次时间中断，该值是内核选项，用户空间程序不能直接访问。
```
grep 'CONFIG_HZ=' /boot/config-$(uname -r)
CONFIG_HZ=1000
```
Linux 通过 /proc 虚拟文件系统，向用户空间提供了系统内部状态的信息，而 /proc/stat 提供的就是系统的 CPU 和任务统计信息。比方说，如果你只关注 CPU 的话，可以执行下 面的命令: 
不过下面的命令统计的是服务器自开机以来，cpu的总的使用情况。 
```
# 只保留各个cpu的数据
$ cat /proc/stat | grep cpu

#cpu编号 us   ni   sys     id          wa       hi    si    st  guest gnice
cpu  9056141 1096 6988305 6402780953 12292704 8478 701190 802089 0 0
cpu0 1335538 141 911099 798864674 2763842 0 9737 123879 0 0
cpu1 1197624 117 867149 798789816 3164608 0 1733 108645 0 0
cpu2 1030262 125 768893 801996495 472455 0 334 90843 0 0
cpu3 982842 151 736619 801357672 1231483 0 130 88422 0 0
cpu4 945860 137 711935 802103852 561925 0 92 83736 0 0
cpu5 1663827 215 1492141 795626152 2884568 8478 688885 140962 0 0
cpu6 946555 102 766415 802339504 286006 0 139 84434 0 0
cpu7 953629 103 734051 801702784 927813 0 137 81165 0 0
```
第一行是总的cpu统计，是后面所有cpu的累加，后面的是每个cpu核单独的使用情况统计；该表每列的含义可以通过man proc进行查看。
下面是每列的含义
```
user(通常缩写为 us)，代表用户态 CPU 时间。注意，它不包括下面的 nice 时间，但 包括了 guest 时间。
nice(通常缩写为 ni)，代表低优先级用户态 CPU 时间，也就是进程的 nice 值被调整 为 1-19 之间时的 CPU 时间。这里注意，nice 可取值范围是 -20 到 19，数值越大，优先级反而越低。
system(通常缩写为 sys)，代表内核态 CPU 时间。
idle(通常缩写为 id)，代表空闲时间。注意，它不包括等待 I/O 的时间(iowait)。
iowait(通常缩写为 wa)，代表等待 I/O 的 CPU 时间。
irq(通常缩写为 hi)，代表处理硬中断的 CPU 时间。
softirq(通常缩写为 si)，代表处理软中断的 CPU 时间。
steal(通常缩写为 st)，代表当系统运行在虚拟机中的时候，被其他虚拟机占用的 CPU 时间。
guest(通常缩写为 guest)，代表通过虚拟化运行其他操作系统的时间，也就是运行虚 拟机的 CPU 时间。
guest_nice(通常缩写为 gnice)，代表以低优先级运行虚拟机的时间。
```
cpu使用率=1-(空间时间id/cpu总时间)       
通常top等性能工具是取一个时间段的先后cpu使用情况，做差计算的这段时间内的平均cpu使用率。     
cpu使用率=1-((新空闲时间-老空闲时间)/(新的cpu总时间-老的cpu总时间))

每个进程运行情况的统计数据，通常在/proc/[pid]/stat文件中；当使用不同的工具进行对比的时候，需要注意不同工具时间间隔的设置。   
### 查看cpu使用率
top   
pidstat   
### cpu使用率过高
GDB(The GNU Project Debugger)，不适合在性能分析早期使用，因为它会中断程序运行。    
Perf，2.6.31后内置的性能分析工具。通过perf可以找出引发性能问题的函数；      
perf top，类似于 top，它能够实时显示占用 CPU 时钟最多的函数或者 指令，因此可以用来查找热点函数，使用界面如下所示:
需要注意的是该命令需要root权限！！
```
perf top -g
Samples: 1K of event 'cpu-clock', 4000 Hz, Event count (approx.): 173331383 lost: 0/0 drop: 0/0
Overhead  Shared               Object         Symbol
  12.60%  perf                  [.] __symbols__insert
   4.68%  perf                  [.] rb_next
   3.07%  libc-2.17.so          [.] __GI_____strtoull_l_internal
   2.87%  perf                  [.] rb_insert_color
   2.65%  [kernel]              [k] kallsyms_expand_symbol.constprop.1
   2.59%  [kernel]              [k] format_decode
```
第一列 Overhead ，是该符号的性能事件在所有采样中的比例，用百分比来表示。    
第二列 Shared ，是该函数或指令所在的动态共享对象(Dynamic Shared Object)， 如内核、进程名、动态链接库名、内核模块名等。   
第三列 Object ，是动态共享对象的类型。比如 [.] 表示用户空间的可执行程序、或者动 态链接库，而 [k] 则表示内核空间。   
最后一列 Symbol 是符号名，也就是函数名。当函数名未知时，用十六进制的地址来表 示。    

-g开启调用关系的采样；-p指定某个已经定位到的进程号；

perf top是实时展示当前系统的性能信息，但是不能对数据进行保存，不能离线分析，通过perf record和perf report用于离线采样和保存数据；
```
[root@mj-9-85 ~]# perf record -g
^C[ perf record: Woken up 17 times to write data ]


[ perf record: Captured and wrote 5.061 MB perf.data (43682 samples) ]
```
perf record的时候需要使用Ctrl +c终止采样；采样后的数据会默认保存在当前的目录下，文件名类似perf.data

### 案例
以Nginx+PHP的web服务为例，通过top等工具找出异常的进程，利用perf找出引发性能问题的函数。   
ab(apache bench)是一个常用的HTTP服务性能测试工具，可以用来模拟Nginx的客户端。   
此处需要使用两台虚拟机，并且需要事先安装docker、sysstat、perf、ab 等工具，如 apt install docker.io sysstat linux- tools-common apache2-utils   

两台服务器其中一台模拟服务器，另一台模拟客户端。

```
# 并发10个请求，总共测试100个请求
ab -c 10 -n 100 http://192.168.0.10:10000/
```
```
perf top -g -p pidNum
```

在第一个终端运行:
```
$ docker run --name nginx -p 10000:80 -itd feisky/nginx
$ docker run --name phpfpm -itd --network container:nginx feisky/php-fpm
```
使用curl命令访问一下第一个终端起的nginx，看是否启动成功
```
# 192.168.0.10 是第一台虚拟机的 IP 地址 
$ curl http://192.168.0.10:10000/
It works!
```
使用ab命令测试一下nginx的性能
```
ab -c 10 -n 100 http://192.168.0.10:10000/
```
从容器phpfpm中将php源码拷贝出来
```
 # 从容器 phpfpm 中将 PHP 源码拷贝出来
$ docker cp phpfpm:/app .

# 使用 grep 查找函数调用
$ grep sqrt -r app/ # 找到了 sqrt 调用
app/index.php: $x += sqrt($x);
$ grep add_function -r app/ # 没找到 add_function 调用，这其实是 PHP 内置函数
```
在第一个终端停止原来的服务
```
 # 停止原来的应用
$ docker rm -f nginx phpfpm
# 运行优化后的应用
$ docker run --name nginx -p 10000:80 -itd feisky/nginx:cpu-fix
$ docker run --name phpfpm -itd --network container:nginx feisky/php-fpm:cpu-fix
```
在第二个终端先使用Ctrl+C停止原来的ab命令，重新执行
```
ab -c 10 -n 10000 http://10.240.0.5:10000/
```

https://www.zybuluo.com/Gugoole/note/1354842

### 案例2，cpu使用率高时，不一定能够找到相应高cpu使用率的进程，
因为中断处理、等待IO以及内核线程等都会消耗CPU，此时不能是某个进程；   
可以使用execsnoop工具专门对短时进程进行监控，通过ftrace实时监控进程的exec()行为，并输出短时进程的基本信息，是一种常用的动态追踪技术。   
对于短时进程，可以通过使用pstree或者execsnoop找到它们的父进程，再从父进程入手分析。    

### 案例3，系统出现大量不可中断进程和僵尸进程
top命令的S列是状态列(Status)，通常有R、D、S、Z、L、X、T共7个状态。
```
R 是 Running 或 Runnable 的缩写，表示进程在 CPU 的就绪队列中，正在运行或者正 在等待运行。
D 是 Disk Sleep 的缩写，也就是不可中断状态睡眠(Uninterruptible Sleep)，一般表 示进程正在跟硬件交互，并且交互过程不允许被其他进程或中断打断。
Z 是 Zombie 的缩写，如果你玩过“植物大战僵尸”这款游戏，应该知道它的意思。它 表示僵尸进程，也就是进程实际上已经结束了，但是父进程还没有回收它的资源(比如进 程的描述符、PID 等)。
S 是 Interruptible Sleep 的缩写，也就是可中断状态睡眠，表示进程因为等待某个事件 而被系统挂起。当进程等待的事件发生时，它会被唤醒并进入 R 状态。
I 是 Idle 的缩写，也就是空闲状态，用在不可中断睡眠的内核线程上。前面说了，硬件交 互导致的不可中断进程用 D 表示，但对某些内核线程来说，它们有可能实际上并没有任 何负载，用 Idle 正是为了区分这种情况。要注意，D 状态的进程会导致平均负载升高，
I 状态的进程却不会。
T 或者 t，也就是 Stopped 或 Traced 的缩写，表示进程处于暂停或者跟踪状 态。
X，也就是 Dead 的缩写，表示进程已经消亡，所以你不会在 top 或者 ps 命令 中看到它。
```