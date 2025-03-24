# 🧠 VPS Headscale + Tailscale Setup Guide

Easily self-host a [Headscale](https://github.com/juanfont/headscale) server on a KVM VPS with Docker and Cloudflare Tunnel for secure remote access to your devices via Tailscale.

---

## ✨ Get Started (with Discount)

👉 **Sign up with Hostinger** to get a **20% discount**:  
[https://hostinger.com?REFERRALCODE=matthammond](https://hostinger.com?REFERRALCODE=matthammond)

---

## 💻 Create a VPS on Hostinger

1. Go to your Hostinger dashboard.
2. Click **Create New VPS**.
3. Choose a **KVM VPS** – we recommend `KVM2` or `KVM4`.
4. Choose your preferred location.
5. Under **Application**, select **Docker**.
6. Set your root password and optionally add an **SSH key** for secure access.
7. (Optional but recommended) Disable password login in SSH once setup is complete.
8. (Optional) Disable the **Monarx malware scanner**.
9. Access the server using:
   - Your terminal: `ssh root@<your-server-ip>`
   - Or Hostinger’s browser-based terminal.

## ✨ Clone git repo

Once connected to your VPS:

```bash
git clone https://github.com/matthewshammond/VPS-headscale.git vps
cd vps && rm -rf .git && cd headscale
```

---

## 🌐 Set Up Cloudflare Tunnel

1. Log in to [Cloudflare](https://www.cloudflare.com).
2. Select the domain you’ll use.
3. Go to **Access → Launch Zero Trust**.
4. Navigate to **Networks → Tunnels**.
5. Click **Create a Tunnel**:
   - Choose **Cloudflared**.
   - Name your tunnel (e.g., `headscale`).
   - Select **Docker** as the platform.
   - Copy the generated Docker command.
6. Extract the **token** from the command and:
   - Open the compose file:
     ```bash
     nano compose.yaml
     ```
   - Replace the token in the appropriate environment variable.
   - Save and close the compose.yaml

---

## ✨ Deploy the Headscale Server

1. Open the config file:
   ```bash
   nano config/config.yaml
   ```

2. Edit the following lines:
   - **Line 13**: Set your domain:
     ```yaml
     server_url: https://headscale.mydomain.com
     ```
   - **Line 275** (optional): Update your local base domain:
     ```yaml
     base_domain: local.mydomain.com
     ```

3. Save and exit.

4. Start the Docker container:
   ```bash
   docker compose up -d
   ```
---

## 🌐 Complete Cloudflare Set Up 

1. Go back to Cloudflare and click **Next**.
2. Set up a public hostname:
   - Subdomain: `headscale`
   - Domain: *(Select from dropdown)*
   - Service: `HTTP`
   - URL: `headscale:8080`
   - Save and finish setup.

---

## 🍓 Add a Raspberry Pi Client

1. Flash an OS to your Pi's SD card and boot it up.
2. SSH into your Pi:
   ```bash
   ssh pi@<raspberrypi-ip>
   ```

3. Update & upgrade packages:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```

4. Install Docker:
   ```bash
   curl -sSL https://get.docker.com | sh
   ```

5. Confirm Docker is working:
   ```bash
   docker --version
   ```

6. Clone the repo:
   ```bash
   git clone https://github.com/matthewshammond/VPS-headscale.git vps
   cd vps && rm -rf .git && cd tailscale
   ```

---

## 🔐 Create Tailscale Auth Key

Back on your VPS:

```bash
docker exec -it headscale headscale user create tailscale
docker exec -it headscale headscale preauthkeys create --user tailscale --reusable --expiration 24h
```

1. Copy the generated **auth key**.
2. Back on the Raspberry Pi, open the compose file:
   ```bash
   nano compose.yaml
   ```

3. Replace the placeholder `TS_AUTHKEY` on line 17 with your copied key.
4. Save and exit.

5. Start Tailscale:
   ```bash
   docker compose up -d
   ```

---

## 🔐 Add More Clients

1. To add more clients, execute the commands from the app in your VPS
```bash
docker exec -it headscale <command from app>
```

---

## ✅ Done!

Your Headscale instance is now live and accessible securely via Cloudflare, and your Raspberry Pi is connected as a client.

---
