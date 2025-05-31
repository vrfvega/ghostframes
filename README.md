# Ghostframes

A Docker Compose bundle that brings together:

| Layer | Purpose | Services |
|-------|---------|-----------|
| **Core** | Reverse-proxy and essential services | `traefik`, `overseerr`, `wizarr`, `watchtower` |
| **Internal** | Automation of downloads & library management | `radarr`, `sonarr`, `prowlarr`, `rdtclient` |
| **Debug** | Ephemeral tools for troubleshooting | `whoami` |

Traefik terminates TLS (Let’s Encrypt + Route 53 DNS-01) and routes every sub-domain  
`*.ghostframes.net` to its corresponding container through the shared bridge network `t3_proxy`.

---

## Requirements

| What | Notes |
|------|-------|
| Docker Engine 25 + Docker Compose v2 | Tested on Linux Mint 22.1 |
| Domain **ghostframes.net** in Route 53 | One A/ALIAS record for `ghostframes.net` and one wildcard `*.ghostframes.net` → your host IP |
| AWS IAM user or role with `route53:ChangeResourceRecordSets` | Needed only by Traefik for DNS-01 |

---

## 1 · Clone the repo

```bash
git clone https://github.com/your-handle/ghostframes.git
cd ghostframes
```

## 2 · Create `.env`

```dotenv
DOMAIN=domain.com
EMAIL=you@example.com
TZ=America/New_York
AWS_ACCESS_KEY_ID=XXXXXXXX
AWS_SECRET_ACCESS_KEY=YYYYYYYY
```

> **Tip:** add more variables (torrent API keys, Radarr/Sonarr secrets) as you need.  
> Everything under `compose/` can reference them with `$VARIABLE`.

---

## 3 · Run with profiles

| Scenario | Command |
|----------|---------|
| **Full production** (core + internal) | `docker compose --profile internal up -d` |
| **Troubleshooting:** run WhoAmI | `docker compose --profile debug up -d whoami` |
| **Shut everything down** | `docker compose down` |

Compose merges all `compose/*.yml` files plus the main `docker-compose.yml`.  
Profiles let you include or exclude optional layers without editing YAML.

---

## 4 · Directory layout

```
.
├── compose/          # service fragments
├── data/             # persistent configs (mapped as volumes)
│   └── traefik/certs/acme.json
├── storage/          # downloads & media libraries (bind-mounts)
└── docker-compose.yml  # main file with networks & includes
```

Back up the entire `data/` directory and your media mounts; rebuilding the stack recreates every container with its previous state.

---

## 5 · Troubleshooting

* Visit `https://traefik.ghostframes.net` for the dashboard.
* `docker compose logs -f <service>` shows live logs.

## 6. Services
### Networking
- **Traefik** -> https://traefik.ghostframes.net
### Frontend
- **Overseerr** -> https://overseerr.ghostframes.net
- **Wizarr** -> https://wizarr.ghostframes.net
### Backend
- **Prowlarr** -> https://prowlarr.ghostframes.net
- **Radarr** -> https://radarr.ghostframes.net
- **Sonarr** -> https://sonarr.ghostframes.net
- **RDTClient** -> https://rdtclient.ghostframes.net
