# GitLab MR 代码审查

请对 MR 编号 $ARGUMENTS 进行全面的代码审查，并将问题以**行级评论**的形式提交到 GitLab。

## 执行步骤

1. **获取 MR 基本信息（包含 diff_refs）**
   ```bash
   glab api "projects/h5%2Fbiz-shopping-mobile/merge_requests/$ARGUMENTS"
   ```
   提取: `diff_refs.base_sha`, `diff_refs.head_sha`, `diff_refs.start_sha`

2. **获取代码改动**
   ```bash
   glab mr diff $ARGUMENTS
   ```

3. **获取现有评论**
   ```bash
   glab api "projects/h5%2Fbiz-shopping-mobile/merge_requests/$ARGUMENTS/notes"
   ```

## 提交行级评论

### 单行评论
```bash
glab api "projects/h5%2Fbiz-shopping-mobile/merge_requests/$ARGUMENTS/discussions" \
  -f "body=评论内容" \
  -f "position[base_sha]=$BASE_SHA" \
  -f "position[head_sha]=$HEAD_SHA" \
  -f "position[start_sha]=$START_SHA" \
  -f "position[position_type]=text" \
  -f "position[new_path]=文件路径" \
  -f "position[new_line]=行号"
```

### 多行块评论
```bash
glab api "projects/h5%2Fbiz-shopping-mobile/merge_requests/$ARGUMENTS/discussions" \
  -f "body=评论内容" \
  -f "position[base_sha]=$BASE_SHA" \
  -f "position[head_sha]=$HEAD_SHA" \
  -f "position[start_sha]=$START_SHA" \
  -f "position[position_type]=text" \
  -f "position[new_path]=文件路径" \
  -f "position[line_range][start][type]=new" \
  -f "position[line_range][start][line_number]=起始行号" \
  -f "position[line_range][end][type]=new" \
  -f "position[line_range][end][line_number]=结束行号"
```

### 提交总结评论
```bash
glab mr note $ARGUMENTS --message "总结评论内容"
```

## 审查流程

### 第一步：审查代码
请从以下维度进行审查：

| 维度 | 检查项 |
|------|--------|
| 代码质量 | 逻辑正确性、可读性、命名规范、重复代码 |
| 安全性 | 潜在漏洞、敏感信息泄露、输入验证 |
| 性能 | 性能影响、内存泄漏、不必要计算 |
| 最佳实践 | 项目规范、错误处理、边界条件 |

### 第二步：提交行级评论
对于发现的问题，**必须**使用行级评论提交到具体代码位置：

1. **精确定位** - 评论必须关联到具体的文件和行号
2. **清晰描述** - 说明问题所在和修改建议
3. **分类标记** - 使用前缀标识问题严重程度：
   - `[必须修复]` - 阻塞合并的问题
   - `[建议优化]` - 可选的改进建议
   - `[疑问]` - 需要作者澄清的问题

### 第三步：提交总结评论
所有行级评论提交后，使用 `glab mr note` 提交总结：

```markdown
## Code Review 完成

### 📊 审查结果
- 🔴 必须修复: X 个
- 🟡 建议优化: X 个
- 🔵 疑问待澄清: X 个

### ✅ 优点
列出做得好的地方

### 结论
- [ ] 可以合并
- [ ] 修复后可合并
- [ ] 需要重大修改
```

## 输出要求

### 第一阶段：审查报告（不提交）
输出完整的审查报告，**不要执行任何提交命令**。报告格式：

```markdown
## Code Review: MR {编号}

### 📋 基本信息
| 项目 | 内容 |
|------|------|
| 标题 | ... |
| 作者 | ... |
| 目标分支 | ... |

### 📝 改动概述
简要描述改动内容

### 📍 行级评论预览
以下评论将在确认后提交：

**1. js/Root.js:123**
> [必须修复] 问题描述...

**2. js/Root.js:150-155**
> [建议优化] 问题描述...

### ✅ 优点
列出做得好的地方

### 📊 审查结果
- 🔴 必须修复: X 个
- 🟡 建议优化: X 个
- 🔵 疑问待澄清: X 个

### 结论
- [ ] 可以合并
- [ ] 修复后可合并
- [ ] 需要重大修改
```

### 第二阶段：等待确认
报告输出后，**暂停并询问用户**：

> 审查报告已生成。是否将以上评论提交到 GitLab MR #XXX？
> - 输入 `y` 或 `确认` 提交所有评论
> - 输入 `n` 或 `取消` 不提交
> - 输入 `编辑` 修改评论内容

### 第三阶段：用户确认后提交
用户确认后，按顺序执行：
1. 逐个提交行级评论
2. 提交总结评论

**重要**:
- 使用中文输出所有内容
- 未确认前**禁止**执行任何 `glab api` 或 `glab mr note` 命令
- 每个问题都要标注具体的文件和行号
