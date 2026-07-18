# Roadmap (posterior a v0.1.0)

**Fuera del alcance de v0.1.0.** La versión inicial es solo backend + web.

El contrato HTTP ya está documentado en [openapi.yaml](https://github.com/B3RT1C/audio-streaming-backend/blob/main/docs/openapi.yaml).

## Objetivo a futuro

Reproducción offline y sincronización tipo Spotify entre el back central y una copia local por plataforma (desktop / mobile; web opcional).

## Fases propuestas

### v0.2.0 — Mini-back local en desktop

- Embeber un servidor ligero en la app desktop.
- Exponer el mismo contrato REST en `http://localhost:<puerto-local>`.
- El resolver de reproducción elige URL local si el fichero existe en caché.

### v0.3.0 — Sincronización

- Implementar `GET /sync/status`.
- Ampliar el modelo `Track` (`checksum` / `updatedAt` además del `contentHash` actual).
- `SyncService` por plataforma: descargar, subir y reconciliar.

### v0.4.0 — Mobile

- Cliente mobile contra la misma API del back.
- Mini-back local adaptado al runtime móvil.

## Reglas de diseño (cuando llegue esa fase)

1. El player no conoce sync ni URLs concretas.
2. El mini-back local replica el contrato del back central.
3. La sincronización corre en background y no bloquea la reproducción.
4. Sin paquete shared: cada front implementa su cliente HTTP contra el OpenAPI del backend.
