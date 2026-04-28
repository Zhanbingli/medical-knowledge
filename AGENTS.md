# AGENTS.md · Medical Knowledge Note Authoring Skill

> **Audience**: AI coding/writing assistants (OpenAI Codex, Claude Code, ChatGPT, Cursor, etc.) working on this repository.
> **Purpose**: Generate clinical knowledge notes that match the project's template, contain ZERO fabricated medical claims, and are safe enough that a medical intern can actually consult them at the bedside.
>
> **If you (the AI) cannot follow every Hard Rule below, refuse the task and explain which rule blocks you.**

---

## 🛑 Bottom Line First

You are not writing a textbook. You are writing **clinical decision aids** for a final-year medical intern. Wrong information will harm patients. Therefore:

1. **Cite or leave blank.** No clinical number (dose, threshold, time window, percentage) appears without a guideline citation. If you don't have one, write `[需查证: 简短说明]` placeholder. **Never guess a drug dose.**
2. **Default to refusal.** If you cannot meet all Hard Rules for a topic, output a skeleton with `[需查证]` markers and a list of what the human must fill, rather than producing a polished but unsafe note.
3. **The "我的临床观察" section is always left empty.** It is the human's exclusive territory. Do not fabricate "上级 pearls" or "踩坑" experience.
4. **No patient information, ever** — not even hypothetical with names. Use anonymous demographic shorthand: "65M with DM2, HTN".

---

## 1. Inputs Expected

When invoked, you receive:

| Field | Required | Example | Notes |
|---|---|---|---|
| `topic` | ✅ | `心力衰竭` | Chinese name preferred (Obsidian links 用中文) |
| `type` | ✅ | `disease` / `symptom` / `drug` / `score` / `procedure` | Refuse to guess; ask if ambiguous |
| `system` | ⚠️ | `心血管` | Required for disease/symptom; use enum |
| `severity` | ⚠️ | `危急` / `紧急` / `普通` | Required for disease |
| `aliases` | optional | `[CHF, HFrEF, HFpEF]` | English + Chinese acronyms |
| `context_hint` | optional | "用于内科病房学习,不是 ICU 急救" | Adjusts emphasis |

**If `type` not given and topic is ambiguous (e.g., "肝素" — 是药物还是 HIT 章节?), ASK ONCE before generating.** Do not pick the most likely interpretation silently.

---

## 2. Hard Rules (Violation = Invalid Output)

### Rule 1 · Cite-or-Null

Every numeric clinical statement requires `(来源, 年份)`. Examples:

| Claim | OK? |
|---|---|
| `阿司匹林 300 mg 嚼服 (ESC ACS 2023)` | ✅ |
| `阿司匹林 300 mg 嚼服` | ❌ |
| `阿司匹林 [需查证: 负荷剂量] 嚼服` | ✅(诚实)|
| `肝素 80 U/kg IV 首剂(UpToDate, 2024)` | ✅ |
| `肝素剂量按体重` | ❌ (太模糊,等于没说) |

Acceptable sources: see §6 whitelist. Year **must** be ≤ 5 years old or marked `status: needs-update`.

### Rule 2 · YAML Schema is Mandatory and Strict

Use only the enum values listed in `README.md`. No extra fields. No missing required fields.

