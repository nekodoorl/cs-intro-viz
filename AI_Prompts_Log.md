# AI 结对编程记录（AI_Prompts_Log）

> 本文件记录团队使用 AI 编程助手（DeepSeek / Cursor / Copilot 等）开发过程中的**代表性对话**。
> 每组至少记录 **3 次**，要求写清：提示词原文 → AI 原始输出 → 人工介入与修改。
> 评分重点：能否识别 AI 的错误（幻觉、逻辑漏洞、过时 API），而不是盲目复制。

---

## 记录规范

| 字段 | 说明 |
|---|---|
| 日期 / 成员 | 谁在什么时候问的 |
| 使用工具 | DeepSeek 网页版 / Cursor / Copilot …… |
| 提示词 | **原文照抄**，不要事后美化 |
| AI 原始输出 | 关键片段照抄，长代码可截取核心部分 |
| 人工修改 | 改了什么、为什么改、验证结果 |
| 收获 | 一句话总结这次对话学到了什么 |

---

## 记录 1：生成界面骨架

**日期**：2026-11-XX　**成员**：成员C　**工具**：DeepSeek 网页版

### 提示词（原文）

> 你是一位有 10 年经验的 Python GUI 开发者。请用 Tkinter 写一个 800x600 的窗口，顶部有一个输入框（输入十进制数）、两个下拉框（源进制、目标进制）、一个「开始演示」按钮；中部是一个表格，用来逐行显示转换步骤；底部有「上一步 / 下一步 / 自动播放」三个按钮。
>
> 约束：
> 1. 代码模块化，界面和逻辑分离，不要把所有代码写在一个函数里；
> 2. 所有控件加中文注释；
> 3. 窗口可调整大小，表格随窗口缩放；
> 4. 不要使用任何第三方库。

### AI 原始输出（关键片段）

```python
# AI 给出的表格实现
from tkinter import ttk

self.tree = ttk.Treeview(self.frame, columns=("step", "expr", "q", "r"), show="headings")
for col in ("step", "expr", "q", "r"):
    self.tree.heading(col, text=col)
self.tree.pack(fill="both", expand=True)
```

### 人工介入与修改

- **问题 1**：AI 用了 `columns=("step",...)` 但表头文字直接用了英文列名，界面上显示 `step/expr/q/r`。
  **修改**：改成中文表头 `序号 / 算式 / 商 / 余数`。
- **问题 2**：AI 把「自动播放」写成了 `while True: ... time.sleep(0.8)`，直接卡死主线程，窗口无响应。
  **修改**：改用 `root.after(800, callback)` 递归调度。
- **问题 3**：`pack`/`grid` 混用导致布局错乱。
  **修改**：统一改为 `grid`。

### 验证结果

界面可正常显示，缩放窗口时表格跟随拉伸，自动播放不再卡死。

### 收获

AI 给的 GUI 代码「能跑」不等于「能用」——**主线程阻塞**和**布局管理器混用**是它最常犯的两个错，必须自己判断。

---

## 记录 2：实现转换算法

**日期**：2026-11-XX　**成员**：成员B　**工具**：DeepSeek 网页版

### 提示词（原文）

> 用 Python 实现函数 `to_base(n: int, base: int) -> list[dict]`：把十进制非负整数 n 转成 base 进制，返回**每一步**的记录，每步包含被除数、商、余数，最后结果用字符串表示（10 以上用 A-F）。
>
> 要求：
> - 纯函数，不依赖任何界面代码；
> - 处理 base 在 2~16 之间，n = 0 时返回单步结果 "0"；
> - 加中文注释和类型标注。

### AI 原始输出（关键片段）

```python
def to_base(n, base):
    steps = []
    while n > 0:
        q, r = divmod(n, base)
        steps.append({"dividend": n, "quotient": q, "remainder": r})
        n = q
    digits = "0123456789ABCDEF"
    result = "".join(digits[s["remainder"]] for s in steps)
    return steps, result
```

### 人工介入与修改

- **问题**：`n = 0` 时 `while` 不执行，返回空字符串 `""`，与需求不符。
  **修改**：循环前加 `if n == 0: return [{"dividend":0,"quotient":0,"remainder":0}], "0"`。
- **问题**：结果拼接用的是 `steps` 正序，但除基取余需要**逆序**，AI 只是「碰巧」在示例里正确（因为字符串拼接顺序问题被掩盖）。
  **修改**：改为 `reversed(steps)`，并补充单元测试 `assert to_base(156,2)[1] == "10011100"`。
- **问题**：没做 base 范围校验。
  **修改**：加 `if not 2 <= base <= 16: raise ValueError(...)`。

### 验证结果

`156 → 2` 得 `10011100`，`255 → 16` 得 `FF`，`0 → 2` 得 `0`，全部通过。

### 收获

边界值（`n=0`）和顺序（逆序）是最容易出错的地方，**写测试比读代码快**。

---

## 记录 3：修复报错

**日期**：2026-11-XX　**成员**：成员D　**工具**：Cursor

### 提示词（原文）

> 运行时报错：
> ```
> TypeError: 'NoneType' object is not subscriptable
>   File "core/steps.py", line 42, in build_note
>     return self.cache[key]["text"]
> ```
> 我的 `cache` 初始化写的是 `self.cache = {}`，key 是 `(n, base)` 元组。帮我定位原因，并给出最小修改方案，不要重写整个文件。

### AI 原始输出（关键片段）

> 原因：`dict.get` 返回值可能为 `None`，你直接下标访问了。建议改为：
> ```python
> if key not in self.cache:
>     self.cache[key] = self._compute(key)
> return self.cache[key]["text"]
> ```

### 人工介入与修改

- **AI 判断正确**：`cache` 命中失败返回 `None`，随后下标访问崩溃。
- **人工补充**：AI 建议直接 `self._compute(key)`，但该方法会抛异常。改为先判空并记录日志，避免缓存污染。
- **人工发现 AI 漏掉的一点**：`key` 在别处用了 `list` 而非 `tuple`，不可哈希，会另报 `TypeError: unhashable type`。一并修正。

### 验证结果

重复点击「开始演示」不再崩溃，日志显示缓存命中率提升。

### 收获

AI 擅长「就错论错」，但**不擅长发现同一处的第二个隐患**——修复后要自己再压测一遍。

---

## 汇总统计

| 指标 | 数值 |
|---|---|
| 记录对话数 | 3 |
| AI 输出可直接使用的比例 | 约 40% |
| 需要人工修改的问题类型 | 边界条件 3 次、主线程阻塞 1 次、顺序错误 1 次、异常处理 1 次 |
| 最大收获 | 提示词写得越具体（约束、输入输出、反例），返工越少 |
