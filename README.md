# Audio Streaming

Repo general del ecosistema. Contiene documentación, estado del proyecto y enlaces a los demás repos. **No contiene código de aplicación.**

Repos en GitHub: `audio-streaming-*`.

## Alcance por versión

| Versión | Alcance |
|---------|---------|
| **v0.1.0 (actual)** | Backend + cliente **web** |
| Futuro | Desktop, mobile, mini-back local, sync |

No hay paquete `shared`. El contrato público es la API HTTP del backend ([OpenAPI](./openapi.yaml)). Cada front futuro lo consumirá por su cuenta.

## Repos (v0.1.0)

| Repo | Rol |
|------|-----|
| [audio-streaming-backend](https://github.com/B3RT1C/audio-streaming-backend) | API central + OpenAPI |
| [audio-streaming-web](https://github.com/B3RT1C/audio-streaming-web) | Cliente web |
| [audio-streaming](https://github.com/B3RT1C/audio-streaming) (este repo) | Documentación, roadmap y estado |

## Documentación

- [Arquitectura](./arquitectura.md) — arquitectura de v0.1.0 (back + web)
- [Roadmap futuro](./roadmap.md) — desktop / mobile / mini-back / sync (**fuera de v0.1.0**)
- [CI/CD con Jenkins](./jenkins-ci-cd.md)
- Contrato API: [openapi.yaml](./openapi.yaml)

## Arranque local (workspace multi-carpeta)

Si clonas los repos hermanos juntos en el mismo directorio:

```bash
# postgres
docker compose up -d   # compose del workspace local, si lo tienes

cd audio-streaming-backend && ./mvnw spring-boot:run
cd audio-streaming-web && npm install && npm start
```

- API: `http://localhost:8080`
- Web: `http://localhost:4200`

Variables de BBDD opcionales: `DB_URL`, `DB_USERNAME`, `DB_PASSWORD` (ver `.env.example` del workspace local).

## Estado actual (v0.1.0)

Alcance cerrado: **solo backend + web**.

- [x] Backend: listar / subir / borrar / stream (HTTP Range)
- [x] Web: biblioteca, controles, upload, UI
- [x] OpenAPI en el backend

Fuera de v0.1.0 (ver [roadmap.md](./roadmap.md)): desktop, mobile, mini-back local, sync.
