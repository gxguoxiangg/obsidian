
#### trunk port 的 pvid 的作用是处理untagged报文，比如对方是终端设备、或者是对方接口是access口。它会给untagged报文打上pvid标签，然后再判断是否在allow-pass列表中是否转发。

比如，交换机A与B相连，其中交换机A的互联口配置为 trunk，允许 vlan10 通过，pvid 没有设置，缺省为 vlan1。交换机B的互联口设置为 access，属于 vlan10。此时，交换机B是不能连通交换机A的，因为按照规则，从 access 发出去的报文为 untagged 的，A 的 trunk 口接收到后会打上 pvid vlan 1 的标签，而 vlan 1 不在 allow-pass 列表中，故丢弃。