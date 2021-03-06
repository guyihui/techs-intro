# Storage(Ceph)
## Ceph简介
Ceph FS是一个维持POSIX兼容的同时还集成了复制、容错的文件系统,它使用Ceph存储集群来存储数据。Ceph始于加州大学圣克鲁兹分校的Sage Weil的博士学位课题，并于2010年3月起，成为了Linux系统进军可扩展存储领域不可或缺的核心系统。目前，Ceph在红帽推动下已扩展到支持容器和文件存储，对对象、块和文件存储同时具备很好的系统支持能力，是可处理PB级数据的统一存储系统  
Ceph的生态系统主要划分为四部分：客户端（使用者）、元数据服务器（管理文件系统的命名空间）、对象存储集群（以对象方式存储数据和元数据）以及集群监控（实现监控）。
## Ceph的主要技术特性
* CRUSH算法  
Ceph采用一种名为可扩展哈希受控复制（CRUSH）算法的伪随机映射，使得数据映射到存储设备不依赖于任何元数据，而是依赖于伪随机映射函数。它最小化了存储的负荷，以及简化了数据的分布和查询。  
* 容错特性及错误恢复
Ceph集成了容错特性来防止单点故障，并假定大规模（PB级）的存储故障是一种常态，而非异常。Ceph的设计没有假定特定的工作负荷，而是包含了可变的分布式工作负荷的适应能力，从而保证了在不同负荷下都能发挥出高性能。Ceph的元数据服务器采用将元数据缓存在短期的日志中的方式，保证可以立即对客户端提供最新的元数据服务，同时日志也能确保元数据的安全性，在发生故障时即使恢复。  
* RADOS  
Ceph采用了RADOS的方式，由Ceph对象存储来提供从“对象”到“块”的映射，而不是由客户端的文件系统层来提供。并且因为Ceph客户端使用CRUSH，不知道磁盘上文件的块映射信息，所以底层的存储设备可以安全的管理从对象到块的映射。这使得即使设备被发现失效，也能依靠节点复制数据。这种失效恢复的分布式机制大幅提高了存储系统的可扩展性。
* 动态增加节点  
Ceph采用了元数据动态子树分区，可以增加元数据服务器和存储节点，增强了容量和性能的可扩展性
## Ceph支持的存储类型
### 块存储：可以直接以逻辑卷的方式对外提供块设备服务
* 典型设备：磁盘阵列，硬盘
* 优点：
    - 通过Raid与LVM等手段，对数据提供了保护
    - 多块廉价的硬盘组合起来，提高容量
    - 多块磁盘组合出来的逻辑盘，提升读写效率
* 缺点：
    - 造价成本高
    - 主机之间无法共享数据
### 文件存储：通过集群的对象存储网关，对外同时提供swift和S3风格的对象存储服务
* 典型设备：FTP、NFS服务器
* 优点：
    - 造价成本低
    - 支持共享数据
* 缺点：
    - 读写、传输速率慢
### 对象存储：提供可供挂载的类POSIX文件存储服务
* 典型设备：内置大容量硬盘的分布式服务器如swift、S3
* 优点：
    - 读写速率快
    - 支持共享数据
## Ceph的优势  
* 采用CRUSH算法，减小了存储符合，提高了整体性能
* 支持数千节点，且元数据服务器支持动态增加节点，可扩展性强
* 容错性强，可预防单点故障
* 可处理PB级数据
## Ceph的缺陷
* Ceph是开源社区，而不是成熟的产品
* Ceph在国内大量部署还只是针对传统软件堆栈,而不是云计算
## 我的评论  
Ceph从2004在一个社区里出现原型，到现在已经发展成可运行大量工作负载的真实系统，我觉得一方面是因为Ceph运用了许多先进的设计理念，如CRUSH算法、元数据动态子树分区等，这些设计理念是Ceph得以成功的基础。另一方面，Ceph的与时俱进也是它能够发展得越来越好必不可少的一个因素。目前，Ceph在国内与国外最大区别是，在国外既能适应新型的云计算的软件堆栈，也能适应传统的应用软件堆栈，而在国内大部分的部署还是针对传统的软件堆栈。Ceph作为一个统一的存储平台，不仅要统一存储类型（块、文件、对象存储），也应给统一起传统模式稳态的应用和新型模式敏态的应用。
</br></br>
# 网络
## 宽带
### 主流网络运营商
* 中国电信宽带
* 中国移动铁通宽带
* 中国联通网通宽带
* 长城宽带
### 宽带的性能指标：带宽
* 带宽的实际含义:在给定时间等条件下流过特定区域的最大数据位数
* 带宽的常用单位是Mbps，即兆比每秒
* ADSL宽带速率一般为1Mbps、2Mbps、4Mbps、6Mbps
、8Mbps</br>光纤宽带速率一般为10Mbps、20Mbps、50Mbps、100Mbps、1Gbps
### 不同种类宽带的优缺点
| 宽带种类 | 特点 | 优点 | 缺点 |
| :------: | :------: | :------: | :------: |
| ADSL宽带 | 以电话为信号媒介，下行速度和上行速度不同 | 每个用户独享分配给自己的带宽，比较稳定 | 上网速度受电信部门的总出口带宽的影响，速度一般不高 |
| 社区宽带 | 通信光纤直接铺设到小区的中心机房，小区的各用户组成局域网，最后通过局域网共享主光纤实现上网 | 当整个小区上网的用户比较少，或在线用户比较少时，网速比较高 | 随着上网用户的增多，或上网高峰期，网速下降的同谋十分明显 |
| 光纤宽带 | 光纤网采用光导纤维作传输介质 | 传输距离长，传输率高，可达数千兆bps，抗干扰性强，不会受到电子监听设备的监听 | 价格较高，且需要高水平的安装技术，所以现在尚未普及 |

