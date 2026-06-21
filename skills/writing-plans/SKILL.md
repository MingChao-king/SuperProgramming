---
name: writing-plans
description: 获得批准的设计后激活。将工作拆分为 TDD 三元组任务（RED→GREEN→REFACTOR），执行防重复造轮子检查，利用 L2/L3 缓存最大化复用。柔性技能。
---

<EXTREMELY-IMPORTANT>
设计大纲批准后，你必须使用此技能来制定实施计划。
每个实现任务必须拆成 RED→GREEN→REFACTOR 三元组。不允许"先写代码后补测试"。
计划末尾必须包含 code-review、verification、closing-the-loop 三个收尾阶段。
</EXTREMELY-IMPORTANT>

# Writing Plans：任务规划 + 防重复 + 缓存利用

## 技能类型：柔性

任务拆分的粒度和分组可适配上下文，但 TDD 三元组结构、防重复检查、执行链完整性是强制的。

---

## 执行链约束

writing-plans 产出的计划处于 SuperProgramming 六阶段工作流的中游（完整执行链见项目 CLAUDE.md § Workflow）：
```
brainstorming(设计大纲) → writing-plans(拆TDD三元组) → TDD执行 → code-review → verification → closing-the-loop
```

**TDD 不是"实现后面的测试阶段"。TDD 是实现方式本身。** 计划中测试必须交织在每个实现任务内，不是独立的后期阶段。

### ❌ 反模式（绝对禁止）
```
阶段 1-9：写代码 → 阶段 10：TDD 测试 → 结束（Waterfall，不是 TDD）
```

---

## 第一步：任务拆分 — TDD 三元组

### 任务粒度标准

| 太粗 | 刚好 |
|------|------|
| "实现用户认证" | RED(写 auth 测试) → GREEN(实现 generate_token) → REFACTOR(清理) |
| "重构数据库层" | RED(写参数化查询测试) → GREEN(改 SQL) → REFACTOR(提取常量) |

### TDD 三元组模板（强制）

```
任务组 X：{业务描述}

X-R  RED：先写测试
     ├─ 文件：src/test/.../{ClassName}Test.java
     ├─ 内容：基于 brainstorming 的 4 类场景写测试方法（每类 ≥2 条）
     ├─ 验证：mvn test → FAIL（红）
     └─ 提交：git commit -m "RED: {场景描述}"

X-G  GREEN：最小实现
     ├─ 文件：src/main/.../{ClassName}.java
     ├─ 内容：只写让 X-R 测试通过的代码，YAGNI
     ├─ L4 签名验证：Read 至少 3 个调用的关键方法签名
     ├─ 验证：mvn test → PASS（绿）
     ├─ 依赖：X-R（GREEN 依赖 RED，不是反过来）
     └─ 提交：git commit -m "GREEN: {实现描述}"

X-F  REFACTOR：清理
     ├─ 文件：同 X-G
     ├─ 内容：消除重复、改善命名、提取常量、保持测试绿
     ├─ 验证：mvn test → PASS
     ├─ 依赖：X-G
     └─ 提交：git commit -m "REFACTOR: {清理描述}"
```

### 依赖方向（关键）

```
❌ 错误：D3 (实现 Service) → T1 (测试 Service)    ← Waterfall
✅ 正确：T1-R → T1-G → T1-F（GREEN 依赖 RED）
```

### 纯数据结构豁免

Entity/Enum/DTO 只含字段声明/getter/setter/注解，没有任何 `if`/`for`/`throw`/`return` → 可跳过 TDD，标注"无 TDD（纯数据结构）"。有任一分支或循环 → 必须三元组。

---

## 第二步：防重复造轮子（强制检查清单）

**在规划每个新类/方法之前，逐项执行。** 完整清单和 grep 命令见项目 CLAUDE.md § Anti-Duplication Checklist。

| # | 检查项 | 操作 |
|---|--------|------|
| 1 | 新 Service 方法？ | Grep 方法名 `**/service/**/*.java` |
| 2 | 新 DTO/VO？ | Glob `**/dto/**`, `**/vo/**` |
| 3 | 新工具方法？ | Grep 方法签名 `**/utils/**`, `**/common/**` |
| 4 | 新 Mapper 方法？ | Grep 方法名 `**/mapper/**` |
| 5 | 新常量/枚举？ | Grep 常量名 `**/enums/**` |
| 6 | 新依赖？ | Grep artifactId `pom.xml` |
| 7 | 功能相似方法？ | Grep 业务关键词 `**/*.java`（模糊搜索） |

**判据**：已有方法名差 ≤2 词且参数列表基本相同 → 复用。已有 DTO 覆盖 ≥80% 字段 → 扩展字段。

---

## 第三步：L2/L3 缓存利用

