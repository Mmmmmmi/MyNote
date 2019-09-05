# **计算机网络**

[**TCP/IP 五层模型**](#TCP/IP五层模型)

# **TCP/IP 五层模型**

<b><details open><summary>应用层</summary></b>

</details>

<b><details open><summary>传输层</summary></b>

<b><details open><summary>1. 端口</summary></b>
    <table>
        <tr>
            <th>应用程序</th>
            <th>FTP</th>
            <th>TELNET</th>
            <th>SMTP</th>
            <th>DNS</th>
            <th>TFTP</th>
            <th>HTTP</th>
            <th>HTTPS</th>
            <th>SNMP</th>
        </tr>
        <tr>
            <td>端口号</td>
            <td>21</td>
            <td>23</td>
            <td>25</td>
            <td>53</td>
            <td>69</td>
            <td>80</td>
            <td>443</td>
            <td>161</td>
        </tr>
    </table>
  </details>

<b><details open><summary>2. TCP (Transmission Control Protocol，传输控制协议) </summary></b>

 - **TCP (Transmission Control Protocol，传输控制协议)** 是一种面向连接的、可靠的、基于字节流的传输层通信协议，其传输的单位是报文段
 - **特征**
    - 面向连接
    - 只能点对点(一对一)通信
    - 可靠交互
    - 全双工通信
    - 面向字节流
- **TCP 如何保证可靠传输**
    - 确认和超时重传
    - 合理分片和排序
    - 流量控制
    - 拥塞控制
    - 数据校验
- **TCP 的报文结构**

    ![sig](https://github.com/Mmmmmmi/MyNote/blob/master/resource/tcpstruct.png

- **TCP 首部**

    ![sig](https://github.com/Mmmmmmi/MyNote/blob/master/resource/tcphead.png

    **16位端口号（Port Number）**，告知主机该报文段来自哪里(源端口)以及传给哪个上层协议或应用程序的(目的端口)

    **32位序号（Sequence Number）**，一次 `TCP` 通信（从 `TCP` 连接建立到断开）过程中某一个传输方向上的字节流的每个字节的编号

    **32位确认序号（Acknowledgement Number）**，用作对另一方发送来的 `TCP` 报文段的响应，其值为收到的 `TCP` 报文段的序号值加 `1`

    **4位首部长度/数据偏移（Header Length/Offset）**，标识该 `TCP` 头部有多少个 `32bit（4字节）`。因为 `4` 位最大能表示 `15`，所以 `TCP` 头部最长位 `60` 字节

    **6位状态控制码（Code，Control Flag）**，占 `6` 比特，含义如下：

    - **URG：紧急比特（Urgent）**，当 `URG＝1` 时，表明紧急指针字段有效，代表该封包为紧急封包。它告诉系统此报文段中有紧急数据，应尽快传送(相当于高优先级的数据)， 且上图中的 `Urgent Pointer` 字段也会被启用

    - **ACK：确认比特（Acknowledge）**。只有当 `ACK＝1` 时确认号字段才有效，代表这个封包为确认封包。当 `ACK＝0` 时，确认号无效

    - **PSH：（Push Function）** 若为 `1` 时，代表要求对方立即传送缓冲区内的其他对应封包，而无需等缓冲满了才送

    - **RST：复位比特（Reset）**，当 `RST＝1` 时，表明 `TCP` 连接中出现严重差错（如由于主机崩溃或其他原因），必须释放连接，然后再重新建立运输连接

    - **SYN：同步比特（Synchronous）**，SYN 置为 `1` ，就表示这是一个连接请求或连接接受报文，通常带有 `SYN` 标志的封包表示『主动』要连接到对方的意思

    - **FIN：终止比特（Final）**，用来释放一个连接。当 `FIN＝1` 时，表明此报文段的发送端的数据已发送完毕，并要求释放运输连接

    **16位窗口大小（Window Size）**，是 `TCP` 流量控制的一个手段，这里说的窗口指的是**接收通告窗口（Receiver Window，RWND）**。它告诉对方本端的 `TCP` 接收缓冲区还能容纳多少字节的数据，这样对方就可以控制发送数据的速度

    **16位校验和（TCP Checksum）**，由发送端填充，接收对端 `TCP` 报文段执行 `CRC` 算法以校验 `TCP` 报文段在传输过程中是否损坏。注意，这个校验不仅包括 `TCP` 头部，也包括数据部分。这也是 `TCP` 可靠传输的一个重要保障

    **16位紧急指针（Urgent Pointer）**，是一个正的偏移量，它和序号字段的值相加表示最后一个紧急数据的下一字节的序号。因此，确切地说，这个字段是紧急指针相对当前序号的偏移，不妨称之为紧急偏移。 `TCP` 的紧急指针是发送端向接收端发送紧急数据的方法

- **TCP的黏包问题**

   **原因**

    - `TCP` 是一个基于字节流的传输服务（ `UDP` 是基于报文的），**流**意味着 `TCP` 传输的数据是没有边界的。所以可能出现两个数据包黏在一起的情况。比如：第一次接收到 `1` 第二次接收到 `a` ，那么有可能直接读成 `1a`

   **解决方法**
    - **发送定长包**，如果每个消息的大小都是一样的，那么在接收对等方只要累计接收数据，直到数据等于一个定长的数值就将它作为一个消息
    - **包头加上包体长度**，包头是定长的 `4` 个字节，说明了包体的长度。接收对等方先接收包头长度，依据包头长度来接收包体
    - **在数据包之间设置边界**，如添加特殊符号 `\r\n` 标记。 `FTP` 协议正是这么做的。但问题在于如果数据正文中也含有 `\r\n` ，则会误判为消息的边界
    - **使用更加复杂的应用层协议**

- **TCP的流量控制**

    **概念**

    - **流量控制（flow control）** 就是让发送方的发送速率不要太快，要让接收方来得及接收

    **方法**

    - 利用可变窗口进行流量控制

- **TCP的拥塞控制**

    **概念**

    - 拥塞控制就是防止过多的数据注入到网络中，这样可以使网络中的路由器或链路不致过载

    **方法**

    - **慢开始（slow start）**
    - **拥塞避免（congestion avoidance）**
    - **快重传（fast retransmit）**
    - **快恢复（fast recovery）**

    **TCP的拥塞控制图**

    ![sig](https://github.com/Mmmmmmi/MyNote/blob/master/resource/tcpcwnd变化.png

    ![sig](https://github.com/Mmmmmmi/MyNote/blob/master/resource/tcp拥塞控制流程 .png


- **TCP三次握手建立连接**
- **TCP为什么要进行三次握手**
- **TCP四次挥手释放连接**
- **TCP为什么要进行四次挥手**



</details>

<b><details open><summary>3. UDP (User Datagram Protocol，用户数据报协议) </summary></b>

- **UDP（User Datagram Protocol，用户数据报协议）** 是 **OSI（Open System Interconnection 开放式系统互联）** 参考模型中一种无连接的传输层协议，提供面向事务的简单不可靠信息传送服务，其传输的单位是用户数据报

- **特征**
    - **无连接**，即发送数据之前不需要建立连接，因此减少了开销和发送数据之前的时延
    - **尽最大努力交付**，即不关心是否到达
    - **面向报文**，意思是 `UDP` 对应用层交下来的报文，既不合并，也不拆分，而是保留这些报文的边界，在添加首部后向下交给 `IP` 层
    - **没有拥塞控制**，因此网络出现的拥塞不会使发送方的发送速率降低
    - **支持一对一、一对多、多对多的交互通信**，即是提供广播和多播的功能
    - **首部开销小**，首部只有 8 个字节，分为四部分
- **UDP报文结构**

    ![sig](https://github.com/Mmmmmmi/MyNote/blob/master/resource/udp报文.png

    **16位源端口号**，在需要对方回信时选用，不需要时可为 `0`
    **16位目的端口号**，在终点交付报文时必须用到
    **16位长度**，`UDP` 用户数据报的长度，在只有首部的情况，其最小值为 `8`
    **16位校验和**，检测 `UDP` 用户数据报在传输过程中是否有错，有错就丢弃

- **UDP首部**

    ![sig](https://github.com/Mmmmmmi/MyNote/blob/master/resource/udp首部.png

- **UDP常用场景**
    - 域名系统（DNS）
    - 文件传送（TFTP）
    - 路由选择协议（RIP）
    - IP 地址配置（BOOTP，DHTP）
    - 网络管理（SNMP）
    - 远程文件服务（NFS）
    - IP 电话
    - 流式多媒体通信

</details>


<b><details open><summary>3. TCP 与 UDP 的区别 </summary></b>

- `TCP` 面向连接，`UDP` 是无连接的
- `TCP` 提供可靠的服务，也就是说，通过 `TCP` 连接传送的数据，无差错，不丢失，不重复，且按序到达；`UDP` 尽最大努力交付，即不保证可靠交付
- `TCP` 的逻辑通信信道是全双工的可靠信道；`UDP` 则是不可靠信道
- 每一条 `TCP` 连接只能是点到点的；`UDP` 支持一对一，一对多，多对一和多对多的交互通信
- `TCP` 面向字节流（可能出现黏包问题），实际上是 `TCP` 把数据看成一连串无结构的字节流；`UDP` 是面向报文的（不会出现黏包问题）
- `UDP` 没有拥塞控制，因此网络出现拥塞不会使源主机的发送速率降低（对实时应用很有用，如 `IP` 电话，实时视频会议等）
- `TCP` 首部开销 `20` 字节；`UDP` 的首部开销小，只有 `8` 个字节

</details>

</details>

<b><details open><summary>网络层</summary></b>

</details>

<b><details open><summary>数据链路层</summary></b>

</details>

<b><details open><summary>物理层</summary></b>

</details>