## 路由器
### 路由器（Router），是连接因特网中各局域网、广域网的设备，它会根据信道的情况自动选择和设定路由，以最佳路径，按前后顺序发送信号。
### 路由器常见性能指标
1. 吞吐量
    * 定义：路由器的包转发能力，一般分为整机和端口吞吐量
    * 单位：Mpps，即每秒可以转发多少百万个数据包
    * 低端的路由器包转发率只有几K到几十Kpps，而高端路由器则能达到几十Mpps（百万包每秒）甚至上百Mpps
2. 路由表能力
    * 定义：路由表内所容纳路由表项数量的极限
    * 单位：条数
    * 高速核心路由器应该能够支持至少25万条路由，平均每个目的地址至少提供2条路径，系统必须支持至少25个BGP对等以及至少50个IGP邻居
3. 背板能力
    * 定义：背板指输入与输出端口间的物理通路
    * 传统的路由器采用的是共享背板的结构，高性能路由器一般采用的是交换式结构。背板能力决定了路由器的吞吐量，背板能力只能在设计中体现，一般无法测试
4. 丢包率
    * 定义：核心路由器在稳定的持续负荷下，由于资源缺少而不能转发的数据包在应该转发的数据包中所占的比例
    * 单位：百分比
    * 计算方法:[(输入报文-输出报文)/输入报文]*100%
    * 丢包率与数据包长度以及包发送频率相关，通常,千兆网卡在流量大于200Mbps时,丢包率小于万分之五;百兆网卡在流量大于60Mbps时,丢包率小于万分之一
5. 时延
    * 定义：数据包第一个比特进入路由器到最后一个比特从核心路由器输出的时间间隔
    * 单位：毫秒，在测试中通常使用测试仪表发出测试包到收到数据包的时间间隔
    * 时延对网络性能影响较大， 作为高速路由器，在最差情况下， 要求对1518字节及以下的IP包时延均都小于1ms
6. 背靠背帧数
    * 定义：以最小帧间隔发送最多数据包不引起丢包时的数据包数量，用于测试核心路由器缓存能力
    * 单位：帧数
    * 测试方法：以最大速率向DUT传送特定数量的测试帧，并统计DUT转发的帧数
    * 指标越大越好，没有上限
### 主流路由器品牌
* 华为
* TP-LINK
* H3C
* 小米
* D-LINK
* Tenda
* 华硕路由器
* NETGEAR
* BUFFALO
* 思科

### 常用路由器分类
| 路由器种类 | 特点 | 优点 | 缺点 |
| :------: | :------: | :------: | :------: |
| 模块化路由器 | 该路由器的接口类型及部分扩展功能是可以根据用户的实际需求来配置的路由器，用户可以根据所要连接的网络类型来选择相应的模块| 当路由器某一部分故障，不影响其他部分正常工作。用户根据实际需求采购模块，节约了成本，因为框架一般很便宜 | 需要自己购买和安装扩展模块，需要一定的动手能力，比较麻烦 |
| 非模块化路由器 | 主要用于连接家庭或ISP内的小型企业客户。它不仅提供SLIP或PPP连接，还支持诸如PPTP和IPSec等虚拟私有网络协议 | 支持许多异构和高速端口，并在各个端口能够运行多种协议 | 一个端口的损坏需将设备上所有业务中断，设备下线更换，不支持功能扩展 |
| 无线路由器 | 带有无线覆盖功能的路由器，支持专线xdsl/ cable，动态xdsl，pptp等接入方式 | 将多种设备合而为一，亦比较适合于初次建网的用户，其集成化的功能可以使用户只用一个设备而满足所有的有线和无线网络需求 | 传输距离较短，一般不超过50米 |
| 无线网络路由器 | 是一种用来连接有线和无线网络的通讯设备，它可以通过Wi-Fi技术收发无线信号来与个人数码助理和笔记本等设备通讯 | 可以在不设电缆的情况下，方便地建立一个电脑网络 | 一般在户外通过无线网络进行数据传输时，它的速度可能会受到天气的影响 |

## 交换机
### 交换机（switch）是一种在通信系统中完成信息交换功能的设备，它应用在数据链路层。交换机有多个端口，每个端口都具有桥接功能，可以连接一个局域网或一台高性能服务器或工作站。实际上，交换机有时被称为多端口网桥。
### 交换机常见性能指标
1. 背板带宽
    * 定义：也叫背板容量，是交换机接口处理器或接口卡和数据总线间所能吞吐的最大数据量
    * 单位：Gbps
    * 计算公式：背板带宽=2 * 端口数 * 相应端口速率
    * 一般的交换机的背板带宽从几Gbps到上百Gbps不等
