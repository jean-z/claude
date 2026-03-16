---
name: gen-commit
description: Analyze code changes and generate structured commit messages following project conventions
userInvocable: true
---

# Generate Commit Message

Analyze code changes and generate a structured commit message following project conventions.

## Usage

```
/gen-commit [target]
```

**Targets:**
- (no argument) - Analyze current uncommitted changes (`git diff`)
- `last` or `HEAD` - Analyze the most recent commit
- `<commit-hash>` - Analyze a specific commit (e.g., `abc123`)

## Instructions

You are a commit message generator. Analyze the code changes and generate a commit message following these steps:

### Step 1: Gather Change Information

Based on the target argument, execute the appropriate git commands:

```bash
# For uncommitted changes (default, no argument)
git diff --stat
git diff

# For last commit (argument: "last" or "HEAD")
git show HEAD --stat
git show HEAD --format="" -p

# For specific commit (argument: commit hash like "abc123")
git show <commit-hash> --stat
git show <commit-hash> --format="" -p
```

### Step 2: Analyze Changes

Analyze the diff content to identify:

1. **Change Type** (choose one):
   - `feat` - New feature
   - `fix` - Bug fix
   - `pref` or `perf` - Performance improvement
   - `refactor` - Code refactoring
   - `style` - Code style changes
   - `docs` - Documentation changes
   - `test` - Adding or modifying tests
   - `chore` - Build process, dependencies, etc.

2. **Scope** (optional):
   - Module/component name affected (e.g., `Chat`, `健康检测`, `智慧屏`)
   - Can be omitted if changes span multiple areas

3. **Change Points**:
   - What specific code/logic was modified
   - Key functions or components affected

4. **Impact Points** (必须详细):
   - 列出所有可能受影响的功能模块和具体页面
   - 格式：`功能模块 > 具体页面/组件`
   - 例如：
     - `健康检测 > 舌诊拍照页面`
     - `商城首页 > 商品列表组件`
     - `个人中心 > 会员信息页面`
   - Potential side effects
   - User-visible changes

### Step 3: Generate Commit Message

Follow this project's commit message format:

```
<type>(<scope>): <brief description>

- <detailed change point 1>
- <detailed change point 2>

影响点：
- <功能模块> > <页面/组件>（影响说明）
- <功能模块> > <页面/组件>（影响说明）
```

**Format Rules:**
- Title line: max 72 characters if possible
- Use Chinese for descriptions (matching project style)
- Include requirement IDs if visible (e.g., `35002 【前端 | APP】`)
- Separate multiple points with `-` on new lines
- **必须包含影响点部分**，列出所有可能受影响的功能模块和页面

### Step 4: Output Format

Output the analysis in this structure:

```markdown
## 📊 改动分析

### 改动文件
| 文件 | 改动类型 | 说明 |
|------|----------|------|

### 改动点
- 改动点1
- 改动点2

### 影响点（详细列出可能受影响的功能页面）
| 功能模块 | 页面/组件 | 影响说明 |
|----------|-----------|----------|
| 模块A | 页面a | 影响描述 |
| 模块B | 组件b | 影响描述 |

---

## 📝 生成的 Commit Message

```
<type>(<scope>): <description>

- <detail 1>
- <detail 2>

影响点：
- <功能模块> > <页面/组件>（影响说明）
```

---

## 💡 建议的完整提交命令

```bash
git add <files>
git commit -m "$(cat <<'EOF'
<generated commit message>
EOF
)"
```
```

### Examples

**Example 1 - Bug Fix:**
```
fix(健康检测): 修复 syndromes 空值问题及舌诊术语调整

- ProfilePicture 组件使用可选链访问 syndromes，避免空值报错
- 舌诊术语调整："舌面照片"→"舌正面照片"，"舌下照片"→"舌反面照片"

影响点：
- 健康检测 > 舌诊拍照页面
- 健康检测 > 面诊结果展示组件
```

**Example 2 - Feature with details:**
```
feat(智慧屏): 优化注册流程

- 抽取 BottomView 组件复用于注册成功弹框底部按钮视图
- 开卡弹框 cardTypeName 增加判空处理
- 新增 goHome/navigateHome 方法统一处理跳转逻辑

影响点：
- 智慧屏 > 注册流程页面
- 智慧屏 > 开卡弹框组件
```

**Example 3 - Performance:**
```
pref: 多余判断去除

- 系统判断多余了，因为 isYofoDevice()的判断已经包含了系统判断

影响点：
- 全局 > 设备类型判断逻辑
```

## Important Notes

1. Always read the actual diff content before generating
2. Consider the context and business logic, not just syntax changes
3. If changes are too complex, suggest splitting into multiple commits
4. Match the existing commit style in the project
5. Use Chinese for descriptions to match project conventions
6. **影响点是必填项**，必须详细列出所有可能受影响的功能模块和页面
