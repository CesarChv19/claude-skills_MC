# claude-skills_MC

Repositorio personal de skills para Claude Code. Cada carpeta es una skill independiente
(`<nombre>/SKILL.md`), pensada para copiarse a `~/.claude/skills/<nombre>/` y quedar disponible
en cualquier proyecto, sin importar el cliente/repo en el que se esté trabajando.

Este repo es solo respaldo/versionado del contenido — Claude Code no lee las skills desde aquí,
las lee desde `~/.claude/skills/`. Si editas una skill, hay que sincronizar ambas copias a mano.

## Skills

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

## Instalación de una skill

```bash
cp -r <nombre-skill> ~/.claude/skills/<nombre-skill>
```
