# 软件工程全流程工作流

版本：1.0.0  
状态：首版基线  
默认语言：中文  
默认格式：Markdown

## 0. 如何触发

当前目录已经包含技能入口 [SKILL.md](SKILL.md)。在本工作区可直接发送：

```text
请读取 F:\codex\software-delivery-workflow\SKILL.md，并为“<项目名称>”启动完整软件工程工作流。
```

将该目录安装到Codex技能目录后，可使用更短的显式调用：

```text
$software-delivery-workflow 为“<项目名称>”启动完整流程
```

常用触发方式：

```text
$software-delivery-workflow 为“<项目>”单独执行02设计阶段，已有资料在 <路径>
$software-delivery-workflow 继续 <WORKFLOW-ID>
$software-delivery-workflow 补充 <WORKFLOW-ID> 的04测试阶段
$software-delivery-workflow 查看 <WORKFLOW-ID> 的状态
$software-delivery-workflow 对 <WORKFLOW-ID> 执行06总体专家评审
```

完整的自然语言触发规则、参数和初始化行为见 [工作流触发协议](shared/trigger-protocol.md)。`$software-delivery-workflow` 是Codex对话中的技能调用方式，不是PowerShell命令。

## 1. 工作流结构

本工作流由五个工程阶段和一个总体专家评审阶段组成：

1. `01-requirements`：需求阶段；
2. `02-design`：设计阶段；
3. `03-development`：开发阶段；
4. `04-testing`：测试阶段；
5. `05-release`：上线阶段，可选；
6. `06-final-review`：总体专家评审阶段。

每个工程阶段均包含独立文档模板和阶段评分表。文档接受质量检查，但不单独评分；阶段全部适用成果完成后，由专家统一给出 0—100 分。06 阶段对当前范围内的全部成果重新进行总体评分。

运行产出固定写入`<项目根目录>/docs/<需求英文名>/`。需求目录根部只保留一个`工作流状态.md`；阶段文档进入对应阶段目录，所有评审结论进入需求级共享目录`输出报告/`。

阶段目录按实际执行进度创建，不预建空目录。05未启用时不创建05目录及评审报告，06总体评审不创建独立阶段目录。

## 2. 执行方式

- 完整执行：01 → 02 → 03 → 04 → 05（可选）→ 06；
- 跳过上线：01 → 02 → 03 → 04 → 06；
- 单阶段执行：直接启动任意阶段，通过后结束本次阶段运行；
- 补充执行：读取已有成果，只生成或修订缺失、不合格、已过期的内容；
- 恢复执行：从`工作流状态.md`中的最近持久化检查点继续，不依赖原聊天窗口持续在线。

## 3. 固定阶段闭环

每个阶段执行：

1. 加载项目上下文和已有成果；
2. 确定适用文档、条件必填项和不适用项；
3. 生成或更新文档；
4. 执行文档质量检查；
5. 开展阶段专家评审并生成阶段评分；
6. 向用户展示评分、意见、风险，以及编号化的待确认/待评估事项；
7. 说明通过后锁定内容和下一步，再询问用户“通过”或“不通过”；
8. 不通过时整改、更新关联文档和问题状态、重新评分；
9. 通过后进入下一启用阶段或结束单阶段运行。

## 4. 用户卡点

询问前必须列出关键决策、未关闭问题、延期安排、红线以及通过后的影响；没有事项时明确写“无待确认问题”。固定询问文本：

> 本阶段已完成执行和专家评审。请回复“通过”，或回复“不通过：问题说明”。

专家评分只提供决策依据，用户拥有最终决定权。存在红线风险时必须醒目标注；用户仍决定通过时，必须记录风险接受说明。用户仅回复“通过”表示确认所列决策并接受所列延期安排，也可以回复“通过，但……”保留例外。

## 5. 目录说明

- 各阶段目录：专业文档模板和阶段评分表；
- `shared/workflow-spec.md`：状态机、独立执行、阶段切换规则；
- `shared/trigger-protocol.md`：触发语句、参数解析、初始化和防误触发规则；
- `shared/scoring-rules.md`：统一评分量尺和复评规则；
- `shared/document-control.md`：编号、版本、状态及适用性规则。

## 6. 模板索引

| 阶段 | 专业模板 | 阶段评分表 |
|---|---|---|
| 01需求 | [需求规格说明书](01-requirements/requirements-specification-template.md)、[用户故事集](01-requirements/user-stories-template.md) | [01评分表](01-requirements/expert-review-scorecard.md) |
| 02设计 | [架构设计](02-design/architecture-design-template.md)、[详细设计](02-design/detailed-design-template.md)、[API设计及OpenAPI/Swagger配套契约](02-design/api-design-template.md)、[数据库设计](02-design/database-design-template.md)、[UX设计](02-design/ux-design-template.md) | [02评分表](02-design/expert-review-scorecard.md) |
| 03开发 | [编码规范](03-development/coding-standards-template.md)、[UT设计](03-development/unit-test-design-template.md)、[代码评审](03-development/code-review-template.md)、[Git工作流](03-development/git-workflow-template.md) | [03评分表](03-development/expert-review-scorecard.md) |
| 04测试 | [测试计划](04-testing/test-plan-template.md)、[测试用例](04-testing/test-case-template.md)、[测试报告](04-testing/test-report-template.md)、[性能测试报告](04-testing/performance-test-report-template.md)、[需求追溯矩阵](04-testing/requirements-traceability-matrix-template.md) | [04评分表](04-testing/expert-review-scorecard.md) |
| 05上线 | [部署方案](05-release/deployment-plan-template.md)、[上线检查清单](05-release/go-live-checklist-template.md)、[MySQL验证清单](05-release/mysql-validation-checklist-template.md)、[发布说明](05-release/release-notes-template.md) | [05评分表](05-release/expert-review-scorecard.md) |
| 06总体评审 | [专家视角评价报告](06-final-review/expert-perspective-evaluation-report-template.md) | [06总体评分表](06-final-review/expert-review-scorecard.md) |

## 7. 规范参考

本模板集参考并裁剪以下公开规范或官方工程指南，不代表获得相应合规认证：

- ISO/IEC/IEEE 29148:2018 Requirements engineering；
- ISO/IEC/IEEE 42010:2022 Architecture description；
- ISO/IEC/IEEE 29119 series Software testing；
- ISO/IEC 25010:2023 Product quality model；
- ISO 9241-210:2019 Human-centred design；
- OpenAPI Specification；
- OWASP ASVS；
- Google Engineering Practices：Code Review；
- MySQL Reference Manual（按项目实际版本使用）。
