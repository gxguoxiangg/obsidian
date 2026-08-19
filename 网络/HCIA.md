## HCIA

### 数据通信网络基础

**通信的三要素：信源、信道、信宿**

### 网络参考模型

**网络分类：局域网、城域网、广域网 LAN、MAN、WAN**

**为什么分层：**

各层次之间分工和界限明确，方便各个部件开发、设计和排障

1. 模块化与解耦

   分层将复杂的网络通信过程分解为多个独立的层级，每一层只负责特定的功能，层与层之间通过明确的接口交互。这样降低了系统的复杂度，使设计、实现和调试变得更容易。

2. 标准化与互操作性

   分层结构允许不同厂商独立开发各层的软硬件（如物理设备、操作系统、应用程序），只要遵循统一的协议标准（如以太网、IP、TCP、HTTP），不同系统之间就能相互通信。这是互联网能够蓬勃发展的基础。

3. 职责分离和复用

   每一层专注解决一类问题，这种分离允许上层协议复用下层服务（例如，HTTP、FTP都可以基于TCP运行），而不必关心下层的具体实现。

4. 易于升级和替换

   分层结构允许单独修改或升级某一层的技术，而不影响其他层。例如：

   - 从IPv4升级到IPv6主要涉及网络层，对应用层和传输层影响有限。
   - 物理层从有线升级到无线（Wi-Fi）时，上层协议几乎无需改动。

5. 故障隔离和调试

   当网络出现问题时，可以逐层排查（如检查物理连接→MAC地址→IP路由→端口→应用程序），快速定位故障所在层级。

   

### 网络设备操作系统

配置时间

设置启动的配置文件


### 以太网交换机基础

CSMA/CD：先听后发，边发边听，冲突停发，随机延迟后重发。

早期网络：建立在CSMA/CD机制上的广播型网络，冲突的产生是限制网络性能的重要因素，早期网络设备如HUB是物理层设备，不能隔绝冲突扩散。

交换机组网：交换机做为一种能隔绝冲突的二层网络设备，提高了网络性能。

![image-20260129120922405](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260129120922405.png)

type 0800 IPv4，type 0806 ARP

**其中的Type，当取值0~1500时，表示长度，1536 ~ 65535 表示类型。**

以太网帧总长度：64 ~ 1518 字节

IP地址和MAC地址为什么需要同时存在，只有一个不行吗：

1. 设计层面：IP地址是根据网络的拓扑结构分配的，MAC地址是根据制造商分配的，若路由选择建立在设备制造商的基础上，这种方案是不可行的。
2. 当存在两层地址寻址时，设备更灵活，易于维护。比如如果一个以太网卡坏了，可以被更换，而无须更换一个新的IP地址，如果一个IP主机从一个网络移到另一个网络，可以给它一个新的IP地址，无须更换一个新的网卡。
3. IP地址的作用是唯一标识网络中的一个节点，可以通过IP地址进行不同网段的数据访问。
4. MAC地址的作用是唯一标识一个网卡，可以通过MAC地址进行同网段的数据访问。

MAC地址：

1. OUI 厂商代码由IEEE分配，长度为24bit

   ![image-20260129143554860](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260129143554860.png)

2. 组播mac地址用于标识链路上的一组节点，不能作为源地址，只能作为目的地址，广播地址是一种特殊的组播mac地址。

二层交换设备通过学习以太网数据帧的源MAC地址来维护MAC地址与接口的对于关系（MAC地址表），通过其目的MAC地址来查找MAC地址表决定向哪个接口转发。

ARP协议：

- 将已知的IP地址解析获得MAC地址。
- 维护IP地址与MAC地址的映射关系。
- 实现网段内重复IP地址的检测。

ARP请求流程：

如果目标设备位于其他网络，则原设备会在ARP缓存表中查找网关的MAC地址，然后将数据发送给网关，最后网关再把数据转发给目的设备。



###	VLAN 原理和配置

通过在交换机上部署VLAN，可以将一个规模较大的广播域在逻辑上划分成为若干个不同的、规模较小的广播域，提高安全性、减少垃圾流量。

好处：

- 网络构建和维护更方便和灵活
- 限制广播域
- 增强局域网安全性
- 提高了网络的健壮性

VLAN数据帧：DMAC | SMAC | Tag | Type | Data | FCS

![image-20260202111824839](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260202111824839.png)

VLAN划分方式：

1. 基于接口划分
2. 基于MAC地址划分
3. 基于IP子网划分
4. 基于协议划分
5. 基于策略划分

缺省VLAN，PVID（Port VLAN ID）

- 每个交换机的接口都应该配置一个PVID，到达这个端口的Untagged帧将一律被交换机划分到PVID所指代的VLAN
- 默认情况下，PVID = 1



![image-20260202115514654](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260202115514654.png)

Access接口：

- 仅允许VLAN ID 与接口PVID相同的数据帧通过。

  ![image-20260202120920782](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260202120920782.png)

Trunk口除了要配置PVID外，还必须配置允许通过的VLAN ID列表，其中VLAN1是默认存在的。

Trunk接口特点：

- 仅允许VLAN ID在允许通过列表中的数据帧通过。
- 可以允许多个VLAN的帧带Tag通过，但只允许一个VLAN的帧从该类接口上发出时不带Tag（剥除Tag）

![image-20260203133909913](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260203133909913.png)

Hybrid接口特点：

- 仅允许VLAN ID在允许通过列表中的数据帧通过。
- 与Trunk最主要的区别就是，能够支持多个VLAN的数据帧，不带标签通过。
- 可以允许从该类接口发出的帧根据需要配置某些VLAN的帧带Tag，某些VLAN的帧不带Tag。



Access接口：

- 接收数据帧：
  - Untagged数据帧，打上PVID，接收。
  - Tagged数据帧，与PVID比较，相同则接收，不同则丢弃。
- 发送数据帧：
  - VID与PVID比较，相同则剥离后发送，不同则丢弃。

Trunk接口：

- 接收数据帧：
  - Untagged数据帧，打上PVID，如果VID在允许列表中，则接收，否则丢弃。
  - Tagged数据帧，检查是否在允许列表中。如果在则接收，不在则丢弃。
- 发送数据帧：
  - 如果不在允许列表中，丢弃。
  - 如果在允许列表中，检查PVID和VID是否相同，相同则剥离后发送，不同则直接发送。

Hybrid接口：

- 接收数据帧：
  - Untagged数据帧，打上PVID，且VID在允许列表中，则接收；VID不在允许列表中，则丢弃。
  - Tagged数据帧，查看是否在允许列表中，在则接收，不在则丢弃。
- 发送数据帧：
  - VID不在允许列表中，直接丢弃。
  - VID在Untagged列表中，剥离标签发送。
  - VID在Tagged列表中，带标签直接发送。



当接收到数据帧时：

- Untagged：Access、Trunk、Hybrid接口都会给数据帧打上Tag，但是Trunk、Hybrid会根据数据帧的VID是否为其允许通过的VLAN来判断是否接收，而Access则是无条件接收。
- Tagged：Access、Trunk、Hybrid都会检查是否为允许通过。

当发送数据帧时：

如果VID在允许通过的列表中则

- Access：直接剥离发送。
- Trunk：当PVID和VID相同时剥离。
- Hybrid：VID在Untagged列表中才剥离。

因此，Access接口发出的数据帧肯定不带Tag，Trunk接口发出的数据帧只有一个VLAN的数据帧不带Tag，其他都带VLAN标签；Hybrid接口发出的数据帧可根据需要设置某些VLAN的数据帧带Tag，某些VLAN的数据帧不带Tag。

**VLAN的基础配置命令**

创建vlan

```bash
# 创建VLAN并进入VLAN视图，vlan-id是整数形式，取值范围是1~4094
[HW] vlan "vlan-id"

# 批量创建VLAN
[HW] vlan batch "vlan-id1" to "vlan-id2"
```

配置接口类型

```bash
[HW-G1/0/1] port link-type access
[HW-G1/0/1] port default vlan "vlan-id"

[HW-G1/0/1] port link-type trunk
[HW-G1/0/1] port trunk allow-pass vlan {"vlan-id" | "vlan-id" to "vlan-id" | all}
[HW-G1/0/1] port trunk pvid vlan "vlan-id"

[HW-G1/0/1] port link-type hybrid
[HW-G1/0/1] port hybrid untagged vlan {"vlan-id" to "vlan-id" | all}
[HW-G1/0/1] port hybrid tagged vlan {"vlan-id" to "vlan-id" | all}

[HW-G1/0/1] port hybrid pvid vlan "vlan-id"	
```



### 生成树原理和配置

**一个根桥**

树形网络必有树根，根桥是网络的逻辑中心，随网络拓扑的变化而变化，网络收敛后，会按照一定时间间隔向外发送配置BPDU，其他设备仅对该 报文进行处理，传达拓扑变化记录，从而保证拓扑的稳定。

**两种度量**

- ID：BID和PID，BID由16位优先级和48位MAC地址组成，PID由4位端口优先级和12位端口号组成。
- 路径开销

**三要素选举**

**四个比较原则**

STP选举四个比较原则，构成消息优先级向量：{根桥ID、路径开销、发送设备BID、发送端口PID}

**五种端口状态**



STP 操作：

- 选举一个根桥
- 每个非根交换机选举一个根端口
- 每个链路选举一个指定端口
- 阻塞非根，非指定端口

STP 三种端口角色：

- 根端口：非根交换机去往根桥路径最优的端口，在一个运行STP协议的交换机上最多只有一个根端口，根桥上没有根端口。
- 指定端口：交换机先所连链路转发配置BPDU的端口，每个链路有且仅有一个指定端口。
- 预备端口：将被阻塞。

STP 的工作流程：

- 选举根桥
- 选举根端口
- 选举指定端口
- 阻塞非指定端口（预备端口）

STP基础配置命令：

1. 配置生成树工作模式

   ```bash
   [HW] stp mode {stp | rstp | mstp}
   ```

   默认情况下工作在MSTP模式。

2. 配置根桥

   ```bash
   [HW] stp root primary
   ```

   默认情况下，交换机不作为任何生成树的根桥，配置后该设备优先级数值自动为0，并且不能更改设备优先级。

3. 备份根桥

   ```bash
   [HW] stp root secondary
   ```

   配置当前交换机为备份根桥，默认情况下，交换机部不作为任何生成树的备份根桥。配置后该设备优先级数值为4096，并且不能更改设备优先级。

4. 配置交换机的 STP 优先级

   ```bash
   [HW] stp priority "priority"
   ```

   默认情况下，交换机的优先级取值为32768。

5. 配置端口路径开销

   ```bash
   [HW] stp pathcost-standard {dot1d-1998 | dot1t | legacy}
   [HW] stp cost "cost"
   ```

   配置端口路径开销计算方法，默认情况下，路径开销计算方法为IEEE 802.1t标准方法。

6. 配置端口优先级

   ```bash
   [HW-GE1/0/1] stp priority "priority"
   ```

7. 启用STP/RSTP/MSTP

   ```bash
   [HW] stp enable
   ```

8. 查看STP端口状态摘要

   ```bash
   <HW> display stp brief
   MSTID	Port	Role	STP State	Protection
   0		GE1/0/21 ROOT	FORWARDING	NONE
   0		GE1/0/22 ALTE	DICARDING	NONE
   ```

STP的时间参数

- Forward Delay：15s。
- Hello Time：2s。
- Max Age：20s。

STP的收敛时间一般需要50s = Max Age + Foraward Delay * 2。

时间参数应该满足以下关系，否则会引起网络频繁震荡。

- 2 *（Forward Delay - 1s） >= Max Age
- Max Age >= 2 * (Hello Time + 1s)



STP 的不足之处：

- STP协议虽然能够解决环路问题，但是由于网络拓扑收敛慢，影响了用户通信质量。如果网络拓扑结构频繁变化，网络也会频繁中断。
- 网络协议的优劣往往取决于协议是否对各种情况加以细致区分：
  - 从用户角度来讲，侦听、学习和阻断状态并没有区别，都同样不转发用户流量。
  - 从使用和配置角度来讲，端口之间最本质的区别并不在于端口状态，而是在于端口扮演的角色。
  - 根端口和指定端口可以处于侦听状态，也可能都处于转发状态。
- STP算法是被动的算法，依赖定时器等待的方式判断拓扑变化，收敛速度慢。
- STP算法要求在稳定的拓扑中，根桥主动发出配置BPDU报文，而其他设备进行处理，传遍整个STP网络。这也是导致拓扑收敛慢的主要原因之一。

![image-20260205165139880](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260205165139880.png)





####	RSTP

RSTP 在许多方面对STP进行了优化，它的收敛速度更快，而且能够兼容STP：

- 端口状态缩减为3种：
  - Discarding：不转发用户流量也不学习MAC地址
  - Learning：不转发用户流量但是学习MAC地址
  - Forwarding：既转发用户流量又学习MAC地址
- 新增了3种端口角色（A根B指）：
  - 备份端口（Backup port），指定端口的备份
  - 预备或替代端口（Alternate port），根端口的备份
  - 边缘端口（Edge port）
- 新增3个快速收敛机制

![image-20260205212240688](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260205212240688.png) 





**配置BPDU的格式发生改变：**

充分利用了STP协议报文中的Flag字段，明确了端口角色。除了保证和STP格式基本一致之外，RSTP作了一些小变化： Type字段，配置BPDU类型不再是0而是2，所以运行STP的设备收到RSTP的 配置BPDU时会丢弃。Flag字段，使用了原来保留的中间6位，这样改变的配置BPDU叫做RST （Rapid Spanning Tree） BPDU。



####	配置BPDU的处理发生变化：

- **配置BPDU报文的发送方式**

  拓扑稳定后由逐级转发变为每台设备自主进行。

- **更短的BPDU超时计时**

  如果一个端口连续3个Hello Time时间没有收到上游设备发送过来的配置BPDU，那么该设备认为与此邻居之间的协商失败。而不像STP那样需要先等待一个Max Age。

- **处理次等BPDU**

  当端口收到上游指定端口发来的RST BPDU时，会与自身存储的RST BPDU进行比较，如果优先级低，则直接丢弃并立即回应自身存储的RST BPDU。RSTP处理次等BPDU报文不再依赖于任何定时器通过超时解决拓扑收敛，从而加快拓扑收敛。

  如果收到次等BPDU的端口当前是阻塞状态，它会立刻认为自己到根桥的路径现在是更优的，立即启动端口状态的转换。



####	快速收敛机制

- **Proposal/Agreement，P/A机制**

  快速使指定端口进入转发状态。RSTP通过阻塞自己的非根端口来保证不会出现环路，消除瓶颈，使用P/A机制加快了上游端口转到Forwarding状态的速度。

- 根端口快速切换机制

- 边缘端口的引入

  ​	在RSTP里面，如果某一个指定端口位于整个网络的边缘，即不再与其他交换设备连接，而是直接与终端设备直连，这种端口叫做边缘端口。边缘端口不接收处理配置BPDU，不参与RSTP运算，可以由Disable直接转到 Forwarding状态，且不经历时延，就像在端口上将STP禁用。但是一旦边缘 端口收到配置BPDU，就丧失了边缘端口属性，成为普通STP端口，并重新进 行生成树计算，从而引起网络震荡。






