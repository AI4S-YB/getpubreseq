# Arabidopsis Gene Function Query Skill

## Description
从 Araport ThaleMine 数据库查询拟南芥基因的功能注释信息。支持单基因查询、批量查询和功能关键词搜索。

**重要**: 所有数据库查询使用英文关键词。

## 数据库说明

**⚠️ 注意**: TAIR 网站 (arabidopsis.org) 部分页面返回 403 Forbidden，**推荐使用 Araport ThaleMine** 作为主要查询入口。

| 数据库 | URL | 状态 | 推荐度 |
|--------|-----|------|--------|
| Araport ThaleMine | https://araport.org/apps/thalemine | ✅ 正常 | ⭐⭐⭐⭐⭐ |
| TAIR | https://www.arabidopsis.org | ⚠️ 部分403 | ⭐⭐⭐ |

## Prerequisites
1. 确保 agent-browser 已安装：`npm install -g agent-browser`
2. 查看 agent-browser 用法：`agent-browser --help`
3. 如首次使用，运行：`agent-browser install`

## When to use
- 用户提供基因号（如 AT1G01010、AT5G65010）时调用
- 用户询问拟南芥基因的功能（如"AT1G01010是什么基因"）
- 用户搜索特定功能相关的基因（如"耐寒性相关基因"、"花发育基因"）
- 用户使用中文查询，需先翻译为英文

---

## 中文关键词对照表

| 中文 | 英文 | 用途 |
|------|------|------|
| 耐寒性/耐寒 | cold tolerance | 抗寒相关基因 |
| 抗寒 | cold resistance / freezing tolerance | 抗冻相关 |
| 花发育 | flower development | 开花相关基因 |
| 抗逆性 | stress response / abiotic stress | 逆境响应 |
| 光合作用 | photosynthesis | 光合作用基因 |
| 根发育 | root development | 根系发育 |
| 叶绿素 | chlorophyll | 叶绿素相关 |
| 病原体/抗病 | pathogen response / defense | 防御相关 |
| 干旱 | drought tolerance | 抗旱相关 |
| 盐胁迫 | salt tolerance / salt stress | 耐盐相关 |
| 胚胎发育 | embryonic development | 胚胎发育 |
| 叶片发育 | leaf development | 叶片发育 |

---

## Parameters
- `gene_list`: 拟南芥基因号列表，格式如 `AT1G01010`、`AT5G65010` 等
- 支持 AT1G ~ AT5G 格式的基因号
- `keyword`: 功能关键词（英文），如 "cold tolerance", "flower development"

---

## 中文查询流程

```
用户输入（中文）
       ↓
翻译为英文关键词
       ↓
打开 Araport ThaleMine
       ↓
在搜索框输入英文关键词
       ↓
等待并获取结果
       ↓
从结果中提取 Gene 类型的条目
       ↓
整理结果返回
```

---

## Workflow - Araport ThaleMine 关键词搜索（推荐）

### Step 1: 打开 Araport ThaleMine
```bash
agent-browser open https://araport.org/apps/thalemine
agent-browser wait --load networkidle
```

### Step 2: 获取页面元素引用
```bash
agent-browser snapshot -i
```

**关键元素**:
- `@e38`: 主搜索框（显示 "e.g. AT1G01640"）
- `@e39`: GO 按钮
- `@e45`: 第二搜索框
- `@e52`: search 按钮

### Step 3: 输入英文关键词
```bash
agent-browser fill @e38 "cold tolerance"
```

### Step 4: 点击搜索
```bash
agent-browser click @e39
```

### Step 5: 等待结果加载
```bash
agent-browser wait --load networkidle
```

### Step 6: 获取结果快照
```bash
agent-browser snapshot -i
```

---

## 结果解析指南

### 结果页面结构

搜索成功后，页面包含以下信息：

```
Search results 1 to 100 out of XXXX for {关键词}
  ↓
Hits by Category:
  - Publication: XXX  ← 文献
  - GeneRIF: XXX      ← 基因功能注释
  - Gene: XXX         ← ← ← 重要！基因记录
  - Protein Domain: XXX
  - GO Term: XXX
  ...
  ↓
Hits by Organism:
  - A. thaliana: XXX ← ← ← 拟南芥结果数量
```

