---
name: commit-push
description: 提交当前改动为新 commit 并推送到远端，同步更新关联 MR
---

# /commit-push

将工作区和暂存区的改动提交为新的 commit，然后 `git push` 推送到远端，并同步更新关联 MR。

## 执行步骤

### 1. 检查改动

```bash
git status --short
git diff --stat
```

如果没有改动，提示用户并退出。

### 2. 确认改动内容

向用户展示当前改动文件列表，确认后继续。

### 3. 生成 commit message

执行 `/gen-commit` 分析未提交的改动，生成 commit message。

如果用户对生成的 message 没有异议，直接使用；如果用户想修改，调整后使用。

### 4. 提交并推送

```bash
git add -A
git commit -m "$(cat <<'EOF'
<生成的 commit message>
EOF
)"
git push
```

### 5. 同步更新 MR

推送成功后，检查当前分支是否有关联的开放 MR，如果有则用最新 commit 的标题和描述更新 MR：

```bash
# 查找关联 MR
glab mr list --source-branch "<当前分支>"
```

如果有开放 MR，用最新 commit message 的标题和描述更新 MR：

```bash
glab mr update <MR_ID> --title "<commit message 标题>" --description "<commit message 描述>"
```

如果没有关联 MR，跳过此步骤。

### 6. 输出结果

显示新 commit、推送结果和 MR 更新状态。

## 异常

- 没有改动 → 提示无需操作
- push 失败 → 展示错误信息，提示远端有新提交需要先 pull
- MR 更新失败 → 提示手动更新，不影响推送结果
