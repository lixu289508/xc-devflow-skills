---
name: qa
description: 测试技能 - 读取PM需求+RD开发成果，全自动执行六阶段测试流程
---

# 测试工作流

## 入口交互
用户输入 `/qa` 后，扫描 `../rd/dev/` 下已归档的文件夹

## 交互流程
1. 扫描 `../rd/dev/` 下的**文件夹**
2. AskUserQuestion 让用户选择要测试的需求
3. 进入六阶段流程

## 测试类型

| 类型 | 工具 | 产出物 |
|-----|------|--------|
| 接口测试 | 后端同技术栈脚本 | `scripts/test_xxx_api.py` |
| 流程测试 | 接口脚本串联 | `scripts/test_flow.py` |
| UI测试 | Playwright | `plan.md` |

## 六阶段流程

### 阶段1：读取需求文档（PM源）
- 读取 `../pm/prd/index.csv` 了解需求索引、关联需求
- 读取 `../pm/prd/{需求名}/context.md`（研究上下文）
- 读取 `../pm/prd/{需求名}/requirement.md`（完整需求）
- 提取：需求目标、功能点、UI/UX、验收标准、历史需求、关联需求
- **写入** `.claude/cache/{需求名}-context.md`

### 阶段2：读取开发成果（RD源）
- 读取 `../rd/dev/{需求名}/plan.md`（技术方案、接口变动）
- 读取 `../rd/dev/{需求名}/report.md`（完成情况）
- 扫描后端代码，提取接口清单（新增/修改/影响）
- 对比需求与实现，检查偏差
- **追加** `.claude/cache/{需求名}-context.md`

### 阶段3：制定测试方案 ⏸️ 需审核
- **接口测试方案**：列出待测接口+测试点
- **流程测试方案**：列出业务流程串联步骤
- **UI测试方案**：列出页面交互测试点
- AskUserQuestion 请求用户审核
- **审核通过后**：
  - 创建 `test-results/{需求名}/` 和 `scripts/` 目录
  - 归档缓存、保存方案

### 阶段4：编写测试计划/脚本
- **接口脚本**：后端同技术栈，保存到 `scripts/test_xxx_api.py`
- **流程脚本**：接口串联，保存到 `scripts/test_flow.py`
- **UI计划**：格式 `[ ] TC-001 | 测试点 | 预期结果 | P0`，保存到 `plan.md`

### 阶段5：执行测试
- ⚠️ **执行前重新读取 CLAUDE.md 规范**
- **接口测试**：执行接口脚本，记录结果
- **流程测试**：执行流程脚本，验证端到端
- **UI测试**：Playwright 执行，截图留证
- 每个用例/脚本完成后标记 `[✅]` 或 `[❌]`

### 阶段6：提交Bug + 输出报告
- 发现问题生成 bug 记录，ID格式：`BUG-yyyymmdd-xxx`
- 追加到 `../rd/bugs/pending.csv`
- 汇总测试结果保存到 `test-results/{需求名}/report.md`

## 相对路径

| 数据源 | 路径 |
|-------|------|
| PM需求索引 | `../pm/prd/index.csv` |
| PM研究上下文 | `../pm/prd/{需求名}/context.md` |
| PM需求文档 | `../pm/prd/{需求名}/requirement.md` |
| RD开发归档 | `../rd/dev/{需求名}/` |
| Bug文件 | `../rd/bugs/pending.csv` |
| 上下文缓存 | `.claude/cache/{需求名}-context.md` |
| 测试归档 | `test-results/{需求名}/` |
| 测试脚本 | `test-results/{需求名}/scripts/` |

## CSV 结构

### pending.csv（QA写入）
`id,title,description,severity,reporter,created_at,status,related_prd`

## 接口测试颗粒度

| 分类 | 测试点 |
|-----|-------|
| 安全 | 认证、鉴权、越权、注入、脱敏 |
| 权限 | 角色、数据、功能、资源 |
| 参数 | 必填、类型、长度（⚠️超长：低风险截断/高风险拒绝） |
| 等价类 | 有效、无效 |
| 边界值 | 最小、最大、临界±1 |
| 响应 | 状态码、错误码、结构、字段 |
| 幂等性 | 重复提交、重复请求 |
| 并发 | 并发请求、竞态条件 |
| 数据 | 一致性、关联、大数据量 |

## 测试范围策略

| 接口类型 | 测试范围 |
|---------|---------|
| 新增接口 | 全覆盖 |
| 修改接口 | 全量回归 |
| 影响接口 | 仅主流程 |
