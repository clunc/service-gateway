# service-gateway

Path-based reverse proxy for homelab services. The included nginx configuration maps host-exposed ports to `/<service>` routes so you can replace an existing webserver with a single gateway container.

## What's included
- `docker-compose.yml` runs nginx as the gateway (host network mode so it can reach the host-published ports from your containers).
- `nginx.conf` defines upstreams for your running services and rewrites `/<service>/...` to each app.

## Default routes (adjust as needed)
- `/action-logger` -> `127.0.0.1:3003`
- `/gym-logger` -> `127.0.0.1:3001`
- `/mobility-logger` -> `127.0.0.1:3002`
- `/nodered` -> `127.0.0.1:1880`
- `/appdaemon` -> `127.0.0.1:5050`
- `/zigbee2mqtt` -> `127.0.0.1:8080`
- `/portainer` -> `127.0.0.1:9000`
- `/grafana` -> `127.0.0.1:3000`
- `/walkingpad` -> `127.0.0.1:8050`
- `/wger` -> `127.0.0.1:8081` (change to your wger backend port; if you remove the wger nginx proxy, point this at the gunicorn container port, e.g. 8000)

## Usage
1) Stop the old webserver that is bound to port 80 (to free it for this gateway).
2) Edit `nginx.conf` upstream blocks to match the host ports your containers publish. If a service is only reachable inside a Docker network, either publish its port to the host or attach this gateway container to that network and change the upstream host to the container name.
3) Start the gateway:
```bash
docker compose up -d
```
4) Visit `http://<your-host>/<service>/` for the mapped apps. A health check is available at `/healthz`.

### TLS
This setup is HTTP-only. To terminate TLS, either:
- Run this gateway behind an existing TLS terminator (e.g., Traefik/Caddy/HAProxy), or
- Add certificates and `listen 443 ssl` blocks to `nginx.conf`.
