
## VLAN
#### Trunk port pvid 的作用
trunk port 的 pvid 的作用是处理untagged报文，比如对方是终端设备、或者是对方接口是access口。它会给untagged报文打上pvid标签，然后再判断是否在allow-pass列表中是否转发。

比如，交换机A与B相连，其中交换机A的互联口配置为 trunk，允许 vlan10 通过，pvid 没有设置，缺省为 vlan1。交换机B的互联口设置为 access，属于 vlan10。此时，交换机B是不能连通交换机A的，因为按照规则，从 access 发出去的报文为 untagged 的，A 的 trunk 口接收到后会打上 pvid vlan 1 的标签，而 vlan 1 不在 allow-pass 列表中，故丢弃。

#### VLAN 接口接收和发送数据帧的规则 [[HCIA#^99fab4|HCIA原纪录]]
接收数据帧：
- Untagged：所有接口都会打上 pvid 的 tag，但是 Trunk、Hybrid 会根据数据帧的 VID 是否为allow-pass 的来判断是否接收，而 Access 则是无条件接收。
- Tagged：所有接口都会检查 tag 检查是否允许通过。
发送数据帧：
- Access、Trunk：当 tag 和 pvid 相同时剥离后发送。
- Hybrid：tag 在 Untagged 列表中才剥离。

因此，Access接口发出的数据帧肯定不带Tag，Trunk接口发出的数据帧只有一个VLAN的数据帧不带Tag，其他都带VLAN标签；Hybrid接口发出的数据帧可根据需要设置某些VLAN的数据帧带Tag，某些VLAN的数据帧不带Tag。
