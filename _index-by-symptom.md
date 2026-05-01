---
name: 症状索引
type: moc
created: 2026-04-27
---

# 症状索引 · 临床实战入口

> 接到病人,从主诉切入。点击对应症状进入决策树。

## 心血管 / 呼吸

- [[symptoms/胸痛|胸痛]]
- [[symptoms/气短|气短]]
- [[symptoms/心悸|心悸]]
- [[symptoms/晕厥|晕厥]]
- [[水肿]]
- [[咳嗽-咯血]]

## 神经

- [[symptoms/头痛|头痛]]
- [[symptoms/头晕|头晕]]
- [[symptoms/意识改变|意识改变]]
- [[抽搐]]
- [[肢体无力-麻痹]]

## 消化

- [[symptoms/腹痛|腹痛]]
- [[呕吐-腹泻]]
- [[symptoms/呕血-黑便|呕血-黑便]]
- [[黄疸]]

## 泌尿 / 全身

- [[symptoms/发热|发热]]
- [[symptoms/少尿-无尿|少尿-无尿]]
- [[血尿]]
- [[腰背痛]]
- [[关节痛]]

---

## 全部症状(自动列表 - Dataview)

```dataview
TABLE without id 
  file.link as 症状,
  killers as "必须排除",
  last_reviewed as "上次复习"
FROM "Medicla_note/symptoms"
WHERE type = "symptom"
SORT file.name ASC
```

## 已写笔记数

```dataview
TABLE length(rows) as 数量
FROM "Medicla_note/symptoms"
WHERE type = "symptom"
GROUP BY status
```
