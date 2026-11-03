# RDMA Protocol Lab

我的 RDMA 协议学习与实验记录，重点关注 **Linux Soft-RoCE（RXE）协议状态机、异常处理和 conformance test**。

这个仓库记录可复现的过程，而不是只整理结论：每个实验尽量包含环境、操作步骤、观测结果、协议解释、失败场景和后续问题。

## 目标

- 理解 RXE 从 QP 建立到数据包完成的状态机
- 通过抓包、内核日志和 trace 分析正常路径与异常路径
- 为协议边界行为建立最小复现和 conformance test
- 将有价值的修复整理为 Linux RDMA 或 rdma-core patch
- 逐步形成公开、可复现的 RDMA 协议实验档案

## 仓库结构

```text
docs/
  experiments/       实验记录
  state-machines/    状态机和时序说明
packet-captures/     抓包文件及其说明
conformance-tests/   协议行为与异常场景测试
tools/               辅助脚本
patches/             实验性补丁和上游补丁记录
```

## 当前路线

1. 确认两台 UTM 虚拟机上的 Soft-RoCE/RXE 环境
2. 跑通 RC Send/Recv、RDMA Write 和 RDMA Read
3. 对照 RXE 源码绘制 QP 状态机与包处理路径
4. 研究 RNR、重传、超时、重复包、乱序和 CQ 错误
5. 为每个行为编写可重复测试
6. 将结果整理为 issue、patch 或 Linux RDMA 社区讨论材料

## 实验约定

- 实验日期使用 `YYYY-MM-DD`
- 记录内核、rdma-core、QEMU/UTM 和网络拓扑
- 区分观察到的事实、源码推断和待验证假设
- 性能结果不把 Soft-RoCE 直接等同于真实 RNIC 结果

## 第一篇实验

见 [`docs/experiments/2026-11-03-environment-and-first-observations.md`](docs/experiments/2026-11-03-environment-and-first-observations.md)。

## 许可证

实验记录和脚本采用 MIT License；引用的 Linux 内核或第三方代码仍以其原许可证为准。
