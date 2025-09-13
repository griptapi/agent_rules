---
trigger: always_on
alwaysApply: true
tags:
  - 预防性编程
  - 编程原则
  - 编程指南
  - 避坑指南
  - 基本规范
weight: "10"
---


面向 Agent 的「零摩擦」CI/CD 规则  
——在设计和开发阶段就必须写进代码、写进 Pipeline 的铁律

目标：  
让 Agent 在 99% 的场景里「自己搞定」，人类只在 1% 的极端风险点出现，且出现前已自动完成回滚所需的一切准备。

------------------------------------------------
1. 自动化优先原则（Automation-First Law）  
   任何新功能、新步骤、新检查点，必须先写「Agent 可独立执行」的实现，再考虑人类 UI。  
   违反此原则的 PR 直接拒绝合并。

2. 可逆性设计（Reversible-by-Design）  
   每个变更必须伴随「回滚计划」——以 Git commit、Terraform plan、DB migration 的逆脚本形式提交。  
   回滚脚本与正向脚本同版本、同签名，Agent 可在 30 秒内完成回滚。

3. 影子/金丝雀双轨（Shadow & Canary Dual-Track）  
   生产流量 100% 复制到影子环境；Agent 对比影子与生产指标，差异 < 0.1% 才允许继续。  
   金丝雀阶段由 Agent 自动扩大/缩小流量，无需人工旋钮。

4. 策略即版本化代码（Policy-as-Versioned-Code）  
   所有审批策略（OPA/Rego、CEL、Kyverno）以 Git 存储，版本化、单测覆盖率 > 90%。  
   Agent 可自提 PR 修改策略，但必须通过同策略的自动审查。

5. 备份先行（Backup-Before-Mutation）  
   任何「写」操作（DB schema、K8s CRD、IAM policy）执行前，Agent 必须：  
   6. 生成快照（point-in-time backup / etcd snapshot / OCI image tag）；  
   7. 把快照坐标写入流水线元数据；  
   8. 校验快照可恢复（自动跑一次 dry-restore）。

9. 风险分级自动路由（Risk-Tier Router）  
   • L0 低风险：Agent 直接执行，无需通知。  
   • L1 中风险：Agent 执行后异步通知人类（Slack/邮件）。  
   • L2 高风险：Agent 预演（dry-run）→ 生成可回滚方案 → 提交人类审批 → 30 分钟无响应则自动回滚。  
   分级规则写在策略仓库，可随统计结果自动调整阈值。

10. 证据链与不可变日志（Immutable Evidence Chain）  
   每一次变更、回滚、Agent 决策都写入 append-only 日志（Rekor + Loki）。  
   日志 Schema 统一（CloudEvents + OpenTelemetry），供后续 AI 审计与合规。

11. 最小权限 + 短期凭证（Least-Privilege & Short-Lived Token）  
   Agent 使用 SPIFFE ID + OIDC 获取 1 小时有效期的 scoped token。  
   每次调用都重新签发，无长期密钥。

12. 声明式契约（Contract-Driven Interface）  
   每个 Agent 暴露 gRPC/REST 契约：  
   • 输入：制品坐标 + 事件上下文 + 期望 SLA；  
   • 输出：结构化结果 + 决策（continue / block / rollback）+ 证据 URI。  
   契约变更必须向后兼容，使用 protobuf + buf CI 检查。

13. 统一测试矩阵（Universal Test Matrix）  
    所有代码、策略、Agent 本身都进入同一张测试矩阵：  
    • 单元测试（pytest / go test）  
    • 契约测试（Pact / buf breaking）  
    • 混沌测试（Litmus / ChaosMesh）  
    任何测试失败即阻断流水线，Agent 不能“跳过”。

14. 灰度配置即代码（Feature-Flag-as-Code）  
    使用统一的 flag 系统（LaunchDarkly / Flagsmith / OpenFeature），配置以 YAML 存 Git。  
    Agent 可自动调整灰度比例，但比例变更本身受策略约束。

15. 一键全局暂停（Global Kill-Switch）  
    任何人类或 Agent 发现严重异常，可触发全局 Kill-Switch（Argo Rollback / Crossplane Delete）。  
    Kill-Switch 事件写入日志，并自动创建 RCA issue。

------------------------------------------------
落地检查清单（在 PR 模板里强制勾选）

- [ ] 新增步骤已提供 Agent 可执行脚本或 WASM 模块  
- [ ] 回滚脚本已提交并通过 dry-run 测试  
- [ ] 风险分级标签已写入 policy.yaml  
- [ ] 备份/快照逻辑已集成到流水线  
- [ ] 契约 protobuf 已更新并通过 CI 校验  
- [ ] 单元 + 契约 + 混沌测试全部通过  
- [ ] 变更已关联 Kill-Switch 触发条件

------------------------------------------------
一句话总结  
把「备份、回滚、灰度、策略」做成代码，把「风险分级」做成配置，Agent 就能在 99% 的场景里闭环，人类只在 1% 的极端异常里被叫醒。