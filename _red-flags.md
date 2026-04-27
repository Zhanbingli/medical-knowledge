---
name: 红旗速查
type: moc
created: 2026-04-27
---

# 🚩 红旗速查表

> 一页纸,值班随手翻。Dataview 自动从所有 `severity: 危急` 文件抽取。
> 建议在 Obsidian 移动端固定为标签页。

## 全部危急级疾病

```dataview
TABLE without id 
  file.link as 疾病,
  system as 系统,
  guideline_year as 指南年份
FROM "Medicla_note"
WHERE severity = "危急" AND type = "disease"
SORT system ASC, file.name ASC
```

## 按系统分组

### 心血管 - 危急

```dataview
LIST FROM "Medicla_note"
WHERE severity = "危急" AND system = "心血管" AND type = "disease"
SORT file.name ASC
```

### 呼吸 - 危急

```dataview
LIST FROM "Medicla_note"
WHERE severity = "危急" AND system = "呼吸" AND type = "disease"
SORT file.name ASC
```

### 神经 - 危急

```dataview
LIST FROM "Medicla_note"
WHERE severity = "危急" AND system = "神经" AND type = "disease"
SORT file.name ASC
```

### 消化 / 外科 - 危急

```dataview
LIST FROM "Medicla_note"
WHERE severity = "危急" AND (system = "消化" OR system = "外科") AND type = "disease"
SORT file.name ASC
```

### 感染 - 危急

```dataview
LIST FROM "Medicla_note"
WHERE severity = "危急" AND system = "感染" AND type = "disease"
SORT file.name ASC
```

## 标记为 must-not-miss 的全部条目

```dataview
TABLE without id file.link as 疾病, severity as 危急度
FROM "Medicla_note"
WHERE contains(tags, "must-not-miss")
SORT severity ASC, file.name ASC
```
