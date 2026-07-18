# Arquitectura

Documento del repo general [`audio-streaming`](https://github.com/B3RT1C/audio-streaming).

## Alcance v0.1.0

Solo **back central + cliente web**. No hay desktop, mobile ni mini-back local en esta versión.

```mermaid
flowchart LR
  webFront[WebFront] -->|HTTP REST| centralBack[CentralBack]
  centralBack --> db[(PostgreSQL)]
  centralBack --> files[MP3 storage]
```

El contrato HTTP vive en el backend: [openapi.yaml](https://github.com/B3RT1C/audio-streaming-backend/blob/main/docs/openapi.yaml). No hay paquete shared.

## Responsabilidades (v0.1.0)

- **Back central**: catálogo, metadata, almacenamiento y contrato OpenAPI.
- **Front web**: UI y reproducción online vía `PlaybackResolver` (URL del back central).

## Contrato de reproducción

- `GET /song`
- `GET /song/file?id=` con soporte HTTP Range
- `POST /song/file`
- `DELETE /song?id=`

## Más adelante

Desktop, mobile, mini-back local y sync quedan fuera de v0.1.0. Ver [roadmap.md](./roadmap.md).
