# claude-skills_MC

Marketplace personal de plugins de Claude Code. El repo es a la vez marketplace
(`.claude-plugin/marketplace.json`) y contenedor de plugins (`plugins/<plugin>/`); hoy solo
publica un plugin, `mc-skills`, con dos skills independientes, pensadas para usarse en cualquier
proyecto/cliente sin importar el repo en el que se esté trabajando.

Este repo **sí es la fuente de verdad** que Claude Code instala directamente — no hace falta
copiar nada a mano a `~/.claude/skills/`. Si editas una skill, comitea/pushea aquí y actualiza el
plugin en cada máquina (ver [Instalación](#instalación)).

## Estructura

```
claude-skills_MC/
  .claude-plugin/marketplace.json        marketplace "claude-skills-mc"
  plugins/mc-skills/
    .claude-plugin/plugin.json           plugin "mc-skills"
    skills/
      analizar-requerimiento/SKILL.md
      implementar-requerimiento/SKILL.md
```

## Skills (dentro del plugin `mc-skills`)

### `implementar-requerimiento/`

Se dispara cuando se empieza a **construir** (no analizar) un requerimiento ya analizado, en
cualquier proyecto/cliente. Trae:

- **§0 — Portabilidad** — regla de anclaje: una carpeta contenedora (`Req\`/`Requerimientos\`) con
  una subcarpeta por requerimiento (código), de la que cuelga todo lo demás.
- **§1 — Bitácora `README.md`** — estructura fija (Qué es esto / Estado general / Arquitectura /
  Pendientes / Bitácora de cambios) + política de rotación cuando supera ~200KB
  (`README_historico_AAAA.md`).
- **§2 — `Compartidos\`** — HU, evidencias, llaves; qué hacer si aparece un documento que no
  corresponde al requerimiento actual.
- **§3 — Scripts `ORG\`/`MOD\`** — numeración independiente por carpeta, convención de nombre
  `_MOD`/`_NEW`, reglas de DDL idempotente y dependencias entre SPs.
- **§4 — `EchasXC\`** — material de trabajo que no es entregable formal.
- **§5 — Encoding** — verificar ISO-8859-1 vs UTF-8 antes/después de cada edición, cómo revertir
  si se corrompe.
- **§6 — Postura senior** — no dar nada por hecho; validar contra BD/código/HU real siempre.
- **§7 — Reglas de BD** — nunca ejecutar escritura directo (solo el usuario), credenciales nunca en
  archivo, `PARSEONLY` para validar sintaxis, objetos de prueba con sufijo `_PRUEBAS`.
- **§8 — Gotchas técnicos recurrentes** — RadGrid/NeedDataSource, scope de `Dim` en VB.NET,
  `Contains()` con acentos, Post-Redirect-Get, case de oración en UI, patrón de tooltip, etc.
- **§9 — Validación en navegador** — límite de sesión no compartida con Claude-in-Chrome.
- **§10 — Relación entre requerimientos** — sección "Requerimientos relacionados" (dependiente/
  independiente) y chequeo de traslape antes de tocar un objeto existente.
- **§11 — Versionado de entregables** — nunca sobrescribir un entregable ya enviado; sufijo `_v1`,
  `_v2`... y registrar el cambio en la bitácora.
- **§12 — Git: commits y autoría** — no comitear por defecto (solo generar resumen para que el
  usuario comitee); si se pide commit explícito, sin firma de coautoría de Claude; aplica igual a
  push/PR/merge.
- **Checklist final** — lista de verificación rápida al iniciar la construcción de un requerimiento
  nuevo, con referencia a la sección correspondiente de cada punto.

### `analizar-requerimiento/`

Se dispara al **analizar** (no construir) un requerimiento nuevo a partir de su HU/reglas de
negocio, en cualquier proyecto/cliente. Complementa a `implementar-requerimiento/` — cubre la fase
previa. Trae:

- **§1 — Lectura y extracción** — objetivo/alcance/actores/reglas, inconsistencias internas del
  documento, revisar TODAS las imágenes/evidencias embebidas (no solo las obvias).
- **§2 — Comparación con lo existente** — reutilizar vs. construir nuevo, aislar en vez de alterar
  comportamiento compartido, verificar contra BD real DEV (nunca reusar credenciales sin
  autorización).
- **§3 — Preguntas abiertas** — Pendiente/Resuelto explícito; nada en la estimación depende de una
  pregunta sin responder.
- **§4 — Inventario** — objetos de BD/pantallas/reglas/reportes, marcados (Nuevo) o (Modificación).
- **§5 — Entregables** — por defecto solo se genera el `.xlsx` de estimación (desde plantilla
  existente, columnas fijas N°/Actividad/Hrs./Días); Análisis de Impacto y Plan de Implementación
  se desarrollan en la conversación pero no se generan como archivo salvo que se pida; horas
  realistas (acreditar reutilización, no heurísticas parejas).
- **§6 — Estado y bloqueantes** — resumen de confirmado/pendiente/bloqueante al cierre del análisis.

## Instalación

Una vez por máquina, dentro de una sesión de Claude Code:

```
/plugin marketplace add CesarChv19/claude-skills_MC
/plugin install mc-skills@claude-skills-mc
```

Las skills se siguen auto-cargando por descripción igual que antes. Para invocarlas explícitamente
por slash command, ahora llevan el prefijo del plugin: `/mc-skills:analizar-requerimiento` y
`/mc-skills:implementar-requerimiento` (antes, sin plugin, eran `/analizar-requerimiento` y
`/implementar-requerimiento` sin prefijo).

### Recibir actualizaciones

Tras pushear un cambio a este repo, en cada máquina:

```
/plugin marketplace update claude-skills-mc
/plugin update mc-skills@claude-skills-mc
```
