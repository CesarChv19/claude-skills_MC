# claude-skills_MC

Repositorio personal de skills para Claude Code. Cada carpeta es una skill independiente
(`<nombre>/SKILL.md`), pensada para copiarse a `~/.claude/skills/<nombre>/` y quedar disponible
en cualquier proyecto, sin importar el cliente/repo en el que se esté trabajando.

Este repo es solo respaldo/versionado del contenido — Claude Code no lee las skills desde aquí,
las lee desde `~/.claude/skills/`. Si editas una skill, hay que sincronizar ambas copias a mano.

## Skills

- **`implementar-requerimiento/`** — Estructura de carpetas, bitácora (`README.md`) y reglas a
  aplicar cuando se empieza a *construir* un requerimiento ya analizado (no para la fase de
  análisis): carpetas `Compartidos\`, `EchasXC\SCRIPTS\{ORG,MOD}\`, versionado de entregables,
  reglas de BD/encoding, y política de commits/autoría en git.

## Instalación de una skill

```bash
cp -r <nombre-skill> ~/.claude/skills/<nombre-skill>
```
