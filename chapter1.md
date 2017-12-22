### 1.简介

这个文档描述了‘virtio’家族设备的规范。这些设备出现在虚拟环境中，通过设计，客户机看待这些设备看起来像物理设备，这篇文档也如此对待这些设备。这样允许客户机使用标准的设备驱动和发现机制。

VIRTIO和这个规范的目的是，虚拟环境和客户机需要一个简单的，有效的，标准的和可扩展的虚拟设备，而不是每个环境一种或者每个os一个机制。

 - **简单**:Virtio设备使用通用总线中断和DMA机制，这样对任何驱动设备开发者是最熟悉的。这些设备没有page-flipping，也没有COW机制，他们仅仅是正常的设备^[这种缺少页共享意味着设备(例如:hypervisor或者客户机)实现需要全接入到客户机内存，与不信任页面的通信(例如:客户机直接的通信)需要拷贝]。
 - **高效**:Virtio设备由ring描述符，组成输入和输出，这样干净的布局避免了缓存对于驱动和设备写到同一个cache line的影响。
 - 标准:Virtio没有假设它操作的环境，支持设备绑定到总线上。本规范中，virtio设备被部署在MMIO，I/O通道和PCI总线上^[linux将会从专门的virtio设备（这些设备被共享在不同的端口上）中分离virtio传输端口的代码]。早期的草案，设备还可以部署在其他总线上。
 - **扩展性**：virtio包括特性位，可以被客户操作系统在初始花设备时候识别。这个允许前向的或后向的能力：这些设备提供所有它知道的特性，同时驱动识别这些并且去使用。






#### 1.1 规范的引用

<span id="RFC2119">**[RFC2119]**</span>
  [Bradner S., “Key words for use in RFCs to Indicate Requirement Levels”, BCP 14,
RFC 2119, March 1997.](http://www.ietf.org/rfc/rfc2119.txt)

**[S390 PoP]**  [z/Architecture Principles of Operation, IBM Publication SA22-7832](http://publibfi.boulder.ibm.com/epubs/pdf/dz9zr009.pdf) 以及其更新的版本

**[S390 Common I/O]**  [ESA/390 Common I/O-Device and Self-Description, IBM Publication SA22-7204](http://publibfp.dhe.ibm.com/cgi-bin/bookmgr/BOOKS/dz9ar501/CCONTENTS) 以及其更新的版本

**[PCI]**  [Conventional PCI Specifications](http://www.pcisig.com/specifications/conventional/) PCI-SIG

**[PCIe**]  [PCI Express Specifications](http://www.pcisig.com/specifications/pciexpress/) PCI-SIG

**[IEEE 802]**  [IEEE Standard for Local and Metropolitan Area Networks: Overview and Architec-ture](http://standards.ieee.org/about/get/802/802.html) IEEE

**[SAM]**  [SCSI Architectural Model](http://www.t10.org/cgi-bin/ac.pl?t=f&f=sam4r05.pdf)

**[SCSI MMC]**  [SCSI Multimedia Commands](http://www.t10.org/cgi-bin/ac.pl?t=f&f=mmc6r00.pdf)

#### 1.2 非规范的引用

<span id="VirtioPCIDraft">[Virtio PCI Draft]</span>
   [Virtio PCI Draft Specification](http://ozlabs.org/~rusty/virtio-spec/virtio-0.9.5.pdf)


#### 1.3 术语

关键字MUST, MUST NOT, REQUIRED, SHALL, SHALL NOT, SHOULD, SHOULD NOT, RECOMMENDED, MAY 和 OPTIONAL在文档 [RFC2119](#RFC2119) 中有解释。

#####  <span id="历史接口">1.3.1 历史接口：术语</span>


早期的草案(例如:1.0版本以前的，可以看 [Virtio PCI Draft](#VirtioPCIDraft) )定义是相似的，但是驱动和设备的接口是不同的。为了更宽泛的实施，这个文档允许可选( OPTIONAL ) 特性，这样可以简单的切换到早期草案的接口。

特殊的设备和驱动可能(MAY)支持:
 
 - **历史接口**(Legacy Interface):特指这个接口是早期草案的的接口(在1.0以前的版本)
 - **历史设备**(Legacy Device):特指在这个版本规范之前的设备，并且实现在主机侧的历史接口上。
 - **历史驱动**(Legacy Driver):特指在这个版本规范之前的设备，并且实现在客户机侧的历史接口上。


历史设备和历史驱动与本规范不兼容。

为了简单的转译这些早起草案的接口，一个设备可能( MAY )实现:

转译设备:一个设备支持本规范也支持历史驱动。

类似的，一个驱动可能( MAY )实现:

转译驱动:一个驱动支持本规范也支持历史设备。

注意：历史接口不是必须的，例如不需要实现历史接口，除非你需要一个向后兼容的能力。

设备和驱动没有历史兼容能力，意味着没有转译的设备和驱动。

##### 1.3.2 从早期文档的转译

对于设备和驱动已经实现的历史接口的一些改变，必须支持本规范。

在这种情况下，他可能对于读者要更注意看本节中的[历史接口](#历史接口)这一节。高亮的是对于早期草案的改变。

### 1.4 结构体的规范

许多在内存中设备和驱动的结构体的布局被文档化使用的是C结构体的语法。所有结构体的声明不包括额外的填充。通常的编译器会插入一些填充在结构体重，为了强制不加填充需要使用 GNU C __attribute__((packed)) 标记的语法。

对于结构中整型数据类型，通常按照下面方式使用:

 - **u8, u16, u32, u64** 不同位长度的无符号整型。
 - **le16, le32, le64**  不同为长度的无符号的整形。小端字节序。
 - **be16, be32, be64** 不同位长度的无符号整型。大端字节序。



