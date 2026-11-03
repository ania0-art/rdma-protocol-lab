# Conformance tests

测试目标是验证可观察的协议行为，而不是验证某个函数的内部实现。

每个测试应说明：

- 前置条件
- 操作序列
- 预期网络行为
- 预期 CQE/QP 状态
- 失败时保留的日志和抓包
- 是否适用于硬件 RNIC、RXE 或两者

初始测试清单：

- [ ] RC Send with a posted receive
- [ ] RC Send without a posted receive (RNR)
- [ ] RDMA Write with valid rkey
- [ ] RDMA Write with invalid rkey
- [ ] Duplicate PSN handling
- [ ] Retry exhaustion and QP error
- [ ] CQ overflow behavior