2. 交换容量
    * 定义：内核CPU与总线的传输容量，一般比背板带宽小
    * 单位：Gbps
    * 计算公式：交换容量=缓存位宽 * 缓存总线频率
3. 包转发率：
    * 定义：衡量网络设备转发数据能力的标准
    * 单位：pps
    * 计算：数据包packet和字节byte对应关系为1packet=64byte，网络中1个数据包最小包含64字节，这64个字节指的是单纯的数据，还要给每个数据包加上8byte的帧头和12byte的帧间隙，因此每传输一个数据包就需要传输64+8+12共84byte。一个1000Mbps的线速端口的最大包转发率为1,000,000,000bps/（84*8bit）≈1,488,095pps=1.488Mpps
    * 万兆线速端口包转发率14.88Mpps</br>千兆线速端口包转发率1.488Mpps</br>百兆线速端口包转发率0.1488Mpps

### 交换机分类
#### 二层交换机
* 二层交换技术的发展比较成熟，二层交换机属数据链路层设备，可以识别数据包中的MAC地址信息，根据MAC地址进行转发，并将这些MAC地址与对应的端口记录在自己内部的一个地址表中
* 二层交换机的特性：
    * 学习端口连接的机器的MAC地址，写入地址表，地址表的大小（一般两种表示方式：一为BEFFER RAM，一为MAC表项数值），地址表大小影响交换机的接入容量
    * 一般都含有专门用于处理数据包转发的ASIC（Application specific Integrated Circuit，专用集成电路）芯片，因此转发速度可以做到非常快
#### 三层交换机
* 三层交换机就是具有部分路由器功能的交换机，三层交换机的最重要目的是加快大型局域网内部的数据交换，所具有的路由功能也是为这目的服务的，能够做到一次路由，多次转发。三层交换技术是在网络模型中的第三层实现了数据包的高速转发，既可实现网络路由功能，又可根据不同网络状况做到最优网络性能
* 三层交换机的特性：
    * 高可扩充性：三层交换机在连接多个子网时，子网只是与第三层交换模块建立逻辑连接，不像传统外接路由器那样需要增加端口，从而保护了用户对校园网、城域教育网的投资。并满足学校3~5年网络应用快速增长的需要。
    * 高性价比：三层交换机具有连接大型网络的能力，功能基本上可以取代某些传统路由器，但是价格却接近二层交换机。一台百兆三层交换机的价格只有几万元，与高端的二层交换机的价格差不多。
    * 内置安全机制：三层交换机可以与普通路由器一样，具有访问列表的功能，可以实现不同VLAN间的单向或双向通讯。如果在访问列表中进行设置，可以限制用户访问特定的IP地址，这样学校就可以禁止学生访问不健康的站点，也可防止非法外部用户访问内部网络资源
    * 多媒体传输：教育网经常需要传输多媒体信息，这是教育网的一个特色。三层交换机具有QoS（服务质量）的控制功能，可以给不同的应用程序分配不同的带宽。例如，在校园网、城域教育网中传输视频流时，就可以专门为视频传输预留一定量的专用带宽，相当于在网络中开辟了专用通道，其他的应用程序不能占用这些预留的带宽，因此能够保证视频流传输的稳定性。
    * 计费功能：在高校校园网及有些地区的城域教育网中，很可能有计费的需求，因为三层交换机可以识别数据包中的IP地址信息，因此可以统计网络中计算机的数据流量，可以按流量计费，也可以统计计算机连接在网络上的时间，按时间进行计费。而普通的二层交换机就难以同时做到这两点。
#### 四层交换机
* 第四层交换机是基于传输层数据包的交换过程的，是一类基于TCP/IP协议应用层的用户应用交换需求的新型局域网交换机，支持TCP/UDP第四层以下的所有协议
* 四层交换机特性
    * 包过滤/安全控制：第四层交换区别于第三层交换的主要不同之处，就是在于这种过滤能力是在ASIC专用高速芯片中实现的，从而使这种安全过滤控制机制可以全线速地进行，极大地提高了包过滤速率。
    * 服务质量：在网络系统的层次结构中，TCP/UDP第四层信息，往往用于建立应用级通信优先权限。第四层交换机允许用基于目的地址、目的端口号（应用服务）的组合来区分优先级，于是紧急应用就可以获得网络的高级别服务。
    * 服务器负载均衡：在相似服务内容的多台服务器间提供平衡流量负载支持时，第四层信息是至关重要的。第四层交换机所支持的服务器负载均衡方式，是将附加有负载均衡服务的IP地址，通过不同的物理服务器组成一个集，共同提供相同的服务，并将其定义为一个单独的虚拟服务器。这个虚拟服务器是一个有单独IP地址的逻辑服务器，用户数据流只需指向虚拟服务器的IP地址，而不直接和物理服务器的真实IP地址进行通信。只有通过交换机执行的网络地址转换（NAT）后，未被注册IP地址的服务器才能获得被访问的能力。这种定义虚拟服务器的另一好处是，在隐藏服务器的实际IP地址后，可以有效地防止非授权访问。
    * 主机备用连接：主机备用连接为端口设备提供了冗余连接，从而在交换机发生故障时有效保护系统，这种服务允许定义主备交换机，同虚拟服务器定义一样，它们有相同的配置参数。由于第四层交换机共享相同的MAC地址，备份交换机接收和主单元全部一样的数据。这使得备份交换机能够监视主交换机服务的通信内容。主交换机持续地通知备份交换机第四层的有关数据、MAC数据以及它的电源状况。主交换机失败时，备份交换机就会自动接管，不会中断对话或连接。

