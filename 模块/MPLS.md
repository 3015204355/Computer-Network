#### MPLS(多协议标签交换)

1. MPLS是一种IP骨干网技术，在无连接的IP网络上引入面向连接的标签交换概率，将第三层路由技术和第二层交换技术相结合，充分发挥了IP路由的灵活性和二层交换的简洁性。

2. 进行MPLS标签交换和转发的网络设备称为标签交换路由器LSR，由LSR构成的网络区域称为MPLS域。位于MPLS域边缘、连接其他网络的LSR称为边缘路由器LER。

3. IP报文进入MPLS网络时，MPLS入口的LER分析IP报文的内容并且为这些IP报文添加合适的标签，所有MPLS网络中的LSR根据标签转发数据，当该IP报文离开MPLS网络时，标签由出口LER弹出。

![image](./pictures/image-20191210102918699.png)

#### 控制平面：负责产生和维护路由信息以及标签信息。

`路由信息表RIB（Routing Information Base）`：由IP路由协议（IP Routing Protocol）生成，用于选择路由。
`标签分发协议LDP（Label Distribution Protocol）`：负责标签的分配、标签转发信息表的建立、标签交换路径的建立、拆除等工作。
`标签信息表LIB（Label Information Base）`：由标签分发协议生成，用于管理标签信息。

#### 数据平面：负责普通IP报文的转发以及带MPLS标签报文的转发。

`转发信息表FIB（Forwarding Information Base）`：从RIB提取必要的路由信息生成，负责普通IP报文的转发。
`标签转发信息表LFIB（Label Forwarding Information Base）`：简称标签转发表，由标签分发协议在LSR上建立LFIB，负责带MPLS标签报文的转发。

#### MPLS报文格式

与普通的IP报文相比增加了MPLS标签信息，MPLS的标签长度为4B，MPLS标签封装在**链路层**和**网络层**之间。

![image](./pictures/mpls.png)

标签共有4个字段：

`Label`：20bit，标签值域。

`Exp`：    3bit，用于扩展。现在通常用做CoS（Class of Service），当设备阻塞时，优先发送优先级高的报文。

`S`：        1bit，栈底标识。MPLS支持多层标签，即标签嵌套。S值为1时表明为最底层标签。

`TTL`：    8bit，和IP报文中的TTL（Time To Live）意义相同。