---
name: fix
description: Bug修复技能 - 读取bugs/pending.csv选择bug，全自动执行八阶段修复流程
---

# Bug修复工作流

## 入口交互

用户输入 `/fix` 后，读取 `bugs/pending.csv` 列出待修复bug

## 交互流程
1. 读取 `bugs/pending.csv`
2. AskUserQuestion 列出待修复bug让用户选择（by ID）
3. 更新选中bug状态为 `in_progress`（整行更新，重写文件无空行）
4. 全自动执行八阶段

## CSV 结构

### pending.csv（QA 写入，RD 读取）
`id,title,description,severity,reporter,created_at,status,related_dev`
- related_dev: 关联的开发任务目录名（如 用户积分-20260120143052）

### archived.csv（RD 写入，QA 验收）
`id,title,description,severity,reporter,created_at,root_cause,fix_plan,affected_scope,fix_report,fixed_at,fixed_by`

## 八阶段流程

### 阶段1：分析Bug
- 解析bug描述
- **结合当前项目代码**复现问题路径
- 输出：问题现象描述

### 阶段2：确定根因
- 定位具体文件、函数、行号
- 分析代码逻辑找出根本原因
- 输出：根因分析报告

### 阶段3：审核根因
- AskUserQuestion 确认根因判断
- 用户确认后继续

### 阶段4：修复方案
- 基于现有架构设计修复方案
- 评估修复复杂度
- 输出：修复方案概要

### 阶段5：影响范围
- 列出受影响的模块/接口/组件
- 评估回归风险
- 输出：影响范围清单

### 阶段6：执行修复
- ⚠️ **执行前重新读取 CLAUDE.md 规范**
- 分批修改（≤20行）
- 实时标记修复进度

### 阶段7：复测验证
- Playwright 自动化验证或手动验证
- 确认问题已修复
- 输出：验证结果

### 阶段8：归档留痕
- ⚠️ **执行前重新读取 CLAUDE.md 规范**
- 从 pending.csv 删除该行（重写文件，无空行）
- 整行数据+修复字段追加到 archived.csv

## CSV 操作规范
- 删除行后必须重写整个文件，确保无空行
- 整行操作，禁止单独操作某列导致错位
- 移动到 archived 时完整复制原行所有字段后追加新字段

## 与 QA 联动
- QA 提交 bug 到 pending.csv（生成唯一 ID: BUG-yyyymmdd-xxx）
- RD 通过 /fix 选择 ID 执行修复
- QA 查询 archived.csv 验收修复结果
