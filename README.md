# Exam PPT Sorter Skill

一个用于整理、清理、重新编号和重建考试题目 PowerPoint 的 Codex Skill。

它适合处理以扫描图片或截图形式保存题目的 `.pptx` 文件，例如题库、历年试卷、复习教材和按章节分类的练习册。

## 这是什么？

`exam-ppt-sorter` 为 Codex 提供一套完整的考试 PPT 处理工作流，包括：

- 从 PPTX 提取题目图片并建立题目清单
- 去除手写答案、圈选、打勾、阴影和轻微扫描噪点
- 保留印刷文字、数学公式、图表、选项和子题结构
- 对照重新排序过的参考 PPT 修正来源标签
- 只移除题目最外侧的旧主序号，保留 `A/B/C/D`、`a/b/c` 和 `i/ii/iii`
- 每个 Chapter 从 `1.` 开始重新编号
- 按 Chapter 和题型整理题目
- 在不裁切题目的前提下进行节省纸张的紧凑排版
- 将来源标签放在题目右下方的预留区域，避免覆盖题目
- 使用题目哈希、PPT 媒体关系和 OCR 进行身份匹配
- 通过数量对账、局部差异、越界、重叠、PPTX 完整性和逐页渲染检查进行 QA
- 支持多智能体或多进程分批处理，并由独立 supervisor 复核

这不是单纯的“PPT 排序脚本”。Skill 会指导 Codex 完成提取、匹配、清理、排版和质量检查。仓库中的 Python 脚本是基础构建工具，复杂项目仍应按照 `SKILL.md` 的完整流程执行。

## 安装

### 方法一：让 Codex 安装

在 Codex 中输入：

```text
请从 https://github.com/q2edu/Exam-PPT-Sorter-Skill 安装 exam-ppt-sorter Skill。
```

安装完成后，在下一个任务中即可使用。

### 方法二：使用 Git 安装

Windows PowerShell：

```powershell
$codexHome = if ($env:CODEX_HOME) { $env:CODEX_HOME } else { Join-Path $HOME ".codex" }
git clone https://github.com/q2edu/Exam-PPT-Sorter-Skill.git `
  (Join-Path $codexHome "skills\exam-ppt-sorter")
```

macOS 或 Linux：

```bash
CODEX_HOME="${CODEX_HOME:-$HOME/.codex}"
git clone https://github.com/q2edu/Exam-PPT-Sorter-Skill.git \
  "$CODEX_HOME/skills/exam-ppt-sorter"
```

如果目标文件夹已经存在，请先备份或更新现有安装，不要直接覆盖正在使用的版本。

安装或更新后，重新打开 Codex，或在下一个任务中调用该 Skill。

## 使用方法

在请求中明确调用：

```text
$exam-ppt-sorter
```

然后附上源 PPTX、参考 PPTX 和具体要求。

### 示例：原位清理图片

```text
使用 $exam-ppt-sorter 提取这个 PPTX 里的题目图片，去除手写答案、圈选、阴影和扫描噪点，保留全部印刷文字与公式，然后原位替换图片。不要改变图片的位置、大小、裁剪和 PPT 页数。
```

### 示例：修正来源标签

```text
使用 $exam-ppt-sorter 对照新版参考 PPT 修正最终版的来源标签。参考 PPT 已经重新排版，不要按页码匹配；请通过图片哈希、媒体关系或 OCR 匹配题目，并确保只有预期标签发生变化。
```

### 示例：制作学生教材

```text
使用 $exam-ppt-sorter 把题目按 Chapter 整理成学生教材。移除旧主序号，每章重新从 1 编号；标签放在题目右下角；一页尽量放多题，但不要裁题、拆题或过度缩小。完成全部对账和逐页 QA 后再交付。
```

### 示例：启用并行处理

```text
使用 $exam-ppt-sorter 创建多个处理智能体分批清理图片，并安排独立 supervisor 检查遗漏、公式变化、旧题号残留和错误标签。定期报告已完成数量和预计完成时间。
```

## 推荐工作流程

1. 审计源文件并记录题目、图片、标签和页面数量。
2. 提取题目并建立稳定的身份清单。
3. 对代表性图片进行小批量清理测试。
4. 批量清理并检查印刷内容是否保持不变。
5. 对照参考资料修正章节、题型和来源标签。
6. 安全移除旧主序号并按章节重新编号。
7. 在不裁切完整题目的前提下进行紧凑排版。
8. 完成结构检查、差异审计和逐页视觉复核。
9. 保留源文件，输出一个名称清晰的新 PPTX。

## 基础构建脚本

当分类 JSON 和题目图片已经准备好，并且基础标签带布局符合需求时，可以运行：

```powershell
python scripts\build_sorted_exam_ppt.py `
  --input-json question-classification.json `
  --out sorted-by-chapter.pptx `
  --source-id "paper-1" `
  --mcq-ranges "1-20,41-60"
```

Python 依赖：

```bash
pip install Pillow python-pptx
```

该脚本不代替图片清理、题目身份匹配、旧题号安全删除、复杂紧凑排版或完整视觉 QA。

## 更新

Windows PowerShell：

```powershell
$codexHome = if ($env:CODEX_HOME) { $env:CODEX_HOME } else { Join-Path $HOME ".codex" }
git -C (Join-Path $codexHome "skills\exam-ppt-sorter") pull
```

macOS 或 Linux：

```bash
git -C "${CODEX_HOME:-$HOME/.codex}/skills/exam-ppt-sorter" pull
```

## 重要说明

- 始终保留源 PPTX，并输出新的文件。
- AI 图片清理可能重新绘制文字或公式，因此必须先测试代表性图片并逐图检查。
- 参考 PPT 经过重新排版后，不应按页码转移标签。
- 放不下的题目应移到下一页，不应为了节省一页而裁掉题目内容。
- 缩略接触表只能用于导航，最终检查必须查看完整分辨率页面。

完整规则请查看 [`SKILL.md`](SKILL.md)。
