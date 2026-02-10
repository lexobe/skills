# Judge 严格 JSON 输出（模式 B）

本提示词定义了 Critiquing Skill 的**严格 JSON 输出格式**。
评估逻辑参照 `knowledge/evaluation_protocol.md` 和 `knowledge/rubric.md`。

---

## 输出要求

**必须严格按以下 JSON Schema 输出；不要输出额外文字。**

> 注意：下方 JSON 是输出 Schema 示例，不是默认值。你必须用真实评估结果填充字段。
> 不要为满足 Schema 而"硬填充"：无法判断就用 N/A、空数组，并在 questions 明确缺失信息。
> 若 mode=single：ranking 必须输出空数组 `[]`。

## JSON Schema

~~~json
{
  "language": "zh-CN | en-US | ...",
  "mode": "single | multi",
  "evaluations": [
    {
      "candidate_id": "candidate_1",
      "verdict": "PASS | FAIL | BLOCK",
      "score": {
        "total": 0,
        "threshold": 12,
        "critical_all_pass": true,
        "critical_pass_count": 0,
        "critical_na_count": 0
      },
      "dimensions": [
        {
          "id": 1,
          "name": "Goal",
          "default_severity": "Critical",
          "status": "PASS | FAIL | N/A",
          "evidence": "用 1-2 句指出你基于 candidate 的哪部分做出判断；若 N/A 写明缺失什么",
          "fix": "用 1 句给修正方向（非成品内容）；若 N/A 优先给澄清问题方向",
          "na_reason": "仅在 status=N/A 时填写，否则为空字符串"
        }
      ],
      "findings": [
        {
          "severity": "Critical | Major | Minor",
          "dimension_id": 1,
          "type": "missing | wrong | unsupported_claim | risk | other",
          "detail": "一句话描述问题",
          "evidence": "指向 candidate 的具体位置/要点；或说明为何无法验证",
          "direction": "修正方向（非成品）"
        }
      ],
      "hard_violations": [
        {
          "type": "safety | privacy | fabrication | noncompliance | reward_hacking | insufficient_info | other",
          "detail": "发生了什么",
          "why_it_matters": "为什么是硬问题",
          "direction": "修正方向（非成品）"
        }
      ],
      "error_signals": {
        "missing": ["缺失项…（对照 output_spec/constraints/rubric）"],
        "wrong": ["错误项…（可验证的矛盾/不符合）"],
        "unsupported_claims": ["无依据主张…（指出对应片段/类型）"],
        "risks": ["风险点…（边界条件/不可执行/歧义/合规）"]
      },
      "correction_directions": [
        "下一轮 Generator 应优先修正的方向（按优先级从高到低，3-7 条）"
      ],
      "questions": [
        "如需补充信息才能更精确判断，提出 1-5 个关键问题；否则留空数组"
      ],
      "notes": "可选：1-3 句补充（不输出方案）"
    }
  ],
  "ranking": [
    {
      "candidate_id": "candidate_1",
      "rank": 1,
      "why": "仅在 mode=multi 且 candidates>=2 时填写；说明排序依据（1-2 句）"
    }
  ],
  "overall_notes": "可选：当 candidates 存在时，总结整体误差模式（不输出方案）"
}
~~~

## 字段约束

| 字段 | 约束 |
| :--- | :--- |
| `language` | 默认 `zh-CN`；除非 `output_spec` 或 `scoring.language` 指定 |
| `mode` | `single`（单候选）或 `multi`（多候选） |
| `verdict` | `PASS` / `FAIL` / `BLOCK`，按 `knowledge/rubric.md` 中的判定规则 |
| `score.total` | 按 `knowledge/rubric.md` 计分规则计算 |
| `score.threshold` | 默认 12，可由 `scoring.threshold` 覆盖 |
| `dimensions` | 按默认 7 维度或用户自定义 rubric 逐维评估 |
| `dimensions[].status` | `PASS` / `FAIL` / `N/A`；N/A 时必须填写 `na_reason` |
| `findings` | 所有发现的问题，按严重度分级 |
| `hard_violations` | 仅 Level 1（生存约束）命中时填写 |
| `error_signals` | 分四类汇总：missing / wrong / unsupported_claims / risks |
| `correction_directions` | 3-7 条，按优先级排序；**不可**包含可直接替换的成品内容 |
| `questions` | 1-5 个最关键澄清问题；无需要时为空数组 |
| `ranking` | 仅 `mode=multi` 时填写；`mode=single` 时**必须**为空数组 |
