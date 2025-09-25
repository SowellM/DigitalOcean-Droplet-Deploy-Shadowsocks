# Deploy Shadowsocks to DigitalOcean

This GitHub Action spins up a **DigitalOcean droplet** preconfigured with **Shadowsocks-Rust**.  

It automatically:

- Creates/updates a DNS A record in your DigitalOcean domain.
- Deploys Shadowsocks in either **TLS (443 TCP)** mode or **TCP/UDP** mode.
- Issues a free Let's Encrypt cert when TLS mode is selected.

## 🛠 Requirements

- A domain managed by DigitalOcean DNS.
- An SSH key registered in DigitalOcean.
- GitHub repo with Actions enabled.

## 🚀 Usage

### 1. Add Secrets

In your GitHub repo → **Settings → Secrets → Actions**:
- `DO_API_TOKEN` → Your DO API token (with `read/write` for Droplets + DNS).
- `DO_SSH_KEY` → The SSH key ID registered in your DO account (find with `doctl compute ssh-key list`).

### 2. Trigger workflow

Go to **Actions → Deploy Shadowsocks → Run workflow** and provide:

- **domain** → Base domain (`example.com`)
- **subdomain** → Subdomain (`ss`)
- **email** → Email for Let's Encrypt
- **password** → Shadowsocks password
- **mode** → `tls` or `tcp-udp`

### 3. Connect Client

Example client config (TLS):

```json
{
  "server": "ss.example.com",
  "server_port": 443,
  "password": "yourpassword",
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
  "password": "yourpassword",
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