# Audio Streaming

Repo general del ecosistema. Contiene documentación, estado del proyecto y enlaces a los demás repos. **No contiene código de aplicación.**

Repos en GitHub: `audio-streaming-*`.

## Alcance por versión

| Versión | Alcance |
|---------|---------|
| **v0.1.0 (actual)** | Backend + cliente **web** (en `main`) |
| Futuro | Desktop, mobile, mini-back local, sync |

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
- [Notas de versión v0.1.0](./RELEASE_v0.1.0.md)
- [Roadmap futuro](./roadmap.md) — desktop / mobile / mini-back / sync (**fuera de v0.1.0**)
- [CI/CD con Jenkins](./jenkins-ci-cd.md)
- [Informe de despliegue Jenkins](./jenkins-deploy-report.md)

## Cómo clonar el ecosistema

```bash
mkdir audio-streaming && cd audio-streaming
git clone https://github.com/B3RT1C/audio-streaming.git
git clone https://github.com/B3RT1C/audio-streaming-backend.git
git clone https://github.com/B3RT1C/audio-streaming-web.git
```

Para Postgres local (si tienes el `docker-compose.yml` del workspace):

```bash
docker compose up -d
```

## Arranque local

```bash
cd audio-streaming-backend && ./mvnw spring-boot:run
# otra terminal
cd audio-streaming-web && npm install && npm start
```

- API: `http://localhost:8080`
- Web: `http://localhost:4200`

Variables de BBDD opcionales: `DB_URL`, `DB_USERNAME`, `DB_PASSWORD`.

Más detalle: READMEs de [backend](https://github.com/B3RT1C/audio-streaming-backend) y [web](https://github.com/B3RT1C/audio-streaming-web).

## Estado actual (v0.1.0)

Alcance cerrado: **solo backend + web**, mergeado en `main` de cada repo.

- [x] Backend: listar / subir / borrar / stream (HTTP Range) + `contentHash`
- [x] Web: biblioteca, controles, upload, delete, estados y errores
- [x] OpenAPI en el backend
- [x] CI/CD Jenkins con staging (API :8080, web :8083)

Fuera de v0.1.0 (ver [roadmap.md](./roadmap.md)): desktop, mobile, mini-back local, sync.
