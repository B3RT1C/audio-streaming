# Roadmap (posterior a v0.4.0)

**v0.4.0 está entregada** (backend + web). Notas: [RELEASE.md](./RELEASE.md). Lo que sigue es el plan hasta el escritorio. v0.3.0 y v0.4.0 quedan abajo, ya entregadas; el siguiente paso es 0.4.1.

El contrato HTTP está en el backend:

[docs/openapi.yaml](https://github.com/B3RT1C/audio-streaming-backend/blob/main/docs/openapi.yaml)

Versionado: [VERSIONING.md](./VERSIONING.md). **`v1.0.0`** = desktop usable + sync mini-back ↔ backend central.

## Objetivo

Biblioteca rica en web, luego desktop con mini-back y sincronización al central. Después: más clientes, carátulas y avisos de novedades vía APIs externas.

## Qué juntó v0.2.0

El plan anterior repartía el refresco de UI (0.2), la metadata (0.3) y las playlists (0.4). Salió junto:

- Refresh de la UI web.
- Parte de la metadata de la pista: artista, álbum, duración y número de pista. El título secundario, la tabla de artistas y los tags salieron después, en 0.3.0.
- Playlists: CRUD, añadir, quitar y orden. Los tags de playlist salieron en 0.3.0.
- El player ya baraja la cola y repite. La búsqueda `q` no sustituye a los filtros por campo: esos, el `q` de playlists y artistas, y el shuffle con semilla salieron en 0.4.0.

## Camino a 1.0 (`0.3` → `0.9`)

0.3.0 y 0.4.0 están entregadas. El trabajo abierto empieza en 0.4.1.

### v0.3.0 — Título secundario y tags (entregada)

- Título secundario (p. ej. otro idioma).
- Tabla de artistas.
- Tags en **canciones**, **artistas** y **playlists** (modelo, API y UI de edición).
- Migración Flyway: `V3__app_0_3_0_secondary_title_artists_and_tags.sql`.

### v0.4.0 — Filtros y reproducción aleatoria (entregada)

- Filtros por `title`, `artist` y por `tag`, `artistTag` y `playlistTag` (repetibles, combinados con AND).
- `q` en `GET /playlists` y `GET /artists`.
- `shuffle` con `seed`.
- En la web, botón **Aleatorio**.
- Sin migración nueva. El player de 0.2.0 ya barajaba la cola y repetía (una o todas); este punto cierra el aleatorio con semilla junto a los filtros.

### v0.4.1 — Tags con tipo y filtro unificado

- Las etiquetas pasan a tener tipo. Tipos del sistema, fijos, que no se pueden borrar ni renombrar: artista, género, álbum, año e idioma. Además, etiquetas libres creadas por el usuario (tipo libre / usuario), que sí se pueden crear, renombrar y borrar.
- Las etiquetas de sistema se generan a partir de los datos existentes (artista y álbum de la canción, año y género de los metadatos del fichero) sin modificar títulos ni el texto de artista importado. El idioma se asigna a mano.
- Artistas y playlists siguen siendo entidades propias (ficha, orden, estadísticas futuras), pero el filtro los trata como un tipo más.
- Buscador y filtro unificados, con prefijos (p. ej. `artista:`, `género:`, `álbum:`, `año:`, `idioma:`, `playlist:`, y etiquetas libres sin prefijo o con `#`), combinables con AND, con autocompletado y chips de color por tipo.
- Compatibilidad con los parámetros de 0.4.0.

### v0.5.0 — Escucha y estadísticas

- Eventos de reproducción (tiempo escuchado, conteos).
- Rankings: artistas, canciones, tags, playlists; por días, meses, años.
- Antes de usuarios: stats de la instancia; con usuarios (0.8) pueden pasar a por usuario.

### v0.6.0 — Testing más sólido

- Backend: Karate (contrato / API).
- Frontend: Playwright (+ Cucumber si encaja).
- Humo e2e estable. El Jenkins de la época 0.1 está parado; ver [jenkins-ci-cd.md](./jenkins-ci-cd.md).

### v0.7.0 — Desktop + mini-back local

- App desktop con mini-back local (mismo contrato OpenAPI en localhost).
- `PlaybackResolver` elige URL local si el fichero está en caché.

### v0.8.0 — Usuarios

- Cuentas / auth cuando ya existe otra plataforma además de web (desktop).
- Ownership de playlists y preferencias.

### v0.9.0 — Sincronización

- Sync entre backend central y mini-back desktop (`sync/status`, checksums, reconcile).
- Reglas de diseño:
  1. El player no conoce sync ni URLs concretas.
  2. El mini-back local replica el contrato del back central.
  3. La sincronización corre en background y no bloquea la reproducción.
  4. Sin paquete shared: cada cliente HTTP contra el OpenAPI del backend.

## v1.0.0 — Desktop + sync listos

Hito (no feature nueva obligatoria): versión **desktop** usable y **sincronización** mini-back ↔ backend en verde.

- Tags / OpenAPI / Maven / npm en `1.0.0`, [RELEASE.md](./RELEASE.md) actualizado; archivar la previa en `releases/vX.Y.Z.md`.
- A partir de aquí, breaking changes de API con más rigor semver.

## Post-1.0

### v1.1.0 — Mobile

- Cliente mobile + mini-back adaptado; reutiliza sync.

### v1.2.0 — TUI

- Cliente terminal contra la misma API.

### v1.3.0 — Carátulas

- Covers de canciones (API + UI en clientes existentes).
- La web 0.2.0 solo pinta una carátula generada (iniciales y color) y no lee la imagen embebida del fichero. Esa extracción podría adelantarse si el player la necesita antes.

### v1.4.0 — Avisos de novedades de artistas

- Detectar lanzamientos nuevos vía APIs **Spotify**, **YouTube** y **SoundCloud**.
- Complejidad: OAuth/credenciales, rate limits, identidad de artista, jobs, bandeja de avisos.
- Depende de metadata de artistas (0.3+) y usuarios/preferencias (0.8+).

## Transversal / ops

- **Logs** (auditoría / ops): tras usuarios o junto a desktop; no bloquea el camino a 1.0.
- Testing: adoptar en 0.6 y mantener en releases siguientes.
