# 🔒 Privacy Policy / 隐私规则

## 这个仓库**不包含**任何患者信息

This repository contains **NO** patient-identifiable information (PHI).

- ❌ 真实姓名 / Real names
- ❌ 出生日期、就诊日期 / DOBs, specific encounter dates
- ❌ 床号、住院号、身份证号 / Bed numbers, MRNs, ID numbers
- ❌ 电话、地址、家属信息 / Contact info
- ❌ 可识别的影像截图 / Identifiable images (CXR/CT/ECG with patient headers)
- ❌ 医院名称(若可能反向识别小样本)

## 我作为作者承诺(Author's Pledge)

1. 所有"我的临床观察"区只写**通用教学要点**,不写可追溯到具体病人的细节
2. 所有具体病例记录在本地 `daily/` 目录,**该目录通过 .gitignore 永不上传**
3. 提交前自检:看一眼 diff,问"陌生人能从这条信息追溯到病人吗?"
4. 任何上传的截图先打码处理(姓名、ID、日期)
5. 数据涉及的临床场景一律去身份化,如 "65 岁男性 + 糖尿病 + 急性胸痛" 而非具体姓名/日期

## 提交前 Checklist(每次 commit 前看一遍)

- [ ] 没有真实姓名(包括拼音、首字母)
- [ ] 没有具体日期 + 患者描述的组合
- [ ] 没有完整的医院名称 + 时间地点的组合
- [ ] 截图已打码或不含可识别信息
- [ ] "我的临床观察"是通用教学,不是病例报告

## 如果发现违规

**外部读者**:请通过 issue 或邮件指出,我会立刻 force-push 删除并审视所有历史。

**作者自己**:发现已推送的违规内容,使用以下命令彻底删除:

```bash
# 从历史中删除某文件(谨慎使用)
git filter-repo --invert-paths --path <文件路径>
git push origin --force --all
```

注意:GitHub 公开仓库一旦被克隆 / fork,即使 force-push 也无法保证彻底删除。
**所以"提交前自检"远比"事后删除"重要。**

---

## 内容说明 / Content Disclaimer

本仓库内容是医学实习生的学习笔记:
- 不是经过同行评议的临床指南
- 可能含错误、遗漏或过期信息
- 仅供学习交流,临床决策务必查阅当前权威指南
- 作者不对任何基于本仓库内容做出的临床决策负责

This is not peer-reviewed clinical guidance. For educational use only.
Always verify against current authoritative sources before clinical application.
