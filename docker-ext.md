# docker ext

### NameSpace：内核级别的完成环境隔离；

```
PID NameSpace：Linux 2.6.24+，PID隔离,每个namespce都可以有pid为1的进程.
Network NameSpace：Linux 2.6.29+，网络设备、网络栈、端口等网络资源隔离
User NameSpace：Linux 3.8+，用户和用户组资源隔离
IPC NameSpace：Linux 2.6.19+，信号量、消息队列和共享内存(shm)的隔离
UTS NameSpace：Linux 2.6.19+，主机名和域名的隔离；
Mount NameSpace：Linux 2.4.19+，挂载点（文件系统）隔离；
```
	
	
### CGroup：Linux Control Group, 控制组
**Linux 2.6.24+,内核级别实现对进程组群的资源限制和控制,如CPU，内存，IO,centos7上用mount 可以查看挂载的cgroup,或者使用lssubsys -m也可以查看**

CGroup主要功能:

```
Resource limitation：资源限制；限制cpu,memery的使用额度.
Prioritization：优先级控制；使用资源的优先级
Accounting：审计和统计，主要为计费设计；
Control：挂起进程，恢复进程；
```

CGroups的子系统：

```
blkio：设定块设备的IO限制；
cpu：设定CPU的限制；如使用40%的cpu
cpuacct：报告cgroup中所使用的CPU资源；
cpuset：为cgroup中的任务分配CPU和内存资源；分配内存,cpu,如使用那科cpu,那段内存
memory：设定内存的使用限制；
devices：控制cgroup中的任务对设备的访问；
freezer：挂起或恢复cgroup中的任务；
net_cls：（classid），使用等级级别标识符来标记网络数据包，以实现基于tc(tc -h获取使用帮助)完成对不同的cgroup中产生的流量的控制；
perf_event：使用后使cgroup中的任务可以进行统一的性能测试；性能控制相关事件
hugetlb：大内存页分配,对HugeTLB系统进行限制；转换后援缓冲期
```


### UnionFS
**把不同的物理位置的目录合并(挂载)到同一个目录中。合并后只有最上层的目录中的文件可写,写下层文件时不会修改源文件,而是会在上层目录中创建副本**

![](http://other-289777576.mtmssdn0.com/UFS.png)

参考

- [Docker背后的内核知识——Namespace资源隔离](http://www.infoq.com/cn/articles/docker-kernel-knowledge-namespace-resource-isolation)

- [Docker背后的内核知识——cgroups资源限制](http://www.infoq.com/cn/articles/docker-kernel-knowledge-cgroups-resource-isolation)
