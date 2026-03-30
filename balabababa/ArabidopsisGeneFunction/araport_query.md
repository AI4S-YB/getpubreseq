# Araport Query Module

## Overview
Araport (Arabidopsis Information Portal) 的 ThaleMine 是拟南芥基因研究的核心查询工具，提供基因注释、蛋白质互作、通路分析等功能。

**官网**: https://araport.org/apps/thalemine

**⚠️ 重要**: 这是推荐的拟南芥基因查询入口，TAIR 部分页面返回 403。

---

## 页面结构

### ThaleMine 主页面元素

| 元素引用 | 类型 | 说明 |
|----------|------|------|
| @e38 | textbox | 主搜索框 (显示 "e.g. AT1G01640") |
| @e39 | button | GO 按钮 |
| @e45 | textbox | 第二搜索框 (显示 "e.g. AT3G24650, FT, APL_ARATH") |
| @e52 | button | search 按钮 |
| @e43 | combobox | Analyse 下拉框 (Gene/Protein) |
| @e46 | textbox | Analyse 输入框 |
| @e56 | button | analyse 按钮 |

---

## 查询方法

### 方法1: 主搜索框查询（推荐）

#### 关键词搜索
```bash
# 1. 打开页面
agent-browser open https://araport.org/apps/thalemine
agent-browser wait --load networkidle
agent-browser snapshot -i

# 2. 输入关键词
agent-browser fill @e38 "cold tolerance"

# 3. 点击搜索
agent-browser click @e39

# 4. 等待结果
agent-browser wait --load networkidle

# 5. 获取结果
agent-browser snapshot
```

#### 基因号搜索
```bash
agent-browser fill @e38 "AT1G01010"
agent-browser click @e39
agent-browser wait --load networkidle
agent-browser snapshot
```

---

### 方法2: Analyse 功能（批量查询）

适用于批量查询多个基因：

```bash
# 1. 选择查询类型
agent-browser click @e43  # 展开下拉框
# 选择 "Gene"

# 2. 输入基因列表（逗号分隔）
agent-browser fill @e46 "AT1G01010, AT1G02000, AT1G03000"

# 3. 点击 analyse
agent-browser click @e56
agent-browser wait --load networkidle

# 4. 获取结果
agent-browser snapshot
```

---

## 结果解析

### 结果页面结构

```
┌─────────────────────────────────────────────────────────────┐
│ Search results 1 to 100 out of 2979 for cold tolerance      │
│                                                              │
│ Hits by Category:                                            │
│   - Publication: 1276                                        │
│   - GeneRIF: 709                                             │
│   - Gene: 223              ← 重要：基因数量                 │
│   - Protein Domain: 209                                      │
│   - GO Term: 113                                            │
│                                                              │
│ Hits by Organism:                                            │
│   - A. thaliana: 322        ← 拟南芥结果数                   │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ Gene 条目:                                                   │
│ cell "Gene" [ref=eXX]                                        │
│ cell "AT2G42530| locus:2041534| COR15B| cold regulated 15b"│
│   cell "Chromosome Location:" [ref=eXX]                     │
│   cell "Chr2: 17708593-17710433" [ref=eXX]                  │
│   cell "Organism . Short Name:" [ref=eXX]                   │
│   cell "A. thaliana" [ref=eXX]                              │
│   cell "TAIR Computational Description:" [ref=eXX]          │
│   cell "cold regulated 15b;(source:Araport11)" [ref=eXX]    │
│   cell "TAIR Curator Summary:" [ref=eXX]                     │
│   cell "Encodes COR15B, a protein that protects..." [ref=eXX]│
│   cell "TAIR Short Description:" [ref=eXX]                   │
│   cell "cold regulated 15b" [ref=eXX]                       │
│   cell "TAIR Aliases:" [ref=eXX]                            │
│   cell "COR15B" [ref=eXX]                                  │
└─────────────────────────────────────────────────────────────┘
```

### 提取字段说明

| 字段名 | 说明 | 提取方法 |
|--------|------|----------|
| Gene ID | AT格式基因号 | 从 "AT2G42530\|..." 提取 |
| Symbol | 基因符号 | 从 "...\| COR15B\|..." 提取 |
| Full Name | 全名 | 从 "...\| cold regulated 15b" 提取 |
| Chromosome | 染色体位置 | 查找 "Chr2: 17708593-17710433" |
| TAIR Computational Description | 计算注释 | 包含 "(source:Araport11)" |
| TAIR Curator Summary | **功能摘要** | 最重要的功能描述 |
| TAIR Short Description | 简短描述 | 基因简短说明 |
| TAIR Aliases | 别名 | 基因其他名称 |

---

## 推荐搜索关键词

### 抗逆相关
| 中文 | 英文关键词 | 变体 |
|------|-----------|------|
| 耐寒 | cold tolerance | cold stress, freezing tolerance |
| 耐热 | heat tolerance | heat stress |
| 耐旱 | drought tolerance | drought stress |
| 耐盐 | salt tolerance | salt stress |
| 耐重金属 | heavy metal tolerance | metal tolerance |
| 冷响应 | cold response | cold responsive |

### 发育相关
| 中文 | 英文关键词 | 变体 |
|------|-----------|------|
| 花发育 | flower development | flowering |
| 根发育 | root development | root hair |
| 叶片发育 | leaf development | leaf morphogenesis |
| 胚胎发育 | embryonic development | embryo |

### 代谢相关
| 中文 | 英文关键词 | 变体 |
|------|-----------|------|
| 光合作用 | photosynthesis | photosynthetic |
| 叶绿素 | chlorophyll | chlorophyll biosynthesis |
| 氮代谢 | nitrogen metabolism | - |

---

## 批量查询示例

### 查询多个基因号
```bash
# 基因号列表（逗号分隔）
AT1G01010, AT1G02000, AT1G03000, AT2G42530, AT5G42900
```

### 查询多个关键词
需要分别搜索每个关键词：
```bash
# 搜索 cold tolerance
agent-browser fill @e38 "cold tolerance"
agent-browser click @e39
# ...记录结果

# 搜索 cold stress
agent-browser fill @e38 "cold stress"
agent-browser click @e39
# ...记录结果

# 合并去重
```

---

## API 查询（可选）

### 直接构造 URL
```
https://araport.org/apps/thalemine
```

### 使用 Template 查询
Araport 提供预设的查询模板：
- Template > Gene > By Gene ID or Symbol
- Template > Gene > By Keyword
- Template > Gene > By Chromosome Location

---

## 常见问题

### 1. 搜索无结果
**原因**: 
- 关键词拼写错误
- 数据库无匹配

**解决**:
- 检查英文拼写
- 尝试同义词
- 确认物种为 Arabidopsis thaliana

### 2. 结果过多
**原因**: 关键词过于宽泛

**解决**:
- 使用更精确的短语
- 添加物种限定 "Arabidopsis thaliana"
- 组合多个条件

### 3. 元素定位失败
**原因**: 页面动态加载

**解决**:
```bash
agent-browser wait --load networkidle
agent-browser snapshot -i
```

---

## 验证清单

- [ ] 使用英文关键词
- [ ] 结果来自 Arabidopsis thaliana (A. thaliana)
- [ ] 提取了 TAIR Curator Summary
- [ ] 记录了数据来源 (Araport ThaleMine)
- [ ] 基因号格式正确 (AT1G ~ AT5G)