### 提取 Gene 类型结果

在 snapshot 中查找 `cell "Gene"` 开头的条目：

```
cell "Gene" [ref=eXX]
cell "AT2G42530| locus:2041534| COR15B| cold regulated 15b" [ref=eXX]
  - cell "TAIR Curator Summary:" [ref=eXX]
  - cell "Encodes COR15B, a protein that protects chloroplast membranes during freezing." [ref=eXX]
```

### 从结果中提取的字段

| 字段 | 来源 | 示例 |
|------|------|------|
| Gene ID | `AT2G42530` | AT2G42530 |
| Symbol | `COR15B` | COR15B |
| Full Name | `cold regulated 15b` | cold regulated 15b |
| Chromosome | `Chr2: 17708593-17710433` | Chr2 |
| TAIR Computational Description | GRAM domain-containing protein | 计算注释 |
| TAIR Curator Summary | Encodes COR15B, a protein... | **功能摘要（最重要）** |
| TAIR Aliases | COR15B | 别名 |

---

## Workflow - Araport 单基因查询

### Step 1: 打开 Araport ThaleMine
```bash
agent-browser open https://araport.org/apps/thalemine
agent-browser wait --load networkidle
```

### Step 2: 获取页面元素
```bash
agent-browser snapshot -i
```

### Step 3: 输入基因号
```bash
agent-browser fill @e38 "AT1G01010"
agent-browser click @e39
agent-browser wait --load networkidle
```

### Step 4: 获取结果
```bash
agent-browser snapshot
```

### Step 5: 提取基因信息
从结果中查找：
- Gene ID
- Symbol
- TAIR Curator Summary（功能描述）
- Chromosome Location
- TAIR Aliases

---

## Workflow - 批量基因查询

### Step 1: 打开 Araport ThaleMine
```bash
agent-browser open https://araport.org/apps/thalemine
agent-browser wait --load networkidle
```

### Step 2: 获取元素引用
```bash
agent-browser snapshot -i
```

### Step 3: 使用 Analyse 功能批量查询
```bash
# 选择 Gene 类型
agent-browser click @e43  # 展开下拉菜单
# 选择 Gene 选项

# 在分析框中输入多个基因号（逗号分隔或每行一个）
agent-browser fill @e46 "AT1G01010, AT1G02000, AT1G03000"

# 点击 analyse 按钮
agent-browser click @e56
agent-browser wait --load networkidle
```

### Step 4: 获取结果
```bash
agent-browser snapshot
```

### Step 5: 逐个提取基因信息
对每个基因记录提取：
- Gene ID
- Symbol
- TAIR Curator Summary

---

## Workflow - TAIR 直接URL查询（备选）

⚠️ TAIR 部分页面返回 403，优先使用 Araport

### 单基因详情页面
```
https://www.arabidopsis.org/servlet/searchform?form=gene&gene_name={GENE_ID}
```

示例：
```bash
agent-browser open "https://www.arabidopsis.org/servlet/searchform?form=gene&gene_name=AT1G01010"
agent-browser wait --load networkidle
```

### 批量检索工具
```bash
agent-browser open https://www.arabidopsis.org/seqtools/BatchGenes.pl
agent-browser wait --load networkidle
agent-browser snapshot -i
```

---

## 关键词搜索技巧

### 推荐的搜索关键词

| 功能类别 | 推荐关键词 | 变体关键词 |
|----------|-----------|-----------|
| 耐寒 | cold tolerance | cold stress, freezing tolerance, cold response |
| 花发育 | flower development | flowering, floral organ development |
| 根发育 | root development | root hair, root meristem |
| 干旱 | drought tolerance | drought stress, dehydration |
| 盐胁迫 | salt tolerance | salt stress, osmotic stress |
| 光合作用 | photosynthesis | photosynthetic, photosystem |
| 抗病 | defense response | pathogen response, immune |

### 搜索策略

1. **从宽到窄**：
   - 先搜索宽泛词：`stress response`
   - 再缩小：`cold stress`
   - 最后精确：`cold tolerance`

2. **组合搜索**：
   - `cold tolerance AND transcription factor`
   - `flower development AND Arabidopsis`