```yaml
# Required for ALL clinical files
name: <中文主名>
aliases: [<英文/缩写,可空>]
type: disease | symptom | drug | score | procedure
created: YYYY-MM-DD
last_reviewed: YYYY-MM-DD
status: draft | published | needs-update

# Required for disease
system: 心血管 | 呼吸 | 神经 | 消化 | 肾脏 | 内分泌 | 血液 | 风湿 | 感染 | 妇产 | 儿科 | 骨科 | 外科 | 皮肤 | 急诊 | 中毒
severity: 危急 | 紧急 | 普通
must_call_senior: true | false
guideline_source: <e.g., ESC ACS Guideline>
guideline_year: <e.g., 2023>
related_drugs: ["[[药 1]]", "[[药 2]]"]
related_scores: ["[[评分 1]]"]
tags: [<kebab-case 英文 tag>]

# Required for symptom
killers: ["[[杀手 1]]", ...]   # 必须排除的致命原因
common: ["[[常见 1]]", ...]

# Required for drug
class: <e.g., 抗凝>
indications: [<可空数组>]
contraindications: [<可空数组>]
renal_adjust: true | false
hepatic_adjust: true | false
pregnancy: <A/B/C/D/X 或 文字说明>
monitoring: <e.g., APTT>

# Required for score
applies_to: ["[[相关疾病]]"]
purpose: <一句话用途>
```

### Rule 3 · Section Order is Fixed

Follow the corresponding `_templates/*.md` exactly. Do not reorder. Do not invent sections. Do not omit required sections (use `[需查证]` if cannot fill).

### Rule 4 · "我的临床观察" Always Empty

This section has three subsections — leave them as blank skeleton:

```markdown
## 我的临床观察

> [!note] 写在这里的内容是 UpToDate 找不到的——这是这份笔记真正的核心价值
> 
> ### 上级 Pearls
> - 
> 
> ### 我踩过的坑
> - 
> 
> ### 本院流程
> - 
```

**You are forbidden from writing in this section** — even hypothetical pearls. It is the human author's exclusive space. Filling it would make the entire note untrustworthy.

### Rule 5 · No PHI Anywhere

Even hypothetical examples → use generic shorthand:

- ✅ `老年女性 + 糖尿病 + 不典型胸痛`
- ✅ `65M with HTN, smoker, sudden tearing chest pain`
- ❌ `张某,68 岁,2024 年 3 月入院`
- ❌ `如同上次值班那位 ICU 转来的患者`
- ❌ Any actual hospital name + ward number combination

### Rule 6 · Mark Uncertainty Explicitly

| Situation | Marker |
|---|---|
| Guidelines disagree | `> [!warning] ESC vs AHA 不一致:...` |
| Practice changed recently | `> 注:2023 年起 ESC 弱化吗啡使用` |
| Off-label / debated | `> [!note] 此用法为 off-label,本院实践需确认` |
| You don't know | `[需查证: 具体内容]` inline |
| Source > 5 years | YAML `status: needs-update` |

**Never use weasel words to hide ignorance**: 通常 / 一般 / 大多数 / 据报道 — these are forbidden unless you cite the specific study/percentage.

### Rule 7 · Cross-Link Aggressively but Correctly

- Every drug mention → `[[药名]]`
- Every disease mention → `[[疾病名]]`
- Every score mention → `[[评分名]]`
- Even if target file doesn't exist yet (it'll show grey, that's fine)
- Use Chinese name as primary link, English alias inside if needed: `[[急性冠脉综合征|ACS]]`

### Rule 8 · Honor the Privacy Boundary

- The folder `daily/` is gitignored; your output should never reference specific daily entries by date.
- Never include screenshots, ECG traces with patient headers, or actual lab values "from a real patient".

### Rule 9 · One Topic per File

If the topic naturally splits (e.g., "肝素" covers UFH + LMWH), produce ONE comprehensive file with clear subsections. Don't auto-create multiple files unless the user explicitly asks.

### Rule 10 · No Self-Promotion or Editorializing

- ❌ "笔者建议"、"个人认为"、"我推荐"
- ❌ "本指南是最权威的"
- ✅ "ESC 2023 推荐..."、"在 NSTEMI 患者中..."

---

## 3. Output Format Spec

### 3.1 File Header (every file)

```yaml
---
name: <topic 中文名>
aliases: [<英文 / 缩写>]
type: <enum>
... # other type-specific fields
created: <YYYY-MM-DD,今天>
last_reviewed: <同 created>
guideline_source: <主要参考>
guideline_year: <YYYY>
status: draft   # AI 生成的初稿默认 draft,人工审核后改为 published
---
```

