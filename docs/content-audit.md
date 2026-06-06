# 内容审计与优化建议

审计时间：2026-06-06

## 总体结论

这个仓库已经形成了五类资料：职业发展、面试准备、招聘活动、周报复盘和生活计划。当前主要问题不是资料缺失，而是历史记录散落在不同目录中，部分文件只有标题或占位内容，导致后续搜索和复盘成本偏高。

本次优化采用轻量整理方式：保留原始文档路径，新增索引、摘要和模板，让历史资料更容易被检索和继续维护。

## 当前资料结构

| 目录 | 定位 | 当前状态 | 优化方向 |
|---|---|---|---|
| `Food/` | 生活计划 | 仅有一份美食月表格，结构清楚。 | 保持轻量，不需要过度整理。 |
| `IDP/` | 个人发展计划 | Word 原件已保留，并补充了 Markdown 摘要。 | 后续年份沿用“原件 + 摘要”的维护方式。 |
| `Interview/` | 面试准备 | 2021 能力项和 2024 日期笔记较有价值，已补知识地图。 | 继续补齐“待补充”项，并沉淀可讲述案例。 |
| `Recruitment/` | 招聘活动 | 春招已有原始记录和复盘摘要，秋招已改为结构化模板。 | 补充秋招真实数据和过程记录。 |
| `WeeklyReport/` | 工作周报 | 已补 2021 汇总和项目线索。 | 继续拆出 CDP、capa、兴趣小组等项目复盘。 |

## 主要空白点

| 类型 | 位置 | 说明 | 建议 |
|---|---|---|---|
| 招聘复盘 | `Recruitment/2021秋招.md` | 已有模板，缺真实数据。 | 按事实补充目标、渠道、转化和结果。 |
| 面试占位 | `Interview/social/` 多个日期文件 | 标题已改为“待补充”。 | 补具体业务背景、技术细节和知识复习。 |
| IDP 搜索 | `IDP/2021IDP.md` | 已有摘要。 | 后续可按半年复盘问题补实际进展。 |
| 周报复盘 | `WeeklyReport/2021-summary.md` | 已有年度汇总。 | 可继续拆成项目级复盘。 |

## 优先级建议

1. 先补齐秋招复盘的事实数据：目标、渠道、触达量、转化、结果、复盘结论。
2. 再整理面试笔记，把“待补充”项按业务知识、技术知识、知识复习补完整。
3. 按季度抽取周报中的项目进展，形成项目经验素材。
4. 如果 IDP 继续使用，建议每年保持 Word 原件和 Markdown 摘要各一份。

## 维护检查

新增或改动文档后建议检查：

```powershell
$ErrorActionPreference='Stop'
$mdFiles = Get-ChildItem -Recurse -Filter *.md
$missing = @()
foreach ($file in $mdFiles) {
  $content = Get-Content -Encoding UTF8 -Raw -Path $file.FullName
  $matches = [regex]::Matches($content, '\[[^\]]+\]\(([^)]+)\)')
  foreach ($m in $matches) {
    $target = $m.Groups[1].Value
    if ($target -match '^[a-zA-Z]+:' -or $target.StartsWith('#')) { continue }
    $targetPath = $target.Split('#')[0]
    if ([string]::IsNullOrWhiteSpace($targetPath)) { continue }
    $resolved = Join-Path $file.DirectoryName $targetPath
    if (-not (Test-Path -LiteralPath $resolved)) {
      $missing += "$($file.FullName) -> $target"
    }
  }
}
if ($missing.Count) { $missing; exit 1 } else { 'All Markdown links resolve.' }
```
