虚拟机网络连接的三种方式：

1. **桥接模式（Bridge Mode）：**
   - 在桥接模式下，虚拟机的网络接口会直接桥接到宿主机所在的物理网络上。这使得虚拟机就像是物理网络中的一个独立设备一样，拥有自己的MAC地址，可以直接与局域网中的其他设备通信。虚拟机可以通过局域网路由器分配到的IP地址直接访问外部网络，而外部网络也可以直接访问虚拟机。（可能发生IP冲突）
2. **主机模式（Host-Only Mode）：**
   - 主机模式下，虚拟机只能与宿主机进行通信，而无法直接访问外部网络。虚拟机之间和虚拟机与宿主机之间可以互相通信，但是虚拟机无法直接访问局域网或互联网。这种模式常用于创建一个隔离的网络环境，例如用于开发和测试，确保虚拟机不会直接暴露在外部网络中。
3. **NAT模式（Network Address Translation Mode）：**
   - 在NAT模式下，虚拟机通过虚拟网络设备连接到物理网络，但是网络地址会被转换。宿主机会为虚拟机分配一个==私有IP地址==，然后通过==网络地址转换技术==将虚拟机的通信流量转发到物理网络上，并将返回流量发送回相应的虚拟机。这种模式类似于路由器对局域网内设备进行NAT转换，使得虚拟机可以访问外部网络，但==外部网络无法直接访问虚拟机==。（不会发生IP冲突）

虚拟机克隆：直接拷贝一份虚拟机文件，可以很方便的来构建一个集群

# Linux目录结构