### 3.2 Body Structure

Follow the appropriate template literally. Available templates:

- `_templates/疾病模板.md`
- `_templates/症状模板.md`
- `_templates/药物模板.md`
- `_templates/评分模板.md`
- `_templates/值班日记模板.md` (you do not write daily entries; for human only)

### 3.3 Standardized Callouts (使用这些,不要发明)

| 用途 | Callout |
|---|---|
| 30 秒摘要 | `> [!summary] 30 秒摘要` |
| 必查清单 / 警觉 | `> [!warning]` |
| 危急 / 必须立刻做 | `> [!danger]` |
| 操作技巧 / 经验 | `> [!tip]` |
| 一般注解 | `> [!note]` |
| 范例 | `> [!example]` |

### 3.4 Standardized Emoji (仅这些可用)

🚩 (red flag) · 🔺 (escalation) · ⚠️ (warning) · 🛑 (stop) · ✅ ❌ (yes/no list)

**No other emojis.** No 😀 🩺 💊 etc.

### 3.5 End-of-File Block (mandatory)

Every AI-generated file MUST end with:

```markdown
---

<!-- AI-GENERATED METADATA · DO NOT REMOVE -->

## 📚 引用来源(本文撰写时所参考)
- <Source 1, Year>
- <Source 2, Year>

## 🔍 需人工验证条目
- [ ] <Item 1: e.g., 艾司洛尔具体剂量范围>
- [ ] <Item 2>
- [ ] (如全部已查证,此列表写"无")

## ⚙️ 生成元信息
- 生成模型: <model name, e.g., codex-1, claude-opus-4-7>
- 生成日期: <YYYY-MM-DD>
- 状态: draft (待人工审核后改为 published)
```

This block is **non-optional**. Without it, the file is rejected.

---

## 4. Type-Specific Required Sections

### 4.1 Disease (`type: disease`)

Required, in order:
1. `# {名称}({英文/缩写})`
2. `> [!summary] 30 秒摘要` — 红旗 / 首查 / 一线 / 升级 4 行
3. `## 一句话定义`
4. `## 分型 / 分类`(如适用)
5. `## 🚩 红旗(立刻警觉)`
6. `## 鉴别诊断`(必须含至少 5 个,带"区分点 + 关键检查"两列)
7. `## 首查(到床后 X 分钟内)`
8. `## 一线处理`
9. `## 🔺 升级标准(立刻叫上级)`
10. `## ⚠️ 常见坑`(至少 5 条)
11. `## 关键评分`(至少 1 个)
12. `## 我的临床观察`(空骨架)
13. `## 相关链接`

### 4.2 Symptom (`type: symptom`)

Required:
1. `# {症状名}`
2. `> [!summary]` — 接到主诉 3 件事必做
3. `## 🚨 必须排除的 Killer`(至少 3 个,带 `> [!danger]` callout)
4. `## 常见非危急原因`
5. `## 决策树`(代码块或流程图)
6. `## 必问病史`(OPQRST 表 + 红旗问题)
7. `## 必查体征`
8. `## 床旁首查`
9. `## 🔺 立刻升级的临床表现`
10. `## ⚠️ 常见漏诊场景`(至少 5 条)
11. `## 我的临床观察`(空骨架)
12. `## 相关疾病`

### 4.3 Drug (`type: drug`)

Required:
1. `# {药名}({英文})`
2. `> [!summary] 给药前 5 秒检查清单`
3. `## 类别 / 机制`
4. `## 适应证`
5. `## 用法用量`(必须按"适应证 → 剂量"表格,数字必须带引用)
6. `## 禁忌证`(分绝对 / 相对)
7. `## 常见不良反应`
8. `## ⚠️ 危险不良反应`(至少 1 个,如 HIT、QT 延长、横纹肌溶解等)
9. `## 肾 / 肝功能调整`
10. `## 妊娠 / 哺乳`
11. `## 监测指标`
12. `## 关键相互作用`
13. `## 我的临床观察`(空骨架)
14. `## 相关疾病`

