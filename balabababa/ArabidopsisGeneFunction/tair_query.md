# TAIR Query Module

## Overview
TAIR (The Arabidopsis Information Resource) 是拟南芥基因研究的历史核心数据库。

**官网**: https://www.arabidopsis.org

## ⚠️ 重要说明

**TAIR 部分页面返回 403 Forbidden 错误**，推荐使用 Araport ThaleMine 作为主要查询入口。

| 查询类型 | TAIR 状态 | 建议 |
|----------|-----------|------|
| 关键词搜索 | ⚠️ 可能403 | 使用 Araport |
| 单基因URL | ⚠️ 可能403 | 使用 Araport |
| Bulk Retrieval | ✅ 可能正常 | 可尝试 |

---

## 可用的 TAIR URL

### 单基因详情页
```
https://www.arabidopsis.org/servlet/searchform?form=gene&gene_name={GENE_ID}
```

示例：
```
https://www.arabidopsis.org/servlet/searchform?form=gene&gene_name=AT1G01010
```

### 执行命令
```bash
agent-browser open "https://www.arabidopsis.org/servlet/searchform?form=gene&gene_name=AT1G01010"
agent-browser wait --load networkidle
agent-browser snapshot
```

---

## TAIR Bulk Retrieval 工具

**URL**: https://www.arabidopsis.org/services/bulk/

### 访问方式
```bash
agent-browser open https://www.arabidopsis.org/services/bulk/
agent-browser wait --load networkidle
agent-browser snapshot -i
```

### 功能
- 批量查询基因注释
- 下载基因列表数据
- 选择特定字段输出

---

## TAIR 批量检索工具

**URL**: https://www.arabidopsis.org/seqtools/BatchGenes.pl

### 访问方式
```bash
agent-browser open https://www.arabidopsis.org/seqtools/BatchGenes.pl
agent-browser wait --load networkidle
agent-browser snapshot -i
```

### 功能
- 批量基因号查询
- 输入格式：每行一个基因号

---

## TAIR 搜索表单

**URL**: https://www.arabidopsis.org/servlet/searchform

### 问题
此页面经常返回 403 Forbidden 错误。

### 备选方案
使用 Araport ThaleMine:
```
https://araport.org/apps/thalemine
```

---

## TAIR 数据下载

TAIR 提供批量数据下载：

| 数据类型 | URL |
|----------|-----|
| Gene Annotation | https://www.arabidopsis.org/download/ |
| Protein Domain | https://www.arabidopsis.org/download/ |
| Expression Data | https://www.arabidopsis.org/download/ |

---

## 验证清单

- [ ] **优先使用 Araport ThaleMine**
- [ ] TAIR 查询失败时切换到 Araport
- [ ] 记录数据来源
- [ ] 确认基因号格式正确
