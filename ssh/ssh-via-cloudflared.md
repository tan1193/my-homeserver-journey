# 🔑 Secure Remote Access with Cloudflared Tunnel

Instead of exposing port 22 to the internet, I used Cloudflare Tunnel for secure, zero-trust SSH access to my home server — no public IP or VPN needed.

Here’s how I did it:

## Prepare a Domain

Before setting up the tunnel, I registered a domain (e.g., yourdomain.com) and connected it to Cloudflare. This gave me access to DNS management and Cloudflare Tunnel services.

1. Go to [Cloudflare](https://www.cloudflare.com/) and create an account.

2. Add your domain to Cloudflare and update your domain registrar's nameservers.

3. Once DNS propagation is complete, you're ready to create tunnels using Cloudflare services.

## On My Home Server
1 . **Install Cloudflared**:

```bash
# Add cloudflare gpg key
sudo mkdir -p --mode=0755 /usr/share/keyrings
curl -fsSL https://pkg.cloudflare.com/cloudflare-main.gpg | sudo tee /usr/share/keyrings/cloudflare-main.gpg >/dev/null

# Add this repo to your apt repositories
echo 'deb [signed-by=/usr/share/keyrings/cloudflare-main.gpg] https://pkg.cloudflare.com/cloudflared any main' | sudo tee /etc/apt/sources.list.d/cloudflared.list

# install cloudflared
sudo apt-get update && sudo apt-get install cloudflared
```

2. **Authenticate Cloudflared**:

```bash  
cloudflared tunnel login
```

3. **Create a Tunnel**:

```bash
cloudflared tunnel create my-tunnel
```

4. **Configure the Tunnel**:

```bash
/home/your-username/.cloudflared/config.yml

tunnel: my-home-server
credentials-file: /home/your-username/.cloudflared/[TUNNEL-ID].json

ingress:
  - hostname: ssh.yourdomain.com
    service: ssh://localhost:22
  - service: http_status:404
```

5. **Create the DNS record automatically:**:

```bash
cloudflared tunnel route dns my-home-server ssh.yourdomain.com
```

6. Start the tunnel:
```bash
cloudflared tunnel run my-home-server
```

## On My Windows Laptop

1. **Install Cloudflared**:
```bash
winget install --id Cloudflare.cloudflared
```

2. **access ssh config**:
```bash
cloudflared access ssh-config --hostname ssh.yourdomain.com

# result:
Add to your /.ssh/config:
Host ssh.yourdomain.com
  ProxyCommand C:\\Program Files (x86)\cloudflared\cloudflared.exe access ssh --hostname %h
``` 

3. **Copy the output to your SSH config file**:
```bash
notepad C:\Users\YourUsername\.ssh\config
```
4. **Connect to your home server**:
```bash
ssh ssh.yourdomain.com
```


if you encounter this error:
```bash
Bad permissions. Try removing permissions for user: UNKNOWN\\UNKNOWN (S-1-5-21-13844415-2831775885-76735119-1002)....
```
Detailed steps:
- Right-click on the file C:\Users\{UserName}\.ssh\config → select Properties.
- Go to the Security tab → click the Advanced button.
- At the top of the new window, click "Disable inheritance".
- When prompted:
    - Choose "Convert inherited permissions into explicit permissions".
- You will now see all permission entries listed.
- Select the line with "UNKNOWN..." → click Remove.
- Click Apply, then OK to save the changes.