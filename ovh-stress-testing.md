---
title: OVH专项压力测试要点
description: OVH防DDoS基础设施的测试方法选择与效果评估
---

# OVH专项压力测试要点

OVH的防DDoS体系分为三层：边缘清洗(vEdge)、网络层过滤、应用层防护。不同层级需要不同的测试方法。

## OVH防御架构

| 层级 | 机制 | 测试目标 |
|------|------|---------|
| vEdge边缘 | 流量清洗，基于签名+行为 | L4泛洪是否被清洗掉 |
| 网络层 | SYN cookie、连接限制 | TCP握手是否被拦截 |
| 应用层 | WAF规则、速率限制 | L7请求是否被识别 |

## 有效测试方法

### Layer4
- **SYN Flood**：OVH的SYN cookie响应很快，纯SYN几乎无效。需要配合ACK-ACK或混合TCP方法
- **UDP Rand**：OVH边缘对UDP泛洪有专门的清洗路径，单源几乎打不穿
- **TCP Connect（ATCP/PTCP）**：建立真实连接消耗OVH的连接表，比纯SYN有效得多

### Layer7
- **HTTP Mix**：混合GET/POST/HEAD请求，绕过单一pattern的WAF规则
- **HTTPS Flood**：TLS握手消耗CPU，OVH的TLS卸载节点是潜在瓶颈
- **Slow HTTP**：慢速请求占连接，测试OVH的连接超时配置

## 常见误区

1. **纯SYN打OVH = 无效**：SYN cookie机制让纯SYN Flood被无损吸收
2. **单源UDP = 无效**：vEdge清洗能力远超单源带宽
3. **只看Gbps不看状态码**：OVV报告"50Gbps被清洗"，源站实际毫发无损

## 评估指标

测试OVH时应该关注：
- 响应延迟从基线到 degraded 的拐点（ms）
- 502/503出现的负载阈值（PPS/RPS）
- 测试停止后的恢复时间（秒）

有效的OVH测试不是"打不打得下"，而是"在什么负载下开始降级"。

---

*相关阅读: [DDoS压力测试完整指南](https://zerodawnsec.com/ddos-stress-test-guide.html)*