## 评价
对于各种网络设备（包括宽带、路由器、交换机）的选择，需要根据实际的应用场景来决定适合的组合。就宽带而言，光纤以其高速率、稳定性、安全性无疑是最优的选择，而近年来上海一直在推进光纤入户，所以以前光纤一直存在的价格昂贵的缺点现在也可以克服，光纤几乎可以说是当下宽带的首选。交换机的作用主要是实现大家通过一根网线上网，一般情况下大家上网都是分别拨号的，都在使用自己的宽带，所以哪怕有人在下载其他东西，也自己上网也没有任何影响的，而且所有使用同一条交换机的电脑都在同一局域网内的。路由器比交换机多了虚拟拨号的功能，可以共用一个宽带账号，所以对于大家上网是会相互影响的，也就是说一台电脑在下载资料，其他电脑就会变得更卡的，网速会慢很多。对于家用而言，共享同一个宽带账号是比较普遍的选择，所以大多数人会选择使用路由器，而在大学寝室这个场景下，大家每个人都有属于自己的宽带账号，这时使用交换机上网可以保证上网不互相影响，也是不错的选择，当然交换机和路由器也可以搭配使用，对于企业而言可以将宽带线连接至路由器，再把路由器的输出宽带线连到交换机，通过交换机分开让数十台电脑使用。对于路由器和交换机各自不同的种类也需要根据使用场景需求的不同选购不同的种类。
</br></br>
# xPU
## **GPU** - *Graphics Processing Unit*
### **图形处理器**（*GPU*），又称显示核心、视觉处理器、显示芯片，是一种专门在个人电脑、工作站、游戏机和一些移动设备（如平板电脑、智能手机等）上进行图像运算工作的微处理器。GPU的构成相对简单，有数量众多的计算单元和超长的流水线，特别适合处理大量的类型统一的数据。但有一点需要强调，虽然GPU是为了图像处理而生的，但是我们通过前面的介绍可以发现，它在结构上并没有专门为图像服务的部件，只是对CPU的结构进行了优化与调整，所以现在GPU不仅可以在图像处理领域大显身手，它还被用来科学计算、密码破解、数值分析，海量数据处理（排序，Map-Reduce等），金融分析等需要大规模并行计算的领域。所以GPU也可以认为是一种较 **通用** 的芯片。
### 供应商
* Intel
* NVIDIA
* AMD ( ATI )
* Matrox
* 3dfx、SiS、VIA
### 主要分类
* 独立显卡
* 集成绘图处理器
### 主要指标
* 频率
* CUDA核数量
* 存储器大小
* 计算性能峰值
* 存储器带宽（一个时钟周期内传输的数据位数）
### GPU具有如下特点：
1. 提供了多核并行计算的基础结构，且核心数非常多，可以支撑大量并行计算
2. 拥有更高的访存速度
3. 更高的浮点运算能力</br>

随着人们的计算需求越来越专业化，人们希望有芯片可以更加符合自己的专业需求，这时，便产生了ASIC（专用集成电路）的概念。ASIC是指依产品需求不同而定制化的特殊规格集成电路，由特定使用者要求和特定电子系统的需要而设计、制造。当然这概念不用记，简单来说就是定制化芯片。
## **TPU** - *Tensor Processing Unit* &ensp;-&ensp; Google
### 张量处理器（TPU），是谷歌专门为加速深层神经网络运算能力而研发的一款芯片，其实也是一款ASIC。
### 原来很多的机器学习以及图像处理算法大部分都跑在GPU与FPGA（半定制化芯片）上面，但这两种芯片都还是一种通用性芯片，所以在效能与功耗上还是不能更紧密的适配机器学习算法，而且Google一直坚信伟大的软件将在伟大的硬件的帮助下更加大放异彩，所以Google便想，我们可不可以做出一款专用机机器学习算法的专用芯片，TPU便诞生了。
### 工艺指标
* TPU ASIC采用了28nm工艺制造，主频700MHz，功耗40W。
* 为了尽快把TPU部署到现有的服务器中，Google选择把这个芯片打包成外部扩展加速器，然后插到SATA硬盘插槽里使用。所以TPU通过PCIe Gen3 x16总线与主机相连，也就是说12.5GB/s的有效带宽。
### 技术
*  神经网络量化</br>

    通常而言，神经网络的预测不需要32位或16浮点计算精度，通过一些方法，可以用8位整数对神经网络进行预测，并保持适当的准确度。所谓量化，就是一种使用8位整数来近似预设的最小值和最大值之间任意数值的优化技术。
    * *降低神经网络预测成本*
    * *减少内存使用*：在Inception中应用量化之后，这个图像识别模型能从91MB压缩到23MB，成功瘦身四分之三。
    * *减小了TPU的硬件尺寸和功耗*：一个TPU钟包含65,536个8位整数乘法器。云环境中使用的主流GPU，通常包含数千个32位浮点乘法器。只要能用8位满足精度需求，就能带来25倍以上的性能提升。

