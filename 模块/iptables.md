`iptables `不是真正的防火墙，可以理解为客户端代理

`netfilter`才是防火墙真正的安全框架，位于内核空间

`netfilter/iptables`组成Linux平台的包过滤防火墙，完成了封包过滤、封包重定向和网络地址转换

规则：源地址，目的地址，传输协议(TCP、UDP、ICMP)，服务类型(HTTP、FTP、SMTP)

当数据包与规则匹配时，`iptables`根据规则定义的方法来处理这些数据包：放行(accept)，拒绝(reject)，丢弃(drop)等。配置防火墙主要工作就是添加、修改和删除这些规则。

`iptables`包含4张表

1.`raw`：用于配置数据包，`raw`中的数据包不会被系统跟踪，高级功能，如网址过滤

2.`filter`：用于存放所有与防火墙相关操作的默认表，定义允许或者不允许（只能在`Input,Forward,Output`三个链上)

3.`nat`：用于网络地址转换（只能在`PreRouting,PostRouting,Output`三个链上)

4.`mangle`:用于对特定数据包的修改，修改报文原数据

`iptables`包含5个链

1.`PreRouting`:用于目标地址转换 (DNAT)（存在于`raw,mangle,nat`表中）

2.`Input`：处理输入数据包（存在于`filter,mangle`表中）

3.`Forward`：处理数据转发数据包（存在于`filter,mangle`表中）

4.`Output`：处理输出数据包（存在于`raw,filter,nat,mangle`表中）

5.`PostRouting`:用于源地址转换(SNAT)（存在于`mangle,nat`表中）

数据包在经过一个链时，会将当前链的所有规则都匹配一遍，并且一条条进行匹配，相同功能的规则会汇聚在一张表中，不同的表在对应的链中可能处于不同的执行优先级。

处理动作

1.`	ACCEPT`:允许数据包通过

2.`DROP`:直接丢弃数据包，不做回应

3.`REJECT`:拒绝数据包通过，必要时会给数据发送端一个响应的信息，客户端刚请求就会收到拒绝信息

4.`SNAT`:源地址转换，解决内网用户用同一个公网地址的问题

5.`MASQUERADE`:是SNAT的一种特殊形式，适用于动态的、临时会变的ip上

6.`DNAT`:目标地址转换

7.`REDIRECT`:在本机做端口映射

8.`LOG`:在/var/log/messages文件中记录日志信息，然后将数据包传递给下一条规则，除了记录日志不做任何处理



```
iptables
-t, --table table 对指定的表 table 进行操作， table 必须是 raw， nat，filter，mangle 中的一个。如果不指定此选项，默认的是 filter 表。

# 通用匹配：源地址目标地址的匹配
-p：指定要匹配的数据包协议类型；
-s, --source [!] address[/mask] ：把指定的一个／一组地址作为源地址，按此规则进行过滤。当后面没有 mask 时，address 是一个地址，比如：192.168.1.1；当 mask 指定时，可以表示一组范围内的地址，比如：192.168.1.0/255.255.255.0。
-d, --destination [!] address[/mask] ：地址格式同上，但这里是指定地址为目的地址，按此进行过滤。
-i, --in-interface [!] <网络接口name> ：指定数据包的来自来自网络接口，比如最常见的 eth0 。注意：它只对 INPUT，FORWARD，PREROUTING 这三个链起作用。如果没有指定此选项， 说明可以来自任何一个网络接口。同前面类似，"!" 表示取反。
-o, --out-interface [!] <网络接口name> ：指定数据包出去的网络接口。只对 OUTPUT，FORWARD，POSTROUTING 三个链起作用。

# 查看管理命令
-L, --list [chain] 列出链 chain 上面的所有规则，如果没有指定链，列出表上所有链的所有规则。

# 规则管理命令
-A, --append chain rule-specification 在指定链 chain 的末尾插入指定的规则，也就是说，这条规则会被放到最后，最后才会被执行。规则是由后面的匹配来指定。
-I, --insert chain [rulenum] rule-specification 在链 chain 中的指定位置插入一条或多条规则。如果指定的规则号是1，则在链的头部插入。这也是默认的情况，如果没有指定规则号。
-D, --delete chain rule-specification -D, --delete chain rulenum 在指定的链 chain 中删除一个或多个指定规则。
-R num：Replays替换/修改第几条规则

# 链管理命令（这都是立即生效的）
-P, --policy chain target ：为指定的链 chain 设置策略 target。注意，只有内置的链才允许有策略，用户自定义的是不允许的。
-F, --flush [chain] 清空指定链 chain 上面的所有规则。如果没有指定链，清空该表上所有链的所有规则。
-N, --new-chain chain 用指定的名字创建一个新的链。
-X, --delete-chain [chain] ：删除指定的链，这个链必须没有被其它任何规则引用，而且这条上必须没有任何规则。如果没有指定链名，则会删除该表中所有非内置的链。
-E, --rename-chain old-chain new-chain ：用指定的新名字去重命名指定的链。这并不会对链内部照成任何影响。
-Z, --zero [chain] ：把指定链，或者表中的所有链上的所有计数器清零。

-j, --jump target <指定目标> ：即满足某条件时该执行什么样的动作。target 可以是内置的目标，比如 ACCEPT，也可以是用户自定义的链。
-h：显示帮助信息；
```