3. **排除词**：
   - `cold tolerance NOT heat`

---

## 结果解析详细指南

### 页面关键信息位置

```
1. 结果统计
   "Search results 1 to 100 out of 2979 for cold tolerance"
   ↑ 结果总数

2. 分类统计
   - Gene: 223           ← 基因数量
   - Publication: 1276   ← 文献数量
   - Protein Domain: 209 ← 蛋白域数量

3. 物种统计
   - A. thaliana: 322    ← 拟南芥结果数

4. Gene 条目结构
   cell "Gene" [ref=eXX]
   cell "AT2G42530| locus:2041534| COR15B| cold regulated 15b" [ref=eXX]
     cell "Chromosome Location:" [ref=eXX]
     cell "Chr2: 17708593-17710433" [ref=eXX]
     cell "Organism . Short Name:" [ref=eXX]
     cell "A. thaliana" [ref=eXX]
     cell "TAIR Computational Description:" [ref=eXX]
     cell "cold regulated 15b;(source:Araport11)" [ref=eXX]
     cell "TAIR Curator Summary:" [ref=eXX]
     cell "Encodes COR15B, a protein that protects chloroplast membranes during freezing." [ref=eXX]
     cell "TAIR Short Description:" [ref=eXX]
     cell "cold regulated 15b" [ref=eXX]
     cell "TAIR Aliases:" [ref=eXX]
     cell "COR15B" [ref=eXX]
```

### 提取优先级

1. **TAIR Curator Summary** - 最权威的功能描述
2. **TAIR Short Description** - 简短描述
3. **TAIR Computational Description** - 计算预测注释
4. **Gene ID** - AT格式基因号
5. **Chromosome Location** - 染色体位置

---

## 常见基因功能分类表

| 类别 | 英文关键词 | 示例基因 |
|------|-----------|---------|
| 冷响应 | cold tolerance/stress | COR15B, COR27, KIN2 |
| 冷休克蛋白 | cold shock domain | CSDP1, CSP3 |
| 转录因子 | transcription factor | ICE2, BBX21 |
| 脱水蛋白 | dehydrin | COR47, RD29A |
| LEA蛋白 | LEA protein | EM1, EM6 |
| 蛋白激酶 | protein kinase | MPK3, MPK6 |
| 锌指蛋白 | zinc finger | STO, STZ |
| 过氧化物酶 | peroxidase | RCI3 |
| 胱蛋白酶抑制剂 | cystatin | CYSA, CYSB |
| WD-40蛋白 | WD-40 repeat | HOS15 |

---

## 耐寒性基因核心列表（参考）

| 基因ID | 符号 | 位置 | 功能 |
|--------|------|------|------|
| AT2G42530 | COR15B | Chr2 | 保护叶绿体膜免受冻害 |
| AT5G42900 | COR27 | Chr5 | 冷调控蛋白27 |
| AT2G15970 | COR413-PM1 | Chr2 | 冷调控质膜蛋白1 |
| AT3G50830 | COR413-PM2 | Chr3 | 冷调控质膜蛋白2 |
| AT4G36020 | CSDP1 | Chr4 | 冷休克域蛋白1 |
| AT2G17870 | CSP3 | Chr2 | 冷休克域蛋白3 |
| AT1G12860 | ICE2 | Chr1 | bHLH转录因子 |
| AT5G67320 | HOS15 | Chr5 | WD-40蛋白，负调控冷耐受 |
| AT5G15970 | KIN2 | Chr5 | 冷响应蛋白 |

---

## Error Handling

### 问题1: 页面返回 403 Forbidden
**症状**: TAIR 网站返回 403 错误

**解决方案**: 
- 使用 Araport ThaleMine 代替
- URL: https://araport.org/apps/thalemine

### 问题2: 元素引用失效
**症状**: `Could not locate element` 错误

**解决方案**:
```bash
agent-browser snapshot -i
```
重新获取页面元素引用。

### 问题3: 页面加载超时
**症状**: 命令执行后长时间无响应

**解决方案**:
```bash
agent-browser wait --load networkidle
```
手动等待页面加载完成。

### 问题4: 基因未找到
**症状**: 页面显示空结果

