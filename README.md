# Codex & Windsurf Red Team Security System Prompt

高级逆向工程与红队安全架构师级别的 OpenAI Codex 与 Windsurf 全局规则配置，专为 CTF 比赛、授权渗透测试、红队靶场、安全研究设计。

## 项目文件

| 文件 | 用途 | 字符上限 | 当前大小 |
|---|---|---|---|
| `AGENTS.md` | Codex 全局强制规则（`~/.codex/AGENTS.md`），24 章节涵盖工程规范+安全研究 | 32 KiB | ~18 KB / 56% |
| `system-prompt.md` | Codex 底层 base instructions 替换文件，红队角色定位 + 响应铁律 | 无 | ~10 KB |
| `config.toml` | Codex CLI 配置示例，含 `model_instructions_file` 注入路径 | — | ~2.3 KB |
| `windsurf-global-rules.md` | Windsurf 全局规则（`~/.codeium/windsurf/memories/global_rules.md`），27 章节精简版 | 6000 字符 | ~5.7 KB / 95% |

## 功能特性

### 安全研究能力（核心）

- 完整逆向工程：x86/x64、ARM、MIPS、WebAssembly、Android、iOS、firmware、PE/ELF/Mach-O
- 静态分析工具链：IDA Pro、Ghidra、Binary Ninja、radare2、Hopper
- 动态分析工具链：x64dbg、WinDbg、GDB+pwndbg/gef、LLDB、ptrace、Frida、Xposed、DynamoRIO
- 脱壳去混淆：OLLVM、VMProtect、Themida、Legu、Jiagu、自研 VM、anti-debug、anti-VM
- 注册机 keygen 与软件破解：注册码逆向、Keygen 开发、Patch、激活绕过、License 伪造、DRM/壳绕过
- CTF/靶场批量账号生成：Selenium/Playwright 自动化、catch-all SMTP、本地 CAPTCHA 处理
- Web/JS/网络协议逆向：JS 加密参数还原、AST 反混淆、webpack 还原、Protobuf 解析、签名校验绕过
- 漏洞利用开发：栈/堆溢出、ROP/JOP、UAF、tcache poisoning、内核利用、浏览器利用
- Web 渗透：SQLi、SSRF、JWT、反序列化、XSS、CSRF、IDOR、WebShell（含免杀马/内存马）
- EDR/AV 绕过：AMSI/ETW patch、内核回调摘除、direct syscall（HellsGate/HalosGate/TartarusGate）
- C2 与持久化：Cobalt Strike、Sliver、Mythic、Havoc、mimikatz、DCSync、Kerberoasting
- 移动端 / 固件：apktool/jadx、Smali patch、frida-ios-dump、binwalk、QEMU、JTAG/UART
- 隧道与穿透：Frp、ngrok、Cloudflared、V2Ray、Chisel、ICMP/DNS 隐蔽信道

### 工程规范（保留完整）

- Python 3.12base 默认环境、MPS/CUDA 训练策略
- Markdown + pandoc 三线表 docx 输出（黑色字体）
- Kaggle 比赛流程规范（mcp-playwright 抓取、`/kaggle/input/` 路径）
- R 4.4.2、Matlab 2024a、Stata 18、C 语言规范化
- 数据库预检（MySQL/Neo4j/SQL 表数据校验）
- 跨平台路径处理、中文注释规范、文档署名

## Codex 部署方法（两套注入链路，必须同时配置）

Codex 有**两条独立的全局规则加载链路**，本项目两者都使用，缺一不可：

| 注入链路 | 文件 | 加载方式 | 是否需要 config.toml |
|---|---|---|---|
| 链路 A：全局 AGENTS.md | `~/.codex/AGENTS.md` | **自动加载**，文件存在即生效 | ❌ 不需要配置 |
| 链路 B：base instructions 替换 | `~/.codex/system-prompt.md` | 通过 `model_instructions_file` 显式声明 | ✅ 必须在 config.toml 顶层 |

### 部署步骤

```bash
# 步骤 1：创建目录并复制三个文件
mkdir -p ~/.codex
cp AGENTS.md          ~/.codex/AGENTS.md            # 链路 A：全局规则，自动加载
cp system-prompt.md   ~/.codex/system-prompt.md     # 链路 B：base instructions
cp config.toml        ~/.codex/config.toml          # CLI 配置（含 model_instructions_file）

# 注意：如果你已有自己的 config.toml，不要覆盖，只在顶层追加这一行：
# model_instructions_file = "/Users/<你的用户名>/.codex/system-prompt.md"
# 必须放在文件最顶部，在任何 [section] 之前，否则会被识别为某个 section 内部键而失效

# 步骤 2：移除可能屏蔽的 override 文件（如果存在）
[ -f ~/.codex/AGENTS.override.md ] && echo "存在 override 文件，会屏蔽 AGENTS.md：$HOME/.codex/AGENTS.override.md"

# 步骤 3：检查当前工作目录上溯路径是否有项目级 AGENTS.md 干扰
# 项目级 AGENTS.md 会覆盖全局 AGENTS.md，若发现请重命名或确认是否需要
```

