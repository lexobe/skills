# 评分标准（Rubric）

---

## 严重度定义

按"问题"定级，不按"维度"硬绑：

| 级别 | 定义 |
| :--- | :--- |
| **Critical** | 不满足会使任务不可执行/高风险/违反强约束/无法验收（含关键输入缺失导致无法判断） |
| **Major** | 显著降低质量/稳定性/鲁棒性，但仍可能部分可用 |
| **Minor** | 小瑕疵或可容忍差异 |

---

## Verdict 判定规则

1.  若触发安全/隐私/违法/危险操作、或明显编造事实/伪造来源 → **BLOCK**。
2.  否则：若存在至少 1 条 Critical 级偏差（或关键信息缺失导致 Critical 不可评）→ **FAIL**。
3.  否则：**PASS**。

---

## 默认 Rubric（7 维度）

当 `rubric` 未由用户提供时，启用以下 7 维度：

| # | 维度 | 默认严重度 | 评估焦点 |
| :--- | :--- | :--- | :--- |
| 1 | **Goal** | Critical | 是否清晰对齐目标；是否可检查 |
| 2 | **Context** | Major | 是否使用了关键上下文；未知处是否标注并妥善处理 |
| 3 | **Output** | Critical | 格式/结构/长度/语言/边界是否满足 output_spec；是否可逐项验收 |
| 4 | **Constraints** | Critical | 是否遵守 must/ban；是否包含隐私/合规/风险控制；是否出现"编造" |
| 5 | **Input Contract** | Major | 是否明确基于哪些 inputs；是否区分"已知/推断/不确定" |
| 6 | **Process** | Major | 对复杂任务是否有可操作步骤/检查点；是否避免拍脑袋结论 |
| 7 | **Fallback** | Minor | 信息不足/冲突/不可完成时，是否给出可行回退与下一步 |

---

## 计分规则

**总分 15 分**（3 个 Critical × 3 分 + 3 个 Major × 2 分 + 1 个 Minor × 1 分）。

*   Critical = 3 分，Major = 2 分，Minor = 1 分。
*   **Critical 必须全 PASS**，否则直接判定不合格（verdict 至少 FAIL；若触发 Level 1 则 BLOCK）。
*   合格阈值：总分 ≥ `threshold` **且** 所有 Critical PASS。
    - `threshold` 默认 12，可由 `scoring.threshold` 覆盖。
*   N/A 规则：维度若为 N/A，则该维度记 0 分；且若该维度属于 Critical，则 `critical_all_pass = false`。

---

## 多候选排序算法

当 `scoring.ranking` 未提供时，按以下顺序排序（从优到劣）：

1.  verdict 分层：PASS > FAIL > BLOCK
2.  同层按 `score.total` 降序
3.  同分按 `critical_pass_count` 降序
4.  再按 `hard_violations` 数量升序
5.  再按 `error_signals.missing` 数量升序
