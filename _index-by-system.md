---
name: 系统索引
type: moc
created: 2026-04-27
---

# 系统索引 · 复习入口

> 按器官系统组织,适合系统性复习和查漏。
> 临床查询请用 [[_index-by-symptom|症状索引]]。

## 心血管

```dataview
TABLE without id file.link as 疾病, severity as 危急度, last_reviewed as 上次复习
FROM "Medicla_note"
WHERE system = "心血管" AND type = "disease"
SORT severity ASC, file.name ASC
```

## 呼吸

```dataview
TABLE without id file.link as 疾病, severity as 危急度, last_reviewed as 上次复习
FROM "Medicla_note"
WHERE system = "呼吸" AND type = "disease"
SORT severity ASC, file.name ASC
```

## 神经

```dataview
TABLE without id file.link as 疾病, severity as 危急度, last_reviewed as 上次复习
FROM "Medicla_note"
WHERE system = "神经" AND type = "disease"
SORT severity ASC, file.name ASC
```

## 消化

```dataview
TABLE without id file.link as 疾病, severity as 危急度, last_reviewed as 上次复习
FROM "Medicla_note"
WHERE system = "消化" AND type = "disease"
SORT severity ASC, file.name ASC
```

## 肾脏

```dataview
TABLE without id file.link as 疾病, severity as 危急度, last_reviewed as 上次复习
FROM "Medicla_note"
WHERE system = "肾脏" AND type = "disease"
SORT severity ASC, file.name ASC
```

## 内分泌

```dataview
TABLE without id file.link as 疾病, severity as 危急度, last_reviewed as 上次复习
FROM "Medicla_note"
WHERE system = "内分泌" AND type = "disease"
SORT severity ASC, file.name ASC
```

## 血液

```dataview
TABLE without id file.link as 疾病, severity as 危急度, last_reviewed as 上次复习
FROM "Medicla_note"
WHERE system = "血液" AND type = "disease"
SORT severity ASC, file.name ASC
```

## 感染

```dataview
TABLE without id file.link as 疾病, severity as 危急度, last_reviewed as 上次复习
FROM "Medicla_note"
WHERE system = "感染" AND type = "disease"
SORT severity ASC, file.name ASC
```

## 妇产 / 儿科 / 骨科 / 外科

```dataview
TABLE without id file.link as 疾病, system as 系统, severity as 危急度
FROM "Medicla_note"
WHERE contains(["妇产", "儿科", "骨科", "外科"], system) AND type = "disease"
SORT system ASC, file.name ASC
```