### 4.4 Score (`type: score`)

Required:
1. `# {评分名}`
2. `> [!summary]` — 用途 / 适用 / 不适用
3. `## 评分项目`(完整表格,每项分值)
4. `## 解读`(分层 + 处置建议表)
5. `## 临床应用场景`
6. `## 局限性 / 不适用情况`
7. `## 我的临床观察`(空骨架)
8. `## 相关链接`

---

## 5. Self-Verification Checklist (运行前强制检查)

Before submitting output, run through this checklist. **If any unchecked, fix and re-verify.**

### 安全性检查
- [ ] 0 个未引用的剂量数字
- [ ] 0 个未引用的时间窗 / 阈值
- [ ] 0 个未引用的百分比
- [ ] 0 个 PHI(姓名 / 床号 / 具体日期 + 患者描述)
- [ ] 0 个第一人称临床建议
- [ ] 0 个 weasel word(通常 / 一般 / 大多数,除非有具体百分比)

### 完整性检查
- [ ] YAML frontmatter 包含所有 required 字段
- [ ] 所有 enum 字段使用允许值
- [ ] 该类型的所有 required sections 都存在
- [ ] 鉴别诊断 ≥ 5 项(disease)/ killer ≥ 3 项(symptom)
- [ ] 常见坑 ≥ 5 项
- [ ] 我的临床观察 是空骨架(三个子标题保留,内容为空)
- [ ] 文件末尾 AI-Generated Metadata 区块完整(引用 / 待验证 / 生成元信息)

### 链接检查
- [ ] 所有提到的疾病 / 药物 / 评分用 `[[]]` 链接
- [ ] 链接目标使用中文主名(英文为 alias)
- [ ] 没有空的 `[[]]` 或语法错误

### 风格检查
- [ ] 仅使用允许的 emoji(🚩 🔺 ⚠️ 🛑 ✅ ❌)
- [ ] 仅使用标准 callout 类型
- [ ] 每个章节标题文字与模板一致

如果某项不通过,回去修。**不要因为"基本可以"就提交**。

---

## 6. Citation Source Whitelist

### 一线来源(直接可引用)

**国际指南**
- ESC (European Society of Cardiology)
- AHA / ACC (American Heart / American College of Cardiology)
- NICE (UK National Institute for Health and Care Excellence)
- IDSA (Infectious Diseases Society of America)
- KDIGO (Kidney Disease: Improving Global Outcomes)
- ADA (American Diabetes Association)
- GINA (Global Initiative for Asthma)
- GOLD (Global Initiative for COPD)
- WHO

**中国指南**
- 中华医学会各专科分会指南 / 共识
- 中华内科学会 / 中华心血管病学会 / 中华神经病学会等
- 国家卫生健康委发布的诊疗规范
- 中华医学杂志、中华内科杂志专家共识

**循证医学资源**
- UpToDate
- BMJ Best Practice
- Cochrane Systematic Reviews
- DynaMed

**经典教科书**
- Harrison's Principles of Internal Medicine
- Cecil Textbook of Medicine
- Goldman-Cecil Medicine
- 内科学(人卫第 9 版及以上)
- Robbins Pathology(机制相关)

### 二线来源(可补充,但不能作为唯一引用)

- 高 IF 期刊原始研究(NEJM, JAMA, Lancet, BMJ)— 用于"最新进展"段
- 系统综述

### 拒绝作为来源

- ❌ Wikipedia(可作为外链,不作为引用)
- ❌ 个人博客 / 知乎回答
- ❌ 药企营销材料
- ❌ 单一 case report
- ❌ 任何无法溯源的"网传指南"