####	RSTP拓扑变化处理

在RSTP中检测拓扑是否发生变化只有一个标准：**一个非边缘端口迁移到Forwarding状态**：

- 为本交换机的所有非边缘端口启动一个TC While Timer，该计时器值是Hello Time的两倍，在这个时间内，清空状态发生变化的端口上学习到的MAC地址。同时由这些端口向外发送RST TC BPDU，一旦TC While Timer超时，则停止发送RST BPDU。
- 其他交换设备接收到RST BPDU后，清空所有端口学习到MAC地址，除了收到RST BPDU的端口和边缘端口。然后也为自己所有的非边缘指定端口和根端口启动TC While Timer，重复上述过程。

如此网络中就会产生 RST TC BPDU的泛洪。







####	MSTP

MSTP网络中包含1个或多个MST域（MST Region），每个MST Region中包含一个或多个MSTI（Multiple Spanning Tree Instance）。

![image-20260206104445551](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260206104445551.png)



**MSTI（Multiple Spanning Tree Instance）**：

​	多生成树实列，vlan需要映射到示例上，各实例之间生成生成树。

![image-20250306025520748](https://img2023.cnblogs.com/blog/3382848/202503/3382848-20250318191249862-1539905417.png)

**MST域（MST Region）**：

**VLAN映射表**：

​	描述VLAN和MSTI之间的映射关系

**CST（Common Spanning Tree）**:

​	公共生成树，是连接交换网络内所有MST域的一颗生成树。

![image-20250306025913340](https://img2023.cnblogs.com/blog/3382848/202503/3382848-20250318191250180-2077057137.png)

​	上图中黑线的部分

**IST（Internal Spanning Tree）**：

C

**SST（Singel Spanning Tree）**：

​	MST域中只有一个交换设备。

**总根（CIST Root）**：

​	是CIST的根，Intance0的根

**域根（Regional Root）**：

​	分为IST域根和MSTI域根：

- IST域根，在MST域中IST生成树中距离总根最近的交换设备是IST域根。
- MSTI域根，是每个多生成树的树根。

**主桥（Master Bridge）**：



总根

CST ：公共生成树（Common Spanning Tree）

IST ：内部生成树（Internal Spanning Tree）

CIST： 所有MST域的IST + CST 就构成一颗完整的生成树



MSTP 7种端口角色：

- 根端口
- 指定端口
- Alternate端口
- Backup端口
- 边缘端口
- Master端口
- 域边缘端口





####	MSTP 拓扑计算

> MSTP可以将整个二层网络划分为多个MST域，每



以下三项均相同，即处于同一MSTP域：

- 域名
- 修订级别：目前保留，默认为0
- vlan映射：指vlan与实例的映射关系

如果出现了Master端口。

区域配置：

```bash
# 进入区域配置视图
[H3C] stp region-configuration
# 配置域名
[H3C-mst-region] region-name "name"
# 配置修订级别
[H3C-mst-region] revision-level "level"
# 配置VLAN和实例的映射
[H3C-mst-region] instance "instance-id" vlan "vlan-list"

```







###	以太网链路聚合

> Eth-Trunk，以太网链路聚合，在不进行硬件升级的条件下，通过将多个物理接口捆绑为一个逻辑接口，提高带宽、负载分担、提高可靠性。

链路 ->  LAG链路聚合组 -> 聚合接口（逻辑接口 Eth-Trunk接口）

活动接口数上限阈值、活动接口数下限阈值

Eth-Trunk 模块内部维护一张转发表：

- HASH-KEY
- 接口号

Eth-Trunk模块根据转发表转发数据帧的过程：

1. Eth-Trunk 从 MAC 层接收到一个数据帧后，根据负载分担方式提取数据帧的源MAC地址/IP地址或目的MAC地址/IP地址。
2. 根据HASH算法进行计算，得到HASH-KEY值。
3. Eth-Trunk模块根据HASH-KEY值在转发表中查找对应的接口，把数据帧从该接口发送出去。



手工负载分担模式链路聚合

LACP模式链路聚合（IEEE802.3ad）

手工模式无法检测到链路层故障、链路错连等故障。LACP模式根据自身配置自动形成聚合接口并启动聚合链路收发数据。聚合链路形成以后，LACP负责维护链路状态，在聚合条件发生变化时，自动调整或解散链路聚合。

系统LACP优先级、接口LACP优先级



LACP模式实现原理：

1. 两端互相发送LACPDU报文
2. 确定主动端和活动链路：比较LACP系统优先级，如果相同则比较MAC地址，越小越优先。

LACP抢占、LACP抢占延时、活动链路和非活动链路切换

LACP实现方式：

- 静态LACP模式

  手工配置Eth-Trunk的建立、成员接口的加入，但是活动接口的选择由LACP协议报文负责。静态LACP模式也称为M：N模式，这种方式同时可以实现负载分担和冗余备份的双重功能。

- 动态LACP模式

  动态LACP模式的主要区别是，LACP协商失败后Eth-Trunk变为Down，但其成员口继承Eth-Trunk的VLAN属性状态变为Indep，可独立进行二层数据转发。动态LACP模式下的Eth-Trunk通常应用于设备和服务器直连的场景。建议部署静态LACP模式。

链路聚合进行负载分担：

为了避免数据包乱序的情况，Eth-Trunk采用逐流负载分担的机制。这种机制把数据帧中的地址通过HASH算法生成HASH-KEY值，然后根据这个数值在Eth-Trunk转发表中寻找对应的出接口，不同的MAC或IP地址HASH得出的HASH-KEY值不同，从而出接口也就不同，这样既保证了同一数据流的帧在同一条物理链路转发，又实现了流量在聚合组内各物理链路上的负载分担，即逐流的负载分担。逐流负载分担能保证包的顺序， 但不能保证带宽利用率。

Eth-Trunk接口不能嵌套。

![image-20260210174105749](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260210174105749.png)



### IP地址与配置



#### 网络层协议

IP协议、ICMP协议、IGMP协议

IP协议作用：

- 为网络层的设备提供逻辑地址
- 负责数据包的寻址和转发

IPv4报文格式：

![image-20260210195818164](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260210195818164.png)





Identification：标识位，主机每发一个报文，加1，分片重组时会用到该字段。

Flags：标志位。

- Bit 0：保留位，必须为0
- Bit 1：DF（Don't Fragment），0表示可以分片，1表示不能分片。
- Bit 2：MF（More Fragment），表示该报文是否为最后一片，0表示最后一片，1代表后面还有分片。

Fragment Offset：片偏移，分片重组时会用到该字段，表示较长的分组在分片后，某片在原分组中的相对位置，以8个字节为偏移单位。





####	子网划分

向网络位借位，或向主机位借位。

假设向网络位借x位，向主机位借y位：

子网数 = 2 ^y^ == 2 ^8-x^

主机数 = 2^8-x^-2 == 2^y^-2



####	DHCP

DHCP Client 首次获取IP地址的交互顺序是：DISCOVER - OFFER - REQUEST - ACK

Client 向外广播报文请求IP地址，其中 DICOVER 和 REQUEST 都是广播报文。

当Client分别发现租期达到 50%、87.5%的时候，会发送DHCP REQUEST请求续约。



由于交换机隔离了广播域，有时候需要使用DHCP Relay 中继功能。



huawei基本配置

```bash
# 开启DHCP功能
[HW] dhcp enable
# 基于接口开启DHCP服务器功能
[HW-G0/0/1] dhcp select interface
# 基于全局方式开启DHCP服务器功能
[HW-G0/0/1] dhcp select global
# 创建地址池
[HW] ip pool "ip-pool-name"
[HW-ip-pool-ip-pool-name] network "ip-address" mask "mask"
# 配置地址池下的地址租期,默认情况下IP地址租期为1天
[HW-ip-pool-ip-pool-name] lease day "xx" hour "xx" minute "xx" | unlimited
# 为DHCP Client配置出口网关地址
[HW-ip-pool-ip-pool-name] gateway-list "x.x.x.x"
# 为DHCP Client分配固定IP地址
[HW-ip-pool-ip-pool-name] static-bind ip-address "x.x.x.x" mac-address "x-x-x-x" description "xxx"
# 开启DHCP中继功能
[HW-GE0/0/1] dhcp select relay
[HW-GE0/0/1] dhcp relay server-ip "x.x.x.x"
```





### IP路由基础

路由包含以下信息：

- 目的网络/掩码：标识目的网段。
- 出接口：数据包被路由后离开本路由器的接口。
- 下一跳：数据包转发时经过下一个路由设备的接口IP地址。



**路由的获取方式：**

- 直连路由
- 静态路由
- 动态路由

**华为路由表的构成：**

- 目的网络地址/掩码长度
- 协议类型
- 路由优先级
- 开销值
- 标志
- 下一跳地址
- 出接口

**路由优先级：**

​	当设备从不同途径获知到达到同一目的网段的路由时，会比较这些路由的优先级，优先级值最小的为最优路由，添加进路由表中。

- Direct 直连：0
- OSPF内部路由：10
- 静态路由：60
- OSPF外部路由：150



**度量值：**

- 当多条路由的优先级相同时，此时度量值将作为路由优选的依据之一。
- 路由度量值表示到达这条路由所指目的地址的代价，如跳数、带宽、时延等。
- 度量值数值越小越优先，度量值最小的路由将会被添加到路由表中。
- 度量值很多时候被称为开销Cost

当网络中到达同一目的地存在同一路由协议发现的多条路由，且这几条路由的开销值也相同，那么这些路由就是等价路由，可以实现负载分担。



**最长匹配原则**



####	直连路由

直连路由的下一条地址并不是其他设备的接口地址。因为该路由的目的网段为接口所在网段，本接口就是最后一条，不需要再转发给下一跳，所以路由表中的下一跳就是接口自身地址。

**使用路由器物理接口：**

- 路由器三层接口无法处理携带VLAN Tag的数据帧，因此下联的交换机上的接口需要配置Access口。
- 路由器的一个物理接口作为一个VLAN 网关，存在一个VLAN就要占用一个物理接口，扩展性较差。

**使用路由器子接口：**

- 子接口（Sub-Interface）是基于路由器以太网接口所创建的逻辑接口，以物理接口ID + 子接口ID进行标识，子接口同物理接口一样可进行三层转发。
- 子接口不同于物理接口，可以终结携带的VLAN Tag的数据帧（VLAN Termination）。
- 基于一个物理接口创建多个子接口，将该物理接口对接到交换机的Trunk接口，即可实现使用一个物理接口为多个VLAN提供三层转发服务。
- 子接口终结VLAN的实质包含两个方面：
  - 对于接收的报文，剥离VLAN标签后进行三层转发或其他处理。
  - 对于发送的报文，将相应的VLAN标签添加到报文中后再发送。

**三层交换机和VLANIF接口：**

三层交换机支持通过三层接口（如VLANIF接口）实现路由转发功能。

- VLANIF接口是一种三层的逻辑接口，支持VLAN Tag的剥离和添加，因此可以通过VLANIF接口实现VLAN之间的通信。
- VLANIF接口编号与所对应的VLAN ID相同，如VLAN 10对应VLANIF10





####	静态路由

创建静态路由时，可以同时指定出接口和下一跳，对于不同的出接口类型，也可以只指定或只指定下一跳。比如对于点到点接口，只需指定出接口。

对于广播接口，如以太网接口，必须指定下一跳。

当路由的下一跳可达，且路由是最优的，则会被添加到路由表中。



缺省路由（默认路由）是在路由表中找不到匹配的路由表项时才使用的路由。



####	路由的高级特性

**路由递归：**

如果路由的下一跳不是直连的，此时就需要路由递归（路由迭代），需要写多条路由。

**浮动路由：**

- 静态路由支持配置时手动指定优先级，可以配置目的地址/掩码相同，但优先级、下一跳不同的静态路由，实现转发路径的备份。
- 浮动路由是主用路由的备份，保证链路故障时提供备份路由。主用路由下一跳可达时该备份路由不会出现在路由表中。

```bash
ip route-static 20.0.0.0 30 10.1.1.2
ip route-static 20.0.0.0 30 10.1.2.2 preference 70
```

**路由汇总：**

- 路由汇总将一组具有相同前缀的路由汇聚成一条路由，从而达到减小路由表规模以及优化设备资源利用率的目的。
- 路由汇总采用了CIDR（无类别域间路由）的思想，将相同前缀的地址聚合成一个。
- 将没有汇聚前的路由称为明细路由，把汇聚之后的路由成为汇总路由或聚合路由。

汇总可能会带来环路，此时可以使用**黑洞路由**避免破除环路：

```bash
# 黑洞接口编号为0
ip route-static x.x.x.x xx 0 NULL0
```



**三层交换机通过检查数据帧的目的MAC地址来决定是进行三层转发还是二层转发，如果目的MAC地址指向交换机自身，那么将进行三层处理；如果目的MAC地址指向网络中的其他设备，则直接进行二层转发**

**ping -a** *source-ip-address destination-ip-address*：用来指定发送ICMP Echo Requset 报文的源IP地址。如果不指定源IP地址，一般采用路由出接口的IP地址作为源地址。

### 内部路由协议 OSFP

#### OSPF基础

OSPF是一个基于链路状态的内部网关协议：

- OSPF 把自治系统AS划分成逻辑上的一个或多个区域。
- OSPF通过链路状态通告LSA的形式发布路由。
- OSPF依靠在OSPF区域内各设备间交互OSPF报文来达到路由信息的统一。
- OPSF报文封装在IP报文内，可以采用单播或组播形式发送。

**OSPF运行机制：**

1. 通过交互Hello报文形成邻居关系。
2. 通过泛洪LSA通告链路状态信息。
3. 通过组建LSDB形成带权有向图。
4. 通过SPF算法计算形成路 由。
5. 维护和更新路由表。

**OSPF报文类型：**

1. Hello报文：
   - 邻居发现，建立邻居关系。
   - 指定DR和BDR。
   - 保活。
2. DD报文
   - 初始化邻接关系，DD报文用来协商主从关系，此时报文中只包含LSA的Header。
   - 邻接关系建立后，路由器通过DD报文描述本端路由器的LSDB，进行数据库同步。DD报文里包括LSDB中每一条LSA的Header，即所有LSA的摘要信息。对端路由器根据LSA Header就可以判断是否已有这条LSA。
3. LSR报文
   - 交换完DD报文后，需要发送LSR报文向对方请求更新LSA，LSR报文里包括所需要的LSA的摘要信息。
4. LSU报文
   - LSU 报文用来向对端路由器发送其所需的 LSA 或泛洪本端更新的 LSA。其报文内容是多条完整的 LSA 的集合。
5. LSAck报文
   - 为了实现可靠性传输，需要 LSAck（Link State Acknowledgment packet）用来对接到的 LSU 报文进行确认。
   - LSAck 报文的内容是需要确认的 LSA 的 Header，一个 LSAck 报文可对多个 LSA 进行确认。



#### DR和BDR

DR：减少邻接状态的建立，避免带宽的浪费。

BDR：避免DR故障，重新选举DR时造成的业务中断。

选举规则：

- 选举制：根据DR优先级，越高越优先。如果优先级相等，则Router ID大的胜出。如果一台交换机的优先级为0，则它不会被选举为DR或BDR。
- 终身制：既是非抢占式。
- 继承制：如果DR发生故障，且存在BDR，那么下一个当选DR的一定是BDR。其他路由器只能去竞选BDR，这个原则可以保证 DR 的稳定性，因为 BDR 和 DR 的数据库是完全同步的，且和其他路由器建立了邻接关系，所以从角色切换到承载业务的时间会很短。当然后续还会选举一个新的 BDR，但是已经不会影响当前路由的计算了。



#### OSPF接口状态机、邻居状态机

OSPF接口共有7种状态：

1. Down：接口的初始状态
2. Loopback：不用用于正常的数据传输，但可以通过Router LSA进行通告。
3. Waiting：设备正在判定网络上的DR和BDR，在设备参与DR和BDR选举之前，接口会启动Waiting定时器（40秒）。在这个定时器超时前，设备发送的Hello报文不包含DR和BDR信息，设备不能被选举为DR或BDR。
4. P2P
5. DROther
6. BDR
7. DR

OSPF邻居状态机：

1. Down：初始状态，表明没有收到邻居设备的Hello报文。
2. Attempt：适用于NBMA网络。
3. Init：表示已经收到了邻居的Hello报文，但是对端没有收到自己的，邻居状态暂未正式建立。
4. 2-Way：此时互为邻居，在此状态后才会进行DR/BDR的选举，若不能形成邻接状态则状态机状态就停留此状态。
5. ExStart：协商主从关系，选举DR/BDR。
6. Exchange：交换DD报文，本端设备将本地的LSDB用DD报文来描述，并发送给邻居设备。
7. Loading：正在同步 LSDB，发送 LSR 报文向邻居请求对方的 LSA，同步 LSDB。
8. Full：建立邻接。两端设备的 LSDB 已同步，建立了完全的领接关系。 



#### OSPF区域

**1.骨干区域**

实际应用中，可能会由于限制无法满足上面的要求，这时可以通过虚连接解决。

**2.虚连接**

虚连接是指两个ABR之间通过一个非骨干区域建立的一条逻辑上的连接通道，它的两端必须是ABR。它们之间的OSPF路由器只是起到一个转发报文的作用。

**3.Stub区域和Totally Stub区域**

- stub区域，ABR会将area间的路由信息转递到本区域，但不会引入AS外部路由。该区域的ABR会生成一条缺省路由Type-3 Network Summary LSA，发布给本区域的其他非ABR路由器。
- totally stub区域，ABR连area间的路由都不会引入，而是直接一条缺省Type3路由代替。

**4.NSSA区域和Totally NSSA区域**

- NSSA区域是Stub区域的变形，与Stub区域的区别在于NSSA区域允许引入AS外部路由，由ASBR发布Type 7 NSSA External LSA，当Type 7 LSA 到达 NSSA ABR时，由ABR将Type 7 LSA 转换为 Type 5 LSA传播给其他区域。
- Totally NSSA区域，该区域的 ABR 不会将area间的路由传递到本区域，而是由ABR生成一条缺省Type 3 LSA 代替。



1.骨干区域 area0，所有非骨干区域必须和骨干区域保持联通、骨干区域自身也必须保持联通。

2.非骨干区域 - 根据能够学习的路由种类来区分：

- 标准区域

  ![OSPF五种区域类型详解 ospf几种区域_路由表_03](https://s2.51cto.com/images/blog/202404/07233140_6612bc5c5107816584.jpg?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_30,g_se,x_10,y_10,shadow_20,type_ZmFuZ3poZW5naGVpdGk=/format,webp/resize,m_fixed,w_1184)

- 末梢区域（stub）

  ![OSPF五种区域类型详解 ospf几种区域_路由表_04](https://s2.51cto.com/images/blog/202404/07233140_6612bc5cf3f0c84304.jpg?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_30,g_se,x_10,y_10,shadow_20,type_ZmFuZ3poZW5naGVpdGk=/format,webp/resize,m_fixed,w_1184)

- 完全末梢区域（Totally stubby）

  ![OSPF五种区域类型详解 ospf几种区域_路由表_05](https://s2.51cto.com/images/blog/202404/07233141_6612bc5d8816596416.jpg?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_30,g_se,x_10,y_10,shadow_20,type_ZmFuZ3poZW5naGVpdGk=/format,webp/resize,m_fixed,w_1184)

- 非纯末梢区域（NSSA）

  ![OSPF五种区域类型详解 ospf几种区域_网络_06](https://s2.51cto.com/images/blog/202404/07233142_6612bc5e29ec026849.jpg?x-oss-process=image/watermark,size_16,text_QDUxQ1RP5Y2a5a6i,color_FFFFFF,t_30,g_se,x_10,y_10,shadow_20,type_ZmFuZ3poZW5naGVpdGk=/format,webp/resize,m_fixed,w_1184)

- Totally NSSA

  取消了3类LSA报文传递，no summary。

![image-20260212225433039](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260212225433039.png)

![image-20260212225441953](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260212225441953.png)

#### OSPF路由类型

OSPF将路由分为四类，优先级由高到低分别是：

- 区域内路由
- 区域间路由
- 第一类外部路由：可信度较高
- 第二类外部路由：可信度较低

#### 路由器ID

Router ID获取方式有两种：

- 手工指定Router ID：必须保证AS内的Router ID 唯一。
- 使用全局Router ID

#### OSPF路由的计算过程

同一个区域内，OSPF路由的计算过程可简述为：

- 每台OSPF路由器根据自己周围的网络拓扑生成LSA，并通过更新报文将LSA发送给网络中的其他OSPF路由器。
- 每台OSPF路由器都会搜集其他路由器通过的LSA，所有LSA放在一起便组成了LSDB。LSA是对路由器周围网络拓扑结构的描述，LSDB则是对整个自治系统的网络拓扑结构的描述。
- OSPF路由器将LSDB转换成一张带权的有向图，这张图便是对整个网络拓扑结构的真实反映，各个路由器得到的有向图是完全相同的。
- 每台路由器根据有向图，使用SPF算法计算出一颗以自己为根的最短路径树，这棵树给出了到自治系统中各节点的路由。

#### OSPF网络类型

缺省情况下，OSPF认为网络类型是广播，通常以组播方式：224.0.0.6 发送 Hello、LSU、LSAck报文，以单播形式发送DD和LSR报文。



#### OSPF DR/BDR

OSPF中邻居和领接是两个不同概念。路由器启动后，会通过 接口向外发送 Hello 报文，收到 Hello 报文的路由器会检查报文中所定义的参数，如果双方一致就 会形成邻居关系。只有当双方成功交换 DD 报文，交换 LSA 并达到 LSDB 同步之后，才形成邻接关 系。

DR是某个网段中的概念，是针对路由器的接口而言的。某个路由器在一个接口上可能是DR，在另一个接口上可能是BDR，或者是DR Other。



#### OSPF 报文详细字段

**OSPF LSU（Link State Update）**

LSU用来向对端路由器发送其所需的LSA或者泛洪自己更新的LSA，内容是多条LSA的集合**。**

**OSPF LSA**







### ACL原理和配置

#### ACL的组成

- ACL编号
- 规则
- 规则编号
- 动作
- 匹配项

规则编号：步长

通配符：

- 0 表示严格匹配，1表示任意
- 如 192.168.1.1/24 == 192.168.1.1 0.0.0.255
- 255.255.255.255 == any，0.0.0.0 == any
- 如果要匹配奇数，可以使用254

#### ACL的匹配规则

- 配置ACL的设备接收报文后，会将该报文与ACL中的规则逐条进行匹配，如果不能匹配上，就会继续尝试去匹配下一条规则。
- 一旦匹配上，则设备会对该报文执行这条规则中定义的处理动作，并且不再继续尝试与后续规则匹配。
- 华为设备支持两种匹配顺序：自动排序和配置排序，默认是配置排序。

#### 基于MQC调用ACL实现报文过滤

MQC 模块化Qos命令行

- 流分类：用来定义一组流量匹配规则，用于对报文进行分类。
- 流行为：用来定义针对某类报文所做的动作。
- 流策略：将指定的流分类和流行为绑定，一个流策略可以有多个流分类和流行为的绑定。

![image-20260225212924712](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260225212924712.png)

```bash
[HW-R1] acl 3000
[HW-R1-acl-advance-3000] rule deny ip souce 192.168.1.0 0.0.0.255 destination 192.168.254.0 0.0.0.255
[HW-R1-acl-advance-3000] rule permit ip source 192.168.2.0 0.0.0.0.255 destination 192.168.254.0 0.0.0.255
```

![image-20260225213145301](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260225213145301.png)



### AAA原理和配置

AAA提高了企业网络的安全性：

- 认证、授权、计费

#### AAA配置管理员使用本地验证

```bash
# 进入AAA视图
[HW] aaa
# 创建本地管理员用户，配置密码
[HW-aaa] local-user "username" password irreversible-cipher "password"
# 配置本地管理员用户的接入类型
[HW-aaa] local-user "username" service-type {none | http | ftp | ssh | telnet | terminal}
# 配置本地管理员用户的级别
[HW-aaa] local-user "username" privilege level "level 取值范围0~3，越大级别越高"
# 查看创建的用户信息
[HW] display local-user username "xxx"
```



#### 配置管理员使用RADIUS认证



### 网络服务和应用

#### SSH

1. 生成本地密钥对

   ```bash
   [HW] rsa local-key-pair create
   [HW] dsa local-key-pair create
   [HW] ecc local-key-pair create
   ```

2. 使能SSH服务器公钥算法

   ```bash
   [HW] ssh server publickey {.....}
   ```

3. 开启STelnet服务器

   ```bash
   [HW] stelnet [ipv4 | ipv6] server enable
   ```

4. 配置SSH服务端的密钥交换算法列表

   ```bash
   [HW] ssh server key-exchange {.....}
   ```

5. 配置SSH服务端的加密算法

   ```bash
   [HW] ssh server cipher {.....}
   ```

6. 配置SSH服务器上的校验算法

   ```bash
   [HW] ssh server hmac {....}
   ```

7. 配置SSH服务器的源接口或源地址

   ```bash
   # 配置SSH服务器的源接口
   [HW] ssh server-source -i "xxxx"
   [HW] ssh server-source all-interface
   # 配置SSH服务器的源地址
   [HW] ssh server-source -a "x.x.x.x"
   [HW] ssh server-source all-insterface
   ```

8. 配置VTY用户界面支持SSH协议

   ```bash
   [HW] user-interface vtr 
   ```

   



![image-20260225221349721](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260225221349721.png)



```bash
# 配置虚拟视图
[HW] user-interface vty 0 4
[HW-ui-vty0-4] authentication-mod aaa
[HW-ui-vty0-4] protocol inbound ssh
[HW-ui-vty0-4] user privilege level 3
# aaa中新建本地用户
[HW] aaa
[HW-aaa] local-user "gx" password irreversible-cipher xxx "gx123"
[HW-aaa] local-user "gx" service-type ssh
[HW-aaa] local-user "gx" level 3
# 开启SSH功能
[HW] stelnet server enable
[HW] ssh server-source all-interface
[HW] ssh user "gx" authentication-type password
[HW] ssh user "gx" service-type stelnet
```



### NAT原理和配置

NAT分类，可以分为源NAT、目的NAT和双向NAT。

在私网内使用私网IP地址，并在网络出口使用NAT技术，可有效减少网络所需的IPv4公网IP地址数目，NAT地址有效缓解了IPv4公网地址短缺的问题。



### 数据中心网络基础

- 数据中心根据内部业务类型的不同分为通用计算数据中心和智能计算数据中心（通算和智算）。
- 通算数据中心网络，主要采用Spine-Leaf架构，结合M-LAG、VXLAN技术满足该场景下用户业务对于网络的隔离性、扩展性、可用性等需求。
- 智算中心网络除了使用Spine-Leaf架构外，还支持Dragonfly架构，结合智能无损网络技术（PFC、ECN）和RDMA协议栈为智算业务提供零丢包、超低时延的无损网络。



### WLAN技术基础



WLAN使用的电磁波是无线电波，无线电波是由振荡电路的交变电流产生，能够通过无线发射和接收，也称为射频。

干扰和信道利用率

#### WLAN关键技术

- 调制方式：QAM

  ​	正交幅度调制QAM技术。同时调制相位和幅度，利用载波的幅度和相位来传递信息，提升数据传输速率。

- 载波数量：OFDM

  ​	正交频分复用，可以多子载波并行传输。

- 信道带宽

- 空间流数：MIMO

  ​	多入多出，允许多个天线同时发送和接收多个空间流，即多份信号。


#### CSMA/CA

先听后发，发完等确认，尽量避免碰撞。

CSMA/CA流程：

1. 预约信道：发送数据前，先发送一个 RTS（请求发送）帧，接收端回复 CTS（允许发送）帧来通知其他设备“我要占用信道了”（这也是为了缓解隐藏节点问题）。
2. 信道空闲检测：不仅检测物理信道是否空闲，还要等待一个特定的帧间隔时间。
3. 随机退避：如果信道忙，会进入一个随机的退避窗口倒计时，倒计时结束后才尝试发送，以减少同时发送的几率。
4. 链路层确认：接收端正确收到数据后，需要回复 ACK（确认）帧。如果发送端没收到 ACK，就认为数据发送失败，进行重传。



#### WLAN组网

无线网络标识SSID

虚拟AP：VAP Virtual Access Point，AP支持创建多个虚拟AP。

BSS（Basic Service Set）：基本服务集，是一个VAP所覆盖的范围，通常由一个VAP和接入此VAP的WLAN用户组成。

BSSID（Basic Service Set Identifier，基本服务集标识）：表示每个VAP的数据链路层MAC地址。

![image-20260226164140583](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260226164140583.png)

WLAN组网架构：

- WAC + FIT AP
- FAT AP
- Leader AP

WAC + FIT AP 组网架构：

- WAC负责WLAN的接入控制、数据转发、AP的配置管理、漫游管理、安全控制。
- FIT AP负责802.11报文的加解密、实现802.11的物理层功能、 接受WAC的管理及空口的统计等简单功能。
- WAC和AP之间使用的通信协议是CAPWAP隧道。

CAPWAP协议（无线接入点控制协议）：

基于UDP的应用层协议，报文分为控制报文和数据报文（UDP port 5246和5247）

CAPWAP隧道：控制隧道和数据隧道。

组网方式：直连式组网和旁挂式组网

![image-20260226170500648](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260226170500648.png)





#### WLAN业务配置流程

**配置AP上线**

1. 配置网络互通
2. 创建AP组
3. 配置源接口或源地址（与AP建隧道）
4. 配置AP上线时自动升级（可选）
5. 添加AP设备（自动发现AP、手工确认等方式）

**配置模板**

1. 域管理模块
2. SSID模块
3. 安全模块
4. VAP模块

**绑定模板**

1. AP组或AP直接绑定模块



创建AP组：

```bash
[HW] wlan
[HW-wlan] ap-group name "group-name"
```

配置CAPWAP源接口或源地址：

```bash
[HW] capwap source interface {loopback xxx | vlanif xxx}
[HW] capwap source ip-address "x.x.x.x"
```

添加AP设备

```bash
# 配置AP认证模式为MAC认证，SN认证或不认证，缺省为MAC认证
[HW-wlan] ap auth-mode {mac-auth | sn-auth | no-auth}

# 配置AP加入AP组
[HW-wlan-ap-ap-id] ap-group "ap-group"
```

配置模板

...

将SSID模板、安全模板绑定至对应的VAP模板

```bash
[HW-wlan] vap-profile name "name"
[HW-wlan-vap-prof-profile-name] security-profile "xxx"
[HW-wlan-vap-prof-profile-name] ssid-profile "xxx"
```

将VAP模板绑定到对应的AP组下，AP上射频使用VAP模板的配置

```bash
[HW-wlan] ap-group name "name"
[HW-wlan-ap-group-group-name] vap-profile "name" wlan "wlan-id" {radio-id | all}
```



### 网络设备管理

SNMP

共有三个版本：SNMPv1、SNMPv2c、SNMPv3

![image-20260227091035278](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260227091035278.png)

SNMP通过MIB（管理信息库）去描述可管理实体的一组对象。

MIB给出了一个数据结构，包含了网络中所有可能的被管理对象的集合，与树相似，MIB又被称为对象命名树。

SNMPv1定义了5种协议操作：

- Get：NMS从被管理设备的代理进程的MIB中提取一个或多个参考值。
- Get-Next：NMS从代理进程的MIB中按照字典式排序提取下一个参考值。
- Set：NMS设置代理进程MIB中的一个或多个参考值。
- Response：代理进程返回一个或多个参考值，它是前三种操作的响应操作。
- Trap：代理进程主动向NMS发送报文，告知设备上发生的紧急或重要事件。

SNMPv2c新增了2种协议操作：

- Get-Bulk：相当于连续执行多次Get-Next操作。
- Inform：被管理设备向NMS主动发送告警，与Trap不同的是，被管理设备发送Inform告警后，需要NMS进行接收确认，否则会重复发送该告警，直到达到最大重传次数。

SNMPv3增加了：

- 身份验证
- 加密处理





### 园区网络典型组网方案

分层组网：

- 出口层：NAT、OSPF、静态路由
- 核心层：OSPF、静态路由、ACL、SNMP、WLAN
- 汇聚层：链路聚合、STP、OSPF、DHCP、静态路由
- 接入层：VLAN、链路聚合、STP

网络项目生命周期：

- 规划与设计
- 部署与实施
- 网络运维
- 网络优化



![image-20260227094317315](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260227094317315.png)



基础业务设计：VLAN设计

- VLAN划分需要区分管理VLAN、互联VLAN和业务VLAN

基础业务设计：IP地址设计

- 业务IP地址
- 管理IP地址：二层设备使用VLANIF地址作为管理IP地址，建议网关下的所有二层交换机属于同一网段
- 互联IP地址：推荐使用30位掩码的IP地址，核心设备使用主机地址较小的IP地址。

IP地址及分配方式的规划

- 出口网关：WAN侧采用手工静态方式配置
- 网络设备：所有网络设备的IP地址采用手工静态配置（FIT AP除外）
- FIT AP和终端：采用DHCP方式动态获取IP地址。

基础业务设计：路由设计

- 同一网段内：通过DHCP分配IP地址后默认会生成一条缺省路由，指向网关。
- 不同网段内：需要在需要转发三层数据的设备上部署动态路由协议或静态路由，实现三层互通。

WLAN设计

- WLAN组网可划分为：直连二层组网、旁挂二层组网、直连三层组网、旁挂三层组网。
- WLAN数据转发方式：集中转发、本地转发。

网络可靠性设计

- 端口级别可靠性：链路聚合。
- 设备级别可靠性：堆叠或集群技术。

二层环路避免设计

- 部署生成树，建议手工配置核心为根桥。

安全设计

- 流量管控：Traffc-Policy、NAT
- DHCP安全：避免员工私接DHCP无线路由器，导致内网地址混乱，出现冲突错误，一般会在接入交换机开启DHCP Snooping 防止这种情况。
- 网络管理安全：通过ACL仅允许固定的用户登录管理。



###	堆叠

华三：IRF（智能弹性架构）

堆叠的优点：

- 简化管理
- 1:N备份
- 跨成员设备的链路聚合
- 强大的网络扩展能力

IRF成员设备的角色：

- 主用设备：负责管管理和控制整个IRF。
- 从属设备：处理业务、转发报文，同时作为主设备的备份，当主设备故障，系统会自动从从设备中选举一个新的主设备接替原主设备工作。

合并与分裂：

- 合并：多个IRF各自已经稳定运行，通过物理连接和必要配置，形成一个IRF称为IRF合并。
- 分裂：由于IRF链路故障，导致IRF中两相邻成员设备不连通，一个IRF分裂成两个，这个过程称为IRF分裂。

MAD：

- IRF链路故障会导致一个IRF分裂成多个新的IRF，这些IRF拥有相同的IP地址等三层配置，会引起冲突，导致故障扩大。MAD机制用来进行IRF分裂检测、冲突处理和故障恢复，提高系统可用性。

IRF域：

- 域是一种逻辑概念，在同一网络中可能部署多个IRF，IRF之间使用域编号来区分，避免相互之间的干扰。

IRF的连接拓扑：

- 成员设备之间的IRF链路带宽需要一致，如果成员设备之间的IRF链路带宽不一致，当业务流量需要跨成员设备转发时，可能会导致丢包。
- 目前只支持链式连接拓扑。

#### IRF角色选举

角色选举会在以下情况下进行：

- IRF建立
- 主设备离开或者故障
- IRF分裂
- 独立运行的两个或多个IRF合并为一个IRF。

角色选举按照如下优先级顺序选择主设备：

- 当前的主设备优先。
- 成员优先级大的设备。
- 系统运行时间长的设备（度量精度为10分钟）。
- CPU MAC地址小的设备。









## HCIP

### 认识网络设备

![image-20260306135347579](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260306135347579.png)



### IP路由

RIB：路由器维护一张本地核心路由表，此外路由器还维护着路由协议各自的路由表。

FIB：路由器将本地核心路由表中的最优路由下载到FIB表。路由器转发芯片根据FIB表转发报文。

IP路由高级应用：

路由引入：

- 路由优先级
- 路由回灌
- 路由度量值

华为设备缺省路由优先级：

- Direct：0
- OSPF：10
- IS-IS：15
- Static：60
- OSPF ASE：150
- OSPF NSSA：150
- IBGP：255
- EBGP：255

当网络规模较大且使用多种路由协议时，路由协议间通过路由引入的方式实现路由的相互通告。由于路由引入可能会引入大量路由，因此在进行路由引入时需要进行路由控制来实现路由的按需分发。



### OSPF

链路状态路由协议通告的是链路状态而不是路由信息。

- LSA泛洪

  运行链路状态路由协议的路由器之间首先会建立邻居关系，然后彼此之间开始交互LSA。

- LSDB维护

  每台路由器都会产生LSA，路由器将接收到的LSA放入自己的LSDB中，路由器通过对LSDB中所存储的LSA进行解析，进而了解全网拓扑。

- SPF计算

  每台路由器基于LSDB，使用SPF算法进行计算。

- 路由表生成

  路由器将计算出来的优选路径，加载进自己的路由表（Routing Table）。

OSPF有以下优点：

- 基于SPF算法，以累计链路开销作为选路参考值。
- 采用组播形式收发部分协议报文。
- 支持区域划分。
- 支持对等价路由进行负载均衡。
- 支持报文认证。



#### OSPF 基础

**Router ID：**

1. Router ID 选举规则。
2. 更改 Router ID 后需要重启OSPF进程生效。

**度量值：**

1. OSPF使用Cost作为路由的度量值，每一个激活OSPF的接口都会维护一个接口Cost值，缺省的接口Cost=100M/接口带宽。
2. OSPF以"累计cost"作为开销值，也就是流量从源网络到目的网络所经过所有路由器的出接口的Cost总和。
3. 实际应用中，推荐自定义度量值。

**OSPF 三大表项：**

1. 邻居表 `display ospf peer`
2. LSDB `display ospf lsdb`
3. 路由表 `display ospf routing-table`

**OSPF报文格式和类型：**

1. Hello
2. Database Description
3. Link State Request
4. Link State Update
5. Link State Ack

​	OSPF Packet header：Version、Router ID、Area ID

#### OSPF工作过程

1. **建立邻居关系**

   - 使用Hello报文发现和建立邻居关系。
   - 缺省时，OSPF采用组播形式发送Hello报文（目的地址224.0.0.5）。
   - Hello报文中包含路由器的Router ID、邻居列表等信息。
   - 对于不支持组播的链路，OSPF支持采用单播方式发送Hello报文。

   Hello报文：

   - Network Mask：发送Hello报文的接口的网络掩码。

   - HelloInterval：发送Hello报文的时间间隔，通常为10s。

   - RouterDeadInterval：失效时间。如果在此时间内未收到邻居发来的Hello报文，则认为邻居失效、通常为40s。

   - Neighbor：邻居，以Router ID标识。

     ![image-20260311094248436](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260311094248436.png)

2. **建立邻接关系**

   - ExStart，路由器开始向邻居发送DD报文，在此状态下发送的DD报文不包含链路状态的描述。
   - Exchange，路由器和邻居之间相互发送包含链路状态信息摘要的DD报文。
   - Loading，路由器和邻居之间相互发送LSR、LSU、LSAck报文。

   DD报文：

   - I 字段：值为1时，是第一个DD报文。否则置为0。
   - M 字段：如果是最后一个DD报文，则置为0，否则置1。
   - MS（Master/Slave）：当两台OSPF路由器交换DD报文时，首先需要确定双方的主从关系，Router ID 大的一方会成为 Master。当值为1时表示发送方为Master。
   - DD sequence number：DD报文序列号，主从双方利用序列号来保证DD报文传输的可靠性和完整性。
   - LSA Header：包含 LS Type、LS ID、Advertising Router、LS Sequence Number、LS Checksum

   Master 向 Slave 发送含有LSDB摘要信息的DD报文，Slave 对每一个DD报文进行确认。当 Master 发送完所有DD报文，Slave 进行全部确认后，Slave 产生一个 Exchange-Done 事件，进行链路状态请求列表是否为空的判断。如果不为空则进入Loading状态，进行 LSR-LSU-LSAck过程。

3. **DR和BDR**

   - DR和BDR的选举规则
     - 非抢占式
     - 基于接口：接口DR优先级越大越优先，DR优先级相同时，Router ID越大越优先。
     - 接口DR优先级设置为0则不参与选举。
   - 选举过程
     - 接口UP后，发送Hello报文同时进入Waiting状态，该状态下会有一个WaitingTimer，默认值40s，不可自行调整。
     - WaitingTimer触发前，发送的Hello报文是没有DR/BDR字段的，在Waiting 阶段，如果收到Hello报文中有DR和BDR，则直接承认，不会触发选举，直接离开Waiting状态，开始邻居同步。

4. **不同网络类型中DR和BDR的选举操作**

   - P2P：不选举DR，直接和邻居建立领接关系。
   - Broadcast、帧中继NBMA：选举DR。
   - P2MP点到多点：需要手工指定DR。

   可按需调整设备接口的OSPF网络类型：

   - 在接口中使用 `ospf network-type {p2p | p2mp | broadcast | nbma}`即可修改该接口的网络类型。
   - 如果实际链路都是点对点链路，此时在链路上选举DR/BDR都是没有必要的，为了提高效率，可以修改为P2P类型。

   

![image-20260311111933887](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260311111933887.png)



#### OSPF区域内路由计算



**LSA概述：**

- LSA是OSPF进行路由计算的关键依据。
- OSPF的LSU可以携带多种不同类型的LSA。
- 各种类型的LSA拥有相同的报文头部。

![image-20260311120545094](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260311120545094.png)



- LS Age：LSA已经生成的时间，当到达MaxAge（缺省3600s），LSA不再用于路由计算。
- LS Type：代表本LSA的类型。
- Link State ID
- Advertising Router (通告路由器)：产生该LSA的路由器的Router ID。
- LS Sequence Number：当LSA每次有新的实例产生时，序列号就会增加。用于判断LSA的新旧和查重。
- LS Checksum（校验和）
- Length：是一个包含LSA头部在内的LSA的总长度值。

![image-20260311121309222](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260311121309222.png)

**Router-LSA**



![image-20260311140838480](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260311140838480.png)



Link State ID：发布该LSA的Router ID

Link/Data：

- TransNet：Data 为宣告该 Router LSA 的路由器接口的IP地址。
- StubNet：Data 为该Stub网络的网络掩码。

Link ID：

- P2P：邻居路由器的Router ID。
- TransNet：DR的接口IP地址。
- StubNet：宣告该Router LSA 的路由器接口的网段地址。

由此可见，TransNet Router LSA 中，缺少掩码信息，此时就需要 Network LSA。

**Network LSA**

![image-20260311152727895](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260311152727895.png)

Link State ID：DR的接口IP地址

Network Mask：子网掩码

Attached Router：连接到该网络的路由器Router-ID（与该DR建立了邻接关系的邻居Router ID，以及DR自己的Router ID）

**SPF计算**



#### OSPF区域间路由计算

区域间路由信息传递通过ABR产生的Network Summary LSA 实现。

**Network Summary LSA**

Type 3 LSA 由 ABR 产生，用于向一个区域通告到达另一个区域的路由。

![image-20260311194339060](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260311194339060.png)

- LS Type：取值 3，代表 Network Summary LSA。
- Link State ID：路由的目的网络地址。
- Advertising Router：生成 LSA 的 Router ID。
- Network Mask：路由的网络掩码。
- metric：到目的地址的路由开销。

**区域间路由防环机制：**

- OSPF要求所有非骨干区域必须与Area0直接相连，区域间路由需经过Area0中转。
- ABR不会将描述到达某个区域内网段路由的3类LSA再注入回该区域。
- ABR从非骨干区域收到的Type3 LSA不能用于区域间路由的计算。 

**虚连接的作用及配置**

OSPF要求骨干区域必须是连续的，但并不一定是物理上的连续。可以使用虚连接使骨干区域在逻辑上连续。

- 虚连接可以在任意两个ABR上建立，但是要求这两个ABR都有端口连接到一个相同的非骨干区域。
- 虚连接的创建使OSPF协议可以通过非骨干区域通信，违背了OSPF区域间的防环规则，在某些场景下会导致路由环路的产生，因此不建议部署OSPF虚连接。



#### 外部路由计算

路由器引入外部路由后，它就成为一个ASBR。

**AS-External LSA：**

- Link State ID：外部路由的目的地址。
- Adv Rtr：发布该LSA的Router ID。
- Network Mask
- E：该外部路由使用的度量值，0 为 Type 1，1 为 Type 2。
- Forwarding address（FA）：到所通告的目的地址的报文将被转发到这个地址。
- External Route Tag（外部路由标记）：外部路由才能携带的标记，常用于部署路由策略。

和发布此LSA的ASBR不属于同一区域的路由器，只凭此LSA，是无法计算路由的，因为不知道下一跳该怎么走？

**ASBR-Summary LSA：**

- Link State ID：ASBR的Router ID。
- Adv Rtr：生成该LSA的Router ID。
- Network Mask：仅保留，无意义。
- metric：到目的地址的路由开销。

区分OSPF外部路由的2种度量值类型：

- Metric Type 1 = AS内开销 + AS外开销，可信度较高。
- Metric Type 2 = AS外开销，可信度较低。





### IS-IS





### BGP

#### BGP基本概念

BGP 是一种实现自治系统AS之间的路由可达，并选择最佳路由的矢量性协议。特点有：

- 使用TCP作为其传输层协议（端口号179），使用触发式路由更新，而不是周期性路由更新。
- BGP能够承载大批量的路由信息，能够支撑大规模网络。
- BGP提供了丰富的路由策略，能够灵活进行路由选路，并能指导对等体按策略发布路由。
- BGP能够支撑MPLS/VPN的应用，传递客户VPN路由。
- BGP提供了路由聚合和路由衰减功能用于防止路由震荡，通过这两项功能有效地提高了网络稳定性。

BGP 特征：

- 运行BGP的路由器被称为BGP发言者（BGP Speaker）。
- 两个建立BGP会话的路由器互为对等体（[Peer），BGP对等体之间交换BGP路由表。
- BGP路由器只发送增量的BGP路由更新，或进行触发式更新（不会周期性更新）。
- BGP能够承载大批量的路由前缀，可在大规模网络中应用。
- BGP通常被称为路径矢量路由协议。
- 每条BGP路由都携带多种路径属性，BGP可以通过这些路径属性控制路径选择。而不像IS-IS、OSPF只能通过Cost控制路径选择，因此在路径选择上，BGP具有丰富的可操作性。

**BGP对等体关系**

- 由于BGP会话是基于TCP建立的，建立BGP对等体关系的两台路由器并不要求必须直连。
- 两种对等体关系类型：
  - EBGP（External BGP）：位于不同AS。
  - IBGP（Internal BGP）：位于相同AS。


**BGP对等体关系建立：**

- TCP三次握手建立连接。
- R1、R2之间相互发送Open报文，携带参数用于对等体建立：
  - My Autonomous System：自身AS号。
  - Hold Time：用于协商后续Keepalive报文发送时间。
  - BGP Identifier：自身Router ID。
- 参数协商正常后，后续双方相互发送Keepalive报文保活。

BGP建立对等体的对等体都会发起TCP三次握手，会建立两个TCP连接，实际BGP会只保留一个，选择Router ID大的发起的TCP连接。

- BGP对等体关系建立后，BGP路由器发送BGP Update 报文通告路由到对等体。

**BGP TCP连接源地址：**

- 缺省情况下，BGP使用出接口作为TCP连接的本地接口。
- 在部署IBGP对等体关系，建议使用Loopback地址作为更新源地址。Loopback接口非常稳定，而且可以借助AS内的IGP和冗余拓扑来保证可靠性。
- 在部署EBGP对等体关系时，通常使用直连接口的IP地址作为源地址，如若使用Loopback接口建立EBGP对等体关系，则应注意EBGP多跳问题。

**BGP报文类型：**

**BGP状态机：**

- idle：开始准备TCP的连接并监听远程对等体。
- connect：正在进行TCP连接，等待确认。如果TCP连接建立失败则进入Active状态，反复尝试连接。
- active：TCP连接没建立成功，反复尝试连接。
- opensent：TCP连接已经建立成功，开始发送Open包，Open包携带参数协商对等体的建立。
- openconfirm：参数、能力特性协商成功，自己发送Keepalive包，等待对方的Keepalive包。
- established：已收到对方的Keepalive包，双方能力特性经协商发现一致，开始使用Update通告路由信息。
- ![[Pasted image 20260817180532.png]]

**BGP路由的生成：**

BGP自身不会发现并计算产生路由，BGP将IGP路由表中的路由注入到BGP路由表中，并通过Update报文传递给BGP对等体。

BGP注入路由的方式有两种：

- network：精确注入。
- import-route：协议注入。

与IGP协议相同，BGP支持根据已有路由条路进行聚合，生成聚合路由。


**BGP聚合路由：**

aggregate x.x.x.x xx detail-suppressed：

- 如果指定了detail-suppressed（抑制明细路由），则BGP只会向对等体通告聚合后的路由，不会通告聚合前的明细路由。

**BGP对路由的处理：**

![[Pasted image 20260817181513.png]]



**BGP路由通告原则：**

- 只发布最优且有效的路由。
- 从EBGP对等体获取的路由，会发布给所有对等体。
- IBGP水平分割：从IBGP对等体获取的路由，不会发送给IBGP对等体。
- BGP同步规则：当路由器从自己的IBGP对等体学习到一条BGP路由时，它不能使用该路由通告给自己的EBGP对等体，除非它又从IGP协议学习到这条路由，如下图：

![image-20260317213745170](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260317213745170.png)

**基本配置命令：**

![image-20260317214013005](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260317214013005.png)



#### BGP的路由属性和反射器

**BGP路由属性**是跟随路由一起发布出去的一组参数，它对特定的路由进行了进一步的描述，使得路由接收者能够根据路由属性值对路由进行过滤和选择。

1.BGP路由属性分类：

- 公认必遵
- 公认可选
- 可选过渡
- 可选非过渡

常见的有：

- ***origin***：公认必遵，定义了路由信息的来源。值为{IGP、EGP、Incomplete}=

- ***as_path***：公认必遵，记录了路由从本地到目的地址所要经过的所有AS号。值为{AS_sequence、AS_set、AS_confed_sequence、AS_confed_set}。

- ***next_hop***：公认必遵

- ***med***（multi-exit Discriminator 多出口区分）：可选非过渡。MED属性仅在相邻的两个AS之间交换，收到此属性的AS不会再将其通告给其他AS。MED相当于IGP使用的度量值，用于判断进入AS时的最佳路由。当有多条条件相同的路由时，优先选择med值较小者作为最佳路由。

  通常情况下，BGP只比较来自同一个AS的路由的med属性值，可以配置compare-different-as-med命令，强制BGP比较来自不同AS的路由的MED属性值。

- ***local_preference***：公认可选，local_pref属性仅在IBGP对等体之间交换，不通告给其他AS。local_pref用于判断流量离开AS时的最佳路由，当路由器通过不同的IBGP对等体得到目的地址相同下一跳不同的多条路由时，将优先选择local_pref属性值较高的路由。

- ***atomic_aggregate***（原子聚合）：公认可选，用于说明携带该属性的路由是一条聚合路由，并警告形成该聚合路由的具体路由在发布的过程中可能丢失。

- ***aggregator***（聚合者）：可选过渡，它说明了发起路由聚合的设备的IP地址以及所属AS号，设备对BGP路由进行聚合时，会为路由同时添加原子聚合属性和聚合者属性。

- ***community***（团队）：可选过渡，是一种路由标记，用于简化路由策略的执行。值为AA：NN格式，AA为AS号，NN为自定义的编号。

- ***preferred-value***（协议首选值，华为设备特有属性），仅在本地有效，当BGP路由表中存在到相同目的地的路由时，将优先选择Preferred-value值高的路由。

**BGP路由反射器：**

由于水平分割的原因，为了保证中转AS200所有的BGP路由器都能学习到完整的BGP路由，就必须在AS内实现IBGP全互联，而实现IBGP全互联存在很多短板：

- 路由器需维护大量的TCP及BGP连接，尤其在路由器数量较多时
- AS内的BGP网络可的可扩展性较差。

引入路由反射器之后存在两种角色：

- RR（Route Reflector）：路由反射器
- Client：RR客户端

RR会将学习到的路由反射出去，使得IBGP路由在AS内传播无需建立IBGP全互联。将一台BGP路由器指定为RR的同时，还需要指定其Client。至于Client本身，无需做任何配置，它并不知晓网络中存在RR。

反射规则：

- 从非Client学习到IBGP路由，会将该路由反射给所有Client。
- 从Client学习到IBGP路由，会将该路由反射给所有Router。
- 如果路由学习自EBGP Peer，则发送给所有IBGP Peer。

被反射出去的路由会被RR插入特殊的路径属性。

RR场景下的路由防环：

- RR的设定使得IBGP水平分割原则失效，这久可能导致环路的产生，为此RR会为BGP路由添加两个特殊的路径属性来避免出现环路：
  - Originator_ID
  - Cluster_List
- Originator_ID、Cluster_List 都属于可选非过渡类型

Originator_ID：

- RR将BGP路由反射时会在反射出去的路由中增加Originator_ID，其值为本地AS中通告该路由的BGP路由器Router ID。
- 若AS内存在多个RR，则Originator_ID属性由第一个RR创建，并且不被后续的RR所更改。
- 当BGP路由器收到一条携带Originator_ID属性的IBGP路由，并且Originator_ID属性值与自身的Router ID相同，则它会忽略关于该路由的更新。

Cluster_List：

- 路由反射族包括反射器RR及其Client，一个AS内允许存在多个路由反射簇。
- 每个簇都有唯一的Cluster_ID，缺省时为RR的BGP Router ID。
- 当一条路由被反射器反射后，该RR（该簇）的Cluster_ID就会被添加到List中，
- 当RR收到一条携带Cluster_List属性的BGP路由时，且该属性值中包含该簇的Cluster_ID时，RR认为这条路由存在环路，将忽略该路由的更新。

#### BGP路由优选

当到达同一个目的网段存在多条路由时，BGP通过如下的次序进行路由优选：

丢弃下一跳不可达的路由：

1. 优选Preferred-Value最大的路由。
2. 优选Local_Preference最大的路由。
3. 本地始发的BGP路由优于从其他对等体学习到的路由，本地始发的路由优先级：优选手动聚合 > 自动聚合 > network > import > 从对等体学到的。
4. 优选AS_Path属性值最短的路由。
5. 优选Origin属性最优的路由，Origin优先级是 IGP > EGP > Incomplete。
6. 优选MED属性值最小的路由。
7. 优选从EBGP对等体学来的路由。EBGP > IBGP
8. 优选到Next_Hop的IGP度量值最小的路由。
9. 优选Cluster_List最短的路由。
10. 优选Originator_ID最小的设备通告的路由。
11. 优选具有最小IP地址的对等体通告的路由。

| 步骤  | 规则概要                         | 核心要义 (越大/小越优)       | 常用场景/备注                                     |
| --- | ---------------------------- | ------------------- | ------------------------------------------- |
| 1   | 优选下一跳可达的路由                   | (丢弃不可达的)            | 路由表里没有下一跳的路由直接失效                            |
| 2   | 优选有最大 `Preferred-Value` 的路由  | 值**大**              | 华为私有，仅本地生效，用于快速干预                           |
| 3   | 优选有最大 `Local_Preference` 的路由 | 值**大**              | 在AS内部传播，用于控制AS出口流量                          |
| 4   | 优选本地发起的路由                    | 本地注入 > 重分发/学习       | `network`/`aggregate` (本地) > `import-route` |
| 5   | 优选 `AS_Path` 最短的路由           | 值**小**              | 常见干预手段：AS路径前追加 `AS_Path Prepend`            |
| 6   | 优选 `Origin` 类型最优的路由          | **`i` > `e` > `?`** | 即 `IGP` > `EGP` > `Incomplete`              |
| 7   | 优选 `MED` 值最小的路由              | 值**小**              | 仅在路由来自同一相邻AS时比较，影响对方选路                      |
| 8   | 优选 EBGP 路由                   | **EBGP > IBGP**     |                                             |
| 9   | 优选到 BGP 下一跳 IGP 开销最小的        | 值**小**              | 让AS内部流量选择最近的出口路由器                           |
| 10  | 优选 `Cluster_List` 最短的路由      | 值**小**              | 路由反射器(环)场景                                  |
| 11  | 优选有最小 `Originator_ID` 的路由    | 值**小**              | 路由反射器(环)场景                                  |
| 12  | 优选从最小 `Router ID` 邻居学来的路由    | 值**小**              | 最终决胜局                                       |
| 13  | 优选 IP 地址最小的邻居的路由             | 值**小**              | 当`Router ID`相同时的最终最终决胜局                     |
**“有效路径优先看，本地注入最优先；**  
**本地优先本地优，AS路径短更优；**  
**起点相同比MED，EBGP比IBGP强；**  
**IGP邻居开销小，最后再看Router-ID。”**



1. AS边界Router，需要配置`next-hop-local`命令修改Next_Hop值为本地更新源地址。
2. preferred-value命令修改接收的BGP路由的优先级，`peer x.x.x.x preferred-value xxx`
3. 通过路由策略，修改通告出去的BGP路由的Local_Preference。
4. 



### 路由策略和路由控制

路由控制可以通过路由策略（Route Policy）实现

- 控制路由的接收：对接收的路由进行过滤，只接收满足条件的路由。
- 控制路由的引入：控制从其他路由协议引入的路由条目，只有满足条件的路由才会引入。

#### 匹配工具1：访问控制列表

- ACL由若干条permit或deny语句组成，每条语句就是该ACL的一条规则，每条语句中的permit或deny就是与这条规则相对应的处理动作。

- 通配符，0 表示匹配，1 表示不匹配。

- ACL的分类
  - acl number 2000 ~ 2999：基本acl，仅使用报文的源IP、分片信息和生效时间段信息来定义规则。
  - acl number 3000 ~ 3999：高级acl，可使用IPv4报文的源IP、目的IP、IP协议类型、ICMP类型、TCP源/目的端口、UDP源/目的端口，生效时间段来定义规则。
  - acl number 4000 ~ 4999：二层acl，使用报文的以太网帧头信息来定义规则，根据源MAC、目的MAC、二层协议类型等。
  - acl number 5000 ~ 5999：用户自定义acl
  - acl number 6000 ~ 6999：用户acl
  
- ACL只能匹配路由的前缀，无法匹配路由的网络掩码。

- 配置举例：

  ```bash
  [HW] acl number 3000
  [HW-acl-adv-3000] rule "1" deny ip source 10.1.1.0 0.0.0.255 destination 10.2.2.0 0.0.0.255
  [HW-acl-adv-3000] rule "2" permit ip
  ```

  

#### 匹配工具2：IP前缀列表

- IP-Prefix List 是将路由条目的网络地址、掩码长度作为匹配条件的过滤器，可在各路由协议发布和接收路由时使用。

- IP-Prefix List能够同事匹配IP地址前缀长度以及掩码长度，增强了匹配的精确度。

  ```bash
  [HW] ip ip-prefix "test" index "10" permit "x.x.x.x" greater-equal "xx" less-equal "xx"
  ```

- 配置举例：

  ```bash
  ip ip-prefix aa index 10 permit 0.0.0.0 8 less-equal 32
  ip ip-prefix bb index 10 deny 0.0.0.0 24 less-equal 32
  ip ip-prefix bb index 20 permit 0.0.0.0 0 less-equal 32
  ```



#### 策略工具1：Filter-Policy

过滤-策略是一个常用的路由信息过滤工具，能够对接收、发布、引入的路由进行过滤，可应用于IS-IS、OSPF、BGP等协议。

- 链路状态路由协议中，Filter-Policy 只能过滤路由信息，无法过滤LSA。
  - filter-policy export
  - filter-policy import
- 





#### 策略工具2：Route-Policy

- Route-Policy 是一个策略工具，用于过滤路由信息，以及为过滤后的路由信息设置路由属性。
- 一个Route-Policy 由一个或多个节点构成，每个节点都可以是一系列条件语句以及执行语句的集合，这些集合按照编号从小到大的顺序排列。![image-20260320142221449](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260320142221449.png)

- 每个节点内可包含多个条件语句，节点内的多个条件语句之间的关系为 &

- 节点之间的关系为 |

- route-policy根据节点编号大小从小到大顺序执行，匹配中一个节点将不会继续向下匹配。

- 基础配置

  ```bash
  [HW] route-policy "policy-name" permit node "node"
  [HW-route-policy] if-match "{acl | cost | interface | ip-prefix}"
  [HW-route-policy] apply "{cost | cost-type{type1、type2} | ip-address next-hop | preference | tag}"
  ```

  

#### 双点双向路由重发布

- 在边界路由器上把两个路由域的路由相互引入，称之为双向路由重发布。
- 两个路由域存在两个边界路由器，并且都执行双向路由重发布，此时称为双点双向路由重发布。
- 双点双向重路由发布虽然增强了网络的可靠性，但是容易引发次优路径，路由环路等问题。

故需要使用以上工具，解决次优路径和路由环路问题。





### 流量路径和转发路径控制

#### 策略路由技术背景

PBR（Policy-Based Routing，策略路由），被匹配的报文优先根据PBR的策略进行转发，即PBR策略的优先级高于传统路由表。

- 命令语法

  ```bash
  policy-based-route "PBR" permit node 10
  	if-match acl 2000
  	apply ip-address next-hop "x.x.x.x"
  ```

  

- PBR分为接口PBR和本地PBR

  - 接口PBR：只对转发的报文起作用。
  - 本地PBR：只对本地始发的流量生效。

- ![image-20260320160936560](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260320160936560.png)



- ![image-20260320161018789](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260320161018789.png)



当ACL的rule配置为permit时，设备会对匹配该规则的报文执行本地策略路由的动作：

- 本地策略路由中策略点为permit时对满足匹配条件的报文进行策略路由。
- 本地策略路由中策略点为deny时对满足匹配条件的报文不进行策略路由，既根据目的地址查找路由表转发报文。
- 如果未匹配上任何rule，则根据目的地址查找路由表转发报文。

可以全局或接口PBR调用：

```bash
# 本地调用
[HW] ip local policy-based-route "policy-name"
# 接口调用
[HW-G0/0/0] ip policy-based-route "policy-name"
```



#### MQC

模块化QoS命令行是指通过将具有某类共同特征的数据流划分为一类，并为同一类数据流提供相同的服务。

MQC包含三个要素：

- 流分类 traffic classifier
- 流行为 traffic behavior
- 流策略 traffic policy

MQC的流行为支持重定向报文，因此可以使用MQC实现IP单播策略路由。

- **流策略不同于PBR，PBR只能调用在三层接口，而流策略支持调用在二层接口。**

基本配置：

1. 创建流分类

   ```bash
   [HW] traffic classifier "classifier-name" [operator {and | or}]
   ```

   缺省情况下，流分类中各规则之间的关系为 or 。

2. 创建流行为

   ```bash
   [HW] traffic behavior "behavior-name"
   ```

   根据实际情况定义流行为中的动作。

3. 创建流策略，并绑定流分类与流行为。

   ```bash
   [HW] traffic policy "policy-name"
   [HW-trafficpolicy-policyname] classifier "classifier-name" behavior "behavior-name"
   ```


配置举例：

1. 配置高级ACL

   ```bash
   [HW] acl number 3000
   [HW-acl-adv-3000] rule 2 permit ip source 10.1.1.0 0.0.0.255 destination 0.0.0.0 0
   ```

2. 创建流分类匹配ACL

   ```bash
   [HW] traffic classifier 1
   [HW-classifier-1] if-match acl 3000
   ```

3. 创建流行为，如将报文重定向到指定地址

   ```bash
   [HW] traffic behavior 1
   [HW-behavior-1] redirect ip-nexthop x.x.x.x
   ```

4. 创建流策略Redirect，将流分类与流行为一一绑定

   ```bash
   [HW] traffic policy "Redirect"
   [HW-trafficpoalicy-Redirect] classifier 1 behavior 1
   ```

5. 应用流策略，在GE0/0/0接口入方向调用流策略Redirect

   ```bash
   [HW] interface G 0/0/0
   [HW-G0/0/0] traffic-policy "Redirect" inbound
   ```

   

####	流量过滤

流量过滤工具：

- Traffic-Filter：只能应用在接口视图。
- MQC：可以调
- 用在多种视图。



###	园区网典型技术应用概述



####	网络可靠性

VRRP实现网关冗余：

- 将多台路由设备组成一个虚拟路由器，通过配置虚拟路由器的IP地址为默认网关，实现默认网关的备份。

集群/堆叠简介：

- iStack 智能堆叠，针对华为盒式交换机。
- CSS 集群交换机系统，针对华为框式交换机。

####	网络服务与管理

NTP网络时间协议：

- 通过UDP传输，端口号为123。

LLDP链路层发现协议：

- 将本端的管理地址、设备标识、接口标识等信息组织起来，并发布给自己的邻居设备。

####	网络安全



####	VPN

####	组播

组播式在一台源IP主机和多台IP主机之间进行，中间的网络设备根据接收者的需要，有选择性的对数据进行复制和转发。

组播网络大体可以分为三个部分：

- 源端网络：将组播源产生的组播数据发送至组播网络。
- 组播转发网络：形成无环的组播转发路径，该转发路径也被称为组播分发树。
- 成员端网络：让组播网络感知组播组成员位置与加入的组播组。

####	IPv6



###	RSTP原理和配置



####	STP不足，RSTP对STP的改进

STP的不足：

- 收敛时间慢
  - 直连故障30s
  - 非直连故障50s
  - 阻塞到侦听：20s（Max Age）
  - 侦听到学习、学习到转发：15s（Forward Delay）

RSTP改进：

- **处理配置BPDU：**在处理次优BPDU时，RSTP的任何端口角色都会处理次优BPDU，从而加快了拓扑收敛。STP只有指定端口会立即处理次优BPDU，其他端口会忽略次优BPDU，等到Max Age计时器超时后，缓存的次优BPDU才会老化，然后发送自身更优的BPDU，进行新一轮的拓扑收敛。
- **快速收敛机制：**proposal/agreement机制目的在于消除为了避免拓扑变化引起的临时环路，必须等待的时间。p/a机制通过阻塞自己的非根端口来保证不会出现环路，加快了上游端口进入Forwarding状态的速度。

- **拓扑变更机制：**RSTP中，检测拓扑是否发生变化只有一个标准：一个非边缘端口迁移到Forwarding状态。
  - STP中，如果拓扑发生变化，需要先向根桥传递TCN BPDU，再由根桥来通知拓扑变更，泛洪TC置位的配置BPDU。
  - RSTP中，如果交换机收不到从根桥发来的RST BPDU后，A端口会快速切换为新的
- **BPDU保护：**正常情况下，边缘端口不会收到RST BPDU。如果有人伪造RST BPDU恶意攻击交换设备，当边缘端口接收到RST BPDU时，交换设备会自动将边缘端口设置为非边缘端口，重新进行生成树计算，从而引起网络震荡。交换设备上启动了BPDU保护功能后，如果边缘端口收到RST BPDU 边缘端口将被 error-down，但是边缘端口属性不变，同时通知网管系统。
- **Root保护：**对于启动根保护功能的指定端口，其端口角色只能保持为指定端口，根保护功能确保了根桥的角色不会因为一些网络问题而改变。但指定端口收到更优的BPDU时，端口会进入Discarding状态，不参与生成树计算，当持续一段时间（Max Age）不再收到更优BPDU时，端口自动恢复为正常指定端口。
- **环路保护：**出现单向链路故障，导致A端口转换为根端口，可能会出现环路。此时A端口角色会切换到Discarding状态，但状态会一直保持在Discarding状态，不转发报文，从而不会在网络中形成环路。配置在根端口或A端口上。
- **防TC-BPDU攻击：**启动防TC-BPDU报文攻击功能后，在单位时间内，交换设备处理TC BPDU报文的次数可配置，交换机只会处理小于等于阈值的次数。

####	RSTP的工作过程

- 网络初始化时，所有交换机都认为自己是根桥，并设置每个端口为指定端口，发送RST BPDU。其中BID最优的会被选举为根桥。
- 上游链路的设备互联端口通过P/A机制，快速进入转发状态。
- 下游链路的设备互联端口会进行新一轮的P/A协商，如果发送Poprosol的不是最优BPDU，则会被忽略。发送方一直收不到Agreement回应报文，则等待2 * Forward Delay后，进入转发状态，称为慢收敛。



####	RSTP基本配置



```bash
[HW] stp mode {stp | rstp | mstp}
[HW] stp root primary
[HW] stp root Secondary

[HW] stp priority "xxx [0 - 61440], 步长 4096，缺省取值为32768"

[HW] stp pathcost-standard {dot1d-1998 | dot1t | legacy}
[HW-G0/0/0] stp cost "cost"

[HW-G0/0/0] stp priority "xxx [0 - 240], 步长16，缺省取值为128"

[HW] stp enable
[HW-G0/0/1] stp edged-port enable

# 配置BPDU保护、根保护、环路保护功能
[HW] stp bpdu-protection
# 当端口角色是指定端口时，配置的根保护功能才生效，配置了根保护的端口，不可以再配置环路保护
[HW-G0/0/1] stp root-protection		
[HW-G0/0/1] stp loop-protection

# 配置TC保护功能
[HW] stp tc-protection interval "value"
# 配置交换机设备在收到TC BPDU后，单位时间内，处理TC类型BPDU报文并立即刷新转发表项的阈值，缺省情况为1
[HW] stp tc-protection threshold "threshold"
# 配置后，在stp tc-protection interval指定的时间内，设备只会处理stp tc-protection threshold 指定数量的TC报文。
```



###	MSTP原理与配置



MST Region 多生成树域：

- 一个网络中可以存在多个MST域，MST域中包含一个或多个生成树实例。

MSTI 多生成树实例：

- 每颗生成树都称为一个MSTI，MSTI使用Instance ID标识，华为设备取值为0~4094。
- VLAN映射表，将VLAN和MSTI一一映射。
- Instance 0 默认存在，默认下华为交换机上所有的VLAN都映射到了Instance 0。
- 一个VLAN只能对应一个MSTI，一个MSTI可以对应多个VLAN，同一VLAN的数据只能在一个MSTI中传输。

CST / CIST 公共生成树：

- 是连接交换网络内所有MST域的一颗生成树。

IST 内部生成树：

- Instance ID 为 0 的 MST，负责域内通信和域间互联。



###	交换机堆叠和集群

####	堆叠系统

**堆叠系统中所有单台交换机都称为成员交换机，按照功能不通，可以分为三种角色：**

- 主交换机（Master）
- 备交换机（Standby）
- 从交换机（Slave）

**堆叠优先级：**

- 堆叠优先级是成员交换机的一个属性，主要用于角色选举，优先级越大越优先，当选主交换机的可能性越大。

**堆叠ID：**

- 即成员交换机的槽位号（Slot ID），用来标识和管理成员交换机，堆叠中所有成员交换机的堆叠ID都是唯一的。

**堆叠逻辑接口：**

- 交换机之间用于建立堆叠的逻辑接口，每台交换机支持两个逻辑堆叠接口，分别是stack-port n/1 和 stack-port n/2，其中n为成员交换机的堆叠ID。

- 一个逻辑堆叠端口可以绑定多个物理成员端口，用来提高堆叠的可靠性和堆叠带宽。

- 堆叠成员设备之间，本端设备的逻辑堆叠端口stack-port n/1 必须与对端设备的逻辑堆叠端口stack-port m/2相连。

  ```bash
  # H3C
  #
  irf-port 1/2
   port group interface Ten-GigabitEthernet1/3/0/47 mode enhanced
   port group interface Ten-GigabitEthernet1/3/0/48 mode enhanced
  #
  irf-port 2/1
   port group interface Ten-GigabitEthernet2/3/0/47 mode enhanced
   port group interface Ten-GigabitEthernet2/3/0/48 mode enhanced
  #
  ```

**堆叠方式：**

![image-20260325143348159](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260325143348159.png)

**堆叠连接拓扑：**

- 链形连接
- 环形连接

**主、备交换机选举：**

- 运行状态：越早运行起来的交换机越优先
- 堆叠优先级：越高越优先
- MAC地址：越小越优先

**堆叠系统组建过程：**

1. 物理连接
2. 主交换机选举
3. 拓扑收集和备交换机选举
4. 软件和配置同步

**软件、配置同步：**

角色选举、拓扑收集完成后，所有成员交换机会自动同步主交换机的系统软件和配置文件：

- 如果备、从交换机和主交换机的软件版本不一致时，备、从交换机会自动从主交换机下载系统软件，然后新系统软件重启，并重新加入堆叠。
- 堆叠具有配置文件同步机制，备、从交换机会把主交换机的配置文件同步到本设备并执行，以保证堆叠中多台设备能够像一台设备一样在网络中工作。

**未上电的交换机加入堆叠：**

- 如果是链形连接，新加入的交换机建议添加到链形的两端，这样对现有的业务影响最小。
- 如果是环形连接，需要把当前环形拆成链形，然后再链形的两端添加设备。

**堆叠成员退出：**

**堆叠合并：**

- 堆叠合并是指稳定运行的两个堆叠系统合并成一个新的堆叠系统。
- 堆叠合并会使得两个堆叠系统之间竞选主交换机，竞选失败侧的堆叠系统所有成员交换机都将会重新启动，不建议对两个正在运行业务的堆叠系统进行合并。竞选失败侧的堆叠系统将保持原有主备从角色和配置不变。

**堆叠分裂：**

- 堆叠分裂后，会产生多个具有相同IP地址和MAC地址的堆叠系统，引起网络故障，为此必须进行IP地址和MAC地址的冲突检查。

**MAD检测：**

- 分裂后的堆叠系统通过MAD检测线缆（普通线缆，手动配置为MAD检测链路）发送MAD检测报文进行竞选，竞选失败的堆叠系统会关闭所有物理端口（手动配置的保留端口除外）。

-  直接检测和代理检测。

**流量本地转发优先：**

- 跨设备链路聚合极有可能出现报文的出接口和入接口不在同一台成员设备的情况，此时堆叠成员之间将通过堆叠线缆转发流量，这增加了堆叠线缆的流量负担，同时也降低了转发效率。



####	堆叠配置

1. 更改堆叠ID和堆叠优先级：

   ```bash
   # 更改堆叠ID后需要重启生效
   irf member a rename b
   irf member b priority 32
   ```

2. 配置堆叠逻辑接口：

   ```bash
   irf-port a/1
   	port group interface Ten-G a/x/x mode enhanced
   	port group interface Ten-G a/y/y mode enhanced
   ```

3. 配置BFD分裂检测：

   ```bash
   interface Vlanif x
   	mad bfd enable
   	mad ip address "ip" "mask" member 1
   	mad ip address "ip" "mask" member 2
   ```

   

###	IP组播基础

组播技术有效地满足了单点发送，多点接收的需求，实现了IP网络中点到多点业务数据的高效传送，能够大量节约网络带宽、降低网络负载。

####	组播数据报文结构

- 组播目的IP地址：地址范围从224.0.0.0 - 239.255.255.255。
- 组播目的MAC地址：组播MAC地址由组播IP地址映射而来。

![image-20260330154609866](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260330154609866.png)

IPv4组播地址的前4位是固定的1110，后28位中只有23位被映射到MAC地址，因此丢失了5位的地址信息。导致结果是有32个IPv4组播地址映射到同一MAC地址上。在分配地址时必须考虑这种情况。

- 一个组播MAC地址所标识的一组设备有着共同的特点，那就是他们都加入了相同的组播组，这些设备将会侦听目的MAC地址为该组播MAC地址的数据帧。组播MAC地址不能作为数据帧的源地址。

####	组播服务模型

组播组成员在接收组播数据时可以对于组播数据源进行选择，因此产生了两种组播服务模型。

- ASM（Any-Source Multicast）：组成员可以接收到任意源发送到该组的数据。
- SSM（Source-Specific Multicast）：组成员只会接收指定源发送到该组的数据。



####	组播路由与RPF检查

- 由于组播转发容易产生环路、次优、重复报文，所以组播路由表项除了目的网络和出接口还需要添加组播源和入接口的信息。设备仅转发从特定唯一的入接口收到的组播数据，从而避免组播转发时产生环路、次优、重复报文等问题。
- 对于相同的组播源，设备通过RPF（Reverse Path Forwarding，反向路径转发）检查可以确定设备上唯一的组播流量入接口。
- 组播路由表包含组播源和组播组，因此有时又被称为（S，G）表现。





###	IPv6

IPv4网络演变为IPv6网络主要有以下三种技术：

- 双栈技术：在一台设备上同时启用IPv4和IPv6。
- 隧道技术：将一种协议的数据封装在另一种协议中。
- 转换技术：将IPv6和IPv4进行转换。

IPv6路由协议：

- OPSFv3
- IS-IS for IPv6
- BGP4+
- PIM



####	IPv6地址说明

IPv6地址格式：

- 首选格式：冒号分割为8段，每一段16bit，每一段内用十六进制表示。
- 压缩格式：
  - 每段前导0可以省略，但是如果该段为全0，则至少保留一个0，，拖尾的0不能被省略。
  - 一个或多个连续的段为全0时，可用`::`表示，整个IPv6地址缩写中只允许一个`::`
- 内嵌IPv4地址的格式：
  - 地址的前96bit为IPv6地址格式，后32bit为IPv4地址格式。
  - IPv6部分可采用首选或压缩格式，IPv4部分采用点分十进制格式。
  - 例如：`0:0:0:0:0:0:166.168.1.2/64`

IPv6 地址为128bit。

- 网络前缀：相当于IPv4地址中的网络地址。
- 接口标识：相当于IPv4地址中的主机ID。

IPv6地址前缀：

- 2001::/16：IPv6公网地址段。
- 2002::/16：用于6to4隧道。
- FE80::/10：链路本地地址前缀，用于本地链路地址范围内的通信。
- FF00::/8：组播地址前缀，用于IPv6组播。
- ::/128：未指定地址，类似0.0.0.0。
- ::1/128：环回地址，类似127.0.0.1。

IPv6地址接口标识：

- 接口ID可通过三种方式生成：手工配置、系统自动生成、基于IEEE EUI-64规范生成。
- IEEE EUI-64规范：
  - 由48位MAC地址自动生成64位Interface ID的方法。
  - 将第7bit0转换为1，在MAC地址中间插入两个字节的FFFE。
  - 这种由MAC地址产生IPv6地址的方法可以减少配置的工作量，只需要配置一个IPv6前缀就可以与接口ID形成IPv6地址。
  - 使用这种方式最大的缺点就是某些恶意用户可以通过二层MAC推算出三层IPv6地址。
- 系统自动生成：
  - 设备采用随机生成的方法产生一个接口ID，目前Windows操作系统使用该方式。

####	IPv6地址类型

- 单播地址
- 组播地址
- 任播地址
- IPv6没有定义广播地址

IPv6常见单播地址：

- GUA Global Unicast Address 全球单播地址：也被称为可聚合全球单播地址。该类地址全球唯一，即公网IP地址。

  ![image-20260331153236870](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260331153236870.png)

- ULA Unique Local Address 唯一本地地址：是私网地址，在公网中不可被路由因此不能直接访问公网。

  ![image-20260331153614523](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260331153614523.png)

- LLA Link-Local Address 链路本地地址：是IPv6中另一种应用范围受限制的地址类型，前缀为FE80::/10

  ![image-20260331163958916](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260331163958916.png)

####	IPv6组播地址

![image-20260331165644850](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260331165644850.png)

IPv6组播MAC地址：

- 前16bit固定为`3333	`
- 后32bit为组播IP地址的后32bit Group ID 直接映射过来。

被请求节点组播地址：

- 当一个节点具有了单播或任播地址，就会对应生成一个被请求节点组播地址，并且加入这个组播组。该地址主要用于邻居发现机制和地址重复检测功能。被请求节点组播地址的有效范围为本地链路范围。
- 前104bit为固定前缀：`FF02::1:FF--:----`
- 后24bit为IP地址的后24bit。
- IPv6没有广播，使用组播代替，相比广播更节约了资源。

####	IPv6报文构成

IPv6报文一般由三个部分组成：

- 基本报头：提供报文转发的基本信息，路由器通过解析基本报头就能够完成绝大多数的报文转发任务。
- 扩展报头：提供一些扩展的报文转发信息，如分段、加密等，该部分不是必需的，也不是每个路由器都需要处理，仅当需要路由器或目的节点做某些特殊处理时，才由发送方添加一个或多个扩展头。
- 上层协议数据单元：一般由上层协议报头和它的有效载荷构成，该部分与IPv4的上层协议数据单元相似。

其中，

- IPv6 Header 长度固定为40字节，必须包含此内容。
- Extension Headers 可选报头，可以包含一个或多个扩展报头，也可以没有扩展头。IPv6扩展报头没有最大长度限制，并不会被路径上所有路由器解析，一般只会被目的路由器解析处理。
- Upper Layer Protocol Data Unit 一般由上层协议报头和它的有效载荷构成，有效载荷可以是ICMPv6、TCP、UDP。

![image-20260401133314039](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260401133314039.png)

####	IPv6基本配置

开启设备IPv6报文转发功能

```bash
[HW] ipv6
```

试能接口IPv6功能，并配置IPv6 GUA

```bash
[HW] interface g 1/1/1
[HW-G1/1/1] ipv6 enable
[HW-G1/1/1] ipv6 address "ipv6-address/prefix-length"
# 每个接口下最多科配置10个GUA
```





###	VRRP

VRRP（Virtual Router Redundancy Protocol 虚拟路由器冗余协议）可以解决这个问题，VRRP功能将可以承担网关功能的一组路由器加入到备份组中，形成一台虚拟路由器，并为该虚拟路由器指定虚拟IP地址。VRRP通过选举机制决定哪台路由器承担转发任务。

- 虚拟IP地址和IP地址拥有者：接口IP地址与虚拟IP地址相同的路由器被称为IP地址拥有者。
- 备份组中路由器的优先级：**VRRP Priority 取值范围 0 到 255，缺省为100。值越大越优先。可配置范围为1 到 255。当路由器为IP地址拥有者时，其优先级始终为255。**
- 备份组中路由器的工作方式：
  - 非抢占模式
  - 抢占模式
- VRRP监视功能：VRRP监视功能通过**NQA**（Network Quality Analyzer 网络质量分析）、**BFD**（Bidirectional Forwarding Detection 双向转发检测）等检测Master路由器和上行链路的状态，并通过**Track**功能在VRRP设备状态和NQA/BFD之间建立关联。

####	VRRP 报文格式

vrrp 只有一种报文，即 Advertisement报文，基于组播方式发送，因此只在同一个广播域传递，Advertisement报文的目的组播地址为224.0.0.18

####	IPv4 VRRP配置举例:

在接口视图上创建备份组，并配置备份组1和虚拟地址：

```bash
[h3c-vlan-interface] vrrp vrid 1 virtual-ip "x.x.x.x"
[h3c-vlan-interface] vrrp vrid 1 priority 110
```

VRRP缺省为抢占模式，为了避免频繁进行状态切换，配置抢占延迟时间为5000毫秒（5s）：

```bash
[h3c-vlan-interface] vrrp vrid 1 preempt-mode delay 5000
```

验证配置：

```bash
display vrrp verbose
```



###	BFD

BFD是一种双向转发检测机制，它是介质无关和协议无关的快速故障检测机制，可以提供毫秒级的检测，可以实现链路的快速收敛，BFD通过和上层路由协议联动，可以实现路由的快速收敛。

BFD的检测机制是两个系统建立BFD会话，并沿它们之间的路径周期性发送BFD控制报文，如果一方在既定时间内没有收到BFD控制报文，则认为路径上发送了故障，BFD控制报文是UDP报文，端口号3784。检测模式为两种：

- **异步模式：**

  系统之间相互周期性地发送BFD控制包，如果某个系统在检测时间内没有收到对端发来地BDF控制报文，就会宣布会话Down。

- **查询模式：**

  系统连续发送多个BFD控制包，如果在检测时间内没有收到返回的报文就宣布会话为Down。

####	BFD工作原理

BFD报文建立会话，报文结构有强制部分和可选的认证部分。

BFD会话的建立有两种方式：

- **静态建立**
- **动态建立**

BFD会话状态：

BFD状态机的建立和拆除都采用三次握手机制。

init、up用来建立会话，down用来断开会话。

- Down
- Init：说明本地会话期望进入up，但是远端还没回应。
- Up：说明BFD会话成功建立，并且正在确认链路的联通性，会话会保持在Up状态直到链路故障或者管理Down状态。
- AdminDown

BFD缺省时间参数：

- BFD报文发送间隔默认1000毫秒，接受间隔默认1000毫秒，本地检测倍数3次。
- BFD会话等待恢复时间0s。

BFD Echo 功能：解决两台检测设备中，一台设备不支持BFD功能的场景。

联动功能简介：

- 检测模块：负责监测，将探测结果通知给Track模块。
- Track模块：收到探测结果后，及时改变Track项的状态，并通知应用模块。
- 应用模块：根据Track项的状态，进行相应的处理，从而实现联动。

####	配置举例

注意事项：

- BFD会话的本地标识符和远端标识符分别对应，即本端的标识符与对端的远端标识符相同。如果不对应则会话无法Up。
- 标识符配置后无法修改，只能删除后重新配置。

三层交换机配置单跳检测：

在HWA上使能BFD，配置与HWB之间的BFD会话atob。

```c#
[HWA] bfd   //全局使能BFD
[HWA-bfd] quit
[HWA] bfd atob bind peer-ip default-ip interface gigabitethernet 1/0/1   //配置BFD会话atob
// 配置BFD会话的本地标识符，SwitchA上的本地标识符需要与SwitchB上的远端标识符一致
[HWA-bfd-session-atob] discriminator local 10
// 配置BFD会话的远端标识符，SwitchA上的远端标识符需要与SwitchB上的本地标识符一致
[HWA-bfd-session-atob] discriminator remote 20   
[SwitchA-bfd-session-atob] commit   //提交BFD会话配置，使配置生效
```

在HWB上试能BFD，配置与HWA之间的BFD会话btoa

```c#
[HWB] bfd
[HWB-bfd] bfd btoa bind peer-ip default-ip interface gigabitethernet 1/0/1	//配置BFD会话btoa
[HWB-bfd-session-btoa] discriminator local 20
[HWB-bfd-session-btoa] discriminator remote 10
[HWB-bfd-session-btoa] commit
```

配置完成后，在HWA和HWB上执行`display bfd session all verbose`，可以看到建立了一个单条 BFD session ，状态为Up。

配置BFD状态与接口状态联动：

```c#
[SWA] bfd atob
[SWA-bfd-session-atob] process-interface-status
```

此时BFD会话的状态直接影响接口的可用性，如果A-B之间的二层交换机线路故障，则BFD会将AB的端口DOWN（BFD status down）。



路由器的BFD会话配置：

```bash
[HW] bfd "session-name" bind peer-ip "ip-address" [vpn-instance "vpn-name"] gigbit 0/0/0 [source-ip "x.x.x.x"]
```

在R1和R2之间建立静态BFD会话：

```bash
[R1] bfd
[R1] bfd 12 bind-peer x.x.x.x interface gigabitethernet 0/0/1
[R1-bfd-session-12] discrimintor local 10
[R1-bfd-session-12] discrimintor remote 20
[R1-bfd-session-12] commit
```

在R1上配置静态路由并绑定BFD会话：

```bash
[R1] ip route-static x.x.x.x 32 x.x.x.x track bfd-session 12
```







###	ERR

1. VLAN的取值范围：`[1, 4094]`
2. 



##	RK



###	GVRP

GARP（Generic Attribute Registration Protocol），主要用于建立一种属性传递扩散的机制，以保证协议实体能够注册和注销该属性。

GVRP（GARP VLAN Registration Protocol）是GVRP的一种应用，用于注册和注销VLAN属性。通过GVRP协议，一台设备上的VLAN信息会迅速传播整个交换网。当你在一个房间点灯（配置 VLAN），GVRP负责自动打开通往这个房间的所有走廊门（Trunk 端口），并关掉无人房间的门。

####	工作原理

GVRP协议可以实现VLAN属性的自动注册和注销，通过声明和回收实现VLAN属性的注册和注销。

手工配置的VLAN称为静态VLAN，通过GVRP协议创建的VLAN称为动态VLAN，GVRP有三种注册模式：

- Normal：允许动态VLAN在端口上注册，同事会发送静态VLAN和动态VLAN的生命信息。
- Fixed：不允许动态VLAN在端口上注册，只发送静态VLAN的声明信息。（禁止动态注册或注销）
- Forbidden：不允许动态VLAN在端口上进行注册，同时删除端口上除VLAN1外的所有VLAN，只发送VLAN1的声明信息。



####	配置举例

全局使能GVRP，并使能接口的GVRP功能，并配置接口注册模式。

```bash
[AGG] vcmp role silent
[AGG] gvrp
[AGG-G0/0/0] gvrp
[AGG-G0/0/0] gvrp registration normal
```



###	VCMP

VLAN集中管理协议 VCMP（VLAN Central Management Protocol），VCMP只能同步VLAN配置，不能帮助其动态划分端口到VLAN，因此VCMP一般需要和LNP配合使用。（华为私有协议）

![img](http://127.0.0.1:51299/icslite/hdx/pages/HDXAZM1017P_11_zh/HDXAZM1017P_11_zh/resources/dc/images/fig_dc_cfg_vcmp_000401.png)

####	原理概述

VCMP使用域来管理交换机，通过角色定义来确定设备的属性。

**VCMP管理域：**

VCMP管理域由一组域名相同的交换机通过Trunk或Hybrid链路类型的接口互连构成。同一域内的每台交换机都必须相同的域名，且一台交换机只能加入一个VCMP管理域，不同域的交换机间不能同步VLAN信息。

**VCMP角色：**

- Server
- Client
- Transparent
- Silent

![image-20260416140554322](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260416140554322.png)

**实现机制：**

VCMP报文只能在Trunk或Hybrid类型接口的VLAN1上传输，发送三种组播模式的报文：

- Summary Advert
- Subset-Advert
- Advert-Request

####	配置举例

一个VCMP管理域只能由一个Server

<img src="http://127.0.0.1:51299/icslite/hdx/pages/HDXAZM1017P_11_zh/HDXAZM1017P_11_zh/resources/dc/images/fig_dc_cfg_vcmp_001702.png" alt="img" style="zoom:80%;" />

指定各设备的角色

``` bash
[HW-AGG] vcmp role server
[HW-ACC] vcmp role client
```

配置VCMP相关参数

```bash
[HW-AGG] vcmp domain vdl
[HW-AGG] vcmp device-id server
[HW-AGG] vcmp authentication sha2-256 password xxx
```

```bash
[HW-ACC] vcmp domain vdl
[HW-ACC] vcmp authentication sha2-256 password xxx
```

验证配置结果

```bash
[HW-AGG] display vcmp status	# 可以查看VCMP配置信息，包括VCMP管理域名、设备角色、设备ID、配置序列号
```



###	GVRP和VCMP

GVRP和VCMP都是VLAN相关的管理协议。

不同点：

- GVRP 是分布式链路协商
- VCMP 是集中式全网同步

| 协议         | GVRP                                                         | VCMP                                                         |
| :----------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| **核心逻辑** | **“点对点”的链路协商**                                       | **“一对多”的全局管控**                                       |
| **工作模式** | **分布式**：两台交换机之间通过 Trunk 链路互相商量着来，你有的 VLAN 分我一点，我有的也告诉你。 | **集中式**：全网必须指定一台 **Server**（管理员），其他设备都是 **Client**（被管理者），Client 必须无条件听从 Server。 |
| **依赖关系** | 依赖 **STP（生成树协议）** 的拓扑变化来触发 VLAN 的重新注册和注销。 | 依赖 **VLAN 1**（默认管理 VLAN）进行管理报文的广播。         |



| 协议       | GVRP 角色                                                    | VCMP 角色                                                    |
| :--------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| **管理者** | 无固定管理者。谁配置了静态 VLAN，谁就是信息的源头。          | **Server**：唯一能创建、删除 VLAN 的角色，全网的绝对权威。   |
| **学习者** | **Normal 模式接口**：既能发送自己的 VLAN 信息，也能学习对方的动态 VLAN。 | **Client**：无任何 VLAN 自主权，本地 VLAN 配置会被 Server 强制覆盖。 |
| **旁观者** | **Fixed 模式接口**：只发送自己的，不学习对方的。             | **Transparent**：不学习，不管理，只帮忙把报文传下去。        |
| **绝缘体** | **Forbidden 模式接口**：不发送不学习，且阻塞对方的。         | **Silent**：收到报文直接丢弃，像断开了连接一样。             |



###	VLAN聚合（Super VLAN ）

VLAN Aggregation，用多个Sub VLAN隔离广播域，并将这些Sub VLAN聚合成一个逻辑VLAN（Super-VLAN）。这些Sub-VLAN使用同一个IP子网和缺省网关，进而达到节约IP地址资源的目的。

####	原理概述

Super-VLAN不包含物理接口，只用来建立三层VLANIF接口，然后再通过建立Super-VLAN和Sub-VLAN间的映射关系，把三层VLANIF接口和物理接口结合起来，实现所有Sub-VLAN共有一个网关与外部网络通信，并用Proxy ARP实现Sub-VLAN间的三层通信。

Sub-VLAN之间的通信问题：

​	Sub-VLAN内的主机使用的是同一网段的地址，共有用同一个网关地址，主机只会做二层转发，而不会送网关进行三层转发。而各个Sub-VLAN之间是隔离的，所需要需要在网关处开启arp-proxy，代替发起arp请求。

####	配置举例

配置Super-VLAN，并加入相应的Sub-VLAN

```bash
[AGG] vlan 4
[AGG-vlan4] aggregate-vlan
[AGG-vlan4] access-vlan 2 to 3
```

配置Proxy ARP，使得Sub-VLAN之间互通

```bash
[AGG] int vlanif 4
[AGG] arp-proxy inter-sub-vlan-proxy enable
```





###	MUX VLAN

MUX VLAN （Multiplex VLAN）提供了一种通过VLAN进行网络资源控制的机制。

例如，在企业网络中，企业员工和企业客户可以访问企业的服务器，对于企业来说，希望企业内部员工之间可以相互交流，而企业客户之间是隔离的，不能够互相访问。



###	VLAN终结

VLAN终结是指对接收到的报文中的VLAN标签进行识别，根据后续的转发行为对报文中的单层或双层VLAN标签进行剥离，然后进行三层转发或其他处理。（一般只有路由器支持？）

VLAN终结的实质包含两个方面：

- 对接口接收的报文，剥除VLAN标签后进行三层转发或其他处理。
- 对接口发出的报文，将相应的VLAN标签添加到报文中后再发送。

####	配置举例

配置Dot1q终结子接口实现同设备VLAN间通信：

```bash
[Router] vcmp role silent
[Router] interface g 0/0/1
[Router-G0/0/1] port link-type hybrid
[Router] interface g 0/0/1.1
[Router-g0/0/1.1] dot1q termination vid 10
[Router-g0/0/1.1] ip address 10.10.10.1 24
[Router-g0/0/1.1] arp broadcast enable
```



###	MSTP

![image-20260420172838534](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260420172838534.png)



配置MSTP基本功能

配置MST域：

```bash
[SW] stp region-configuration
[SW-mst-region] region-name "xxx"
[SW-mst-region] instance 1 vlan 2 to 10
[SW-mst-region] instance 2 vlan 11 to 20
[SW-mst-region] active region-configuration
```

配置MSTI1和MSTI2的根桥和备份根桥：

```bash
[SWA] stp instance 1 root primary
[SWB] stp instance 1 root secondary
```

配置实例MSTI中将要被阻塞端口的路径开销值大于缺省值：

```bash
# 配置swa的端口路径开销值的计算方法为华为计算法
[SWA] stp pathcost-standard legacy
# 配置swc的端口路径开销计算方法为华为计算法，将端口G0/0/2在实例MSTI2中的路径开销值配置为20000
[SWC] int g 0/0/2
[SWC-G0/0/2] stp instance 2 cost 20000
```

配置边缘端口和BPDU保护功能：

```bash
[SWC-G0/0/2] stp edged-port enable
[SWC] stp bpdu-protection
```



###	VxLAN

VxLAN是MAC in UDP的网络虚拟化技术，所以其报文封装是在原始以太网之前添加了一个UDP封装及VxLAN头封装，可用数量是2^24^ 次方。

###	DHCP



#### Options 自定义选项字段介绍

- Option 82：中继代理信息选项，DHCP中继或DHCP Snooping 设备接收到DHCP Client发送给Server的Request报文后，在该报文中添加Option 82后，转发给DHCP Server。管理员可以从Option 82中获得DHCP Client的信息，如DHCP Client所连交换机端口的VLAN ID、二层端口号、中继设备的MAC地址等。
- Option 43：厂商特定信息选项，Server 和 Client 通过 Option 43 交换厂商特定的信息。在WLAN组网中，AP作为DHCP Client，DHCP Server可以为AP指定AC的IP地址，以方便AP与AC建立连接。

####	DHCP分配IP地址顺序

1. MAC静态绑定的IP
2. 已使用过的IP
3. 空闲状态的IP
4. 超过租期的IP
5. 产生冲突的IP

####	DHCP 配置

- 配置基于接口方式的地址池：接口地址所属的IP地址网段即为接口地址池。
- 配置基于全局方式的地址池：

####	DHCP中继

DHCP Relay 是解决DHCP Server 和 Client不在同一个广播域而提出的，提供了对DHCP Boradcast 报文的中继功能，能够把DHCP Client的广播报文透明的传送到其他广播域的DHCP Server上。

DHCP Relay  配置介绍：

```bash
# 使能接口的DHCP中继功能
[HW-G0/0/0] DHCP select relay
# 在接口视图下配置DHCP服务器的IP地址
[HW-G0/0/0] DHCP relay server-ip "ip-address"
# 创建DHCP服务器组
[HW] DHCP Server group "group-name"
# 在DHCP服务器组中配置DHCP服务器成员
[HW-DHCP-server-group-HW] DHCP-server "ip-address"
# 配置接口应用的DHCP服务器组
[HW-G0/0/0] DHCP relay server-select "group-name"
# 开启接口下的DHCP Client功能
[HW-G0/0/0] ip address DHCP-alloc
```

![image-20260421101434189](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260421101434189.png)



###	EIGRP

思考私有路由协议



###	网桥

工作在数据链路层，核心认为是连接两个相似的局域网，并根据MAC地址来决定是是否放行。

| **设备类型**        | **工作层级** | **核心功能**             | **转发依据**         | **隔离冲突域** | **隔离广播域** |
| :------------------ | :----------- | :----------------------- | :------------------- | :------------- | :------------- |
| **集线器 (Hub)**    | 物理层       | 信号放大与广播           | 无（广播到所有端口） | ❌ 否           | ❌ 否           |
| **网桥 (Bridge)**   | 数据链路层   | 连接网段、按需转发/过滤  | **MAC 地址**         | ✅ 是           | ❌ 否           |
| **交换机 (Switch)** | 数据链路层   | 多端口网桥，高效数据交换 | **MAC 地址**         | ✅ 是           | ❌ 否           |
| **路由器 (Router)** | 网络层       | 连接不同网络，路径选择   | **IP 地址**          | ✅ 是           | ✅ 是           |

交换机本质是一个多端口，高性能的网桥。

网桥又分为基于生成树的透明网桥和源路由网桥。

| 对比维度         | **透明网桥 (Transparent Bridge)**                            | **源路由网桥 (Source-Route Bridge)**                         |
| :--------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| **核心逻辑**     | **网桥负责寻址**：网桥通过自学习构建MAC地址表，并据此决定转发路径，对终端设备完全透明 。 | **终端负责寻址**：由发送数据的终端设备负责发现并指定完整的传输路径，网桥仅根据数据帧头部的路由信息进行转发 。 |
| **主要应用**     | 以太网 (Ethernet)，这是现代局域网的基础 。                   | 令牌环网 (Token Ring) 等，是IBM网络环境中的传统技术 。       |
| **工作机制**     | **自学习与泛洪**：网桥会记录每个源MAC地址和对应的端口。若目标地址未知，则将帧“泛洪”到所有端口；若已知，则精准转发 。 | **探测与指定**：源站先发送“探测帧”遍历网络，收集所有可能路径。选定最佳路径后，将路径信息写入后续每个数据帧的帧头中 。 |
| **对终端的要求** | **零要求**：终端设备完全不需要知道网桥的存在，是“即插即用”的 。 | **功能复杂**：终端设备需要具备参与路由探测、选择和维护的能力。 |
| **优缺点**       | **优点**：易于部署和管理，即插即用。 **缺点**：存在广播风暴风险，需要通过生成树协议(STP)来避免环路 。 | **优点**：能利用多条路径，理论上效率更高。 **缺点**：对终端要求高，部署复杂，可扩展性差。 |

最终，透明网桥称为了行业标准，IEEE 802.1委员会最终采纳了透明网桥方案作为局域网互联的标准架构。

今天使用的所有交换机，本质上都是多端口、高性能的透明网桥，其底层工作原理完全基于透明网桥的自学习和转发机制。



19-38



###	存储介质

易失性存储器（SDRAM）：

- 静态RAM（SRAM）
- 动态RAM（DRAM）

非易失性存储器（NVRAM）：

- ROM：只读存储器。

  



###	IEEE

####	IEEE 802.1x



###	命令

loopback-detect enable



###	杂项



- 采用存储-转发方式处理信号的设备是交换机。交换机还有直通式和无碎片的方式。
- 与服务质量管理有关的协议是 RSVP。
- ![image-20260423162105288](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260423162105288.png)
- 若主机采用以太网接入Internet，TCP段格式，数据字段的最大长度为1460字节。
- ![image-20260424111741327](C:\Users\46823\AppData\Roaming\Typora\typora-user-images\image-20260424111741327.png)
- 802.3z还定义帧扩展和帧突发技术，其中帧突发是指一个站可以连续发送多个帧。
- CSMA/CD采用P-坚持型算法，与其他监听算法相比，这种算法传输介质利用率高，冲突概率随着吞吐量增加而增加。













##	RK-JC


###	数据链路层

####	检查和纠错

- 海明码
- CRC编码

####	对点对协议

- PPP
- PPPoE
- HDLC

####	数据链路层结构

IEEE 802把数据链路层分为两个子层：

- 逻辑链路控制层：LLC
- 媒体接入控制层：MAC

以太网MAC帧格式：

```bash
			7          1         6      6      2     0~1500    0~46    4
帧间隙 | 帧前导字符 | 帧起始字符 | DMAC | SMAC | Type |   Data   | 填充 | FCS
```

以太网最小帧长64字节，是指从目的地址到校验和的长度。

CSMA/CD 在网络负载较小时，CSMA/CD 协议的通信效率很高；但在网络负载较大时，发送时间增加，发送效率急剧下降。这种网络协议适合传输非实时数据。

**坚持算法：**

- **1-持续CSMA：**当信道发生冲突时，要发送帧的站点一直持续监听，一旦发现信道有空闲（即在*帧间最小间隔*时间内没有检测到信道上有信号）便可发送。（有利于抢占信道，减少信道空闲时间；较长的传播延迟和同时监听会导致多次冲突，减低系统性能）
- **非持续CSMA：**发送方不支持侦听信道，而是在冲突时等待随机的一段时间N，再发送。（有更好的信道利用率，由于随机时延后退，从而减少了冲突的概率；后退使信道闲置一段时间，会使信道利用率降低，增加了发送时延）
- **p-持续CSMA：**发送方按P概率发送帧，当信道空闲时，发送方不一定发送数据，而是按照P概率发送，1-P概率不发送。如果不发送，下一时间间隔 ζ 仍空闲，同理进行发送。若信道忙，则等待随机的一段时间重新开始。**ζ 为单程网络传输时延**。

几个重要定义和数据：

- 冲突检测最长时间为两倍的总线端到端的传播时延 2ζ ，2ζ 称为争用期，又称为碰撞窗口。
- 网络利用率 = 吞吐率 / 网络数据速率。
- 吞吐率：单位时间实际传送的数据位数。
- 强化碰撞：当发送碰撞时，发送数据的站除了立刻停止发送当前数据外，还需要发送32bit/48bit的干扰信号，所有站都会收到阻塞信息。
- 以太网帧最小帧长64字节，最大帧长1518字节，最大传输单元MTU 1500 字节

**退避算法：**退避算法减少了重传时再次发生碰撞的概率。

- 设置基本退避时间为争用期 2ζ。
- 从整数集合[0, 2^k^-1] 中随机取一个整数r，则 r * 2ζ 为发送站点的等待时间。其中 k = Min[重传次数，10]  。
- 重传次数大于16次，则丢弃该数据帧。

可以看出，该算法的特点是网络负载越重，可能后退的时间越长，没有对优先级进行定义，不适合突发性业务和流式业务。该算法考虑了网络负载对冲突的影响，在重负载下能有效化解冲突。
