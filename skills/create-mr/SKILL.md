---
name: create-mr
description: 创建 GitLab MR，支持快速模式和交互模式
---

# /create-mr

| 用法 | 模式 | 说明 |
|------|------|------|
| `/create-mr` | 快速 | 当前分支 → 最新 release，直接创建 |
| `/create-mr <branch>` | 快速 | 当前分支 → 指定目标分支，直接创建 |
| `/create-mr -i` | 交互 | 选择源/目标分支，确认标题后创建 |

## 1. 检查并提交本地改动

在确定分支之前，先检查工作区是否有未提交的改动：

```bash
git status --porcelain
```

- **无改动** → 跳过，直接进入步骤 2
- **有改动** → 执行以下流程：
  1. 调用 `/gen-commit` 分析改动并生成 commit message
  2. 用户确认后执行提交
  3. 推送到远端：
     ```bash
     git push -u origin <current-branch>
     ```
     如果远端分支已存在且本地落后，先 pull 再 push：
     ```bash
     git pull --rebase origin <current-branch> && git push origin <current-branch>
     ```

## 2. 确定分支

- **源分支**：快速模式用当前分支；交互模式用 AskUserQuestion 让用户选择（当前分支 / 手动输入）
- **目标分支**：用户传入的参数，或自动检测：
```bash
git branch -r | grep -oP 'origin/release-v\d+\.\d+\.\d+$' | sort -V | tail -1 | sed 's|origin/||'
```
- 交互模式目标分支选项：最新 release / master / 手动输入

## 3. 生成标题和描述

```bash
git log -1 --format="%s" origin/<target>...<source>   # 标题：commit subject
git log -1 --format="%b" origin/<target>...<source>   # 描述：commit body
```

交互模式下需让用户确认或修改标题。

## 4. 检查重复并创建

```bash
# 检查是否已有开放 MR
glab mr list --source-branch "<source>" --target-branch "<target>"
```
- 无重复 → 直接创建
- 有重复 → 提示已有 MR 编号，询问关闭旧 MR 重建还是跳过

```bash
glab mr create --source-branch "<source>" --target-branch "<target>" --title "<title>" --description "<description>" --yes
```

## 5. 输出

MR 链接、源→目标分支、标题。

## 异常

- glab 未安装 → 提示 `brew install glab`
- 源=目标 → 提示重新选择
- 检测不到 release → 提示手动指定
