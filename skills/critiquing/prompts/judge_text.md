# Judge 三段式文本输出（模式 A）

本提示词定义了 Critiquing Skill 的**三段式文本输出格式**。
评估逻辑参照 `knowledge/evaluation_protocol.md` 和 `knowledge/rubric.md`。

---

## 输出格式契约

**仅输出三段，不得输出其他标题/分割线/表格/过程说明：**

~~~text
Verdict: PASS | FAIL | BLOCK

Error Signals:
- missing (Critical|Major|Minor) @<anchor>: <缺失什么>；<为何影响验收/风险>；<需要的最小信息/约束>
- wrong (Critical|Major|Minor) @<anchor>: <哪里错/矛盾/不符合>；<可验证理由>
- unsupported (Critical|Major|Minor) @<anchor>: <无依据主张>；<缺的依据是什么>
- risks (Critical|Major|Minor) @<anchor>: <风险点/边界条件/歧义>；<为什么是风险>；<触发条件>

Correction Directions:
- [P0] <方向性动词短语>：<约束/验收点>（对应：@<anchor> / <signal 类型>）
- [P1] ...
- [P2] ...
~~~

## 多候选扩展

若提供 `candidates`（多条候选）：
*   仍然只输出三段。
*   在 Verdict 行末追加：`Ranking: <id1> > <id2> > ...`

## 锚定规则

`@<anchor>` 的格式：
*   优先：行号（如 `@L12`）。
*   否则：引用不超过 25 字的原文片段（如 `@"数据库连接池耗尽"`）。

## 信号类型说明

| 类型 | 含义 |
| :--- | :--- |
| `missing` | 缺失——对照 output_spec/constraints/rubric 应有但未有 |
| `wrong` | 错误——可验证的矛盾或不符合 |
| `unsupported` | 无依据——主张缺乏 inputs 支撑 |
| `risks` | 风险——边界条件/不可执行/歧义/合规风险 |
