# Medical Knowledge Base · 医学临床实战知识库

> 一份面向住院医师 / 实习医生的**结构化、可检索**临床决策外骨骼。
> Built with Obsidian + Markdown + Dataview.

[![License: CC BY-NC-SA 4.0](https://img.shields.io/badge/License-CC%20BY--NC--SA%204.0-lightgrey.svg)](LICENSE)

---

## ⚠️ Important Disclaimer / 重要免责声明

**English**: This is a personal learning notebook by a medical trainee, NOT peer-reviewed clinical guidance. Content may contain errors or outdated information. **Always verify against current authoritative guidelines (UpToDate, ESC, AHA, NICE, local hospital protocols) before clinical application.** The author bears no responsibility for clinical decisions made based on this content.

**中文**:本仓库为临床实习生个人学习笔记,**不是**经过同行评议的临床指南,可能含错误或过期信息。**临床决策务必查阅当前权威指南**(UpToDate、各专科指南、本院规范)。作者不对任何基于本仓库内容做出的临床决策承担责任。

---

## 🔒 Privacy / 隐私

This repository contains **NO** patient information. See [PRIVACY.md](PRIVACY.md) for details.
所有内容已去标识化,患者隐私规则详见 [PRIVACY.md](PRIVACY.md)。

---

## 🎯 设计哲学

不是为了备份 UpToDate,**是为了塑造临床思维**。

每个条目用统一模板,强制回答 7 个问题:
- 定义是什么?
- 红旗是什么?
- 怎么鉴别?
- 床旁先查什么?
- 一线处理是什么?
- 什么时候打电话给上级?
- 常见坑在哪?

> 写不出"我的临床观察"的条目 = 你还没真正掌握。

---

## 📂 目录结构

```
Medicla_note/
├── README.md              ← 你正在读
├── LICENSE                ← CC-BY-NC-SA 4.0
├── PRIVACY.md             ← 隐私规则
├── .gitignore             ← 隐私护栏(daily/ 等永不上传)
│
├── _templates/            ← 5 个统一模板(疾病/症状/药物/评分/日记)
├── _index-by-symptom.md   ← 症状索引(临床实战入口)
├── _index-by-system.md    ← 系统索引(复习入口)
├── 临床实习毕业能力总纲.md ← 毕业前安全胜任力地图
├── 临床笔记生成清单.md     ← 待补全主题 backlog
├── _red-flags.md          ← 红旗速查(自动 Dataview)
├── _must-call-senior.md   ← 升级速查(含 SBAR 模板)
├── _to-review.md          ← 待复习清单(自动 Dataview)
│
├── symptoms/      ← 20 个常见症状
├── emergencies/   ← Must-not-miss 急症
├── diseases/      ← 内科常见病
├── drugs/         ← 药物
├── scores/        ← 临床评分
├── procedures/    ← 操作技能
├── examples/      ← 完整范例(参考用)
│
├── daily/         ← ⚠️ 本地专用,不上传(值班日记)
└── attachments/   ← 附件(private/ 子目录不上传)
```

---

## 🧬 YAML 字段约定

每个临床文件顶部必须有 YAML frontmatter,Dataview 靠它索引。

### 疾病文件(disease)

```yaml
---
name: 急性冠脉综合征
aliases: [ACS, STEMI, NSTEMI, UA]
type: disease
system: 心血管
severity: 危急             # 危急 | 紧急 | 普通
must_call_senior: true
tags: [chest-pain, must-not-miss]
related_drugs: ["[[drugs/阿司匹林|阿司匹林]]", "[[drugs/肝素|肝素]]"]
related_scores: ["[[GRACE]]"]
created: 2026-04-27
last_reviewed: 2026-04-27
guideline_source: ESC ACS Guideline
guideline_year: 2023
status: published          # draft | published | needs-update
---
```

### 受控字段值

**system**:`心血管` `呼吸` `神经` `消化` `肾脏` `内分泌` `血液` `风湿` `感染` `妇产` `儿科` `骨科` `外科` `皮肤` `急诊` `中毒`

**severity**:
- `危急` = 不立刻处理会死人
- `紧急` = 数小时内必须处理
- `普通` = 可择期

**status**:`draft` / `published` / `needs-update`

完整 YAML 字段定义见 `_templates/` 各模板。

---

## 🚀 工作流

### A. 床旁查询(30 秒)
1. `Cmd+O` 拼音搜索症状
2. 进入症状 MOC,看决策树
3. 点疾病链接,看顶部"30 秒摘要"

### B. 值班学习(每天)
1. 在 `daily/` 写当天值班日记(本地专用)
2. 引用相关疾病:`[[diseases/急性冠脉综合征|急性冠脉综合征]]`
3. 反向链接自动出现在疾病页底部 → 长期沉淀真实病例

### C. 周复习
1. 打开 `_to-review.md`,Dataview 列出 90 天没复习的危急疾病
2. 复习后更新 YAML 的 `last_reviewed` 字段

### D. 年度指南更新
每年 1 月做一次,过滤 `severity: 危急` 的所有条目,核对最新指南。

---

## 🛠️ Obsidian 配置

### 必装插件
- **Dataview**(核心,索引和查询)
- **Templater**(模板自动化)
- **Spaced Repetition**(药物剂量/评分用闪卡复习)
- **Periodic Notes** + **Calendar**(每日笔记)
- **Excalidraw**(手绘 ECG / 流程图)

### 模板路径配置
设置 → 核心插件 → 模板 → 模板文件夹位置 → `Medicla_note/_templates`

### Git 同步配置(obsidian-git 插件)
若整个 Obsidian Vault 包含其他非公开内容,建议在 obsidian-git 设置里:
- Custom base path → `Medicla_note`
- Auto commit interval → 30 min
- Pull on startup → on

---

## 📜 License

[CC-BY-NC-SA 4.0](LICENSE) — 你可以自由复制、修改、分享,但需署名、非商用、且衍生作品同等授权。

---

## 🤝 贡献 / Contribute

欢迎 issue 和 PR。但请遵守:
1. 任何新条目必须遵循模板和 YAML 约定
2. 必须列指南来源和年份
3. **绝不**包含可识别患者信息
4. 通用教学要点 > 个人案例细节

---

## 👤 关于作者

临床医学实习生,目标是 safe junior-doctor competence。
这份笔记是**学习过程的副产物**,不是终点产品。
