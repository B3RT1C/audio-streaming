# Music Streaming

Repo general del ecosistema. Contiene documentación, estado del proyecto y enlaces a los demás repos. **No contiene código de aplicación.**

Nombre canónico: `music-streaming`. Carpetas locales: `audio-streaming-*`.

## Alcance por versión

| Versión | Alcance |
|---------|---------|
| **v0.1.0 (actual)** | Backend + cliente **web** |
| Futuro | Desktop, mobile, mini-back local, sync |

No hay paquete `shared`. El contrato público es la API HTTP del backend (`audio-streaming-backend/docs/openapi.yaml`). Cada front futuro lo consumirá por su cuenta.

## Repos (v0.1.0)

| Carpeta local | Repo / rol |
|---------------|------------|
| [`audio-streaming-backend`](../audio-streaming-backend) | `B3RT1C/music-streaming-backend` — API + OpenAPI |
| [`audio-streaming-web`](../audio-streaming-web) | `B3RT1C/music-streaming-web` — cliente web |
| `audio-streaming` (este repo) | Documentación, roadmap y estado |

## Documentación

- [Arquitectura](./arquitectura.md) — arquitectura de v0.1.0 (back + web)
- [Roadmap futuro](./roadmap.md) — desktop / mobile / mini-back / sync (**fuera de v0.1.0**)
- [CI/CD con Jenkins](./jenkins-ci-cd.md)
- Contrato API: [`../audio-streaming-backend/docs/openapi.yaml`](../audio-streaming-backend/docs/openapi.yaml)

## Arranque local (workspace)

Desde la raíz del workspace (`../`):

```bash
docker compose up -d
cd ../audio-streaming-backend && ./mvnw spring-boot:run
cd ../audio-streaming-web && npm install && npm start
```

Variables de BBDD: ver [`../.env.example`](../.env.example).

## Estado actual (v0.1.0)

Alcance cerrado: **solo backend + web**.

- [x] Backend: listar / subir / borrar / stream (HTTP Range)
- [x] Web: biblioteca, controles, upload, UI
- [x] OpenAPI en el backend

Fuera de v0.1.0 (ver [roadmap.md](./roadmap.md)): desktop, mobile, mini-back local, sync.
