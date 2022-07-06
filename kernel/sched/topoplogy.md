# CPU拓扑结构

# 介绍
&emsp;&emsp;在现代计算机系统中，CPU的拓扑结构是非常复杂的。不同架构的CPU，在拓扑层次上也有很大区别。例如，x86架构，存在逻辑核、物理核和不同Socket上CPU，三种层次。而ARM还有CPU cluster的层次。

&emsp;&emsp;在调度过程中，CPU的拓扑结构会影响任务迁移后的执行效率。例如，在同一个物理核的两个逻辑核间迁移，任务的L1/L2 cache不会发生变动，迁移后执行效率是最高的；而在不同物理核间迁移，L1/L2 cache数据缺失，但是LLC中的数据还能命中，效率是居中的；最后，对于不同Socket上的CPU的任务迁移，不仅所有的cache都会缺失，而且非对称内存之间访问存在远近，会导致最大的延迟。因此，调度子系统必须充分知晓和有效管理CPU拓扑结构，才能很好设计负载均衡。

&emsp;&emsp;Linux内核在调度子系统中使用调度域的概念来描述CPU拓扑结构。以x86架构为例，如图所示，物理核中的两个超线程构成了SMT域，同一Socket中的两个物理核构成了一个MC域，两个Socket构成了NUMA域。
<center>
    <img style="border-radius: 0.3125em;
    box-shadow: 0 2px 4px 0 rgba(34,36,38,.12),0 2px 10px 0 rgba(34,36,38,.08);" 
    src="sched_domain.jpg">
    <br>
    <div style="color:orange; border-bottom: 1px solid #d9d9d9;
    display: inline-block;
    color: #999;
    padding: 2px;">图1. x86架构CPU的基本拓扑结构和调度域</div>
</center>


# 源码剖析