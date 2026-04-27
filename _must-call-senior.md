---
name: 升级速查
type: moc
created: 2026-04-27
---

# 🔺 必须立刻打电话给上级的疾病

> 当 `must_call_senior: true` 时自动出现在这里。
> 临床原则:**不确定就打电话。被骂胜过病人死。**

## 全部清单

```dataview
TABLE without id 
  file.link as 疾病,
  system as 系统,
  severity as 危急度
FROM "Medicla_note"
WHERE must_call_senior = true
SORT system ASC, file.name ASC
```

## 通用升级触发(任何病人)

> [!danger] 看到下面任何一条,立刻打电话,不要试图自己处理
> - 收缩压 < 90 mmHg(或较基线下降 > 40)
> - 心率 > 130 或 < 40
> - 呼吸 > 30 或 < 8
> - SpO2 < 90% 即使吸氧
> - GCS 下降 ≥ 2 分
> - 新发意识改变 / 抽搐
> - 尿量 < 0.5 mL/kg/h 持续 > 6 h
> - 乳酸 > 4 mmol/L
> - 胸痛 + ECG 任何 ST 改变
> - 任何"病人看起来不对劲"的直觉

## SBAR 沟通模板

> 打电话前用这 4 句话组织:
> 
> **S**ituation:我是 X 科 X 年级,病房 X 床 XX 病人,主要问题是 ___
> **B**ackground:他是 XX 岁,因 XX 入院,既往 XX
> **A**ssessment:我现在的判断是 ___,生命体征 ___
> **R**ecommendation:我已经做了 ___,请您来看 / 需要 ___ 指示
