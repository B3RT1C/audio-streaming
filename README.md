# Audio Streaming

Repo general del ecosistema. Contiene documentación, estado del proyecto y enlaces a los demás repos. **No contiene código de aplicación.**

Repos en GitHub: `audio-streaming-*`.

## Alcance por versión

| Versión | Alcance |
|---------|---------|
| **v0.1.0 (actual)** | Backend + cliente **web** (en `main`) |
| Camino a **v1.0.0** | Biblioteca web rica → desktop + sync (ver [roadmap.md](./roadmap.md)) |
| Post-1.0 | Mobile, TUI, carátulas, avisos externos |

No hay paquete `shared`. El contrato público es la API HTTP del backend:

- OpenAPI (fuente de verdad): [audio-streaming-backend/docs/openapi.yaml](https://github.com/B3RT1C/audio-streaming-backend/blob/main/docs/openapi.yaml)

## Repos (v0.1.0)

| Repo | Rol |
|------|-----|
| [audio-streaming-backend](https://github.com/B3RT1C/audio-streaming-backend) | API central + OpenAPI |
| [audio-streaming-web](https://github.com/B3RT1C/audio-streaming-web) | Cliente web |
| [audio-streaming](https://github.com/B3RT1C/audio-streaming) (este repo) | Documentación, roadmap y estado |

## Documentación

- [Arquitectura](./arquitectura.md) — arquitectura de v0.1.0 (back + web)
- [Versionado](./VERSIONING.md) — producto, tags, OpenAPI, Maven, npm, Flyway
- [Notas de versión (actual)](./RELEASE.md) — siempre la release vigente; histórico en [`releases/`](./releases/)
- [Roadmap](./roadmap.md) — plan post-v0.1.0 hasta 1.0 (desktop+sync) y después
- [CI/CD con Jenkins](./jenkins-ci-cd.md)

## Cómo clonar el ecosistema

En local conviene tener los repos hermanos en la misma carpeta (workspace):

```bash
mkdir audio-streaming && cd audio-streaming
git clone https://github.com/B3RT1C/audio-streaming.git
git clone https://github.com/B3RT1C/audio-streaming-backend.git
git clone https://github.com/B3RT1C/audio-streaming-web.git
```

```
audio-streaming/                 ← carpeta local (no es un repo)
├── audio-streaming/             → docs (este repo)
├── audio-streaming-backend/     → API + OpenAPI
└── audio-streaming-web/         → cliente web
```

## Arranque local

Necesitas PostgreSQL en `localhost:5432` (DB `music-streaming-db`, user/pass `postgres` por defecto). En el servidor de staging eso va por WSL; en desarrollo, el Postgres que tengas instalado.

```bash
cd audio-streaming-backend && ./mvnw spring-boot:run
# otra terminal
cd audio-streaming-web && npm install && npm start
```

- API: `http://localhost:8080`
- Web: `http://localhost:4200`

Variables opcionales: `DB_URL`, `DB_USERNAME`, `DB_PASSWORD` (ver README del [backend](https://github.com/B3RT1C/audio-streaming-backend)).

Más detalle: READMEs de [backend](https://github.com/B3RT1C/audio-streaming-backend) y [web](https://github.com/B3RT1C/audio-streaming-web).

## Estado actual (v0.1.0)

Alcance cerrado: **solo backend + web**, mergeado en `main` de cada repo.

- [x] Backend: listar / subir / borrar / stream (HTTP Range) + `contentHash`
- [x] Web: biblioteca, controles, upload, delete, estados y errores
- [x] OpenAPI en el backend
- [x] CI/CD Jenkins (Multibranch scan ~5 min): tests → integration → staging  
  LAN: Jenkins `:8081`, API staging `:8080/audios`, web staging `:8083`  
  Detalle: [jenkins-ci-cd.md](./jenkins-ci-cd.md)

Fuera de v0.1.0: ver [roadmap.md](./roadmap.md) (próximo foco: UI/metadata/playlists en web; **1.0** = desktop + sync).
