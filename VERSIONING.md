# Versionado

Una sola **versión de producto** para el ecosistema (hoy **0.1.0**). No hay versiones compuestas tipo `app_back` / `app_front`.

## Fuente de verdad

| Pieza | Dónde | Debe coincidir con |
|-------|--------|-------------------|
| Producto actual | Este doc + [README](./README.md) + [RELEASE.md](./RELEASE.md) | `MAJOR.MINOR.PATCH` |
| Notas de release | [RELEASE.md](./RELEASE.md) (**siempre presente**) | versión actual |
| Histórico de releases | [`releases/vX.Y.Z.md`](./releases/) | solo versiones **ya sustituidas** (vacío hasta el primer bump) |
| Tags git | `audio-streaming-backend`, `audio-streaming-web` (y docs si hay release notes) | `vMAJOR.MINOR.PATCH` |
| OpenAPI | `audio-streaming-backend/docs/openapi.yaml` → `info.version` | misma versión (sin `v`) |
| Maven | `audio-streaming-backend/pom.xml` → `/project/version` | misma versión |
| npm | `audio-streaming-web/package.json` (+ root de `package-lock.json`) | misma versión |
| Flyway | `…/db/migration/V{seq}__app_{x}_{y}_{z}_….sql` | `{seq}` ordena; `app_x_y_z` = producto en el que entró el cambio |

Roadmap ([roadmap.md](./roadmap.md)) define **features** por release; este doc define **cómo se numera**.  
**`v1.0.0`**: cuando desktop + sync mini-back ↔ backend estén listos (ver roadmap).

## Reglas

1. Un release = mismo número en OpenAPI, Maven, npm y tags `v*` de backend + web.
2. Bump de artefactos **en release** (o al abrir la siguiente), no en cada PR.
3. Flyway solo añade migración si cambia el esquema; el `app_*` de esa migración es la versión de producto de ese cambio.
4. No renombrar migraciones ya aplicadas sin repair de `flyway_schema_history`.
5. Entre releases se puede usar `X.Y.Z-SNAPSHOT` en Maven si se prefiere; al etiquetar, quitar `-SNAPSHOT` y alinear todo.
6. [RELEASE.md](./RELEASE.md) se actualiza en cada release; el contenido anterior se archiva en `releases/vX.Y.Z.md` (no en la raíz del repo).
7. No crear `releases/vP.md` mientras `P` sea todavía la versión actual (evita duplicar `RELEASE.md`).

## Checklist de release

```
- [ ] Decidir versión N (p. ej. 0.1.1 / 0.2.0)
- [ ] OpenAPI `info.version` = N
- [ ] pom.xml version = N
- [ ] package.json (+ lock root) = N
- [ ] Copiar RELEASE.md → releases/v{anterior}.md
- [ ] Reescribir RELEASE.md para la versión N
- [ ] Actualizar README (versión actual) si aplica
- [ ] Migraciones Flyway nuevas (si hay) con `__app_x_y_z_`
- [ ] Tag anotado `vN` en backend y web (+ push)
- [ ] Jenkins archive del tag (sin deploy staging)
```

## Comprobar

Skill del workspace: `workspace-version-check` (revisa drift entre producto, artefactos, tags, `RELEASE.md` / `releases/`, y Flyway).  
Rule: `.cursor/rules/release-notes.mdc`.
