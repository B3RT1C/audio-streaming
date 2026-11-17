# Audio Streaming

Repo general del ecosistema. Contiene documentación, estado del proyecto y enlaces a los demás repos. **No contiene código de aplicación.**

Repos en GitHub: `audio-streaming-*`.

## Alcance por versión

| Versión | Alcance |
|---------|---------|
| **v0.2.0 (actual)** | Backend + cliente **web**: metadata de pista, playlists, búsqueda e importación de carpeta |
| Camino a **v1.0.0** | Tags y filtros → escucha → desktop + sync (ver [roadmap.md](./roadmap.md)) |
| Post-1.0 | Mobile, TUI, carátulas, avisos externos |

No hay paquete `shared`. El contrato público es la API HTTP del backend:

- OpenAPI (fuente de verdad): [audio-streaming-backend/docs/openapi.yaml](https://github.com/B3RT1C/audio-streaming-backend/blob/main/docs/openapi.yaml)

## Repos (v0.2.0)

| Repo | Rol |
|------|-----|
| [audio-streaming-backend](https://github.com/B3RT1C/audio-streaming-backend) | API central + OpenAPI |
| [audio-streaming-web](https://github.com/B3RT1C/audio-streaming-web) | Cliente web |
| [audio-streaming](https://github.com/B3RT1C/audio-streaming) (este repo) | Documentación, roadmap y estado |

## Documentación

- [Arquitectura](./arquitectura.md) — arquitectura de v0.2.0 (back + web)
- [Versionado](./VERSIONING.md) — producto, tags, OpenAPI, Maven, npm, Flyway
- [Notas de versión (actual)](./RELEASE.md) — siempre la release vigente; histórico en [`releases/`](./releases/)
- [Roadmap](./roadmap.md) — plan post-v0.2.0 hasta 1.0 (desktop+sync) y después
- [CI/CD con Jenkins](./jenkins-ci-cd.md) — histórico: Jenkins está parado

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

PostgreSQL en `localhost:5432`. Por defecto la base es `music-streaming-db`, usuario y clave `postgres`. Eso es el defecto de desarrollo. La instalación de casa usa otra base (`audio_streaming`); en local se deja el defecto salvo que definas `DB_URL`.

```bash
cd audio-streaming-backend && ./mvnw spring-boot:run
# otra terminal
cd audio-streaming-web && npm install && npm start
```

- API: `http://localhost:8080`
- Web: `http://localhost:4200` (en local pide la API en el puerto 8080 del mismo host)

Variables del backend:

| Variable | Default | Uso |
|----------|---------|-----|
| `DB_URL` | `jdbc:postgresql://localhost:5432/music-streaming-db` | JDBC |
| `DB_USERNAME` | `postgres` | Usuario de la base |
| `DB_PASSWORD` | `postgres` | Clave de la base |
| `AUDIO_STORAGE_DIR` | vacío | Carpeta de los audios. Vacía: `uploads/songs/`. Definida: ruta absoluta |
| `AUDIO_MAX_FILE_SIZE` | `500MB` | Tamaño máximo de un fichero |
| `AUDIO_MAX_REQUEST_SIZE` | `510MB` | Tamaño máximo de la petición. Al menos `AUDIO_MAX_FILE_SIZE` |
| `CORS_ALLOWED_ORIGINS` | `*` | Orígenes del navegador, separados por comas |
| `AUDIO_IMPORT_DIR` | vacío | Carpeta base de `POST /admin/import`. Vacía: el endpoint responde `IMPORT_NOT_CONFIGURED` |

La web lee `config.json` al abrir la página (`public/config.json` en desarrollo). `apiUrl` vacío, o un fichero ausente, dejan el mismo host en el puerto 8080. Para otro sitio:

```json
{
  "apiUrl": "http://localhost:8080"
}
```

Más detalle: READMEs de [backend](https://github.com/B3RT1C/audio-streaming-backend) y [web](https://github.com/B3RT1C/audio-streaming-web).

## Estado actual (v0.2.0)

Alcance cerrado en `main` de cada repo: **backend + web**.

- [x] Backend (Spring Boot 4, Java 25): listar con `q`, subir, editar metadata, borrar, stream con Range (206/416), playlists y `POST /admin/import`
- [x] Web (Angular 21): UI oscura tipo Spotify, biblioteca, búsqueda, playlists, PWA y Media Session. `apiUrl` en `config.json` (por defecto, el mismo host en el puerto 8080)
- [x] OpenAPI 0.2.0 en el backend
- [x] Instalación de casa, manual y a petición (sin auto-update). Mini PC Windows, solo Tailscale y la LAN: API `:8090`, web `:8091`, Postgres en WSL (DB `audio_streaming`). En esa web, `config.json` apunta la API al puerto 8090

Jenkins está parado: ya no hay staging automático. El diseño de aquel CI/CD se conserva en [jenkins-ci-cd.md](./jenkins-ci-cd.md) como histórico.

Fuera de v0.2.0: ver [roadmap.md](./roadmap.md) (próximo foco: título secundario y tags; **1.0** = desktop + sync).