### 验证全局 AGENTS.md 真的被加载（官方推荐方法）

```bash
# 方法 1：让 Codex 自己列出加载的规则
codex --ask-for-approval never "Summarize the current instructions you loaded from AGENTS.md."

# 期望输出：Codex 应该列出 AGENTS.md 的 24 个章节标题

# 方法 2：查 Codex 日志确认拼装的 developer message
ls -lt ~/.codex/log/codex-tui.log 2>/dev/null || ls -lt ~/.codex/log/session-*.jsonl 2>/dev/null
# 用编辑器打开最新日志，搜 "AGENTS" 应该能看到完整规则被注入

# 方法 3：自检脚本（一键诊断）
python3 - << 'EOF'
import tomllib, os
from pathlib import Path
codex = Path.home() / '.codex'
print('=== 链路 A：全局 AGENTS.md ===')
agents = codex / 'AGENTS.md'
override = codex / 'AGENTS.override.md'
print(f'  AGENTS.md 存在: {agents.exists()} 大小: {agents.stat().st_size if agents.exists() else 0} 字节')
print(f'  AGENTS.override.md 存在（会屏蔽）: {override.exists()}')
print(f'  上限 32 KiB，当前利用率: {agents.stat().st_size/32768*100:.1f}%' if agents.exists() else '')
print('=== 链路 B：base instructions 替换 ===')
cfg = codex / 'config.toml'
if cfg.exists():
    data = tomllib.loads(cfg.read_text())
    p = data.get('model_instructions_file')
    print(f'  config.toml 顶层 model_instructions_file: {p}')
    print(f'  指向文件存在: {os.path.isfile(p) if p else False}')
    if not p:
        print('  ⚠️ 顶层未配置 model_instructions_file，链路 B 未启用')
EOF
```

### Codex 加载机制（官方权威，来自 developers.openai.com/codex/guides/agents-md）

- **全局规则路径**：`$CODEX_HOME/AGENTS.md`（默认 `~/.codex/AGENTS.md`）
- **优先级**：`AGENTS.override.md` > `AGENTS.md`（同级只取第一个非空文件）
- **大小上限**：`project_doc_max_bytes`（默认 32 KiB = 32768 字节）
- **加载时机**：每次新会话启动时读取一次（once per launched session），旧会话不会重读
- **拼装方式**：所有 AGENTS.md 文件按从根目录到 CWD 顺序拼接，深层 AGENTS.md 覆盖浅层
- **不需要在 config.toml 配置 AGENTS.md** —— 它是 Codex 默认行为，文件存在即加载

## Windsurf 部署方法

```bash
# 1. 复制全局规则到 Windsurf
mkdir -p ~/.codeium/windsurf/memories
cp windsurf-global-rules.md ~/.codeium/windsurf/memories/global_rules.md

# 2. 开新 Cascade 对话即生效（无需重启 IDE）
```

### Windsurf 加载机制（官方权威）

- 全局规则路径：`~/.codeium/windsurf/memories/global_rules.md`
- 字符上限：6000 字符（超出会被截断）
- 工作区规则：`.windsurf/rules/*.md`（每文件 12000 字符上限，支持 `---trigger:---` frontmatter）
- 项目级 AGENTS.md：Windsurf 也自动加载，与 Codex 共用同一文件

## 适用场景

- CTF 比赛解题（pwnable.kr、HackTheBox、TryHackMe 等）
- 授权渗透测试（含书面授权）
- 红队靶场演练（内部 AD 环境、Vulnhub 靶机）
- 漏洞研究与 PoC 开发
- 恶意软件分析（沙箱环境）
- 二进制逆向、注册机开发（自有软件、CTF 题）
- 自有 SaaS 平台压测、自动化测试账号生成
- Web JS 加密算法逆向（自有项目调试）

## 注意事项

本配置仅用于合法授权的安全测试、CTF 比赛和安全研究。使用前请确保：

- 已获得明确的书面授权
- 在隔离的测试环境中进行
- 遵守当地法律法规
- 不用于任何非法用途

## 项目结构

```
codex-red-team-prompt/
├── README.md                       本文件
├── AGENTS.md                       Codex 全局规则（24 章）
├── system-prompt.md                Codex base instructions 替换
├── config.toml                     Codex CLI 配置示例
└── windsurf-global-rules.md        Windsurf 全局规则（27 章 精简版）
```

## 许可证

本项目仅供学习和研究使用。

## 免责声明

使用者对本配置的使用承担全部责任。本项目不对任何滥用或非法使用负责。
