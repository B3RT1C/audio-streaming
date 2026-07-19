# Roadmap (posterior a v0.1.0)

**Fuera del alcance de v0.1.0.** La versión actual es solo backend + web (ver [RELEASE.md](./RELEASE.md)).

El contrato HTTP está en el backend:

[docs/openapi.yaml](https://github.com/B3RT1C/audio-streaming-backend/blob/main/docs/openapi.yaml)

Versionado: [VERSIONING.md](./VERSIONING.md). **`v1.0.0`** = desktop usable + sync mini-back ↔ backend central.

## Objetivo

Biblioteca rica en web, luego desktop con mini-back y sincronización al central. Después: más clientes, carátulas y avisos de novedades vía APIs externas.

## Camino a 1.0 (`0.2` → `0.10`)

### v0.2.0 — Refresh UI web

- Reestructurar la UI web actual (navegación / composición) antes de acumular pantallas nuevas.
- Sin cambio grande de contrato API salvo lo mínimo.

### v0.3.0 — Metadata de biblioteca

- Autor / artista de la canción.
- Título secundario (p. ej. otro idioma).
- Tags en **canciones** y **artistas** (modelo, API, UI de edición).
- Migraciones Flyway: `V{n}__app_0_3_0_…`.

### v0.4.0 — Playlists

- CRUD de playlists, añadir/quitar tracks, orden.
- Tags en **playlists**.
- UI web de gestión.

### v0.5.0 — Filtros y reproducción aleatoria

- Filtros por nombre de canción, artista y tags (también tags de playlist donde aplique).
- Shuffle / reproducción aleatoria en el player web.

### v0.6.0 — Escucha y estadísticas

- Eventos de reproducción (tiempo escuchado, conteos).
- Rankings: artistas, canciones, tags, playlists; por días, meses, años.
- Antes de usuarios: stats de la instancia; con usuarios (0.9) pueden pasar a por usuario.

### v0.7.0 — Testing más sólido

- Backend: Karate (contrato / API).
- Frontend: Playwright (+ Cucumber si encaja).
- Humo e2e estable en Jenkins.

### v0.8.0 — Desktop + mini-back local

- App desktop con mini-back local (mismo contrato OpenAPI en localhost).
- `PlaybackResolver` elige URL local si el fichero está en caché.

### v0.9.0 — Usuarios

- Cuentas / auth cuando ya existe otra plataforma además de web (desktop).
- Ownership de playlists y preferencias.

### v0.10.0 — Sincronización

- Sync entre backend central y mini-back desktop (`sync/status`, checksums, reconcile).
- Reglas de diseño:
  1. El player no conoce sync ni URLs concretas.
  2. El mini-back local replica el contrato del back central.
  3. La sincronización corre en background y no bloquea la reproducción.
  4. Sin paquete shared: cada cliente HTTP contra el OpenAPI del backend.

## v1.0.0 — Desktop + sync listos

Hito (no feature nueva obligatoria): versión **desktop** usable y **sincronización** mini-back ↔ backend en verde.

- Tags / OpenAPI / Maven / npm en `1.0.0`, [RELEASE.md](./RELEASE.md) actualizado, archive `RELEASE_v1.0.0.md` si se archiva el anterior.
- A partir de aquí, breaking changes de API con más rigor semver.

## Post-1.0

### v1.1.0 — Mobile

- Cliente mobile + mini-back adaptado; reutiliza sync.

### v1.2.0 — TUI

- Cliente terminal contra la misma API.

### v1.3.0 — Carátulas

- Covers de canciones (API + UI en clientes existentes).

### v1.4.0 — Avisos de novedades de artistas

- Detectar lanzamientos nuevos vía APIs **Spotify**, **YouTube** y **SoundCloud**.
- Complejidad: OAuth/credenciales, rate limits, identidad de artista, jobs, bandeja de avisos.
- Depende de metadata de artistas (0.3+) y usuarios/preferencias (0.9+).

## Transversal / ops

- **Logs** (auditoría / ops): tras usuarios o junto a desktop; no bloquea el camino a 1.0.
- Testing: adoptar en 0.7 y mantener en releases siguientes.