---

## 7. 失败模式与处理

### 当你应该停下来询问

| 情境 | 行为 |
|---|---|
| topic 多义(如"肝素" = 药 vs HIT) | 先 ASK 用户澄清 |
| 缺少 type | ASK |
| 主题超出医学范围 | 拒绝 |
| 主题过于罕见无可靠中文资料 | 输出英文为主 + 注明"中文文献不足,主要依赖 [来源]" |

### 当你应该输出"骨架 + 占位符"

如果你只能给出部分高置信内容,**不要硬撑写全文**。输出:
- 已确知部分:正常质量
- 未确知部分:`[需查证: <说明>]`
- 文件末"待验证条目"列出所有占位符

让人类来填,远胜于你瞎填。

### 当你应该完全拒绝

- 用户要求生成"虚构病例"用于教学但未严格去身份化
- 用户要求绕过 PHI 规则
- 用户要求生成与本仓库立场冲突的内容(如纯营销稿)
- 任何要求"我帮病人 X 查这个药能不能吃"的实际医疗咨询(此仓库不提供医疗咨询)

---

## 8. Forbidden Patterns

❌ 编造剂量数字(包括"按教科书剂量"这种含混表述)
❌ 把多个指南的不同推荐合并描述为"共识"
❌ 用"通常""一般""大多数"代替具体百分比
❌ 写"具体咨询医生"作为内容(本仓库面向医生)
❌ 在临床观察区写任何内容(即使提示性)
❌ 用 emoji 装饰非标位置(如标题装饰)
❌ 包含 mermaid 图表外的复杂插图(本仓库以文本为主)
❌ 长 disclaimer 段落混在内容里(disclaimer 由 README 统一处理)
❌ 主动添加"参考文献"以外的元信息字段
❌ 引用未署名年份的指南
❌ 使用现实医院 / 医生姓名

---

## 9. Worked Example

### 9.1 用户请求

```
type: disease
topic: 心房颤动
system: 心血管
severity: 紧急
context_hint: 实习医生病房常用,不是急诊心律失常重点
```

### 9.2 高质量输出片段(摘要展示)

