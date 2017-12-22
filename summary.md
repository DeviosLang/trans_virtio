# Virtual I/O Device(VIRTIO) Version 1.0
### 摘要
  这个文档描述了virit设备的规范。这些设备是虚拟环境中的设备，在虚拟机中这些设备看起来像物理设备，这篇文档主要描述这些。允许客户机使用标准驱动和发现机制。
  
  
VIRTIO和这个规范的目的是，虚拟环境和客户机需要一个简单的，有效的，标准的和可扩展的虚拟设备，而不是一个环境一种或者一个os一个机制。


现状

这个文档是最新的修订版本，经过的Virtio Device TC如上面日期所示。改进的列表也在上面的列表中。检出最新版本的，注意上面本文档最新的版本。任何别的版本和技术工作通过技术委员会（ Technical Committee,TC）产出并放在[这里](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=virtio#technical)。

TC成员会发送这个文档的comments到TC的mail list。别人只能通过[TC网站的](https://www.oasis-open.org/committees/virtio/)[‘发送一个comment’按钮](https://www.oasis-open.org/committees/comments/index.php?wg_abbrev=virtio)把comments发给TC。

对于任何公布的专利的信息，将会是实施在这个规范中，其他任何专利的授权条款，请参考[TC官网](https://www.oasis-open.org/committees/virtio/ipr.php)的Intellectual Property Rights这一节。

引用格式:

当引用这个规范的时候，需要使用下面的引用格式:

[VIRTIO-v1.0]
Virtual I/O Device (VIRTIO) Version 1.0. Edited by Rusty Russell, Michael S. Tsirkin, Cornelia
Huck, and Pawel Moll. 03 March 2016. OASIS Committee Specification 04. http:
//docs.oasis-open.org/virtio/virtio/v1.0/cs04/virtio-v1.0-cs04.html. Latest version: http://docs.
oasis-open.org/virtio/virtio/v1.0/virtio-v1.0.html.

##### [翻译来源](http://docs.oasis-open.org/virtio/virtio/v1.0/virtio-v1.0.pdf)



