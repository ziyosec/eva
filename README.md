<p align="center">
  <img src="./assets/readme-hero.svg" alt="EVA hero banner" width="100%" />
</p>

<p align="center">
  <strong>如果一个智能体的执行层小到只是一个脚本，那它具有病毒传播一样的潜力。</strong>
</p>

<p align="center">
  EVA 是一个极致轻量、可接本地模型、带安全审查与目录级 Session 的单文件 Agent。
</p>

<p align="center">
  <a href="https://github.com/usepr/eva/stargazers">
    <img src="https://img.shields.io/github/stars/usepr/eva?style=flat-square" alt="GitHub stars" />
  </a>
  <a href="https://github.com/usepr/eva/forks">
    <img src="https://img.shields.io/github/forks/usepr/eva?style=flat-square" alt="GitHub forks" />
  </a>
  <a href="https://github.com/usepr/eva/commits">
    <img src="https://img.shields.io/github/last-commit/usepr/eva?style=flat-square" alt="Last commit" />
  </a>
  <a href="./LICENSE">
    <img src="https://img.shields.io/badge/license-MIT-0f766e?style=flat-square" alt="License" />
  </a>
  <img src="https://img.shields.io/badge/agent-single--file-1d4ed8?style=flat-square" alt="Single file agent" />
  <img src="https://img.shields.io/badge/session-directory--scoped-f97316?style=flat-square" alt="Directory scoped sessions" />
</p>

<p align="center">
  <a href="#快速开始"><strong>Quick Start</strong></a> ·
  <a href="./showcase"><strong>Showcase</strong></a> ·
  <a href="./showcase/wechat-bot/README.md"><strong>WeChat Bot</strong></a> ·
  <a href="#关于-evamd"><strong>EVA.md</strong></a> ·
  <a href="./README_ENG.md"><strong>🇬🇧 English</strong></a>
</p>

## 简介

EVA是个麻雀虽小、五脏俱全的Agent智能体，相当于低配版Claude Code，能帮你写脚本、写测试案例、执行shell、分析数据等。我自己就是EVA的重度用户，日常处理各种任务。

各种好玩案例见当前仓库的 [showcase](./showcase) 🦖🦖🦖

## 特性

- 本地化：可以接入本地部署的OpenAI接口模型，如vLLM，或者是外网模型
- 极致轻量化：单文件，仅一个`eva.py`，有python就能运行
- 目录级Session：下次同样目录启动会延续之前对话
- 安全审查：默认只执行读命令，其他命令需要安全确认
- 移植性：很容易将EVA接入你现有的自动化流程，例如：`eva -au '计算100w以内所有素数和并写到/tmp/result.txt'`。当前就借助`-asu`选项将EVA接入了微信Bot


## 快速开始

0. 直接创建一个eva.py并复制本仓库的eva.py文本内容粘贴进去（docker环境、运维环境等也很容易粘贴代码，无需复杂安装，Just **Paste and Go**）。当然，你也可以git clone本仓库。

1. 在终端执行`export EVA_API_KEY=你的deepseek API key`（Windows系统则是`set`命令）

EVA支持OpenAI接口形式的LLM，可以是Ollma、vLLM拉起的本地模型，也可以是DeepSeek、OpenAI等官网API。切换方法是设置`EVA_BASE_URL`, `EVA_MODEL_NAME`, `EVA_API_KEY`这三个环境变量：

Linux设置方法：

```bash
export EVA_BASE_URL=http://xxxxxxxxx/v1
export EVA_MODEL_NAME=xxxxx
export EVA_API_KEY=sk-xxxxx
```

macOS 设置方法（zsh，如需在 macOS 上长期生效，可以将上述 export 配置写入`~/.zshrc`。）：

```bash
export EVA_BASE_URL=http://xxxxxxxxx/v1
export EVA_MODEL_NAME=xxxxx
export EVA_API_KEY=sk-xxxxx
```

Windows 命令行设置方法：

```cmd
set EVA_BASE_URL=http://xxxxxxxxx/v1
set EVA_MODEL_NAME=xxxxx
set EVA_API_KEY=sk-xxxxx
```

Windows PowerShell设置方法：

```powershell
$env:EVA_BASE_URL="http://xxxxxxxxx/v1"
$env:EVA_MODEL_NAME="xxxxx"
$env:EVA_API_KEY="sk-xxxxx"
```

2. 运行`python3 eva.py`。首次运行会生成`eva`脚本，Linux 下执行`source ~/.bashrc`让脚本生效；macOS 下执行`source ~/.zshrc`让脚本生效。后续直接输入命令`eva`即可

输入增强说明：

- 如果环境里安装了`prompt_toolkit`，EVA会自动开启多行输入：`Enter`提交，`Ctrl+N`换行；如果终端支持，也可以用`Alt+Enter`换行

输出增强说明：

- 如果环境里安装了`rich`，EVA会自动开启rich美化输出

```python
eva支持的选项：
usage: eva.py [-h] [-a] [-l] [-c] [-u USER_ASK] [-s] [-g]

options:
  -h, --help            show this help message and exit
  -a, --allow-all       允许所有命令无需用户确认即可执行
  -l, --list-session    列出所有session
  -c, --clear-session   清除当前目录session
  -u USER_ASK, --user-ask USER_ASK
                        独立地针对一条用户提问执行EVA
  -s, --with-session    搭配-u使用，载入并保存session
  -g, --goal            goal模式，循环直到达成目标
```

绝大部分同学都带上-a来启动eva，虽然很方便，但要对eva行为多加关注下。

## EVA退出说明（按Ctrl + C）

1、EVA运行过程可以随时打断，无论是打断LLM推理、打断工具执行、还是退出EVA，都是按 Ctrl + C

2、打断是个很有用的行为，比如某个命令超时时间太久你不想再等待，或者你想起有个背景忘记向EVA澄清需要补充说明，或者你发现前面对话有错别字需要重新问。

3、打断后，可以继续输入新的问题，不需要重新启动EVA；如果想完全退出EVA，连续按两下Ctrl + C

## 关于 EVA.md

`.eva/EVA.md`是EVA的设定，通过它你可以对EVA进行各种设定、甚至赋予它各种技能。除了手动编辑`EVA.md`，你可以在eva启动时让它"分析下xxx/xxx/skills目录中所有的文件，给我生成一份EVA.md"，EVA会自动生成设定内容。

## 贡献者 ✨

<a href="https://github.com/usepr/eva/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=usepr/eva" />
</a>