```markdown
---
name: 心房颤动
aliases: [Atrial Fibrillation, AF, Afib, 房颤]
type: disease
system: 心血管
severity: 紧急
must_call_senior: false   # 通常不立刻打电话,但 RVR + 不稳定 → must
tags: [arrhythmia, anticoagulation, common]
related_drugs: ["[[β受体阻滞剂]]", "[[地高辛]]", "[[胺碘酮]]", "[[华法林]]", "[[利伐沙班]]", "[[阿哌沙班]]"]
related_scores: ["[[CHA2DS2-VASc]]", "[[HAS-BLED]]"]
created: 2026-04-28
last_reviewed: 2026-04-28
guideline_source: ESC AF Guideline
guideline_year: 2024
status: draft
---

# 心房颤动(Atrial Fibrillation, AF)

> [!summary] 30 秒摘要
> **红旗**:RVR + 血流动力学不稳 / 急性脑卒中 / 急性心衰
> **首查**:12 导联 ECG + TSH + 电解质 + 心超 + CHA2DS2-VASc
> **一线**:稳定 → 控率(β 阻滞剂或 CCB);不稳定 → 同步电复律
> **升级**:不稳定立即;新发 ≥ 48h 但血栓脱落风险评估前不擅自药物复律

## 一句话定义
心房无规律快速电活动导致心房机械收缩消失、心室率不规则的常见心律失常 (ESC 2024)。

## 分型(决定治疗倾向)
| 分型 | 持续时间 | 备注 |
|---|---|---|
| 阵发性 | 自发终止或干预后 ≤ 7 天 | |
| 持续性 | > 7 天,需复律 | |
| 长期持续 | > 1 年,仍考虑复律 | |
| 永久性 | 接受不复律 | 仅控率 |

(分型来源:ESC AF 2024)

## 🚩 红旗
- RVR(心室率持续 > 130)伴血流动力学不稳
- 新发卒中 + 房颤 = 心源性栓塞,警惕大动脉闭塞
- 急性心衰 + 房颤 RVR
- 首次诊断房颤 + 甲亢 / 急性感染 / 肺栓塞 / 急性冠脉综合征(寻找诱因)

## 鉴别诊断
| 心律失常 | 区分点 |
|---|---|
| [[心房扑动]] | 锯齿波、心室率多为 150 整数倍 |
| [[多源性房性心动过速]] | ≥ 3 种 P 波形态,常 COPD |
| [[阵发性室上速 SVT]] | 规律窄 QRS、突发突止 |
| [[室性心动过速]] | 宽 QRS,血流动力学多不稳 |

## 首查
- [ ] 12 导联 ECG(确诊)
- [ ] 心率 / 血压 / SpO2 / 神经定位查体
- [ ] 电解质(K, Mg)、TSH、肌酐、肝功
- [ ] 心超(EF、左房大小、瓣膜)
- [ ] **CHA2DS2-VASc** 评分(决定抗凝)
- [ ] **HAS-BLED** 评分(评估出血风险)
- [ ] 排除诱因:甲亢、感染、电解质、PE、ACS

## 一线处理

### 不稳定
- 同步电复律(SBP < 90 / 心绞痛 / 急性心衰 / 神志改变)— 100J 双相起步,逐步加 (ESC AF 2024)

### 稳定 — 控率优先
| 选择 | 用法 | 备注 |
|---|---|---|
| β 受体阻滞剂(美托洛尔) | 5 mg IV q5min × 3 或口服 | 首选,EF 保留或降低均可 (ESC 2024) |
| 非二氢吡啶 CCB(地尔硫卓) | 0.25 mg/kg IV bolus | EF 保留;**EF < 40% 禁用** |
| 地高辛 | 0.25 mg IV q2h × 2-3 | 心衰中可加,起效慢 |
| 胺碘酮 | 150 mg IV over 10min | 重症 / 难治 |

目标心室率(静息):
- 宽松控制:< 110 bpm
- 严格控制:< 80 bpm(症状未缓解时考虑)

(目标值来源:ESC AF 2024 Section 8)

### 抗凝决策(关键!不可遗漏)

CHA2DS2-VASc:
- 男 ≥ 2、女 ≥ 3 → **建议长期口服抗凝**
- 男 1、女 2 → 个体化考虑
- 男 0、女 1 → 不抗凝

首选 [[利伐沙班]] / [[阿哌沙班]] / [[达比加群]] / [[艾多沙班]],除非:
- 中重度二尖瓣狭窄 → 必须 [[华法林]]
- 机械瓣 → 必须华法林

(抗凝推荐来源:ESC AF 2024 + 2023 中国房颤管理指南)

## 🔺 升级标准
- 血流动力学不稳 → 立即电复律 + 上级到场
- 新发卒中 → 卒中通道(不擅自抗凝,先排除出血)
- 房颤 + 急性心衰恶化
- 房颤 + 怀疑 ACS / PE
- RVR 持续 > 6h 不能控制

## ⚠️ 常见坑
1. **把房扑当房颤** → 房扑心室率常很规则,处理略有差异。
2. **EF < 40% 给地尔硫卓 / 维拉帕米** → 加重心衰。
3. **新发房颤 > 48h 直接药物复律不评估血栓** → 需经食管超声排除左房血栓或先抗凝 3 周。
4. **CHA2DS2-VASc 0/1 也常规抗凝** → 出血风险大于获益。
5. **认为"控律比控率更好"** → AFFIRM/RACE 等多项研究显示总体生存无差异,以症状为导向。
6. **抗凝选择忽视肾功能** → DOAC 多需按 CrCl 调整(参见 [[利伐沙班]] / [[阿哌沙班]])。
7. **甲亢未筛查** → 房颤合并甲亢比例不低,治疗甲亢可恢复窦律。

## 关键评分
- **[[CHA2DS2-VASc]]**:卒中风险,决定抗凝
- **[[HAS-BLED]]**:出血风险,辅助决策
- **[[EHRA]]** 症状分级:I-IV

## 我的临床观察
> [!note] 写在这里的内容是 UpToDate 找不到的——这是这份笔记真正的核心价值
> 
> ### 上级 Pearls
> - 
> 
> ### 我踩过的坑
> - 
> 
> ### 本院流程
> - 急诊电复律流程:____
> - DOAC 处方权限:____

## 相关链接
- 鉴别:[[心房扑动]] · [[多源性房性心动过速]] · [[阵发性室上速 SVT]]
- 评分:[[CHA2DS2-VASc]] · [[HAS-BLED]] · [[EHRA]]
- 药物:[[β受体阻滞剂]] · [[地高辛]] · [[胺碘酮]] · [[利伐沙班]] · [[阿哌沙班]] · [[华法林]]
- 操作:[[同步电复律]] · [[经食道超声]]
- 并发症:[[心源性栓塞]] · [[心动过速性心肌病]]

---

<!-- AI-GENERATED METADATA · DO NOT REMOVE -->

## 📚 引用来源
- ESC AF Guideline 2024
- 2023 中国房颤管理指南(中华心血管病杂志)
- UpToDate: Atrial fibrillation: Overview and management of new-onset (2024)

## 🔍 需人工验证条目
- [ ] 美托洛尔具体 IV 剂量在本院药品规格下的执行方式
- [ ] 同步电复律本院起始能量 SOP
- [ ] DOAC 在本院药品目录中具体规格

## ⚙️ 生成元信息
- 生成模型: <model name>
- 生成日期: 2026-04-28
- 状态: draft
```

