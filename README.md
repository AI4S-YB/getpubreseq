# 🔬 Balabababa (扒拉扒扒扒)


<img width="453" height="377" alt="c08cad7e92799f18e6800ff9a83bc36a" src="https://github.com/user-attachments/assets/103765cc-9b2e-4be5-a571-e3f4b4301995" />

麦当劳钟意你黎，钟意你又黎，钟意你每日都黎（不是）

一个让 AI 助手（Opencode, 龙虾，codex）自动选择合适的数据库扒拉并检索的小插件，支持SRA数据库的智能查询和结果整理，功能基因搜索等......更新中。

## ✨ 功能特性

- 🔍 **智能检索**：自动构建 NCBI Entrez 检索式，支持物种名称、测序策略等条件
- 📊 **结果分组**：按 BioProject 自动分组展示，清晰呈现数据来源
- 🎯 **智能过滤**：自动排除 RNA-Seq、Amplicon、ChIP-Seq 等非 WGS 数据
- 📈 **详细信息**：展示平台、数据量、布局类型（PE/SE）等关键元数据
- 🔗 **下载链接**：生成 prefetch 命令、FTP 链接和 SRA 页面直达链接
- 🌍 **多中心支持**：自动识别并整合 SRR/ERR/DRR 数据

## 🚀 快速开始
<img width="595" height="84" alt="75db1034-c988-4a45-9bda-b69018ee4716" src="https://github.com/user-attachments/assets/78c990a4-b6bf-4a4c-9ef7-916c42aad0b9" />


加载 Skill 后，直接告诉 AI 你想检索的物种即可：


AI 会自动完成以下操作：

1. 构建 NCBI 检索式：`物种名[Organism] AND WGS`
2. 通过 Entrez API 获取 SRA 记录
3. 过滤筛选：Strategy=WGS, Source=DNA, Platform=Illumina/PacBio/Nanopore
4. 按 BioProject 分组整理并生成报告

## 📋 输出示例

### 检索结果摘要

| 项目 | 内容 |
|------|------|
| **物种关键词** | Oryza meridionalis |
| **最终检索式** | `Oryza meridionalis[Organism] AND WGS` |
| **筛选条件** | Strategy=WGS, Source=DNA, Platform=Illumina/PacBio/Nanopore |
| **检索时间** | 2026-03-28 |
| **结果数量** | 60 条 SRR/DRR/ERR |

### 按 Bioproject 分组

#### PRJNA1311239 - Multi-omics profiling of nine cultivated and wild rice varieties

| SRR/DRR/ERR | 平台 | 数据量 (Spots/Bases) | 布局 |
|-------------|------|---------------------|------|
| SRR35146725 | PacBio Sequel II (HiFi) | 2.0M / 29.3G | SE |

#### PRJEB73710 - A pangenome reference of wild and cultivated rice

| SRR/DRR/ERR | 平台 | 数据量 (Spots/Bases) | 布局 |
|-------------|------|---------------------|------|
| ERR13336047 | Oxford Nanopore PromethION | 1.8M / 31.6G | SE |

#### PRJNA833653 - Resequencing data of different species of Oryza

| SRR/DRR/ERR | 平台 | 数据量 (Spots/Bases) | 布局 |
|-------------|------|---------------------|------|
| SRR19032671 | Illumina HiSeq 2000 | 14.0M / 4.2G | PE |
| SRR19032672 | Illumina HiSeq 2000 | 13.9M / 4.2G | PE |

### 📥 数据下载方式

**使用 prefetch 批量下载：**
```bash
prefetch --option-file SRA_Accession_List.txt
