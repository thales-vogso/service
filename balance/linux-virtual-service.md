# LVS


可伸缩网络服务的几种结构，它们都需要一个前端的负载调度器（或者多个进行主从备份）。我们先分析实现虚拟网络服务的主要技术，指出IP负载均衡技术是在负载调度器的实现技术中效率最高的。在已有的IP负载均衡技术中，主要有通过网络地址转换（Network Address Translation）将一组服务器构成一个高性能的、高可用的虚拟服务器，我们称之为VS/NAT技术（Virtual Server via Network Address Translation）。在分析VS/NAT的缺点和网络服务的非对称性的基础上，我们提出了通过IP隧道实现虚拟服务器的方法VS/TUN （Virtual Server via IP Tunneling），和通过直接路由实现虚拟服务器的方法VS/DR（Virtual Server via Direct Routing），它们可以极大地提高系统的伸缩性。VS/NAT、VS/TUN和VS/DR技术是LVS集群中实现的三种IP负载均衡技术。