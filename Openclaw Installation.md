## 1. 以管理員帳號登入遠端主機

```bash
ssh your-admin-user@your-server 
```

## 2. 設定 OpenRouter API Key

```bash
export OPENROUTER_API_KEY='sk-or-xxxxxxxxxxxxxxxx'
```

## 3. 建立專用使用者

```bash
sudo useradd -m -s /bin/bash openclaw
passwd openclaw
sudo usermod -aG sudo openclaw
```

## 4. 安裝基礎工具：Ubuntu / Debian

```bash
sudo apt-get update
sudo apt-get install -y curl ca-certificates git jq openssl build-essential sudo
```

## 5. 安裝基礎工具:  RHEL/ Rocky / Alma / Fedora

```bash
sudo dnf install -y curl ca-certificates git jq openssl gcc gcc-c++ make sudo
```

## 6. 允許 openclaw 使用者登出後繼續跑服務

```bash
sudo loginctl enable-linger openclaw
```

## 7. 切換到 openclaw 使用者

```bash
sudo -iu openclaw
```
安装 Node.js, 
```
sudo apt update
sudo apt install -y nodejs npm
```

## 8. 安裝 OpenClaw

```bash
curl -fsSL https://openclaw.ai/install.sh | bash
```

## 9. 重新載入 PATH

```bash
export PATH="$HOME/.local/bin:$HOME/.npm-global/bin:/usr/local/bin:$PATH"
```

## 10. 確認 Node.js / npm / OpenClaw

```bash
node --version
npm --version
openclaw --version
```

## 11. 用 OpenRouter API Key 初始化 OpenClaw

```bash
openclaw onboard --auth-choice apiKey --token-provider openrouter --token "$OPENROUTER_API_KEY"
```

## 12. 安裝 OpenClaw Gateway 背景服務

```bash
openclaw onboard --install-daemon
```

## 13. 檢查設定檔, 设定 Gateway_Token

```bash
cat ~/.openclaw/openclaw.json
openclaw config set gateway.auth.token "pai123"
```

## 14. 檢查 OpenClaw 狀態

```bash
openclaw doctor
openclaw gateway status
```

## 15. 檢查 systemd user service

```bash
systemctl --user status openclaw-gateway --no-pager
```

## 16. 若服務沒啟動，手動啟動

```bash
systemctl --user enable --now openclaw-gateway
```

## 17. 查看 Gateway log

```bash
journalctl --user -u openclaw-gateway -f
```

## 18. 回到本機，建立 SSH tunnel

```bash
ssh -N -L 18789:127.0.0.1:18789 your-admin-user@your-server
```

## 20. 本機連線位址

```text
ws://127.0.0.1:18789
```

依據：官方安裝指令、onboarding 指令與 OpenRouter 快速設定。([[docs.openclaw.ai](https://docs.openclaw.ai/start/getting-started?utm_source=chatgpt.com)][1])

## EXTRA1: 请安装下列的工具，帮助更好的完成相关的操作
- 网络搜索：BraveSearch
- 记忆管理：QMD
- 邮件设置：Agentmail


## EXTRA2： 请安装 Antfarm 和 Paperclip 的AGENT 框架

除了Openclaw 本身的框架之外，要有效利用 Multiple Agents 的框架，可以安装 Antfarm （Sequential Operation）还有 Paperclip （Hireachiture Operation) 。 

可以直接告诉Open claw
``` OpenClaw
Install antfarm (https://github.com/snarktank/antfarm) and make sure you tested before you report back as completed
Install Paperclip (https://github.com/paperclipai/paperclip) and make sure you tested before you report back as completed
```
