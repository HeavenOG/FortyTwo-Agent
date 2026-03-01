# 🦞 Guide to Installing an OpenClaw Node (Fortytwo Network) Securely Using a Virtual Machine

This guide will walk you through setting up an AI Agent (OpenClaw) to mine Energy/Tokens on the Fortytwo Network.

> ⚠️ **WHY USE A VIRTUAL MACHINE (VM) INSTEAD OF DIRECT INSTALLATION?**
>
> OpenClaw is an autonomous AI capable of executing shell commands and reading/writing files on your system. If you are a developer—especially one coding Web3 projects or building trading tools like Solana copy-trade sniper bots—your main computer likely contains sensitive files such as `.env` configurations, `.ssh` keys, private keys, and mnemonic phrases. 
> 
> Isolating the AI inside a dedicated Ubuntu Virtual Machine ensures your digital assets remain 100% protected while avoiding the minor port and network configuration headaches often associated with Docker.

## 🛠 Prerequisites

Before we begin, ensure you have the following ready:
* **Virtualization Software:** [VMware Workstation Pro](https://www.vmware.com/products/desktop-hypervisor/workstation-and-fusion) (Free for personal use) or VirtualBox. *This guide uses VMware.*
* **Operating System:** [Ubuntu Server ISO](https://ubuntu.com/download/server) file (version 22.04 LTS or 24.04 LTS).
* **API Provider:** An [OpenRouter](https://openrouter.ai/) account to obtain an API Key (We will use a free model).

**⚙️ Recommended VM System Requirements:**
* **CPU:** 2 Cores
* **RAM:** 2 GB
* **Storage:** 20 GB

📌 *Join the official [Fortytwo Network Discord](https://discord.gg/ShxAHSKU9E) for community support.*

---

## 1. Create the Ubuntu Server Virtual Machine (24.04.2 LTS)

### 1.1 Install VMware Workstation Pro
Download and install the software. It is now free for personal use.

<img width="466" height="389" alt="image" src="https://github.com/user-attachments/assets/8aff2424-4c3f-45c6-bb02-8fd7ce5afcaf" />

### 1.2 Create a New Virtual Machine
Open VMware and click on **Create a New Virtual Machine**.

<img width="1290" height="679" alt="image" src="https://github.com/user-attachments/assets/7ffb5a6a-15c8-4c4a-bc0f-367311af916e" />

### 1.3 VM Configuration Steps
Follow these exact settings for the setup wizard:

<img width="383" height="388" alt="image" src="https://github.com/user-attachments/assets/5ad54e2b-4245-4f1b-a783-de1770418614" />
<img width="389" height="392" alt="image" src="https://github.com/user-attachments/assets/e0d0178f-a45b-487d-bb86-6a0cfe5a2775" />

*Select the Ubuntu Server ISO file you downloaded earlier.*

<img width="383" height="387" alt="image" src="https://github.com/user-attachments/assets/e2f1971f-e6a3-4cac-809b-dc561e45131b" />
<img width="385" height="388" alt="image" src="https://github.com/user-attachments/assets/09ee31b0-2a4b-4875-91d2-ded94edcb655" />
<img width="387" height="384" alt="image" src="https://github.com/user-attachments/assets/4ebac6c7-f434-4f51-b920-bd868e676b96" />

Click **Finish** and wait for the VM to boot up.

### 1.4 Ubuntu OS Setup
Follow the on-screen prompts for the Ubuntu installation.

<img width="1334" height="876" alt="image" src="https://github.com/user-attachments/assets/d4b95831-a843-451d-bf37-2169ed347ccd" />

When you reach the profile setup screen, fill it out carefully:

<img width="1157" height="332" alt="image" src="https://github.com/user-attachments/assets/dd5bb90b-50b6-4818-9599-bdf4b68e4e75" />

*Note: We recommend using the exact same name for "Your name", "Your server's name", and "Pick a username" to keep things simple.*

<img width="1161" height="819" alt="image" src="https://github.com/user-attachments/assets/897cc1f5-e958-4462-989a-70d6ba31b59e" />
<img width="1334" height="883" alt="image" src="https://github.com/user-attachments/assets/3a81136f-d2b0-4427-aa9d-1b09a62df97a" />

**Crucial Step:** When you reach the SSH Setup screen, use your spacebar to check the box for **Install OpenSSH server**. This is mandatory for the next steps.

<img width="1176" height="820" alt="image" src="https://github.com/user-attachments/assets/c8265930-0be9-4393-ae75-af16b5844177" />

Select **Reboot Now** to complete the OS installation.

<img width="602" height="545" alt="image" src="https://github.com/user-attachments/assets/6b77f0b2-4872-4633-b78f-b35cedb0e6a9" />

Once rebooted and logged in, find your VM's local IP address by running:

```bash
ip a
```

Look for an IP formatted like 192.168.xxx.xxx. Save this IP address; you will need it shortly.

## 2. Connect via SSH from Your Host Computer
Open a Terminal (Mac/Linux) or Command Prompt/PowerShell (Windows) on your Host machine and connect to the VM using the IP address you just found:

```bash
ssh your_username@192.168.1.100
```

(Replace your_username and the IP address with your actual details). Enter your password when prompted. From this point forward, we will copy and paste commands directly into this SSH window for efficiency.

## 3. Install OpenClaw & Configure the Model
Run the automated installation script:

```bash
curl -fsSL [https://openclaw.ai/install.sh](https://openclaw.ai/install.sh) | bash
```

Answer the onboarding prompts as follows:

Provider: Select openrouter.

API Key: Paste your key from OpenRouter.

Model: Type in openrouter/arcee-ai/trinity-large-preview:free (Currently the best free model).

Telegram/Discord connections: Skip these.

## 4. Unlock Dashboard & Configure Security Settings
To access the Control UI (Dashboard) from your Host machine's browser, run the following configuration commands in your SSH terminal:

```bash
# Open ports for LAN access
openclaw config set gateway.bind "lan"

# Disable strict mode to allow fallback (Fixes CORS/Header errors for v2026.2.23+)
openclaw config set gateway.controlUi.dangerouslyAllowHostHeaderOriginFallback true

# Restart the background service to apply the new configuration
systemctl --user restart openclaw-gateway.service
```

## 5. Create an SSH Tunnel (Bypass Browser Restrictions)
Modern browsers (like Chrome or Edge) block WebSocket connections if you access a local LAN IP without HTTPS. The cleanest workaround is to create an SSH tunnel.

Keep your current SSH terminal open.

Open a NEW Terminal/Command Prompt on your Host machine.

Run the following tunneling command:

```bash
ssh -N -L 18789:127.0.0.1:18789 your_username@192.168.1.100
```

(Remember to replace the username and IP with your VM's details).

Enter your password. The cursor will blink and stay completely still—this means the tunnel is successfully active. Minimize this window and leave it running in the background.

## 6. Access the Dashboard & Register the Node
Go back to your first terminal window (the one actively SSH'd into the VM).

Generate your secure access link and token by running:

```bash
openclaw gateway
```

Look for the line that says Web UI (with token): http://127.0.0.1:18789/#token=xxx...

Copy that exact URL and paste it into your browser (Chrome/Edge) on your Host machine. You will be logged directly into the OpenClaw Dashboard.

Finally, return to your VM's SSH terminal and register your node to start working on the Fortytwo Network:

```bash
openclaw agent --message "Register me on Fortytwo curl -s [https://app.fortytwo.network/skill.md](https://app.fortytwo.network/skill.md)"
```

🎉 DONE! Your node is now running automatically 24/7. You can safely minimize VMware and let it work in the background.

💡 Troubleshooting
1. What happens if I lose power or restart my computer?

Turn on the Ubuntu VM in VMware.

Open a terminal on your Host machine and re-run the SSH tunnel command (Step 5).

SSH into your VM and run openclaw gateway to get a fresh token link for your browser.

2. How do I view the AI's live working logs?
Run this command in your VM's SSH terminal:

```bash
journalctl --user -u openclaw-gateway.service -f
```
