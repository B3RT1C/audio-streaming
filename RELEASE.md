# Notas de versión — v0.4.0 (actual)

Este archivo **existe siempre** y describe la **versión de producto actual**.  
Al publicar un release nuevo: copiar este fichero a `releases/vX.Y.Z.md` (versión que deja de ser actual) y reescribir `RELEASE.md`.

Biblioteca usable en web: título secundario, catálogo de artistas, etiquetas, filtros por campo y reproducción aleatoria con semilla. Incluye lo entregado en 0.3.0. Sustituye a [v0.2.0](./releases/v0.2.0.md). 0.3.0 no tiene ficha propia en `releases/`: estas notas describen el producto tal como está en 0.4.0.

## Alcance

- Backend (Spring Boot 4, Java 25) + cliente web (Angular 21)
- Sin desktop, mobile ni mini-back local
- Sin login: la API se usa dentro de la red privada

## Backend

Contrato: [`docs/openapi.yaml`](https://github.com/B3RT1C/audio-streaming-backend/blob/main/docs/openapi.yaml) (`info.version` **0.4.0**).

Flyway:

- `V2__app_0_2_0_library_metadata_and_playlists.sql` — metadata de pista y playlists (ver [v0.2.0](./releases/v0.2.0.md))
- `V3__app_0_3_0_secondary_title_artists_and_tags.sql` — `secondary_title`, tablas `artists` y `tags`, y los vínculos con canciones, artistas y playlists. Copia a `artists` los textos de artista distintos y no vacíos. No reescribe títulos, `audios.artist` ni el resto de lo importado
- 0.4.0 no añade migración. Los filtros reutilizan el plegado de texto de `q` (sin `unaccent` ni `pg_trgm`) y los índices de etiquetas de V3

Al subir o importar, jaudiotagger lee título, artista (si falta, el artista del álbum), álbum, duración y número de pista. Si no hay título, se usa el nombre del archivo sin extensión. Si ese texto de artista aún no está en `artists`, se crea la fila. Borrar el artista de una canción no borra la fila.

Formatos: `mp3`, `m4a`, `aac`, `flac`, `wav`, `ogg`, `opus`, `webm`.

### Audios

- `GET /audios` — lista, sin paginación. Los filtros de abajo se combinan con AND. Un parámetro vacío se ignora. Si nada coincide, la lista va vacía (no es un error). Sin `shuffle`, el orden es por id.
  - `q` — subcadena sin distinguir mayúsculas ni acentos, sobre título, título secundario, artista, álbum, nombre y nombre de archivo.
  - `title` — subcadena de `title` o `secondaryTitle`, sin mayúsculas ni acentos. No mira artista, álbum, nombre ni archivo.
  - `artist` — subcadena de `audios.artist`, sin mayúsculas ni acentos. Una canción sin artista no entra.
  - `tag` — repetible (`?tag=a&tag=b`). La canción tiene que tener todas. Nombre exacto, sin distinguir mayúsculas; los acentos sí distinguen (`jose` no es `José`). No es subcadena. Un nombre desconocido deja la lista vacía. Un solo `tag` se comporta como en 0.3.0.
  - `artistTag` — repetible. La fila de `artists` cuyo `name` es exactamente `audios.artist` (`Ana` y `ana` son distintas) tiene que tener todas esas etiquetas. Sin artista, o sin fila, no entra.
  - `playlistTag` — repetible. La canción está en alguna playlist que tiene todas esas etiquetas. No vale repartirlas entre playlists.
  - `shuffle=true` — devuelve el resultado ya filtrado, en orden aleatorio. `seed` entero opcional repite la misma mezcla para el mismo conjunto (en orden de id, antes de mezclar). `shuffle` distinto de `true` o `false`, o un `seed` que no sea entero cuando `shuffle=true`, responde **400** `INVALID_QUERY`. Un `seed` sin barajar se ignora.
- `GET /audios/{id}` — stream. `Content-Type` según la extensión. Un `Range` válido responde **206**. Un rango que empieza más allá del fichero responde **416** (`RANGE_NOT_SATISFIABLE`, `Content-Range: bytes */length`). Sigue siendo el audio, no el JSON.
- `POST /audios` — multipart (`file` obligatorio, `name` opcional). **201** `{ "message": "File received" }`, sin id. Por encima del límite, **413** `FILE_TOO_LARGE`. Un hash repetido se anota en el log y el alta sigue adelante.
- `PATCH /audios/{id}` — solo cambian los campos enviados. `null` borra título secundario, artista, álbum, número de pista o duración. Un título secundario en blanco se guarda como `null`. Título y nombre no se pueden vaciar. Los campos desconocidos se ignoran, incluido `tags`.
- `PUT /audios/{id}/tags` — `{ "tags": ["Live", "rock"] }` sustituye el conjunto. Crea las que no existan. `[]` las quita de la canción y no borra la etiqueta. Como mucho 100 nombres, cada uno de 1 a 100 caracteres. `Live` y `LIVE` son la misma: se conserva la grafía con la que se creó. Responde la canción.
- `DELETE /audios/{id}` — **204**. Borra metadata, fichero y la pista en todas las playlists. Quita los vínculos de etiquetas; no borra las etiquetas ni la fila de artista.

Campos de cada pista, además de los de 0.1.0 (`id`, `name`, `extension`, `path`, `contentHash`): `title`, `secondaryTitle`, `artist`, `album`, `durationSeconds`, `trackNumber`, `originalFilename`, `createdAt`, `tags`. `secondaryTitle` y `tags` vienen siempre (`null` y `[]` si no hay). Artista, álbum, duración y número de pista pueden ser `null`. `artist` sigue siendo texto, no el id de `/artists`.

### Etiquetas y artistas

- `GET /tags` — catálogo ordenado sin distinguir mayúsculas. Cada una trae `id`, `name`, `audioCount`, `artistCount` y `playlistCount`. Una etiqueta sin usos se queda, con ceros.
- `GET /artists` — una fila por texto de artista exacto (mayúsculas y espacios incluidos). `audioCount` cuenta canciones cuyo `artist` es exactamente `name`. Orden: sin distinguir mayúsculas y, a igualdad, por el texto exacto. No hay `POST /artists`. `q` es subcadena del nombre, sin mayúsculas ni acentos. `tag` repetible exige todas, con las mismas reglas de nombre que en las canciones. El orden de los que quedan no cambia.
- `PUT /artists/{id}/tags` — mismas reglas que en la canción. No cambia el nombre ni el texto de las canciones. Responde el artista.
- `PUT /playlists/{id}/tags` — mismas reglas. Responde la playlist con `items` y `tags`, y actualiza `updatedAt`.

### Playlists

- `GET /playlists` — resúmenes, sin `items` (sí `itemCount` y `tags`), ordenados por id. `q` es subcadena del nombre, sin mayúsculas ni acentos. `tag` repetible exige todas.
- `POST /playlists` `{ "name" }` — **201**, con `items: []`
- `GET /playlists/{id}` — detalle. `items` es `[{ position, audioId, audio }]`, en orden de reproducción. `audio` incluye `secondaryTitle` y `tags`. `position` empieza en 1 y puede tener huecos si se borra una canción de la biblioteca
- `PATCH /playlists/{id}` `{ "name" }` — renombra y devuelve la playlist con sus items
- `DELETE /playlists/{id}` — **204**. No borra las canciones. Quita los vínculos de etiquetas; no borra las etiquetas
- `POST /playlists/{id}/items` `{ "audioId" }` — **201** si se añade al final; **200** si ya estaba (no duplica). Devuelve el ítem, no la playlist
- `DELETE /playlists/{id}/items/{audioId}` — **204**
- `PUT /playlists/{id}/items` `{ "audioIds": [3, 1, 2] }` — reordena y reescribe `position` desde 1. También acepta el array `[3, 1, 2]`. La lista tiene que ser exactamente las pistas actuales, cada una una vez. Si no, **400** `INVALID_ORDER`

### Importar una carpeta

`POST /admin/import`, cuerpo opcional `{ "path" }`. Recorre las subcarpetas, lee etiquetas y copia al almacenamiento. No mueve ni borra los originales. Salta un fichero si su ruta original o su `contentHash` ya están guardados. Lo que no es audio se ignora (también se ignora lo que ya está dentro de la carpeta de almacenamiento).

`path` puede ser absoluto o relativo a `AUDIO_IMPORT_DIR`, y tiene que quedar dentro de esa carpeta. Si se omite, se importa `AUDIO_IMPORT_DIR`. Sin esa variable, **400** `IMPORT_NOT_CONFIGURED`. Una ruta que se sale de la carpeta base es `PATH_NOT_ALLOWED`.

Respuesta: `{ "imported", "skipped", "failed", "ignored", "errors" }`. Un fichero ilegible suma `failed` y no corta el resto. `errors` guarda hasta 100 mensajes.

La misma importación puede ejecutarse una sola vez al arrancar el jar con `--app.import.path` (detalle en el README del backend).

Errores: `{ message, code }` — `FILE_REQUIRED`, `FILE_TOO_LARGE`, `INVALID_NAME`, `INVALID_FILE`, `INVALID_BODY`, `INVALID_QUERY`, `INVALID_ORDER`, `NOT_FOUND`, `STORAGE_ERROR`, `INTERNAL`, `IMPORT_NOT_CONFIGURED`, `PATH_NOT_ALLOWED`, `RANGE_NOT_SATISFIABLE`.

Variables (las mismas que en 0.2.0):

| Variable | Default | Uso |
|----------|---------|-----|
| `AUDIO_STORAGE_DIR` | vacío | Carpeta de los audios. Vacía: se sigue usando `uploads/songs/`. Definida: ruta absoluta |
| `AUDIO_MAX_FILE_SIZE` | `500MB` | Tamaño máximo de un fichero |
| `AUDIO_MAX_REQUEST_SIZE` | `510MB` | Tamaño máximo de la petición. Al menos `AUDIO_MAX_FILE_SIZE` |
| `CORS_ALLOWED_ORIGINS` | `*` | Orígenes del navegador, separados por comas |
| `AUDIO_IMPORT_DIR` | vacío | Carpeta base de `POST /admin/import`. Vacía: el endpoint responde `IMPORT_NOT_CONFIGURED` |

Siguen `DB_URL`, `DB_USERNAME` y `DB_PASSWORD`.

## Web

- Rediseño oscuro tipo Spotify, pensado primero para el móvil
- Biblioteca con título, título secundario, artista, álbum, número de pista, duración y etiquetas
- Buscador (`GET /audios?q=`, espera de 300 ms), también sobre el título secundario
- Panel de filtros (plegable en el móvil): título, artista, etiquetas de canción, de artista y de lista. Se combinan entre sí y con la búsqueda. Los activos se ven como chips quitables y se pueden limpiar. La vista filtrada queda en la query string
- Filtro por nombre y por etiquetas en Playlists y en Artistas
- Edición de título, título secundario, artista, álbum, número de pista y etiquetas
- Vista de artistas (conteo y etiquetas) y vista de etiquetas
- Playlists: crear, renombrar, borrar, etiquetar, añadir, quitar y reordenar
- Reproducir la biblioteca, el resultado filtrado o una lista. El botón **Aleatorio** baraja esa lista completa en el cliente, con una semilla, sin repetir canciones hasta acabarla. El reproductor sigue pudiendo mezclar la cola y repetir (una o todas); si el aleatorio está activo, se indica
- Barra de progreso con salto
- Media Session (controles de la pantalla de bloqueo), cuando el navegador lo permite
- Subida con barra de progreso, hasta 500 MB, y aviso si el archivo no es audio o se pasa del límite
- Listados largos con ventana virtual: solo se pintan las filas visibles
- PWA instalable (`manifest.webmanifest` e iconos). El service worker no guarda caché: no hay modo sin conexión. Hace falta HTTPS o localhost para instalarla
- `apiUrl` en `config.json` (en desarrollo, `public/config.json`; el build lo copia junto al `index.html`). Se lee al abrir la página. Vacío o ausente: `{protocolo}//{hostname}:8080`. No hace falta recompilar
- `PlaybackResolver` sigue armando la URL de stream a partir de ese origen

## Ops

- Repos: `audio-streaming`, `audio-streaming-backend`, `audio-streaming-web`
- Instalación manual en un mini PC Windows de casa, accesible solo por Tailscale y la LAN. API en el puerto **8090**, web en el **8091**, Postgres en WSL con la base `audio_streaming`. Despliegue a petición, sin auto-update. En esa web, `config.json` apunta `apiUrl` al mismo host en el puerto 8090, y `CORS_ALLOWED_ORIGINS` tiene que permitir el origen de la página (puerto 8091)
- Jenkins está parado. El diseño de aquel CI/CD queda en [jenkins-ci-cd.md](./jenkins-ci-cd.md) como histórico

## Fuera de esta versión

Siguiente trabajo → [roadmap.md](./roadmap.md): tags con tipo y filtro unificado (0.4.1), escucha y estadísticas, tests, desktop y sync.

`GET /sync/status` sigue declarado en el OpenAPI y no tiene controlador.
