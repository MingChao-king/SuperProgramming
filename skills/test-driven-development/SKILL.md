---
name: test-driven-development
description: 实现阶段激活。强制执行 RED-GREEN-REFACTOR 循环，每次 GREEN 后做 L4 签名验证。刚性技能。
---

<EXTREMELY-IMPORTANT>
此技能是刚性技能。TDD 循环不可跳过。先写测试，看到它失败，再写最小代码让它通过。
如果你正在为自己找"这次不用写测试"的理由，立刻跳到 Red Flags 表。
</EXTREMELY-IMPORTANT>

# Test-Driven Development：TDD + L4 验证

## 技能类型：刚性

严格遵循 RED-GREEN-REFACTOR。不能跳过任何阶段。**跳过 TDD = 用用户的时间做你的调试器。**

---

## 哲学

**测试场景设计在 brainstorming，TDD 负责执行。** TDD 不设计场景——它从 brainstorming 产出中加载测试场景，转化为可执行测试用例。

TDD 不负责想测试场景。如果 brainstorming 没产出测试场景 → 退回 brainstorming 补全。

---

## RED-GREEN-REFACTOR 循环

```
RED：基于 brainstorming 场景写失败的测试
  → 4 类场景每类 ≥2 条 → 总共 ≥8 个测试方法
  → 运行测试 → 确认全部失败（红）
  → 如果某个测试直接通过 → 测试写错了
  → git commit -m "RED: {场景描述}"

GREEN：写最小代码让测试通过
  → 只写让当前测试通过的代码（YAGNI）
  → ★ L4 签名验证：Read 至少 3 个调用的关键方法，确认签名一致
  → 运行测试 → 确认全部通过（绿）
  → git commit -m "GREEN: {场景描述}"

REFACTOR：清理代码
  → 消除重复（DRY）、改善命名、提取常量
  → 每改一步运行一次测试（保持绿色）
  → git commit -m "REFACTOR: {清理描述}"
```

---

## 前置检查清单（写代码之前逐项打勾）

| # | 检查项 | 说明 |
|---|--------|------|
| 1 | 已从 brainstorming 加载测试场景？ | 4 类场景（隔离/边界/并发/异常）每类 ≥2 条 |
| 2 | L4 签名已验证？ | 本次调用的关键类，Read 源码确认签名，≥3 个 |
| 3 | 测试用例清单已写出？ | 方法名表达意图（`should_xxx_when_yyy`） |
| 4 | 测试已运行且至少一个失败？ | RED — 失败原因是"代码还没写"而不是"测试写错" |

---

## L4 签名验证（GREEN 阶段强制执行）

每次 GREEN 后，验证你使用的关键类方法签名：

```
调用了 UserService.getById(Long id)？
→ Read UserService.java → 确认返回类型（User 还是 Optional<User>？）
→ 确认异常声明（throws XxxException？）
→ 确认参数顺序和类型
```

**为什么要做**：LLM 凭记忆写的方法签名有 ~15% 的概率是错的。30 秒验证 > 30 分钟 debug。

---

## Bug 修复的 TDD 模式

```
bug 报告 → RED（写复现测试，确认失败）→ GREEN（应用修复，测试变绿）
→ 同类搜索（Grep 相同 bug 模式）→ REFACTOR → 触发 closing-the-loop
```

**先写复现测试 → 证明 bug 存在 → 修复 → 证明 bug 消失。** 不先写测试 = 你不知道是"修好了"还是"恰好不报错了"。

---

## 测试反模式

| 反模式 | 正确做法 |
|--------|---------|
| 测试依赖外部服务 | Mock 外部依赖 |
| 测试之间有顺序依赖 | 每个测试独立 |
| 方法名不表达意图 | `should_throw_when_email_is_empty()` |
| 只测 happy path | 覆盖 null、空列表、极大值 |
| 为了覆盖率写测试 | 测试行为，不测试实现细节 |
| 写完代码再补测试 | 先写测试——RED 阶段的设计思考是 TDD 价值的一半 |

---

## Red Flags — 你在跳过 TDD

| 你的想法 | 真相 |
|---------|------|
| "这个太简单，不需要测试" | 简单代码也有边界 bug。简单 = 测试更简单 = 没有借口。 |
| "先写实现，测试后补" | 后补的测试变成"验证实现"而非"验证行为"。TDD 一半价值在 RED 阶段的设计思考。 |
| "我知道这个方法签名" | 看 L4 确认。15% 概率记得是错的。30 秒验证 > 30 分钟 debug。 |
| "这是修 bug，直接改就行了" | 不先写复现测试 = 你不知道 bug 的根因。 |
| "写测试比写代码还花时间" | 写测试 < debug。不写省 10 分钟 → 线上出 bug → 花 4 小时。 |
| "brainstorming 没产出测试场景" | 退回 brainstorming 补全。没有测试场景 = 不知道自己在测什么。 |

---

## 完成标准

- [ ] 已从 brainstorming 加载测试场景（4 类每类 ≥2 条）
- [ ] L4 签名已验证（≥3 个关键方法签名与源码一致）
- [ ] ≥8 个测试方法已写出（4 类 × ≥2 条）
- [ ] RED 阶段：测试已运行且失败（不是测试写错）
- [ ] GREEN 阶段：全部测试通过 + L4 签名验证完成
- [ ] REFACTOR 阶段已完成（消除重复、改善命名、保持绿色）
- [ ] 每次 RED/GREEN/REFACTOR 已分别 git commit
