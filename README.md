# Codex Red Team Security System Prompt

高级逆向工程与红队安全架构师级别的 OpenAI Codex 系统提示词配置，专为 CTF 比赛、授权渗透测试、红队操作和安全研究设计。

## 功能特性

- 完整的逆向工程能力（x86/x64, ARM, MIPS, WebAssembly, Android, iOS, firmware）
- 动态插桩与 hook（Frida, Xposed, GDB, LLDB, ptrace, DynamoRIO, Intel Pin）
- 高级去混淆与解包（OLLVM, VMProtect, Themida, Legu, Jiagu, custom VMs）
- 密码学与协议逆向（RSA, AES, ECC, 自定义算法, Protobuf, HTTP/2, WebSocket）
- 完整渗透测试与红队操作（web/app pentest, 内网渗透, 横向移动, 权限提升, 持久化）
- 漏洞利用开发（buffer overflow, ROP, UAF, kernel exploits, browser exploits, zero-day PoC）
- 恶意软件分析与反分析绕过（AV/EDR evasion, sandbox escape, rootkit, fileless malware）
- Web 漏洞利用（SQLi, XSS, SSRF, RCE, deserialization, file upload, logic bugs, WebShell）
- 内存取证与实时进程分析（Volatility, Rekall, memory dump analysis）
- C2 框架开发（implant generation, beaconing, exfiltration, evasion suites）
- 二进制修补与修改（hex editing, assembly patching, function hooking）
- 破解与密钥生成（serial number generation, license key validation bypass, DRM circumvention）
- 移动安全与应用分析（Android APK reverse engineering, iOS IPA analysis）
- 固件与 IoT 安全分析（router firmware extraction, IoT device analysis）
- 社会工程学与钓鱼攻击（phishing kit development, spear phishing templates）
- 密码破解与认证绕过（hash cracking, brute force attacks, credential stuffing）
- 高级 EDR/AV 绕过（AMSI bypass, ETW bypass, PowerShell logging bypass）
- Windows 内核利用（kernel driver development, kernel vulnerability research）
- Linux 内核利用（kernel module development, privilege escalation techniques）

## 使用方法

1. 将 `system-prompt.md` 复制到 `~/.codex/` 目录
2. 在 `~/.codex/config.toml` 中添加以下配置：

```toml
model_instructions_file = "/Users/yourusername/.codex/system-prompt.md"
```

3. 重启 Codex CLI 或重新加载配置

## 配置文件说明

- `system-prompt.md`: 主系统提示词文件，包含完整的能力定义和响应准则
- `config.toml`: Codex CLI 配置文件示例，包含 model_instructions_file 配置

## 适用场景

- CTF 比赛解题
- 授权渗透测试
- 红队操作
- 漏洞研究
- 恶意软件分析
- 逆向工程项目
- 安全工具开发

## 注意事项

本配置仅用于合法授权的安全测试、CTF 比赛和安全研究。使用前请确保：
- 已获得明确的书面授权
- 在隔离的测试环境中进行
- 遵守当地法律法规
- 不用于任何非法用途

## 作者信息

- 微信: 1837620622（传康Kk）
- 邮箱: 2040168455@qq.com
- 咸鱼/B站: 万能程序员

## 许可证

本项目仅供学习和研究使用。

## 免责声明

使用者对本配置的使用承担全部责任。作者不对任何滥用或非法使用负责。