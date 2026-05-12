<div align="center">

# 🕸️ WireGuard Mesh Appliance

### Open-source WireGuard mesh network manager — self-hosted alternative to Tailscale, Headscale, Netbird

**Production-grade WireGuard mesh orchestration. Go + PostgreSQL + React. Designed for organizations who need Tailscale-level UX but want self-hosted control of their network.**

[![Network Security](https://img.shields.io/badge/Network%20Security-Mesh%20VPN-1F4FD8?style=for-the-badge)](https://www.networkershome.com/best-network-security-course-in-bangalore/)
[![Cybersecurity](https://img.shields.io/badge/Cybersecurity-ZTNA-FF0040?style=for-the-badge)](https://www.networkershome.com/best-cybersecurity-course-in-bangalore/)
[![License: Apache 2.0](https://img.shields.io/badge/License-Apache%202.0-green?style=for-the-badge)](LICENSE)
[![Built by Networkers Home](https://img.shields.io/badge/Built%20by-Networkers%20Home-000000?style=for-the-badge)](https://www.networkershome.com/)

</div>

---

## 🏛️ Built by Networkers Home

The WireGuard Mesh Appliance was built by **[Networkers Home](https://www.networkershome.com/)** — India's leading Cisco + cybersecurity training institute (Bengaluru, since 2005). It's a free open-source companion to our [Network Security](https://www.networkershome.com/best-network-security-course-in-bangalore/) and [Cybersecurity Pro](https://www.networkershome.com/best-cybersecurity-course-in-bangalore/) programs — built for students learning **modern zero-trust mesh networking** without locking into a paid Tailscale or Cloudflare WARP tenant.

> Tailscale teaches you Tailscale. **Networkers Home teaches you the underlying WireGuard mesh architecture that all modern ZTNA platforms are built on.** This appliance is the reference implementation our students inspect, modify, and deploy in their final-project capstones.
> [Book a demo class →](https://www.networkershome.com/networkers-home-demo-class/)

**Compare top cybersecurity + network security institutes:**
[Top 10 Network Security Bangalore](https://www.networkershome.com/top-10-network-security-training-institutes-bangalore-2026/) · [Top 10 Cybersecurity India](https://www.networkershome.com/top-10-cybersecurity-training-institutes-india-2026/) · [Top 10 Cloud Security India](https://www.networkershome.com/top-10-cloud-security-training-institutes-india-2026/)

**Sibling Networkers Home open-source projects:**
[Secure Tunnel Appliance](https://github.com/NETWORKERS-HOME-123/secure-tunnel-appliance) · [QuickFW Firewall](https://github.com/NETWORKERS-HOME-123/quickfw-firewall-appliance) · [QuickLogs SIEM](https://github.com/NETWORKERS-HOME-123/quicklogs)

---

# Simple Mesh Appliance

> Self-hosted WireGuard mesh network manager — Go + PostgreSQL + React on a single $12/mo server.

**Live demo:** https://mesh.networkershome.com

---

## What it is

A complete WireGuard VPN mesh controller you can run on any Ubuntu server. Replaces commercial mesh VPN products and Supabase-based backends with a fully self-contained appliance.

- Create and manage WireGuard mesh networks
- Invite peers by email or shareable link
- Auto-generate X25519 keypairs in the browser (private keys never leave the client)
- Assign virtual IPs from `10.10.0.0/24` automatically
- Export WireGuard config files and QR codes
- Real-time peer status (online/stale/offline) via Server-Sent Events
- Full activity log

## Stack

| Layer | Technology |
|---|---|
| Frontend | React 18, TypeScript, Vite, Tailwind CSS, shadcn/ui |
| Backend | Go 1.22, gorilla/mux |
| Database | PostgreSQL 16 (auto-migrated at startup) |
| Auth | JWT HS256 + bcrypt |
| Real-time | Server-Sent Events (SSE) |
| Crypto | Web Crypto API (X25519 / Curve25519) |
| Proxy | Nginx + Let's Encrypt |

## Quick Deploy (Ubuntu 24.04)

```bash
# On a fresh Ubuntu 24.04 droplet as root:
git clone https://github.com/vikasswaminh/simple-mesh-appliance.git /opt/wgctrl-src
bash /opt/wgctrl-src/deploy/deploy.sh
```

The script installs Go, Node.js, PostgreSQL, Nginx, Certbot, and configures everything end-to-end.

**Requirements:**
- Ubuntu 24.04 LTS
- DNS A record pointing your domain to the server IP (before running the script)
- Update `DOMAIN` in `deploy/deploy.sh` to match your domain

## Local Development

**Backend:**
```bash
cd backend
export DB_URL="postgres://user:pass@localhost/wgctrl?sslmode=disable"
export JWT_SECRET="your-secret"
go run .
# API available at http://localhost:8081
```

**Frontend:**
```bash
npm install
npm run dev
# UI available at http://localhost:5173
```

## Project Structure

```
├── backend/
│   ├── main.go              # Router, middleware, server setup
│   ├── config/config.go     # Environment variable loading
│   ├── db/
│   │   ├── db.go            # Connection pool
│   │   ├── migrations.go    # Auto-run DDL at startup
│   │   └── schema.sql       # Reference schema
│   ├── handlers/            # HTTP handler per resource
│   ├── middleware/          # Auth (JWT), CORS, rate limit
│   └── sse/broker.go        # In-memory pub/sub for SSE
├── src/
│   ├── lib/
│   │   ├── api.ts           # All REST API calls
│   │   └── wireguard-keys.ts # X25519 key generation (WebCrypto)
│   ├── hooks/
│   │   ├── useAuth.tsx      # JWT auth context
│   │   └── useRealtimePeers.ts # SSE peer updates
│   ├── components/          # UI panels
│   └── pages/               # Route pages
├── deploy/
│   └── deploy.sh            # Full production deploy script
└── DOCUMENTATION.md         # Full technical documentation
```

## API

All endpoints under `/api/`. See [DOCUMENTATION.md](DOCUMENTATION.md) for the full API reference.

```
POST /api/auth/signup
POST /api/auth/signin
POST /api/networks/create
GET  /api/networks
POST /api/peers/join
GET  /api/peers
GET  /api/sse/peers        (Server-Sent Events)
```

## Security

- Private keys are generated in-browser and never transmitted
- bcrypt password hashing
- JWT authentication on all protected routes
- Rate limiting: 10 req/s per IP (burst 20)
- UFW firewall: ports 22, 80, 443 only
- systemd service hardening (NoNewPrivileges, ProtectSystem, PrivateTmp)
- HSTS, X-Frame-Options, X-Content-Type-Options headers

## License

MIT
