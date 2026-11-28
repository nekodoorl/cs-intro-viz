# 进制转换步骤演示器

> 计算机科学导论课程大作业 · 4 人小组项目

一个桌面小程序，把十进制、二进制、八进制、十六进制之间的转换过程一步一步显示出来，看清每次「除以基数、取余数、倒序排列」到底发生了什么。

---

## 项目简介

课上老师写一遍「156 ÷ 2 = 78 余 0……」，抄下来容易，但余数为什么要倒着排常常说不清。

这个程序把每一步都列成表格：输入一个数和目标进制，逐行显示除法、商和余数，最后把结果拼出来，再按位权展开验算一遍。

---

## 功能特性

- 十进制转二进制 / 八进制 / 十六进制，逐行显示每一步的除法和余数
- 反向转换：二进制 / 八进制 / 十六进制转十进制，按位权展开
- 2–16 进制互转
- 单步控制：上一步、下一步、自动播放，当前行高亮
- 结果验算：算完自动展开校验，对错一眼看出来

---

## 运行环境

| 项目 | 要求 |
|---|---|
| 语言 | Python 3.10 及以上 |
| GUI 库 | Tkinter（Python 自带，不需要额外安装） |
| 操作系统 | Windows 10/11、macOS、Linux |
| 第三方依赖 | 无 |

---

## 安装与运行

```bash
# 克隆仓库
git clone https://github.com/nekodoorl/cs-intro-viz.git

# 进入目录
cd cs-intro-viz

# 运行
python main.py
```

> Windows 上如果 `python` 不识别，改用 `py main.py`。

---

## 项目结构

以下为计划的目录结构，当前完成到需求与设计阶段，代码目录随开发进度补上。

```
cs-intro-viz/
├── main.py                     # 程序入口（开发中）
├── ui/
│   ├── main_window.py          # 主窗口与布局（开发中）
│   └── widgets.py              # 步骤表格等控件（开发中）
├── core/
│   ├── converter.py            # 进制转换算法，纯逻辑，不依赖界面（开发中）
│   └── steps.py                # 每一步的说明文字（开发中）
├── docs/
│   ├── Project_Requirements_and_Design.md
│   └── images/wireframe.png    # 界面草图
├── AI_Prompts_Log.md           # AI 结对编程记录
├── .gitignore
├── LICENSE
└── README.md
```

---

## 界面预览

![界面草图](docs/images/wireframe.png)

图 1　主界面草图：上方输入参数，中间是步骤表格，下方是控制按钮和结果区

---

## 团队分工

| 姓名 | 学号 | 负责模块 | GitHub 用户名 |
|---|---|---|---|
| 梁钊源（组长） | 2610250234 | 需求文档、仓库管理、PR 审核、集成与演示 | @nekodoorl |
| 孙依晨 | 2026xxxx | 核心算法 `core/converter.py`、`core/steps.py` 与单元测试 | @member-b |
| 姜邵峰 | 2026xxxx | 界面 `ui/main_window.py`、`ui/widgets.py` 与运行截图 | @member-c |
| 余浩源 | 2610250226 | `README.md`、`AI_Prompts_Log.md`、演示 PPT 与验收测试 | @NorioTomokawa6767 |

---

## 开发流程

- `main` 分支受保护，不直接推送
- 每人从 `main` 拉自己的功能分支：`feature/xxx` 或 `docs/xxx`
- 完成后推送并发起 Pull Request，由组长查看后合并
- 至少一次合并冲突需要手动解决并记录

---

## 开源协议

本项目采用 [MIT License](LICENSE)。

---

## 贡献者

- 梁钊源 —— 项目管理、需求文档、集成与演示
- 孙依晨 —— 核心算法与单元测试
- 姜邵峰 —— 界面实现与联调
- 余浩源 —— README、AI 记录、测试与 PPT
