---
name: amend-push
description: 将当前改动合并到上一次提交并强制推送到远端
---

# /amend-push

将工作区和暂存区的改动合并到上一次 commit，然后 `git push --force-with-lease` 推送到远端。

## 执行步骤

### 1. 检查改动

```bash
git status --short
git diff --stat
```

如果没有改动，提示用户并退出。

### 2. 确认上一次提交

```bash
git log -1 --oneline
```

向用户展示将要 amend 的 commit 和当前改动文件列表，确认后继续。

### 3. 暂存并合并代码

```bash
git add -A
git commit --amend --no-edit
```

先将改动合并到上一次 commit，`--no-edit` 保持原 commit message 不变。

### 4. 重新生成 commit message

执行 `/gen-commit last` 分析合并后的完整改动，生成新的 commit message。

拿到生成结果后，用 `--amend` 只更新 commit message：

```bash
git commit --amend -m "$(cat <<'EOF'
<生成的 commit message>
EOF
)"
```

如果用户对生成的 message 没有异议，直接使用；如果用户想保留原 message，跳过此步骤。

### 5. 推送

```bash
git push --force-with-lease
```

### 6. 同步更新 MR

推送成功后，检查当前分支是否有关联的开放 MR，如果有则同步更新标题和描述：

```bash
# 查找关联 MR
glab mr list --source-branch "<当前分支>"
```

如果有开放 MR，用 commit message 的标题和描述更新 MR：

```bash
glab mr update <MR_ID> --title "<commit message 标题>" --description "<commit message 描述>"
```

如果没有关联 MR，跳过此步骤。

### 7. 输出结果

显示 amend 后的 commit、推送结果和 MR 更新状态。

## 异常

- 没有改动 → 提示无需操作
- 不是 amend 的作者 → 警告用户
- push 失败 → 展示错误信息，提示远端有新提交需要先 pull
- MR 更新失败 → 提示手动更新，不影响推送结果
