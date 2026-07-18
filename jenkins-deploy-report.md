# Informe de despliegue Jenkins

**Fecha:** 2026-07-18 (actualizado)  
**Host Jenkins:** `192.168.1.79:8081` (servicio `.\b3-ml`)  
**Rama observada:** solo **`main`**

## Acceso (LAN)

| Campo | Valor |
|-------|--------|
| Jenkins UI | http://192.168.1.79:8081/ |
| Staging API | http://192.168.1.79:8080/ |
| Staging Web | http://192.168.1.79:8083/ |

Credenciales de Jenkins: **no se publican en este repo**. Están en el gestor de secretos / configuración local del servidor.

## Flujo actual

```
push backend main
  → Poll SCM (~2 min)
  → audio-streaming/backend: test → package
  → audio-streaming/integration (smoke API :8082)
  → audio-streaming/deploy-staging (API :8080)

push web main
  → Poll SCM (~2 min)
  → audio-streaming/web: test → build
  → audio-streaming/integration (smoke API :8082)
  → audio-streaming/deploy-staging-web (UI :8083)
```

| Job | Trigger | Acción |
|-----|---------|--------|
| `backend` | Poll SCM | test → package → **integration** → **deploy-staging** |
| `web` | Poll SCM | test → build → **integration** → **deploy-staging-web** |
| `integration` | Llamado por backend/web | Smoke jar + Postgres en **:8082** (no toca staging :8080) |
| `deploy-staging` | Llamado por backend | API staging **:8080** |
| `deploy-staging-web` | Llamado por web | Front staging **:8083** |

## Limitaciones

- Poll: hasta ~2 min tras el push.
- Staging API depende de Postgres en WSL.
- Integration usa puerto **8082** a propósito, para no tumbar la API de staging.

Diseño: [jenkins-ci-cd.md](./jenkins-ci-cd.md).
