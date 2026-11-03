# RXE QP 状态机（工作笔记）

这是用于实验的工作笔记，不替代 Linux 内核文档或 IBTA 规范。

## 状态迁移主线

```text
RESET --INIT--> INIT --RTR--> RTR --RTS--> RTS
  |                         |              |
  +-------------------------+--------------+--> ERR
```

需要用具体内核版本的 `rxe_qp.c`、`rxe_req.c`、`rxe_resp.c` 和 `rxe_comp.c` 校对：

- 哪些迁移由 verbs 调用触发
- 哪些错误由 requester、responder 或 completer 设置
- QP error 后 outstanding WR 如何完成
- retry、RNR retry 和 timeout 的边界

## 研究记录格式

| 项目 | 内容 |
|---|---|
| 触发事件 | 例如收到 RNR NAK |
| 所在状态 | requester/responder/completer 当前状态 |
| 输入 | 包头、PSN、opcode、WQE、计时器 |
| 判定 | 源码中的检查条件 |
| 输出 | ACK/NAK、重试、CQE 或 QP error |
| 测试 | 可复现命令或测试名称 |
