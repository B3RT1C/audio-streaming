# Notas de versión — v0.1.0

Primera versión usable del ecosistema **audio-streaming**.

## Alcance

- Backend (Spring Boot) + cliente web (Angular)
- Sin desktop, mobile ni mini-back local

## Backend

- `GET /audios` — listar metadata
- `GET /audios/{id}` — streaming con HTTP Range
- `POST /audios` — subir MP3
- `DELETE /audios/{id}` — borrar metadata + fichero
- `contentHash` (SHA-256) en cada pista
- Seed con hashes; secuencia de IDs sincronizada tras el seed
- Conflicto 409 si el nombre ya existe
- OpenAPI: [`docs/openapi.yaml`](https://github.com/B3RT1C/audio-streaming-backend/blob/main/docs/openapi.yaml)

## Web

- Lista de canciones, selección por defecto
- Controles previous / play-pause / stop / next + barra de progreso
- Upload (selector + drag & drop) con feedback
- Borrar con confirmación
- Mensajes de error del backend (p. ej. nombre duplicado)
- `PlaybackResolver` para no acoplar el player a la URL del back

## Ops

- Repos: `audio-streaming`, `audio-streaming-backend`, `audio-streaming-web`
- Jenkins en LAN con Poll SCM y staging (ver [jenkins-ci-cd.md](./jenkins-ci-cd.md))

## Fuera de esta versión

Desktop, mobile, mini-back local y sync → [roadmap.md](./roadmap.md).
