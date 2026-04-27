---
name: 待复习清单
type: moc
created: 2026-04-27
---

# 📅 待复习清单

> Dataview 自动按"上次复习日期"过滤。复习完成后,把对应文件 YAML 的 `last_reviewed` 改成今天,这条会自动消失。

## 高危疾病 - 90 天没复习

```dataview
TABLE without id 
  file.link as 疾病,
  last_reviewed as "上次复习",
  (date(today) - date(last_reviewed)).day as "距今(天)"
FROM "Medicla_note"
WHERE severity = "危急" 
  AND type = "disease"
  AND last_reviewed
  AND (date(today) - date(last_reviewed)).day > 90
SORT (date(today) - date(last_reviewed)).day DESC
```

## 普通疾病 - 180 天没复习

```dataview
TABLE without id 
  file.link as 疾病,
  last_reviewed as "上次复习",
  (date(today) - date(last_reviewed)).day as "距今(天)"
FROM "Medicla_note"
WHERE severity != "危急" 
  AND type = "disease"
  AND last_reviewed
  AND (date(today) - date(last_reviewed)).day > 180
SORT (date(today) - date(last_reviewed)).day DESC
```

## 标记为需要更新的(指南可能过期)

```dataview
LIST FROM "Medicla_note"
WHERE status = "needs-update"
SORT file.name ASC
```

## 还在草稿状态的

```dataview
LIST FROM "Medicla_note"
WHERE status = "draft"
SORT file.name ASC
```

## 指南来源 > 3 年的

```dataview
TABLE without id 
  file.link as 疾病,
  guideline_source as 来源,
  guideline_year as 年份
FROM "Medicla_note"
WHERE guideline_year AND guideline_year < 2023
SORT guideline_year ASC
```

---

## 年度指南更新 SOP

每年 1 月做一次:
1. 打开本页,看"指南来源 > 3 年的"列表
2. 对每个疾病,查 UpToDate / ESC / AHA / 国内最新指南
3. 更新内容 + 改 YAML 的 `guideline_year` 和 `last_reviewed`
4. 如有重大变化,把 status 暂时设为 `needs-update`,改完再设回 `published`
