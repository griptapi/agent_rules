## 核心方法论

### STCAM核心评估协议

```mermaid
sequenceDiagram
    participant O as 问题观察者
    participant A as 分析引擎
    participant R as 可靠性门控
    participant V as 验证机制
    participant D as 决策门控
    participant DE as 执行器

    Note over O,DE: 每个关键节点都有REM检验

    O->>A: 问题信号触发
    A->>R: 信号可靠性初检
    R-->>A: 证据强度评估
    
    A->>A: 多维信息采集
    A->>R: 采集结果可靠性检验
    R-->>A: 模式置信度评估
    
    A->>V: 表象层分析
    V->>R: 表象分析可靠性检验
    R-->>V: 实践验证反馈
    
    V->>A: 机制层分析
    A->>R: 机制分析可靠性检验
    R-->>A: 历史实践对比
    
    A->>V: 本质层分析
    V->>R: 根因分析可靠性检验
    R-->>V: 跨域实践验证
    
    V->>D: 综合结论
    D->>R: 最终方案可靠性检验
    R-->>D: 实践成功率预测
    
    D->>DE: 执行方案
```

### 可靠性指标（PREM）
**Practice-based Reliability Evaluation Metric**

```mermaid
%%{init: {'theme':'base'}}%%
sequenceDiagram
    participant C as 调用方
    participant P as calculate_prem
    participant E as 证据评估
    participant R as 模式评估
    participant D as 债务评估

    Note over C,D: PREM指标计算流程

    par 主流程
        C->>P: 传入待分析项目结构和细节
        activate P
            P->>E: 传入实践考验数据<br>实验结论、测试规模、历史验证
            activate E
            E-->>P: 返回归一化证据强度
            deactivate E

            P->>R: 传入项目形式、逻辑、架构、思想
            activate R
            R->>R: 知识树模型<br>向上回溯关联的规则 < 原则 < 定律 < 公理
            R-->>P: 返回归一化模式置信度
            deactivate R

            P->>D: 传入代码质量、兼容性缺陷
            activate D
            D-->>P: 返回技术债务惩罚
            deactivate D

            P->>P: 计算最终PREM值
	Note over P,D: REM =100*E*R-D
            P-->>C: 返回PREM结果(0-100)
        deactivate P
    end
```

```python
def classify_rem(rem: float) -> str:
    """REM分级"""
    if rem > 80: return "极高可靠，参考复用"
    elif rem >= 60: return "高可靠，验证接口兼容性"  
    elif rem >= 30: return "中等可靠，检查环境配置"
    elif rem >= 10: return "低可靠，审查核心逻辑"
    else: return "极低可靠，深度质疑"
```
