# Email Agent (IMAP + 聚类 + DeepSeek)

通过 IMAP 拉取未读邮件，先以“主题-发件人-时间窗”规则预聚类，再用 TF‑IDF 余弦相似度细化合并，将线程交给 DeepSeek（OpenAI 兼容）生成优先级待办或小结，输出到终端与 Markdown。

## 安装

```bash
pip install -r requirements.txt
```

## 配置环境变量（PowerShell 示例）

```powershell
$env:IMAP_HOST="imap.example.com"#imap.gmail.com
$env:IMAP_PORT="993"
$env:IMAP_USER="your_email@example.com"#vetchzeng@gmail.com
$env:IMAP_PASSWORD="your_app_password"#qnax qxqg qfir tnre
$env:MAILBOX="INBOX"
$env:DEEPSEEK_API_KEY="sk-xxx"#sk-48e952c36b3b4dbaa07848a563f09046
# 可选：
# $env:DEEPSEEK_BASE_URL="https://api.deepseek.com"
# $env:DEEPSEEK_MODEL="deepseek-chat"
# $env:TIME_WINDOW_HOURS="72"
# $env:SIM_THRESHOLD="0.55"
# $env:STATE_PATH="imap_state.json"
# $env:REQUEST_TIMEOUT="60"
```

## 运行示例

```bash
python -m src.cli --since 7d --mailbox INBOX --output out/todo.md --instruction "根据邮件生成我的待办并按优先级排序"
```

## 说明
- 增量同步：UNSEEN + last_seen_uid，处理后更新 imap_state.json。
- 预聚类：主题指纹（去 Re/Fwd/票号）+ 发件人域 + 时间窗（默认 72h）。
- 细化合并：TF‑IDF + 余弦，相似度阈值默认 0.55。
- LLM：OpenAI 兼容 SDK，DEEPSEEK_BASE_URL 与 DEEPSEEK_MODEL 可配置。
- 输出：控制台打印 + 可选导出 Markdown 文件。