* TPU指令集 & CISC</br>

    可编程性是TPU的另一个重要设计目标。TPU不是设计用来运行某一种神经网络，而是要能加速许多不同类型的模型。大多数当代CPU都采用了精简指令集(RISC)。但Google选择复杂指令集(CISC)作为TPU指令集的基础，这一指令集侧重于运行更复杂的任务。
    * TPU包括以下计算资源：
        * 矩阵乘法单元(MUX)：65,536个8位乘法和加法单元，运行矩阵计算
        * 统一缓冲(UB)：作为寄存器工作的24MB容量SRAM
        * 激活单元(AU)：硬件连接的激活函数
    
        为了控制MUX、UB和AU进行计算，Google定义了十几个专门为神经网络推理而设计的高级指令。简而言之，TPU设计封装了神经网络计算的本质，可以针对各种神经网络模型进行编程。为了编程，Google还创建了一个编译器和软件栈，将来自TensorFlow图的API调用，转化成TPU指令。

* 矩阵乘法单元的并行计算</br>

    典型的RISC处理器提供简单计算的指令，例如乘法或加法。这些事所谓的标量（Scalar）处理器，因为它们每个指令处理单一运算，即标量运算。即使主频千兆赫兹的CPU，仍然需要很长时间才能通过一系列标量运算来完成大型矩阵的计算。</br></br>
    改进的方法就是矢量（Vector）运算，同时针对多个数据元素执行相同的操作。
    GPU的流处理器（SM）就是一种高效的向量处理器，在单个时钟周期内，可以处理数百到数千次运算。</br>
    至于TPU，Google为其设计了MXU作为矩阵处理器，可以在单个时钟周期内处理数十万次运算，也就是矩阵（Matrix）运算。

* **脉动阵列**</br>
    
    在这种结构中，数据一波一波地流过芯片，与心脏跳动供血的方式类似。CPU和GPU在每次运算中都需要从多个寄存器（register）中进行存取；而TPU的脉动阵列将多个运算逻辑单元（ALU）串联在一起，复用从一个寄存器中读取的结果。

    MXU中的权值阵列专门为矩阵乘法运算进行了优化，并不适用于通用计算。MXU的脉动阵列包含256 × 256 = 65,536个ALU，也就是说TPU每个周期可以处理65,536次8位整数的乘法和加法。TPU以700兆赫兹的功率运行，也就是说，它每秒可以运行65,536 × 700,000,000 = 46万亿次乘法和加法运算，或每秒92万亿次矩阵单元中的运算。
    
    这种基于复杂指令集计算（CISC）的矩阵运算设计，实现了出色的性能功耗比：TPU的性能功耗比，比同时期的CPU强83倍，比同时期的GPU强29倍。

* 极简&确定性的设计
    * 单用途的TPU就是一个单线程芯片，不需要考虑缓存、分支预测、多道处理等问题。
    * TPU的控制单元更小，更容易设计，面积只占了整个冲模的2%，给片上存储器和运算单元留下了更大的空间。而且，TPU的大小只有其他芯片的一半。硅片越小，成本越低，良品率也越高。
    * 使用TPU能轻易预测运行一个神经网络、得出预测，需要多长时间，能让芯片以吞吐量接近峰值的状态运行，同时严格控制延迟。
    * 以MLP0为例，在同样将延迟控制在7毫秒之内的情况下，TPU的吞吐量是CPU和GPU的15到30倍。在六种神经网络上的性能对比，在CNN1上，TPU性能最为惊人，达到了CPU的71倍

