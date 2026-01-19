# QA 测试体系完整方案

## 一、目录结构

```
{项目根目录}/
├── pm/
│   └── prd/
│       ├── index.csv                            # 需求索引
│       └── {需求名}-yyyymmddhhmmss/             # 每个需求一个目录
│           ├── context.md                       # 研究上下文
│           └── requirement.md                   # 完整需求文档
│
├── rd/
│   ├── bugs/                                    # Bug 共享目录
│   │   ├── pending.csv                          # QA 写入
│   │   └── archived.csv                         # RD 写入
│   └── dev/
│       └── {需求名}-yyyymmddhhmmss/             # RD 开发归档
│           ├── {需求名}-yyyymmddhhmmss.md       # 需求文档（从PM复制）
│           ├── plan.md                          # 执行计划
│           └── report.md                        # 执行报告
│
└── qa/
    ├── CLAUDE.md                                # 测试规范
    ├── .claude/
    │   ├── cache/                               # 上下文缓存（临时）
    │   │   └── {需求名}-context.md
    │   └── skills/
    │       └── qa/SKILL.md                      # /qa 入口
    │
    └── test-results/                            # 测试归档目录
        └── {需求名}-yyyymmddhhmmss/
            ├── context.md                       # 测试上下文（从cache归档）
            ├── scheme.md                        # 测试方案
            ├── scripts/                         # 测试脚本
            │   ├── test_xxx_api.py              # 接口测试
            │   └── test_flow.py                 # 流程测试
            ├── plan.md                          # UI测试计划
            └── report.md                        # 测试报告
```

---

## 二、相对路径汇总（从 qa/ 出发）

| 数据源 | 相对路径 |
|-------|---------|
| PM 需求索引 | `../pm/prd/index.csv` |
| PM 需求目录 | `../pm/prd/{需求名}/` |
| PM 研究上下文 | `../pm/prd/{需求名}/context.md` |
| PM 需求文档 | `../pm/prd/{需求名}/requirement.md` |
| RD 开发归档 | `../rd/dev/{需求名}/` |
| RD 执行计划 | `../rd/dev/{需求名}/plan.md` |
| RD 执行报告 | `../rd/dev/{需求名}/report.md` |
| Bug pending | `../rd/bugs/pending.csv` |
| 上下文缓存 | `.claude/cache/{需求名}-context.md` |
| 测试归档 | `test-results/{需求名}/` |
| 测试脚本 | `test-results/{需求名}/scripts/` |

---

## 三、测试类型

| 类型 | 工具 | 产出物 | 说明 |
|-----|------|--------|------|
| 接口测试 | 后端同技术栈脚本 | `scripts/test_xxx_api.py` | 单接口测试 |
| 流程测试 | 接口脚本串联 | `scripts/test_flow.py` | 业务流程走接口，不走UI |
| UI测试 | Playwright | `plan.md` | 页面交互验证 |

---

## 四、接口测试规范

### 测试颗粒度

| 分类 | 测试点 |
|-----|-------|
| **安全** | 认证（未登录/token过期/无效）、鉴权、越权（水平/垂直）、注入、脱敏 |
| **权限** | 角色权限、数据权限、功能权限、资源权限 |
| **参数-必填** | 未传、传空、传null |
| **参数-类型** | 类型错误、格式错误、特殊字符、空格、大小写 |
| **参数-长度** | 最小、最大、超长（⚠️低风险截断/高风险拒绝） |
| **等价类** | 有效等价类、无效等价类 |
| **边界值** | 最小值、最大值、临界值±1 |
| **响应** | 状态码、错误码、返回结构、字段类型、字段值 |
| **幂等性** | 重复提交、重复请求 |
| **并发** | 并发请求、竞态条件 |
| **数据** | 数据一致性、关联数据、大数据量 |

### 测试范围策略

| 接口类型 | 测试范围 |
|---------|---------|
| 新增接口 | 全覆盖 |
| 修改接口 | 全量回归 |
| 影响接口 | 仅主流程 |

### 流程测试
- 业务流程串联：添加员工 → 分配部门 → 分配设备 → ...
- **走接口**，不走 UI（省 token）

### 脚本规范
- **技术栈**：与后端保持一致
- **一次性**：为留痕目的
- **编写时机**：阶段4

---

## 五、CSV 结构

### PM index.csv（只读）
```
需求名称,目录名,创建时间,状态,关联需求
```

### pending.csv（QA 写入）
```
id,title,description,severity,reporter,created_at,status,related_prd
```
- **id**: `BUG-yyyymmdd-xxx`
- **severity**: P0/P1/P2/P3
- **status**: pending / in_progress
- **related_prd**: 关联的需求目录名

---

## 四、上下文缓存策略

### 缓存文件结构（`.claude/cache/{需求名}-context.md`）

```markdown
# 测试上下文 - {需求名}

## 一、需求摘要（阶段1写入）
- 需求目标：
- 核心功能点：
- UI/UX 要求：
- 验收标准：

## 二、关联分析（阶段1写入）
- 历史需求：
- 关联需求：
- 影响范围：
- 技术栈说明：

## 三、开发实现（阶段2追加）
- 技术方案要点：
- 接口变动：
- 完成情况：

## 四、需求vs实现对比（阶段2追加）
- 偏差点：
- 重点测试项：

## 五、接口清单（阶段2追加）
- 新增接口：
- 修改接口：
- 影响接口：
```