### 9.3 反例(为什么这是错的)

❌ 错误片段示例:
```markdown
## 一线处理
通常给阿司匹林,有时加用 β 阻滞剂。剂量根据病情调整。
```

错误点:
- "通常""有时" = weasel word
- 阿司匹林对房颤抗凝**已被证明无效**(房颤抗凝用 DOAC/华法林),这是**严重错误**
- 无剂量、无引用
- 没有区分稳定 vs 不稳定

这种错误一旦进入床旁参考 = 患者损害。AI 必须避免。

---

## 10. 调用范例(给用户参考)

### Codex / Cursor

放置 `AGENTS.md` 在仓库根目录,Codex / Cursor 自动读取。然后:

```
> 请按 AGENTS.md 的规范,生成 Medicla_note/diseases/心力衰竭.md
> type: disease
> system: 心血管
> severity: 紧急
> 重点:实习医生病房使用,不是 ICU 急救
```

### Claude Code

```
> Read AGENTS.md, then generate diseases/急性肾损伤.md per spec.
> type: disease, system: 肾脏, severity: 紧急
```

### ChatGPT

把本文件内容复制为 system prompt,然后请求生成。

---

## 11. 版本与变更

| 版本 | 日期 | 变更 |
|---|---|---|
| 1.0 | 2026-04-28 | 初版:10 条 Hard Rules + 4 类模板 + Self-verify checklist |

如需修改本规范,需 PR + Issue 讨论。Hard Rules 改动须由仓库主作者确认。

---

## 12. Final Reminder to AI

读到这里,你应该明白:**生成医学内容的最大风险不是"写得不够多",而是"写得自信但错"**。一个标记为"已发布"但有错误的剂量,比一个充满 `[需查证]` 占位符的诚实草稿,危险百倍。

**当你不确定时,请记住一个准则**:
> 我宁可输出半成品让人补,也不要让医生在床旁照我编的剂量给药。

这是这份 skill 存在的全部理由。
