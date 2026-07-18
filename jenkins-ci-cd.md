# Integración CI/CD con Jenkins

Diseño y operación del CI/CD del ecosistema **audio-streaming** en Jenkins on-prem (LAN).

## 1. Objetivo

Al hacer push a **`main`** (o a una rama `feat/*` / `fix/*`):

1. Detectar el cambio (Multibranch scan ~cada 5 min).
2. Validar contrato OpenAPI + tests del repo.
3. En **`main`**: smoke de integración → deploy staging → smoke staging.
4. En **tags `v*`**: build + archive (sin deploy).

No hay producción automatizada todavía. Desktop/mobile quedan fuera de v0.1.0.

## 2. Acceso e infraestructura

| Servicio | Detalle |
|----------|---------|
| Host | `192.168.1.79` (usuario Windows `b3-ml`) |
| Jenkins | http://192.168.1.79:8081/ |
| Staging API | http://192.168.1.79:8080/audios |
| Staging Web | http://192.168.1.79:8083/ |
| RDP / SSH | `:3389` / `b3-ml@192.168.1.79:22` |
| Agente | JDK 26 (builds), Temurin 21 (Jenkins), Maven 3.9, Node 24, Git |
| BD | PostgreSQL en WSL Ubuntu (`127.0.0.1:5432`, red espejo) |
| Trigger | Multibranch Git scan `5m` (sin webhooks ni túnel) |

Credenciales de Jenkins: **no van en este repo**.

La raíz `/` de la API responde 404; el listado es **`/audios`**.

## 3. Repos

| Carpeta local | Repo remoto | Rol |
|---------------|-------------|-----|
| `audio-streaming-backend` | [B3RT1C/audio-streaming-backend](https://github.com/B3RT1C/audio-streaming-backend) | API + OpenAPI |
| `audio-streaming-web` | [B3RT1C/audio-streaming-web](https://github.com/B3RT1C/audio-streaming-web) | Cliente Angular |
| `audio-streaming` | [B3RT1C/audio-streaming](https://github.com/B3RT1C/audio-streaming) | Docs |

Contrato: [docs/openapi.yaml](https://github.com/B3RT1C/audio-streaming-backend/blob/main/docs/openapi.yaml).

## 4. Jobs (carpeta `audio-streaming/`)

| Job | Tipo | Qué hace |
|-----|------|----------|
| `backend` | Multibranch | Contract + test + package; en `main` → integration → deploy API → staging-smoke; en `v*` → archive |
| `web` | Multibranch | Contract + test + build; en `main` → integration → deploy web → staging-smoke; en `v*` → archive |
| `integration` | Pipeline | Smoke API temporal en **:8082** + Postgres |
| `deploy-staging` | Pipeline | API staging **:8080** |
| `deploy-staging-web` | Pipeline | Front staging **:8083** |
| `staging-smoke` | Pipeline | E2E ligero: `GET /audios` + web `/` |

Ramas descubiertas: `main`, `feat/*`, `feature/*`, `fix/*`, tags `v*`.

```
push feat/*     → contract + tests (+ build)     [sin deploy]
push main       → … → integration → deploy → staging-smoke
tag v0.x.y      → contract + tests + archive     [sin deploy]
```

## 5. Contract tests

- Backend: `node scripts/ci/check-openapi-contract.mjs` — OpenAPI v0.1 (`/audios`, `/audios/{id}`) vs `AudioController`.
- Web: `node scripts/ci/check-api-contract.mjs` — `API_PATHS` vs contrato `/audios`.

## 6. Operación en el servidor

Scripts (`C:\Users\b3-ml\jenkins-scripts\`):

- `deploy-staging.ps1` / `start-staging-api.ps1`
- `deploy-staging-web.ps1`
- `ensure-staging.ps1`
- `start-api-smoke.ps1`
- `smoke-staging.ps1`

Tareas: `Start-WSL-Postgres`, `Ensure-MusicStreaming-Staging`, `Run-MusicStreaming-StagingAPI`.

Workspaces Multibranch: `audio-streaming_backend_main`, `audio-streaming_web_main`, etc.

## 7. Tags

- Ejemplo publicado: `v0.1.0` en backend y web.
- Crear: `git tag -a v0.x.y -m '...' && git push origin v0.x.y`
- Jenkins indexa el tag; el pipeline archiva artefactos y **no** despliega staging.

## 8. Fases

### Hecho

- [x] Carpeta Jenkins `audio-streaming/` (antes `music-streaming/`)
- [x] Multibranch `backend` / `web` (main + feat/fix + tags)
- [x] Contract tests OpenAPI / API_PATHS
- [x] Integration smoke `:8082` + staging-smoke API+web
- [x] Deploy staging solo desde `main`
- [x] Tags `v*` con archive (sin deploy)

### Más adelante

- [ ] Deploy a producción (definir dónde vive prod)
- [ ] Notificaciones en rojo
- [ ] Auto-build de tags al descubrirlos (hoy se puede lanzar el job del tag a mano tras el scan)
- [ ] Desktop / mobile CI → [roadmap.md](./roadmap.md)

## 9. Limitaciones

- Scan Multibranch: hasta ~5 min tras el push (o “Scan Multibranch Pipeline Now”).
- Si WSL/Postgres se duerme, `/audios` puede fallar hasta `ensure-staging` / redeploy.
- Integration es smoke de API; staging-smoke cubre front HTTP básico, no e2e de UI completo.

## Referencias

- [arquitectura.md](./arquitectura.md)
- [roadmap.md](./roadmap.md)
- [README](./README.md)
