# 实验 001：Soft-RoCE 环境确认与协议研究起点

- 日期：2026-11-03
- 主题：两台 UTM 虚拟机互联后的 Soft-RoCE 协议研究准备
- 状态：记录环境，待在虚拟机中补充命令输出

## 研究问题

本实验不以“跑通一个 ping-pong 程序”为终点，而是建立后续协议分析的基线：

1. 当前 RDMA 设备是硬件 RNIC 还是 Soft-RoCE/RXE？
2. 一个 RC 请求经过哪些 QP 状态和 RXE 处理函数？
3. 丢包、RNR、重试和超时分别在哪里被检测和完成？
4. 哪些行为可以用两个节点和软件故障注入稳定复现？

## 拓扑

```text
UTM VM A                         UTM VM B
Soft-RoCE (rxe)  <---- IP ---->  Soft-RoCE (rxe)
       eth0                         eth0
```

待补充：两台虚拟机的系统版本、内核版本、rdma-core 版本、网卡名称、IP、MTU 和 GID。

## 环境确认命令

在两台机器分别执行并保存输出：

```bash
uname -a
rdma link
rdma system show
ibv_devices
ibv_devinfo
ibdev2netdev
rdma resource show
```

如果尚未创建 RXE 设备：

```bash
sudo rdma link add rxe0 type rxe netdev eth0
rdma link
```

## 第一组基线实验

先使用最小 RC 示例确认控制面与数据面：

```bash
ibv_rc_pingpong <peer-ip>
```

然后使用 perftest 记录不同消息大小的基线：

```bash
ib_write_bw <peer-ip>
ib_read_bw <peer-ip>
ib_send_bw <peer-ip>
ib_send_lat <peer-ip>
```

每次实验记录：

- 命令行参数
- 本地/远端节点
- 消息大小和迭代次数
- 带宽、平均延迟、尾延迟（如果工具提供）
- CPU 使用率
- QP 类型、MTU、GID index
- 是否出现 RNR、重试或 CQ 错误

## 协议观察计划

### 正常路径

```text
QP RESET -> INIT -> RTR -> RTS
  -> post_recv / post_send
  -> RXE 解析包
  -> 校验 PSN、权限和长度
  -> 投递完成到 CQ
```

后续需要对照 Linux 源码确认每一步的实际函数和状态字段，避免把抽象 verbs 语义误写成线格式协议。

### 异常路径优先级

1. 接收端没有预先投递 Recv WQE，观察 RNR NAK、重试和最终错误。
2. 丢弃数据包，观察 requester 的 retry、timeout 和 QP error。
3. 发送重复或过期 PSN，观察 RXE 的响应和状态变化。
4. 远端内存地址、长度或 rkey 不合法，观察 remote access error。
5. CQ 不及时消费，观察 CQ overflow 和错误传播。

## 待补充证据

- [ ] 两台 VM 的完整环境输出
- [ ] 一次成功 RC Write 的抓包和时序图
- [ ] RXE QP 状态机源码定位
- [ ] 一个可重复的 RNR 实验
- [ ] 一个可重复的超时/重传实验
- [ ] 第一个 conformance test

## 结论

当前仓库的第一阶段目标是建立“现象—源码—协议语义—测试”闭环。后续每个实验都应提交对应的命令、原始输出、抓包或日志，避免只留下结论。
