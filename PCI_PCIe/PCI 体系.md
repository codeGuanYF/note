## 1 基本概念

### 1.1 组成

基于 PCI 总线的处理器系统中存在 HOST 主桥、PCI 总线、PCI 桥、PCI 设备四种角色。

HOST 主桥
	通过 FSB 与 CPU 相连；通过 PCI 总线与 PCI 设备相连；通过数据总线与 DRAM相连。
	功能：CPU 与 外设（PCI 设备、DRAM等）沟通桥梁。在 PCI 体系中，实现 
	（1）平衡CPU 时钟频率与外设时钟频率； （2）存储器地址空间与 PCI 地址空间转换；

PCI 总线
	并行总线，局部总线。每条 PCI 总线带宽远大于除 PCIe 外其他总线：
	32 位 / 33 MHz PCI 总线峰值速率 132 MB/s， 64位 / 66 MHz PCI 总线峰值速率 532 MB/s。

PCI 桥
	PCI 桥上下连接两条 PCI 总线，起中继功能。PCI 总线通过 PCI 桥扩展，构成 PCI 总线树。

PCI 设备
	PCI 设备分为 PCI 主设备、 PCI 从设备。主设备能够主动发起事务；从设备仅能接受事务。

![[Pasted image 20230718224109.png]]

### 1.2 信号

![[Pasted image 20230718224654.png]]

### 1.3 事务

PCI 总线中，数据主要有三条流：
- CPU 读写 PCI 设备 IO 空间，即 IO Read / IO Write 事务；
- CPU 通过 DMA 方式将数据从内存搬移到 PCI 设备；PCI 设备通过 DMA 方式将数据搬移到内存；即 Memory Read / Memory Write 事务；
- CPU 配置 PCI 设备，即 Configuration Read / Configuration Write 事务。

![[Pasted image 20230718224800.png]]

## 2 读写

### 2.1 不跨 PCI 桥的存储器读写事务


![](https://cdn.nlark.com/yuque/0/2023/png/21738646/1689679400833-fd1b9cdd-e86e-4cd3-8525-a964cd6888f9.png)

PCI 主设备在发起存储器读写事务之前，首先通过仲裁机制获取所在 PCI 总线控制权。

存储器读写事务各信号变化：
- **FRAME**：标识事务从开始到结束全过程。在事务传输第一个时钟拉低，主设备开始传输数据；在最后一个数据传输时拉高，提示从设备中止数据传输。
- **AD**：第一个时钟由传输事务操作地址，之后时钟传输读写数据。
- **C/BE**：第一个时钟传输事务类型，之后时钟控制数据是否有效。
- **IRDY、TRDY**：主设备准备好发送时拉低 IRDY，从设备准备好接收时拉低 TRDY。
- **DEVSEL**：FRAME 信号有效后，该 PCI 总线上所有设备译码，根据读写地址确定自己是否为目标设备，如果是，则拉低 DEVSEL 信号，认领事务。

## 3 配置

## 4 中断