# Versionado

Una sola **versión de producto** para el ecosistema (hoy **0.1.0**). No hay versiones compuestas tipo `app_back` / `app_front`.

## Fuente de verdad

| Pieza | Dónde | Debe coincidir con |
|-------|--------|-------------------|
| Producto actual | Este doc + [README](./README.md) + [RELEASE_v\*.md](./RELEASE_v0.1.0.md) | `MAJOR.MINOR.PATCH` |
| Tags git | `audio-streaming-backend`, `audio-streaming-web` (y docs si hay release notes) | `vMAJOR.MINOR.PATCH` |
| OpenAPI | `audio-streaming-backend/docs/openapi.yaml` → `info.version` | misma versión (sin `v`) |
| Maven | `audio-streaming-backend/pom.xml` → `/project/version` | misma versión |
| npm | `audio-streaming-web/package.json` (+ root de `package-lock.json`) | misma versión |
| Flyway | `…/db/migration/V{seq}__app_{x}_{y}_{z}_….sql` | `{seq}` ordena; `app_x_y_z` = producto en el que entró el cambio |

Roadmap ([roadmap.md](./roadmap.md)) define **features** por release; este doc define **cómo se numera**.

## Reglas

1. Un release = mismo número en OpenAPI, Maven, npm y tags `v*` de backend + web.
2. Bump de artefactos **en release** (o al abrir la siguiente), no en cada PR.
3. Flyway solo añade migración si cambia el esquema; el `app_*` de esa migración es la versión de producto de ese cambio.
4. No renombrar migraciones ya aplicadas sin repair de `flyway_schema_history`.
5. Entre releases se puede usar `X.Y.Z-SNAPSHOT` en Maven si se prefiere; al etiquetar, quitar `-SNAPSHOT` y alinear todo.

## Checklist de release

```
- [ ] Decidir versión N (p. ej. 0.1.1 / 0.2.0)
- [ ] OpenAPI `info.version` = N
- [ ] pom.xml version = N
- [ ] package.json (+ lock root) = N
- [ ] RELEASE_vN.md + mención en README si aplica
- [ ] Migraciones Flyway nuevas (si hay) con `__app_x_y_z_`
- [ ] Tag anotado `vN` en backend y web (+ push)
- [ ] Jenkins archive del tag (sin deploy staging)
```

## Comprobar

Skill del workspace: `workspace-version-check` (revisa drift entre producto, artefactos, tags y Flyway).
