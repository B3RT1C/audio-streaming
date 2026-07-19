# Notas de versión — v0.1.0 (actual)

Este archivo **existe siempre** y describe la **versión de producto actual**.  
Al publicar un release nuevo: copiar este fichero a `releases/vX.Y.Z.md` (versión que deja de ser actual) y reescribir `RELEASE.md`.

Primera versión usable del ecosistema **audio-streaming**.

## Alcance

- Backend (Spring Boot) + cliente web (Angular)
- Sin desktop, mobile ni mini-back local

## Backend

- `GET /audios` — listar metadata
- `GET /audios/{id}` — streaming con HTTP Range
- `POST /audios` — subir MP3 (`name` opcional; títulos repetibles; fichero con `storageKey` UUID)
- `DELETE /audios/{id}` — borrar metadata + fichero
- `contentHash` (SHA-256) en cada pista (informativo; no bloquea duplicados)
- Errores API: `{ message, code }` (`FILE_REQUIRED`, `INVALID_NAME`, `NOT_FOUND`, …)
- Seed con hashes; secuencia de IDs sincronizada tras el seed
- OpenAPI: [`docs/openapi.yaml`](https://github.com/B3RT1C/audio-streaming-backend/blob/main/docs/openapi.yaml)

## Web

- Lista de canciones, selección por defecto
- Controles previous / play-pause / stop / next + barra de progreso
- Upload (selector + drag & drop) con diálogo de nombre
- Borrar con confirmación
- Errores de API / red / reproducción mapeados a mensajes en español
- `PlaybackResolver` para no acoplar el player a la URL del back

## Ops

- Repos: `audio-streaming`, `audio-streaming-backend`, `audio-streaming-web`
- Jenkins en LAN con Multibranch scan (~5 min) y staging (ver [jenkins-ci-cd.md](./jenkins-ci-cd.md))

## Fuera de esta versión

Siguiente trabajo → [roadmap.md](./roadmap.md).