**说明**: 该基因号在数据库中不存在，可能是基因号格式错误。

### 问题5: 关键词无结果
**症状**: 搜索返回空结果或结果过少

**解决方案**:
- 尝试同义词 (如 "cold tolerance" → "cold stress")
- 尝试更通用的词 (如 "cold" → "abiotic stress")
- 使用 "AND" 组合多个词

### 问题6: 结果过多难以筛选
**症状**: 返回成千上万条结果

**解决方案**:
- 添加物种限定词 "Arabidopsis thaliana"
- 选择 Results per page 为较小值
- 使用更精确的关键词短语

---

## 快速参考命令

```bash
# 1. 打开 Araport ThaleMine
agent-browser open https://araport.org/apps/thalemine
agent-browser wait --load networkidle

# 2. 获取页面元素
agent-browser snapshot -i

# 3. 搜索关键词（@e38 是搜索框，@e39 是 GO 按钮）
agent-browser fill @e38 "cold tolerance"
agent-browser click @e39
agent-browser wait --load networkidle

# 4. 获取结果
agent-browser snapshot

# 5. 搜索基因号
agent-browser fill @e38 "AT1G01010"
agent-browser click @e39
agent-browser wait --load networkidle
```

---

## 返回结果格式

### 单基因结果
```json
{
  "gene_id": "AT2G42530",
  "symbol": "COR15B",
  "full_name": "cold regulated 15b",
  "chromosome": "Chr2: 17708593-17710433",
  "short_description": "cold regulated 15b",
  "curator_summary": "Encodes COR15B, a protein that protects chloroplast membranes during freezing.",
  "aliases": "COR15B",
  "source": "Araport ThaleMine"
}
```

### 关键词搜索结果（表格格式）

| 基因ID | 符号 | 位置 | TAIR Curator Summary |
|--------|------|------|---------------------|
| AT2G42530 | COR15B | Chr2 | Encodes COR15B, a protein that protects chloroplast membranes during freezing. |
| AT5G42900 | COR27 | Chr5 | Acts with COR28 as a key regulator in the COP1-HY5 regulatory hub... |
| AT4G36020 | CSDP1 | Chr4 | Encodes a cold shock domain protein. Involved in cold acclimation... |

---

## Notes

1. **推荐数据库**: Araport ThaleMine (https://araport.org/apps/thalemine)
2. **备选数据库**: TAIR (https://www.arabidopsis.org) - 部分页面403
3. **支持格式**: AT1G... 到 AT5G... (TAIR格式)
4. **结果语言**: 功能注释为英文描述
5. **等待时间**: 每次查询后建议等待 2-3 秒让页面完全加载
6. **元素引用**: 页面刷新或结构变化时需要重新获取 `snapshot -i`
7. **中文处理**: 必须将中文关键词翻译为英文再查询
8. **关键摘要**: TAIR Curator Summary 是最重要的功能描述来源

---

## 扩展搜索关键词

### 发育相关
| 中文 | 英文 | 变体 |
|------|------|------|
| 花发育 | flower development | floral organ development |
| 叶片发育 | leaf development | leaf morphogenesis |
| 根发育 | root development | root hair development |
| 胚胎发育 | embryonic development | embryo development |
| 维管发育 | vascular development | xylem/phloem development |
| 开花时间 | flowering time | flowering |

### 胁迫响应
| 中文 | 英文 | 变体 |
|------|------|------|
| 耐寒 | cold tolerance | cold resistance |
| 耐热 | heat tolerance | heat stress |
| 耐旱 | drought tolerance | drought resistance |
| 耐盐 | salt tolerance | salt resistance |
| 耐重金属 | heavy metal tolerance | metal tolerance |
| 耐氧化 | oxidative stress | ROS response |

### 代谢相关
| 中文 | 英文 | 变体 |
|------|------|------|
| 光合作用 | photosynthesis | photosynthetic |
| 叶绿素 | chlorophyll | chlorophyll biosynthesis |
| 氮代谢 | nitrogen metabolism | nitrogen assimilation |
| 次生代谢 | secondary metabolism | metabolite |
| 激素响应 | hormone response | auxin/cytokinin/gibberellin |