从 brainstorming 的 L4 勘探结果中，标注每个任务可复用的组件：

```
任务组 T1：CouponTemplateService
  T1-R → 复用：已有 Service 测试模式（参考 XxxServiceTest）
  T1-G → 复用：BaseEntity 的 createTime/updateTime
```

L2 跨项目模式搜索：在 `knowledge-base/通用编码经验.md` 中搜索当前问题域的最优解（分页/缓存/排序/并发）。原则遵循 CLAUDE.md § Conflict Priority。

---

## 计划输出格式

```markdown
## 实施计划：{功能名称}

> 关联设计大纲：{roadmap 阶段} | 涉及模块：{列表}

### 复用组件汇总
| 已有组件 | 路径 | 用于任务 |
|---------|------|---------|

### 阶段 1-N：业务实现（TDD 交织）

**规则**：每个任务组先 RED(测试→FAIL) → GREEN(最小实现+验L4) → REFACTOR(清理)。

#### 任务组 X：{业务描述}
| # | 子任务 | 文件 | 要点 | 验证 | 依赖 |
|---|--------|------|------|------|------|
| X-R | RED: {测试描述} | src/test/.../XxxTest.java | 4类场景→测试方法 | mvn test → FAIL | — |
| X-G | GREEN: {实现描述} | src/main/.../Xxx.java | 最小实现+L4验≥3签名 | mvn test → PASS | X-R |
| X-F | REFACTOR: {清理描述} | 同 X-G | DRY+命名+常量 | mvn test → PASS | X-G |

#### 纯数据结构任务
| # | 任务 | 文件 | 验证 | 依赖 |
|---|------|------|------|------|
| B1 | 创建 XxxEnum | enums/XxxEnum.java | 编译通过 | — |

### 阶段 N：Code Review
| # | 任务 | 操作 | 验证 |
|---|------|------|------|
| R1 | 对照设计大纲审查 | 调用 requesting-code-review | 审查意见处理完毕 |
| R2 | 测试场景核对 | 对照 brainstorming 测试场景清单 | 全部场景有对应测试 |

### 阶段 N+1：Verification
| # | 任务 | 操作 | 验证 |
|---|------|------|------|
| V1 | 全量编译 | mvn clean install -DskipTests | 0 错误 |
| V2 | 全部测试 | mvn test | 全部通过 |
| V3 | 冒烟测试 | 启动 → 关键 API 手动验证 | 全部 200 |
| V4 | 边界抽查 | 抽查 3 个边界条件 | 行为符合预期 |

### 阶段 N+2：Closing the Loop
| # | 任务 | 操作 | 验证 |
|---|------|------|------|
| CL1 | 代码推送 | git commit + push | git status 干净 |
| CL2 | 知识库拉取 | git pull --ff-only | Already up to date |
| CL3 | L3 回写 | 子代理更新 experience.md | 新类/API/模式已记录 |
| CL4 | Roadmap 追加 | 子代理追加实现记录 | 新行已添加 |
| CL5 | L2 检查 | 子代理检查通用编码经验 | 有则回写，无则记录 |
| CL6 | 知识库推送 | git add + commit + push | 推送成功 |
```

---

## Red Flags — 你的计划有结构性问题

| 你的想法 | 真相 |
|---------|------|
| "写一个新的更快，搜索已有的太慢" | 写新的 5 分钟 + 调试 20 分钟。搜索已有 2 分钟。 |
| "这个功能太特殊，肯定没有现成的" | 7 次搜索里有 3 次能找到可复用的。不要靠直觉，靠 Grep。 |
| "先把所有实现列完，测试放最后一个阶段" | 这是 Waterfall。TDD 要求每个实现任务前面有 RED。 |
| "测试依赖实现，所以实现必须先做完" | 依赖方向反了。RED 先写，GREEN 依赖 RED（让测试通过）。 |
| "收尾阶段不属于计划的一部分" | code-review + verification + closing-the-loop 是交付必选。 |
| "计划就是任务列表，执行链是执行时的事" | 计划结构决定执行结构。测试放最后 → 执行时就是"写完再补测试"。 |

---

## 完成标准

进入实现阶段前必须确认：

- [ ] 所有业务任务已拆分为 RED → GREEN → REFACTOR 三元组（纯数据结构除外）
- [ ] 每个三元组依赖方向正确（GREEN 依赖 RED）
- [ ] 防重复 7 项检查已逐项执行
- [ ] 每个任务标注了可复用的已有组件（L3 命中）
- [ ] L2 搜索已完成，相关模式已标注
- [ ] 计划末尾显式列出了 Code Review、Verification、Closing the Loop
- [ ] 无独立"测试阶段"（测试已交织在业务任务的三元组中）
- [ ] 用户已确认任务清单
