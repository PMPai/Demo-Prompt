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

## 19. 本機連線位址

```text
http://127.0.0.1:18789
```

## 20. 从其他机器访问小龙
本文说明如何从另一台电脑登录到 DigitalOcean 服务器，并通过 SSH 端口转发打开服务器上的小龙虾界面。
### 1. 服务器信息
服务器 IP：
```
167.71.197.61
```
当前可用登录用户：root
小龙虾界面在服务器本机监听：
```
127.0.0.1:18789
```

这类地址只能从服务器内部访问，所以外部电脑需要先做 SSH 端口转发。
### 2. 准备 SSH 私钥
其他机器必须有能登录这台服务器的 SSH 私钥。
当前已验证可用的 Windows 私钥路径示例：
```
C:\Users\ray\.ssh\id_ed25519
```
注意：
- 私钥文件没有 .pub 后缀。
- .pub 文件是公钥，不能单独用于登录。
- 私钥不要发到微信群、邮件正文、截图或公开文档里。
- 如果要复制到另一台机器，建议用加密 U 盘、密码管理器附件，或公司认可的安全文件传输方式。

Windows 上复制私钥
把下面这个文件复制到新机器的相同目录或你指定的安全目录，路径可能类似：
```
C:\Users\你的用户名\.ssh\id_ed25519
```
确认文件存在：
``` Shell
dir C:\Users\你的用户名\.ssh
```

### 3. 在新机器上做端口转发
在新机器的 PowerShell 中运行：
```
ssh -i C:\Users\你的用户名\.ssh\id_ed25519 -N -L 18789:127.0.0.1:18789 root@167.71.197.61
```

参数含义：
- i：指定 SSH 私钥。
- N：只建立连接，不进入远程命令行。
- L 18789:127.0.0.1:18789：把本机的 18789 端口转发到服务器内部的 127.0.0.1:18789。
- root@167.71.197.61：使用 root 用户登录服务器。

运行后，如果窗口停住没有报错，说明端口转发已经建立。不要关闭这个 PowerShell 窗口。
### 4. 打开小龙虾界面

保持上面的 PowerShell 窗口打开，然后在浏览器访问：
http://127.0.0.1:18789
或：
http://localhost:18789
浏览器会通过 SSH 隧道访问服务器上的小龙虾界面。在登录页面中输入小龙虾的**令牌**，直接连接就可

---

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