### 生命周期

| 阶段 | 操作 |
|-----|------|
| 阶段1 | 创建文件，写入「需求摘要」+「关联分析」 |
| 阶段2 | 追加「开发实现」+「需求vs实现对比」+「接口清单」 |
| 阶段3审核通过 | 移动到 `test-results/{需求名}/context.md` |

---

## 五、/qa 六阶段流程

```
/qa → 选择需求 → 阶段1 → 阶段2 → 阶段3[审核] → 阶段4 → 阶段5 → 阶段6 → 结束
```

### 阶段1：读取需求文档（PM源）
- 扫描 `../rd/dev/` 下已归档的文件夹
- AskUserQuestion 让用户选择要测试的需求
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
- 基于缓存的上下文制定测试方案
- **接口测试方案**：列出待测接口+测试点
- **流程测试方案**：列出业务流程串联步骤
- **UI测试方案**：列出页面交互测试点
- AskUserQuestion 请求用户审核
- **审核通过后**：
  - 创建 `test-results/{需求名}/` 目录
  - 创建 `test-results/{需求名}/scripts/` 目录
  - 移动缓存到 `test-results/{需求名}/context.md`
  - 保存方案到 `test-results/{需求名}/scheme.md`

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
- 发现问题立即记录

### 阶段6：提交Bug + 输出报告
- **提交Bug**：
  - 生成唯一 ID：`BUG-yyyymmdd-xxx`
  - 追加到 `../rd/bugs/pending.csv`
  - 关联需求目录名便于追溯
- **输出报告**：
  - 汇总：通过数/失败数/阻塞数
  - 列出提交的 bug 清单
  - 保存到 `test-results/{需求名}/report.md`

---

## 六、CLAUDE.md 内容

```markdown
# 测试（QA）角色规范

## 角色定义
资深测试工程师，专注功能测试与bug发现，严格遵循下述规范

## 测试范围铁律
- 所有测试必须基于 PM 原始需求 + RD 开发成果
- 对比需求与实现，发现偏差即为 bug
- 禁止脱离需求文档空测

## 文件修改规则
- **单次修改上限**：每次 Edit/Write ≤ **20行**，超过分批执行
- **目的**：避免大块内容撑爆上下文

## 禁止技术负债
- 禁止 `TODO`、`FIXME` 等占位注释
- 禁止遗漏用例不执行
- 禁止"后续再测"等延迟测试

## MCP 工具规则
- **Playwright 复用**：优先复用已打开的浏览器实例
- **Playwright 防超限**：优先 `browser_evaluate` 或 `browser_take_screenshot`，避免 `browser_snapshot`

## CSV 操作规范
- **删除行时禁止留空行**：删除后重写整个文件
- **整行操作原则**：读取/修改/删除以整行为单位
- **字段顺序固定**：禁止随意调整列顺序
- **特殊字符处理**：字段内容含逗号时用双引号包裹

## Bug ID 生成规则
格式：`BUG-yyyymmdd-xxx`（xxx为当日序号，从001开始）
生成前先读取 pending.csv 获取当日最大序号

## 上下文缓存规则
- 缓存路径：`.claude/cache/{需求名}-context.md`
- 阶段1-2 写入/追加，阶段3 审核通过后归档
- 归档路径：`test-results/{需求名}/context.md`

## 测试用例标记
- `[ ]` 待执行
- `[✅]` 通过
- `[❌]` 失败
- 每个用例执行后**立即**标记
```

---

## 七、/qa SKILL.md 内容

```markdown
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
- 对比需求与实现，检查偏差
- **追加** `.claude/cache/{需求名}-context.md`

### 阶段3：制定测试方案 ⏸️ 需审核
- 基于缓存上下文制定测试方案
- 输出：测试范围、测试策略、重点测试项
- AskUserQuestion 请求用户审核
- **审核通过后**：创建目录、归档缓存、保存方案

### 阶段4：编写测试计划
- 拆解测试用例：用例ID、测试点、操作步骤、预期结果、优先级
- 格式：`[ ] TC-001 | 测试点 | 预期结果 | P0`
- 保存到 `test-results/{需求名}/plan.md`

### 阶段5：执行测试
- ⚠️ **执行前重新读取 CLAUDE.md 规范**
- Playwright 执行自动化测试
- 逐个用例执行，截图留证
- 每个用例完成后标记 `[✅]` 或 `[❌]`

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
```

---

## 八、联动流程图

```
PM /pm 产出需求
    ↓
pm/prd/{需求名}/
├── context.md       ← 研究上下文
└── requirement.md   ← 完整需求
    ↓ 复制 requirement.md
RD /rd 开发
    ↓
rd/dev/{需求名}/
├── {需求名}.md      ← 需求文档
├── plan.md          ← 执行计划
└── report.md        ← 执行报告
    ↓
QA /qa 读取
    ├─ 阶段1: 读取 PM（context.md + requirement.md）→ 写入缓存
    ├─ 阶段2: 读取 RD（plan.md + report.md）→ 追加缓存
    ├─ 阶段3: 制定方案 → [审核] → 归档缓存
    ├─ 阶段4: 编写计划
    ├─ 阶段5: 执行测试 (Playwright)
    └─ 阶段6: 提交bug + 输出报告
              ↓
         rd/bugs/pending.csv
              ↓
RD /fix 修复 → rd/bugs/archived.csv
```
