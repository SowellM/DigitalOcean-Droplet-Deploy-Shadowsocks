# DigitalOcean Droplet Deploy Shadowsocks GitHub Action

This GitHub Action deploys a **DigitalOcean Droplet** with Docker and a **Shadowsocks-Rust** container, in TLS camouflage or TCP/UDP mode. It automatically creates free Let’s Encrypt TLS certificates and manages DNS records in DigitalOcean.  

<br>
<p>
  <a href="https://www.digitalocean.com">
    <img src="https://opensource.nyc3.cdn.digitaloceanspaces.com/attribution/assets/PoweredByDO/DO_Powered_by_Badge_blue.svg" alt="Powered by DigitalOcean" width="200px" />
  </a>
</p>

## Features

- Deploy a droplet from GitHub Actions with **one click**
- Uses `doctl` and a `cloud-init.yml` file
- Provisioned with **cloud-init**
  - Installs Docker & Docker Compose
  - Installs Certbot and issues a valid Let’s Encrypt certificate when TLS mode is selected
  - Waits until Docker is ready
  - Launches a Shadowsocks container in either **TLS (443 TCP)** mode or **TCP/UDP** mode
  - Auto-renews certs and restarts Shadowsocks after renewal
- Auto-creates or updates a **DigitalOcean DNS A record** for your domain/subdomain
- Works with defaults but allows overriding region, size, image, and hostname

## Requirements

- A **DigitalOcean account** with:
  - An existing **SSH key** already uploaded (you’ll provide its ID in the secrets).
- A **domain managed in DigitalOcean DNS**:
  - The GitHub Action automatically creates or updates an **A record** for your chosen subdomain.
  - Your domain must already be added to [DigitalOcean’s DNS control panel](https://cloud.digitalocean.com/networking/domains).
  - If your domain is currently managed elsewhere (Cloudflare, registrar DNS, etc.), you must first point your domain’s nameservers to DigitalOcean:
    ```txt
    ns1.digitalocean.com
    ns2.digitalocean.com
    ns3.digitalocean.com
    ```
- A **GitHub repository fork** with required secrets set up (see [Secrets Setup](#2-secrets-setup)).

## Usage

### 1. Fork the Repository

1. Click the **Fork** button at the top of the GitHub repository page.
2. Once forked you can use it directly from your account.

### 2. Secrets Setup

You'll need to create the following secrets in your forked repository for the action to work:

1. **`DIGITALOCEAN_ACCESS_TOKEN`**: Your DigitalOcean API token. You can generate it in your DigitalOcean API settings.
    1. Create A New Personal Access Token and give the token a name.
    2. Choose **Custom Scopes** then select the following granular scopes:
       1. `ssh_key:read`
       2. `droplet:read,create`
       3. `domain:read,create,update`
2. **`DIGITALOCEAN_SSH_KEY_ID`**: The DigitalOcean SSH key ID you'd like to use. List your SSH key IDs with ```doctl compute ssh-key list```.

To add these secrets:

1. Go to your forked repository on GitHub.
2. Navigate to **Settings** > **Secrets and variables** > **Actions**.
3. Click **New repository secret** and add `DIGITALOCEAN_ACCESS_TOKEN` then `DIGITALOCEAN_SSH_KEY_ID`.

### 3. Run the Included Workflow

After forking this repository into your own GitHub account:

1. Go to your forked repo’s **Actions** tab.
2. Select **Deploy Droplet to DigitalOcean**.  
3. Click **Run workflow** and fill in the inputs:
   - `domain` - Root domain (example.com).
   - `subdomain` - Subdomain (e.g., app, www, test). Leave blank for root.
   - `hostname` - Droplet hostname.
   - `password` - Shadowsocks password.
   - Optionally, override mode, region, size, or image.
4. Wait for job to complete.
5. Your droplet will be live with:
   - Shadowsocks running in either **TLS (443 TCP)** mode or **TCP/UDP** mode.
   - Valid TLS certificate from Let’s Encrypt.
   - Auto-renew enabled.
   - Shadowsocks running, accessible via 443 (TLS camouflage or TCP/UDP).

### Alternate Usage: Use as a GitHub Action in Your Own Workflow

Instead of forking, you can also use this as an Action directly in your own workflows:

```yaml
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Deploy Droplet
        uses: marksowell/DigitalOcean-Droplet-Deploy-Shadowsocks@v1
        with:
          domain: example.com
          subdomain: app
          hostname: app-server
          password: shadowsockspassword
        env:
          DIGITALOCEAN_ACCESS_TOKEN: ${{ secrets.DIGITALOCEAN_ACCESS_TOKEN }}
          DIGITALOCEAN_SSH_KEY_ID: ${{ secrets.DIGITALOCEAN_SSH_KEY_ID }}
```

### 4. Connect Client

Example client config (TLS):

```json
{
  "server": "ss.example.com",
  "server_port": 443,
  "password": "shadowsockspassword",
  "method": "chacha20-ietf-poly1305",
  "plugin": "v2ray-plugin",
  "plugin_opts": "tls;host=ss.example.com",
  "local_address": "127.0.0.1",
  "local_port": 9053
}
```

Example client config (TCP/UDP):

```json
{
  "server": "ss.example.com",
  "server_port": 443,
  "password": "shadowsockspassword",
  "method": "chacha20-ietf-poly1305",
  "mode": "tcp_and_udp",
  "local_address": "127.0.0.1",
  "local_port": 9053
}
```

## Contributing

Contributions are welcome! Please open an issue or submit a pull request for any improvements or bug fixes.

## License

This project is licensed under the MIT License. See the [LICENSE](./LICENSE) file for details.