## xPU-s
据说每过18天，集成电路领域就会多出一个XPU，直到26个字母被用完。
这被戏称为AI时代的xPU版摩尔定律。
据不完全统计，已经被用掉的有：
- **A**PU -- Accelerated Processing Unit, 加速处理器，AMD公司推出加速图像处理芯片产品。
- **B**PU -- Brain Processing Unit, 地平线公司主导的嵌入式处理器架构。
- **C**PU -- Central Processing Unit 中央处理器， 目前PC core的主流产品。
- **D**PU -- Deep learning Processing Unit, 深度学习处理器，最早由国内深鉴科技提出；</br>另说有Dataflow Processing Unit 数据流处理器， Wave Computing 公司提出的AI架构；</br>Data storage Processing Unit，深圳大普微的智能固态硬盘处理器。
- **F**PU -- Floating Processing Unit 浮点计算单元，通用处理器中的浮点运算模块。
- **G**PU -- Graphics Processing Unit, 图形处理器，采用多线程SIMD架构，为图形处理而生。
- **H**PU -- Holographics Processing Unit 全息图像处理器， 微软出品的全息计算芯片与设备。
- **I**PU -- Intelligence Processing Unit， Deep Mind投资的Graphcore公司出品的AI处理器产品。
- **MP**U/**MC**U -- Microprocessor/Micro controller Unit， 微处理器/微控制器，一般用于低计算应用的RISC计算机体系架构产品，如ARM-M系列处理器。
- **N**PU -- Neural Network Processing Unit，神经网络处理器，是基于神经网络算法与加速的新型处理器总称，如中科院计算所/寒武纪公司出品的diannao系列。
- **R**PU -- Radio Processing Unit, 无线电处理器， Imagination Technologies 公司推出的集合集Wifi/蓝牙/FM/处理器为单片的处理器。
- **T**PU -- Tensor Processing Unit 张量处理器， Google 公司推出的加速人工智能算法的专用处理器。目前一代TPU面向Inference，二代面向训练。
- **V**PU -- Vector Processing Unit 矢量处理器，Intel收购的Movidius公司推出的图像处理与人工智能的专用芯片的加速计算核心。
- **W**PU -- Wearable Processing Unit， 可穿戴处理器，Ineda Systems公司推出的可穿戴片上系统产品，包含GPU/MIPS CPU等IP。
- **X**PU -- 百度与Xilinx公司在2017年Hotchips大会上发布的FPGA智能云加速，含256核。
- **Z**PU -- Zylin Processing Unit, 由挪威Zylin 公司推出的一款32位开源处理器。

## 评价
对于常规通用的 CPU 和 GPU ，由于其通用性，势必不能在人工智能领域拥有出色的表现，所以根据不同的用途以及目的，种类繁多的 xPU 例如 TPU，NPU等专一化的芯片应运而生。相信在如今人工智能的热潮中，更多的芯片将会出现，而人工智能也会进一步得以发展。
</br></br>

# Memory
### 通常意义上的memory既可以指volatile memory，即一般人们所说的**内存**，又可以指 non-volatile memory，即一般人们所说的**硬盘**。这里主要介绍的是内存。

## 内存

 内存，即易失性储存器，主要指RAM，随机存取储存器。RAM主要有以下两种
 * SRAM，静态随机存取存储器，是指这种存储器只要保持通电，里面储存的数据就可以恒常保持。SRAM是比DRAM更为昂贵，但更为快速，相对应占用面积更大。被用为为强大的微处理器的主caches，如x86系列与许多其它CPU（从8 kiB到几百万字节的量级）、寄存器、硬盘缓冲区，路由器缓冲区等。
 * DRAM，动态随机存取存储器，利用电容内存储电荷的多寡来代表一个二进制比特（bit）是1还是0。主要应用即通常意义上的“内存”。

### 以下，我们主要讨论DRAM，“内存”也代指的是DRAM。
## 内存（颗粒）种类
DRAM中，用于电脑、工作站、服务器的是SDRAM；用于移动平台的是LPDDR，即低功耗的DDR SDRAM内存；用于显卡的是GDDR，即graphic DDR内存。后两者都是基于DDR SDRAM内存的。


* SDRAM，同步动态随机存取內存，是有一个同步接口的动态随机存取內存（DRAM）。有一个同步接口，在响应控制输入前会等待一个时钟信号，这样就能和计算机的系统总线同步。时钟被用来驱动一个有限状态机，对进入的指令进行管线(Pipeline)操作。SDRAM在一个时钟周期内只传输一次数据，它是在时钟上升期进行数据传输。

* DDR SDRAM，双倍数据率同步动态随机存取存储器，DDR SDRAM 在系统时钟的上升沿和下降沿都可以进行数据传输。速度上比SDRAM快了两倍。目前市场上的电脑内存基本都是 DDR3和DDR4 内存。
   * DDR3 
   提供相较于DDR2 SDRAM更高的运行性能与更低的电压，是DDR2 SDRAM（四倍数据率同步动态随机存取存储器）的后继者（增加至八倍）。
   * DDR4 
   提供比DDR3/DDR2-SDRAM更低的供电电压以及更高的带宽，但由于电压标准、物理接口等诸多设计与DDR3-SDRAM等的不一致，因此DDR4-SDRAM与前代DDR3/DDR2/DDR等一样，不会向后兼容。

## 内存重要参数
### 内存位宽
* 内存位宽是在一个时钟周期内所能传送数据的位数，位数越大则瞬间所能传输的数据量越大，这是内存的重要参数之一。
### 内存频率
* 代表着该内存所能达到的最高工作频率。内存主频是以MHz（兆赫）为单位来计量的。内存主频越高在一定程度上代表着内存所能达到的速度越快。
### 内存带宽
* 内存的带宽决定常常说道的“内存速度”，是测量内存的吞吐量。
* 内存带宽计算公式：带宽=内存时钟频率×内存总线位数×倍增系数/8。以DDR400内存为例，它的运行频率为200MHz，数据总线位数为64bit，由于上升沿和下降沿都传输数据，因此倍增系数为2，此时带宽为：200×64×2/8＝3.2GB/s（如果是两条内存组成的双通道，那带宽则为6.4 GB/s）。
### 内存时序
* 内存时序（英语：Memory timings或RAM timings）是描述同步动态随机存取存储器（SDRAM）性能的四个参数：CL、TRCD、TRP和TRAS，单位为时钟周期。它们通常被写为四个用破折号分隔开的数字，例如7-8-8-24。这些参数指定了影响随机存取存储器速度的潜伏时间（延迟时间）。较低的数字通常意味着更快的性能。决定系统性能的最终元素是实际的延迟时间，通常以纳秒为单位。
* 举例来说，DDR3-2000内存的时钟频率是1000 MHz，其时钟周期为1 ns。基于这个1 ns的时钟，CL=7给出的绝对延迟为7 ns。而更快的DDR3-2666（时钟1333 MHz，每个周期0.75 ns）则可能用更大的CL=9，但产生的绝对延迟6.75 ns更短。

###
| 名称 | 符号 | 定义 | 
| :------: | :------: | :------: | 
| CAS潜伏时间 | CL| 发送一个列地址到内存与数据开始响应之间的周期数。这是从已经打开正确行的DRAM读取第一比特内存所需的周期数。与其他数字不同，这不是最大值，而是内存控制器和内存之间必须达成的确切数字。 |
| 行地址到列地址延迟 | TRCD | 打开一行内存并访问其中的列所需的最小时钟周期数。从DRAM的非活动行读取第一位内存的时间是TRCD + CL。 | 
| 行预充电时间 | TRP | 发出预充电命令与打开下一行之间所需的最小时钟周期数。从一个非正确打开行的DRAM读取内存第一比特的时间是TRP + TRCD + CL。 |
| 行活动时间 | TRAS | 行活动命令与发出预充电命令之间所需的最小时钟周期数。这是内部刷新行所需的时间，并与TRCD重叠。在SDRAM模块中，它只是TRCD + CL。否则，约等于TRCD + 2×CL。 | 

## 内存模组
为了要增加内存的容量和带宽，内存颗粒（chip，芯片）会利用模块结合，即我们看到的**内存条**。
### rank,面数，位宽
* CPU与内存之间的接口位宽是64bit，也就意味着CPU在一个时钟周期内会向内存发送或从内存读取64bit的数据。可是，单个内存颗粒的位宽仅有4bit、8bit或16bit，个别也有32bit的。因此，必须把多个颗粒并联起来，组成一个位宽为64bit的数据集合，才可以和CPU互连。生产商把64bit集合称为一个物理BANK（Physical BANK），也称为rank。

* “模组构成”中的“R”是“RANK”的意思。“2R”是说组成模组的RANK数（Number of ranks of memory installed）是2个。有“1R”和“2R”两种；

* “模组构成”中的“×8”是颗粒的位宽(bit width)，有×4、×8和×16三种

### 模组种类
* DIMM
双列直插式存储器模块（Dual In-line Memory Module，简称DIMM）是指一系列（DRAM）组成的模块。DIMM通常是数颗至数十颗DRAM芯片焊接安装于一块已制作好电路的印刷电路板的形式，用于个人计算机、工作站、服务器。相比SIMM两边针脚相连在一起，DIMM两边针脚是独立的。SIMM的数据总线为32-bit宽度，DIMM则是64-bit宽度。现有的内存条绝大多数都为DIMM模组。
  * UDIMM：全称Unbuffered DIMM，即无缓冲双列直插内存模块，指地址和控制信号不经缓冲器，无需做任何时序调整，直接到达DIMM上的DRAM芯片。UDIMM由于在CPU和内存之间没有任何缓存，因此同频率下延迟较小。
  * RDIMM：全称Registered DIMM，带寄存器的双列直插内存模块。RDIMM在内存条上加了一个寄存器进行传输，其位于CPU和内存颗粒之间，既减少了并行传输的距离，又保证并行传输的有效性。由于寄存器效率很高，因此相比UDIMM，RDIMM的容量和频率更容易提高
  * LRDIMM：全称Load Reduced DIMM，低负载双列直插内存模块。相比RDIMM，LRDIMM并未使用复杂寄存器，只是简单缓冲，缓冲降低了下层主板上的电力负载，但对内存性能几乎无影响。此外，LRDIMM内存将RDIMM内存上的Register芯片改为iMB（isolation Memory Buffer）内存隔离缓冲芯片，直接好处就是降低了内存总线负载，进一步提升内存支持容量。

  三者间的利弊：
   * UDIMM由于并未使用寄存器，无需缓冲，同等频率下延迟较小。此外，UDIMM的另一优点在于价格低廉。其缺点在于容量和频率较低，容量最大支持4GB，频率最大支持2133 MT/s。此外，由于UDIMM只能在Unbuffered 模式工作，不支持服务器内存满配（最大容量），无法最大程度发挥服务器性能。在应用场景上，UDIMM不仅可用于服务器领域，同样广泛运用于桌面市场。
   * 而RDIMM支持Buffered模式和高性能的Registered模式，较UDIMM更为稳定，同时支持服务器内存容量最高容量。此外，RDIMM支持更高的容量和频率，容量支持32GB，频率支持 3200 MT/s 。缺点在于由于寄存器的使用，其延迟较高，同时加大了能耗，此外，价格也比UDIMM昂贵。因此，RDIMM主要用于服务器市场。
   * LRDIMM可以说是RDIMM的替代品，其一方面降低了内存总线的负载和功耗，另一方面又提供了内存的最大支持容量，虽然其最高频率和RDIMM一样，均为3200 MT/s，但在容量上提高到64GB。并且，相比RDIMM，Dual-Rank LRDIMM内存功耗只有其50%。LRDIMM也同样运于服务器领域，但其价格，较RDIMM也更贵些。

## 内存技术

### 多通道技术
* 双通道： 双通道便是利用并联方式运作，当连接两条存储器时，总线宽度将会达到128-bit。如果是纯粹的CPU运算，使用双通道近乎没有性能增长。但对于高端游戏玩家或是绘图需求较大的用户而言，双通道技术不吝为一大帮助，因为GPU要经常访问纹理贴图数据，需要很大的存储器带宽。所以在使用双通道时，双倍的带宽可使内置绘图核心(GPU)的性能增长近一倍。
* 多通道：增加更多的并行通信通道，以增加数据发送的带宽。理论上每增加一条通道，数据发送性能相较于单通道而言会增加一倍。

### 服务器内存技术
* ECC错误校验： 在ECC技术出现之前，内存中应用最多的另外一种错误检查技术，是奇偶校验位（Parity）技术。带有“奇偶校验”的内存在每一字节（8位）外又额外增加了一位用来进行错误检测。ECC与奇偶校验不同的是，如果数据位是8位，则需要增加5位来进行ECC错误检查和纠正。数据位每增加一倍，ECC只增加一位检验位。也就是说当数据位为16位时ECC位为6位，32位时ECC位为7位，数据位为64位时ECC位为8位，依此类推。在内存中ECC能够容许错误，并可以将错误自动更正，使系统得以正常的操作，不致因错误而中断。目前ECC技术在服务器内存中广泛采用，成为几乎所有服务器上的内存标准。
* Chipkill技术： 是IBM公司为了解决目前服务器内存中ECC技术的不足而开发的，是一种新的ECC内存保护标准。我们知道ECC内存只能同时检测和纠正单一比特错误，但如果同时检测出两个以上比特的数据有错误，则一般无能为力。在现有频率上一般来说同时出现多比特错误的现象很少发生。
* Register技术： 
Register即寄存器或目录寄存器，在内存上的作用可以理解成书的目录，通过Register，当内存接到读写指令时，该技术会先检索此目录，接着进行读写操作，若所须数据在目录中则直接取用不再进行读写操作，极大提高了服务器内存工作效率。
* 热插拔技术： 指允许用户在不关闭系统，不切断电源的情况下取出和更换损坏的内存，提高服务器系统对灾难的及时恢复能力、扩展性和灵活性。

## 内存生产厂商
目前内存颗粒生产厂主要有：

现代(hynix) 也被称为海力士 

三星(SAMSUNG) 内存工艺上要领先其他内存颗粒生产厂

南亚(elixir) 曾经的华邦winbond

奇梦达(Qimonda) 德国英飞凌拆分出来的子公司，已经倒闭但是市场上还有一些存货，特别是很多显卡都是用他们的颗粒

爱必达(elpida) 日本的内存生产厂，内存颗粒性能上一般

镁光(Micron) 曾经被叫做美凯龙，世界第二大内存颗粒制造商

胜创(KINGMAX) 只在自己的内存上用的内存颗粒

力晶(Powerchip) 和爱必达合作产销内存颗粒

内存条成品上：

韩国的三星、现代、日本的金士顿、金泰克、尔必达、金邦，还有海盗船、英飞凌等都比较受欢迎，其中海盗船、英飞凌、芝奇依然是高端品牌，三星、威刚、金士顿都属廉价主流产品行列。

## 内存指标及选购
对于内存来说，选购时要考虑的指标有内存种类、内存容量、内存频率、内存时序、稳定性等。而在重要性上来说， 一般内存容量最为重要，毕竟直接决定了能跑多少台虚拟机，能容纳多少用户，决定了上限。其次是内存频率和内存时序，这两者决定了内存性能上的快慢问题。而内存的稳定性和质量，取决于内存颗粒和内存条的生产厂商、内存颗粒的等级（major、ett、utt）等因素。一般来说，只要选大厂如三星、金士顿、海盗船、镁光等一般质量上还是有保证。

在选购内存条时，关注的不仅仅是内存条本身，还要关注主板上内存插槽支持的内存类型。一般来说，服务器上的内存必须要求为ecc内存，且要根据是否为RDIMM插槽来选择内存条带不带寄存器，还要看是否支持ddr3还是ddr4.其次，主板上支持的通道数也是关键因素。通道数越多作用越大. 不过这项功能需要至少2 Ranks的条子, 而且应尽量插满通道数, 在有限的资金下, 2Rank RDIMM是最好的选择(当前为32GB). 2DPC无降速可达到32GB*16=512GB, 假如遇到2DPC降速1DPC 32GB*8=256GB应该也能满足大部分普通应用。

## 我的评论
内存相对于cpu和显卡来说，它的种类，生产商都比较多，可以说水很深。而且由于内存颗粒产能下降，价格随着水涨船高，16年8g内存条一两百块钱，到现在甚至翻了一番，去年内存条甚至被戏称为硬通货。这就给我们内存的选择上出了很大的难题。个人选择内存时，主要考虑的应该是性价比， 可能选择一些二线品牌，甚至二手内存。但服务器内存选购时，要考虑到稳定性，安全性方面。有趣的地方是，服务器内存一般来说要比普通pc内存价格要低一点，网上有些人看中这一点去买服务器内存用结果因为主板不支持翻了车。



















