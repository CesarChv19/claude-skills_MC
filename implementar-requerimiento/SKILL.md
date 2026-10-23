---
name: implementar-requerimiento
description: Usar cuando se empieza a CONSTRUIR (no analizar) un requerimiento ya analizado de cualquier proyecto/cliente — define estructura de carpetas (README bitácora, Compartidos, EchasXC/SCRIPTS con ORG/MOD), convención de versionado de entregables, reglas de BD/encoding, y política de commits/autoría en git. Dispara con frases como "empecemos a construir", "ya tengo el análisis aprobado, vamos a implementarlo", o cuando se trabaja dentro de una carpeta de requerimiento con código tipo RV2-054-132-019. No aplica a la fase de análisis de un requerimiento (eso es un prompt distinto).
---

# Implementación de requerimiento (estructura de carpetas + bitácora)

Indicaciones generales a aplicar cuando se empieza a **construir** un requerimiento ya analizado
(no para la fase de análisis). Reconstruido a partir del patrón ya usado en
`RV2-054-132-019_ Integración proceso ADG` (CONSUBANCO), que es donde se aplicó por primera vez.
**Esta skill es portable: aplica igual sin importar en qué proyecto/cliente se use**, siguiendo la
regla de anclaje de la §0.

## 0. Dónde vive esto (portabilidad entre proyectos)

Este prompt no asume una ruta absoluta ni el nombre de un cliente en particular. La regla de
anclaje, válida en cualquier proyecto donde se use:

- Siempre existe una carpeta que agrupa todos los requerimientos de ese proyecto/cliente — el
  nombre literal puede variar (`Req\`, `Requerimientos\`, u otro que el usuario indique la primera
  vez que se trabaja en un proyecto nuevo) — y dentro de ella, **una subcarpeta por requerimiento,
  nombrada con su código** (ej. `RV2-054-132-019`, a veces seguido de `_<nombre corto>`, ej.
  `RV2-054-132-019_ Integración proceso ADG`).
- **Todo lo demás en este documento (`README.md`, `Compartidos\`, `EchasXC\`, `SCRIPTS\...`) es
  relativo a ESA subcarpeta del requerimiento específico**, sin importar cómo se llame la carpeta
  contenedora de arriba. No hace falta preguntar el nombre de la carpeta contenedora cada vez — si
  ya existe una en el proyecto (`Req\` o `Requerimientos\`), usar esa; si no existe ninguna todavía
  (proyecto nuevo), preguntar al usuario cómo la quiere nombrar antes de crearla.
- Un requerimiento nuevo y separado (ver §10) se identifica por tener (o necesitar) su **propia**
  subcarpeta bajo esa carpeta contenedora, con su propio código — nunca se mete dentro de la
  carpeta de otro requerimiento ya existente, aunque esté en el mismo cliente/módulo.

**Árbol concreto** (todo lo de §1-§4 cuelga directo de la carpeta del requerimiento, nada vive un
nivel más arriba ni más abajo de lo que muestra este árbol):

```
Req\                                     (o "Requerimientos\", el nombre que ya use el proyecto)
└─ RV2-054-132-019_ Integración ADG\     ← carpeta del requerimiento (§0), ancla de todo lo demás
   ├─ README.md                         ← §1, la bitácora
   ├─ Compartidos\                      ← §2, HU/imágenes/evidencia/llaves
   ├─ EchasXC\                     ← §4, todo lo que Claude genera que no es entregable formal
   │  └─ SCRIPTS\                       ← §3, la carpeta DE TRABAJO de Claude (no la raíz)
   │     ├─ ORG\                       ← rollback, numeración propia
   │     └─ MOD\
   │        ├─ ALTERS\                 ← solo tablas
   │        ├─ SP\                     ← solo stored procedures
   │        ├─ FN\                     ← solo funciones
   │        └─ VI\                    ← solo vistas
   ├─ SCRIPTS\                          ← carpeta raíz que cura el usuario (§3), NO tocar sin permiso
   ├─ Analisis_Impacto_[CÓDIGO].docx    ← entregables de la fase de análisis (si se pidieron)
   ├─ Estimacion_tiempos_[CÓDIGO].xlsx
   └─ Plan_Implementacion_[CÓDIGO].docx
```

## 1. Bitácora de cambios — `README.md` en la raíz de la carpeta del requerimiento

Un solo archivo, vivo, que se actualiza **cada vez** que se construye/modifica algo, se resuelve o se
agrega una pregunta pendiente, o cambia el estado de algo. Estructura fija:

- Encabezado con código y nombre del requerimiento + "Última actualización: AAAA-MM-DD".
- **Qué es esto** — resumen de 2-4 líneas del requerimiento.
- **Estado general — qué ya está construido** — una viñeta por fase/módulo, marcando construido /
  parcial / pendiente.
- **Arquitectura (dónde está cada cosa)** — rutas de pantallas, SPs clave (con sus banderas),
  jobs, tablas clave, archivos de config. **Acceso a BD (servidor, nombre de base, usuario y
  contraseña) NO se documenta aquí ni en ningún archivo** — cambia según el portal/proyecto, y el
  usuario lo da en el momento que se necesite, junto con la contraseña (ver §7).
- **Pendientes** — separado en "Preguntas al cliente" (tachar con `~~texto~~` cuando se resuelvan,
  sin borrarlas) y "Desarrollo pendiente".
- **Bitácora de cambios** — una entrada por sesión/fecha (`**AAAA-MM-DD (título corto)**`), más
  reciente arriba, con: qué hueco/bug real se encontró, qué se decidió y por qué, qué archivos
  cambiaron, qué falta que el usuario haga (correr script, recompilar, probar en pantalla).

No reemplaza `Analisis_Impacto_[CÓDIGO].docx` ni `Plan_Implementacion_[CÓDIGO].docx` (esos son
entregables de la fase de análisis) — este README es el estado vivo de la construcción.

**Rotación cuando crece demasiado** — si `README.md` se acerca o supera ~200KB, archivar las
sesiones más viejas antes de seguir agregando (caso real ya ocurrido: un `README.md` llegó a pasar
256KB y el tool de lectura dejó de poder abrirlo). Mecánica: mover las entradas de bitácora más
antiguas a un archivo nuevo `README_historico_AAAA.md` (uno por año, o el que haga falta), dejando
en el `README.md` activo solo las ~10-15 sesiones más recientes + un resumen de 2-3 líneas de lo
archivado (para no perder continuidad leyendo solo el activo). Las demás secciones (Qué es esto,
Estado general, Arquitectura, Pendientes) nunca se archivan, siempre viven en el activo — solo la
bitácora de sesiones rota.

## 2. Carpeta `Compartidos\`

Todo lo que es insumo o evidencia compartida con el cliente/proveedor externo, no código:
HU y sus anexos, imágenes/capturas embebidas extraídas del documento, llaves (PGP/certificados),
archivos de mapeo de datos, archivos de prueba recibidos o enviados a un tercero (ej. CDC).

**Si aparece un documento en `Compartidos\` cuyo tema no corresponde al requerimiento actual** (ej.
una HU de "Logs/auditoría" dentro de la carpeta de "Integración proceso ADG"): no asumir ninguna de
las dos salidas fáciles — ni tratarlo como evidencia de este requerimiento solo por estar ahí, ni
ignorarlo/moverlo por cuenta propia. **Preguntar al usuario** cuál de estos tres casos es:
- **Mal ubicado** — pertenece a otra carpeta, se copió por error.
- **Requerimiento nuevo y separado** que todavía no tiene su propia carpeta — crearle una con esta
  misma estructura, y si comparte objetos de BD/pantalla con el actual, documentarlo en
  "Requerimientos relacionados" de ambos (§10).
- **Set de requerimientos agrupados a propósito** en una sola carpeta de cliente (existe un
  precedente de este patrón en la plantilla de correo de pase a QA, que contempla explícitamente un
  "Set de requerimientos") — si es así, confirmar si igual conviene una subcarpeta por código.

## 3. Carpeta de scripts — `SCRIPTS\ORG\` y `SCRIPTS\MOD\`

Hay **dos carpetas `SCRIPTS` distintas** en la carpeta del requerimiento: `<requerimiento>\SCRIPTS\`
(raíz) y `<requerimiento>\EchasXC\SCRIPTS\`. **La de trabajo, donde Claude va dejando cada
script según lo construye, es `EchasXC\SCRIPTS\`.** La carpeta raíz `SCRIPTS\` la cura el
usuario manualmente, promoviendo desde `EchasXC\SCRIPTS\` cuando él considere que algo ya está
bien — no tocar esa carpeta raíz salvo que el usuario lo pida explícitamente.

**`MOD\`** — una subcarpeta por tipo de objeto, sin agrupar, cada una con numeración secuencial
**propia e independiente** (no comparten numeración entre sí):
- `MOD\ALTERS\` — solo tablas (`CREATE TABLE` / `ALTER TABLE`).
- `MOD\SP\` — solo stored procedures.
- `MOD\FN\` — solo funciones (`CREATE FUNCTION`).
- `MOD\VI\` — solo vistas.
- Si aparece un tipo de objeto que no tiene carpeta dedicada (índice, trigger, job, etc.),
  **preguntar al usuario cómo quiere llamar la carpeta** en vez de asumir dónde va o meterlo en la
  que "se parece". (Error real ya cometido una vez: una función se puso primero en `SP\`, luego en
  `ALTERS\`, hasta que el usuario pidió crear `FN\` dedicada.)
- Patrón de nombre en todas: objeto **ya existente que se modifica** → `N_NOMBRE_MOD.sql`; objeto
  **completamente nuevo** (no existe en BD) → `N_NOMBRE_NEW.sql`.

**`ORG\`** — rollback de los objetos que se **modifican** (los `_NEW` no llevan pareja aquí: el
rollback de algo nuevo es simplemente eliminarlo, no hay nada previo que capturar). Antes de tocar
un objeto existente, extraer su definición actual tal como vive hoy en BD y guardarla aquí — ese
archivo ES el rollback.
- **`ORG\` NO es un espejo numérico de `MOD\`.** Es fácil asumirlo y es incorrecto — llevan
  numeración independiente. Puede incluso numerarse por categoría dentro de sí misma (p. ej. un
  rollback consolidado de todos los `MOD\ALTERS` en una sola pasada respetando dependencias, y por
  separado un rollback por cada SP tocado). **Confirmar con el usuario el esquema de numeración de
  `ORG\` antes de asumir "mismo número que en MOD" — ya se corrigió una vez por asumir esto mal.**
- Cada vez que se toque/despliegue un objeto, avisar explícitamente al usuario cuál es (nombre) para
  que quede claro qué se movió.

**Reglas generales para cualquier script de este apartado:**
- DDL siempre idempotente (`IF NOT EXISTS` / `COL_LENGTH(...) IS NULL`).
- Un SP nuevo que llama a otro SP nuevo se define en el mismo archivo/batch que su dependencia,
  nunca en un número de archivo posterior.
- Si luego se "unifican"/limpian scripts incrementales (columnas agregadas después vía ALTER sueltos
  que se doblan de vuelta al CREATE original): el ALTER de respaldo se deja igual debajo del CREATE
  ya completo (las dos rutas conviven, no se elige una); el ORG correspondiente no necesita cambios
  si ya hace `DROP` completo del objeto — si hace rollback columna por columna, revisar orden
  (índices/constraints se dropean antes de tocar la columna en el rollback, se crean después de
  fijar la nulabilidad final en el alta).

## 4. Material de trabajo que no es entregable — `EchasXC\`

Todo lo que Claude genera para apoyar la construcción pero que no se entrega formalmente al
cliente ni al equipo (queries de validación, pruebas manuales, hojas técnicas, correos borrador):
va en una subcarpeta `EchasXC\`, separada de `Compartidos\` y de los entregables formales.

## 5. Encoding — por ningún motivo modificar el ya establecido en el archivo

Antes de editar cualquier archivo con acentos (`.aspx`/`.aspx.vb`/`.master`/scripts, etc.),
**no asumir que es UTF-8 solo porque lo declara o porque `file` lo reporta así.** Algunos archivos
viejos de portales legacy son realmente ISO-8859-1/Windows-1252 por dentro (acentos como bytes
altos únicos), y el `Edit`/`Write` estándar decodifica/reescribe TODO el archivo como UTF-8 —
cualquier acento preexistente en el archivo completo (no solo cerca de la edición) se convierte a
basura (`U+FFFD`, se ve como "ï¿½" o similar en el navegador), de forma silenciosa y permanente en
cuanto se guarda.

- Verificar encoding real antes de tocar un archivo de este tipo (`file archivo.aspx`, y ojo: un
  archivo YA corrompido antes puede reportar "UTF-8" limpio sin serlo realmente).
- Si es ISO-8859-1, editar vía PowerShell con `[System.Text.Encoding]::GetEncoding("ISO-8859-1")` —
  decodificar bytes crudos con esa codificación, reemplazar en memoria, reescribir con la misma
  codificación. Nunca con el `Edit`/`Write` tool estándar sobre un archivo con esta sospecha.
- **Verificar después de CADA edición**, no solo antes: si el repo tiene git, `git diff --stat`
  inmediatamente después — un número de deletions mucho mayor a lo que se intentó cambiar es la
  señal de corrupción, no la edición esperada. Sin git, comparar tamaño de archivo antes/después.
- Si ya se corrompió sin darse cuenta: revertir con `git checkout -- archivo` (recupera los bytes
  originales intactos) antes de reintentar con el método correcto — los acentos perdidos no se
  pueden recuperar una vez sobrescritos.
- Para archivos nuevos que Claude crea (no edita): sí llevan BOM UTF-8 explícito si el proyecto lo
  requiere (confirmar con el usuario si aplica).

## 6. Postura general — actuar como senior, nunca dar nada por hecho

No asumir que algo "debería funcionar así", que un patrón se repite igual que en otro lado, o que
un dato/esquema es el que la memoria o la lectura superficial sugieren. **Validar el proceso real
siempre:**
- Contra la BD real (DEV) antes de afirmar esquema, datos o comportamiento — no asumir, consultar.
- Contra el código real (grep/leer el archivo) antes de afirmar qué hace una pantalla/SP — no
  confiar en el nombre o en la primera lectura.
- Contra la HU/documento del requerimiento antes de agregar algo que "parece necesario" — si no
  está explícito, no se construye por inercia.
- Validando el resultado de cada cambio (`SET PARSEONLY ON`, `git diff`, ejecución de prueba) antes
  de darlo por terminado, en vez de asumir que compiló/corrió bien porque la lógica se ve correcta.

## 7. Reglas de ejecución contra BD (aplica a todo, no solo DDL de script)

- **Nunca ejecutar INSERT/UPDATE/DELETE/DDL contra la BD, ni en DEV, ni con confirmación explícita
  en el chat.** El usuario ejecuta él mismo todo lo que escriba en BD; el rol de Claude es preparar
  el script y, después de que el usuario lo corra, **validar con SELECT** que quedó como se esperaba.
  Esto es independiente de la regla de carpetas `ORG`/`MOD` — aplica también a catálogos, datos de
  prueba, cualquier escritura.
- Consultas de solo lectura (`SELECT`, `INFORMATION_SCHEMA`, `sys.sql_modules`) sí son libres, sin
  pedir permiso cada vez.
- **Servidor, nombre de base, usuario y contraseña de BD no se documentan en ningún archivo**
  (`README.md`, `Compartidos\`, scripts `.sql`, comentarios de código) — el nombre de la base en
  particular cambia según el portal/proyecto, así que no hay un valor fijo que valga la pena fijar
  por escrito. El usuario los da en el chat cuando se necesiten, junto con la contraseña, y se usan
  solo por variable de entorno en el comando de esa sesión — pedirlos de nuevo la próxima vez, no
  guardarlos "para no tener que volver a preguntar". Si ya se tiene una contraseña/conexión vista en
  otro lado (otro script, otro comentario de código, otra sesión), **no reutilizarla sin
  autorización explícita del usuario** en la conversación actual, aunque funcione.
- **`sqlcmd -i archivo.sql` EJECUTA el archivo completo, no lo "parsea".** Para validar sintaxis sin
  desplegar: envolver en `SET PARSEONLY ON; GO ... GO SET PARSEONLY OFF;` y correr ese wrapper (vive
  en el scratchpad, no directo en el home del usuario o da "Access is denied"). **Ojo:** en un SP
  tipo "bandera" (`@V_Bandera` + muchos `IF`/`ELSE IF`), `PARSEONLY` no detecta errores de *binding*
  (ej. `ORDER BY` con expresión sobre un `UNION`) — eso solo se dispara al ejecutar esa rama real,
  que la ejecuta el usuario. No asumir "PARSEONLY sin errores" = "la bandera nueva va a correr bien".
- Si al armar el wrapper de `PARSEONLY` el `.sql` original trae BOM UTF-8, el BOM puede quedar
  enterrado a la mitad del archivo temporal y `sqlcmd` lo malinterpreta como mojibake (`»`/`¿`/`ï` en
  el mensaje de error) — quitar el BOM antes de concatenar y correr `sqlcmd -f 65001`.
- Objetos de BD creados solo para validar un diseño (no como entregable) van con sufijo `_PRUEBAS`
  y se eliminan (`DROP`) al terminar — nunca probar directo sobre el nombre final, para que el
  usuario (quien compila/despliega todo) no pierda visibilidad de qué es real y qué es prueba.
- `xp_cmdshell` (vía `cmd.exe`) solo preserva comillas si el comando completo tiene **exactamente
  2** — con más de 2 corrompe el string en silencio (sin error de sintaxis). Si varios valores
  dinámicos necesitarían comillas, quitarle la necesidad a todos menos uno, o no pasar ese valor por
  línea de comandos.
- Patrón preferido para que un job de SQL Agent dispare lógica que no es T-SQL puro (cifrado,
  llamadas .NET): un `.exe` de consola propio invocado por `xp_cmdshell` desde el SP — nunca un
  timer in-process del portal (muere al reciclar el Application Pool) ni el job llamando por HTTP al
  portal (depende de que esté arriba). El mismo SP es el único punto de disparo, tanto para el botón
  manual como para el job automático.

## 8. Gotchas técnicos recurrentes (generales — no solo del requerimiento que los encontró)

- **RadGrid con `AllowPaging`:** bindear siempre vía `OnNeedDataSource` + `.Rebind()`, nunca
  `.DataSource = dt; .DataBind()` directo en un botón/`Page_Load` — se vacía en el primer postback
  que el grid dispara solo (cambiar de página, ordenar).
- **Todo `telerik:RadGrid` nuevo lleva `Caption=""` explícito.**
- **VB.NET: `Dim` dentro de `If`/`ElseIf`/`Select Case` NO crea scope propio** (a diferencia de
  C#) — el scope real es toda la función contenedora. Reusar un nombre de variable (incluye
  variables de rango LINQ `From x In` y `For Each x In`) en dos ramas de la misma función truena en
  compilación (`BC30616`); no lo detecta una revisión visual, solo compilar de verdad.
- **VB.NET: comparar `String.Contains()` con un literal acentuado contra texto de una columna
  `varchar`** puede fallar en silencio por desajuste de encoding/collation, aunque el texto sí esté
  ahí. Usar un fragmento ASCII del mismo texto para el match; si no hay ninguno, confirmar primero
  con `sqlcmd` directo que el string existe antes de asumir que la lógica está mal.
- **WebForms: F5 reenvía el último POST.** Todo botón que escribe en BD (Insert/Update/Delete real)
  necesita Post-Redirect-Get (`window.location.href=...` tras guardar) o un candado anti-duplicado
  en el SP (`EXISTS`/validación de estatus) — un `RadAjaxPanel`/`UpdatePanel` async sigue siendo un
  POST real a nivel navegador.
- **Textos visibles de UI (labels, botones, títulos, mensajes de aviso): case de oración en
  español**, solo la primera palabra con mayúscula — nunca Title Case al estilo inglés. Siglas del
  dominio conservan su mayúscula.
- **Tooltip de aclaración ("colócale un (!)"):** nunca es el carácter literal con `<span title=>`.
  Patrón preferido: ícono `<i class="material-icons" ...>error</i>` + control de tooltip nativo del
  framework (ej. `telerik:RadToolTip` con `ShowEvent="OnMouseOver"`). Revisar con
  `grep "span title="` antes de dar por terminado cualquier cambio que agregue tooltips.
- **Carpetas de assets (CSS/imágenes) que no viven en el checkout de git:** no se puede verificar
  por código qué estilo real tiene algo que dependa de ahí. Para apariencia crítica, usar estilos
  inline explícitos o pedir una captura real al usuario — no asumir que algo "ya debería verse bien"
  solo por reusar las mismas clases que otra pantalla.
- **Redirect a una pantalla "de sistema" (sesión expirada, error) que da 404 con un segmento de
  carpeta de más:** sospechar tanto de `Response.Redirect("~/...")` de servidor en postback async
  (no resuelve bien el `~/` ahí) como de scripts compartidos con rutas relativas hardcodeadas — son
  bugs de capas distintas que pueden coexistir. Revisar también el MasterPage del módulo (chequeo de
  sesión propio, corre antes que la página hija), no solo la página que falló.
- **Comentarios y textos descriptivos mínimos** en cualquier script SQL o código nuevo (incluye
  valores de columnas tipo `OBSERVACIONES`) — solo lo que no sea obvio, nada de relleno ni
  referencias a qué HU lo originó (eso vive en la documentación del requerimiento, no en la BD).

## 9. Validación en navegador

El navegador que controla la extensión Claude-in-Chrome **no comparte sesión/cookies** con el
Chrome real que usa el usuario — no sirve para reproducir/probar una pantalla donde el usuario ya
inició sesión en su propio navegador, aunque confirme "ya inicié sesión" (la cookie quedó en otra
ventana/perfil). Para esos casos, pedirle al usuario que corra la prueba con su propia extensión,
con un prompt preparado de antemano — no insistir en reintentos de navegación cuando ya se confirmó
una vez que la sesión no se comparte.

## 10. Identificación y relación entre requerimientos

Con el tiempo va a haber varios requerimientos en la misma carpeta de cliente, y algunos van a
tocar los mismos objetos de BD/pantallas que uno anterior. Para que eso no se confunda ni se pierda:

- **Todo script en `SCRIPTS\ORG\` y `SCRIPTS\MOD\...` lleva, en comentario al inicio del archivo, el
  código del requerimiento al que pertenece** (ej. `-- Requerimiento: RV2-054-132-019`), además de
  vivir ya dentro de la carpeta de ese requerimiento. Esto importa sobre todo para `ORG\`: si el
  objeto ya había sido tocado por un requerimiento previo, el rollback debe dejar claro a qué estado
  regresa (¿al original de antes de TODO, o al estado que dejó el requerimiento anterior?) —
  **preguntar al usuario cuál de los dos quiere** en vez de asumir "el más viejo que se encuentre".
- **El `README.md` (bitácora) de cada requerimiento lleva una sección "Requerimientos
  relacionados"** (puede ir vacía/omitida si de verdad no aplica) con dos casos posibles:
  - **Dependiente** — este requerimiento necesita que otro ya esté construido/desplegado primero
    (ej. usa una tabla/SP que otro requerimiento creó), o modifica un objeto que otro requerimiento
    también toca — listar el código del otro requerimiento y qué objeto comparten.
  - **Independiente** — se confirma explícitamente que este requerimiento no comparte ningún objeto
    de BD/pantalla con otros en curso, aunque sea del mismo cliente/módulo — decirlo igual de
    explícito, no dejarlo implícito por omisión.
- **Antes de tocar un objeto de BD/pantalla ya existente**, buscar si pertenece a otro requerimiento
  ya documentado (grep del nombre del objeto en los demás `README.md`/carpetas de requerimiento del
  mismo cliente) — no asumir que el objeto es "territorio libre" de este requerimiento solo porque
  la HU actual lo menciona. Si hay traslape, avisar al usuario antes de construir, no después.
- Esto es una extensión de la regla general de §6 (no dar nada por hecho): la relación entre
  requerimientos se **verifica** (leyendo los otros README/bitácoras reales), no se asume por
  cercanía de fechas o de módulo.

## 11. Versionado de entregables ante cambios del cliente

Cuando el cliente pide cambios a un entregable de análisis (`Analisis_Impacto_[CÓDIGO].docx`,
`Plan_Implementacion_[CÓDIGO].docx`, `Estimacion_tiempos_[CÓDIGO].xlsx`) después de ya haberlo
enviado una primera vez:

- No sobrescribir el archivo original en silencio. Antes de generar la versión nueva, renombrar la
  que ya existía agregándole sufijo de versión (ej. `Estimacion_tiempos_[CÓDIGO]_v1.xlsx`); el
  archivo sin sufijo es siempre la versión vigente. Confirmar este patrón con el usuario la primera
  vez que aplique en un proyecto nuevo, igual que con el nombre de la carpeta contenedora (§0).
- Cada versión nueva se anota en la Bitácora (§1): qué cambió respecto a la anterior y qué pidió el
  cliente para justificarlo.
- Las versiones viejas no se borran — quedan en la misma carpeta del requerimiento como historial.

## 12. Git — commits, autoría y colaboración remota

- **No hacer commit por defecto** al terminar un cambio de código. En vez de eso, generar un
  resumen de qué se modificó y por qué (el mismo tipo de detalle que va a la Bitácora, §1) para que
  el usuario revise y haga el commit él mismo.
- **Si el usuario pide explícitamente que Claude haga el commit**, el mensaje no lleva firma de
  coautoría de Claude/Anthropic (`Co-Authored-By: Claude...`) ni nada equivalente — el commit queda
  atribuido únicamente a la cuenta/usuario de git configurada en la máquina, como si lo hubiera
  hecho el usuario directamente.
- Esta misma regla aplica igual a cualquier operación de git que quede visible para terceros o en
  historial compartido (push, pull request, merge, etc.): nunca debe aparecer Claude como
  colaborador/autor/revisor en metadata visible para otros.
- **Formato del mensaje — Conventional Commits:** `<tipo>(<alcance>): <descripción>`. Tipos
  válidos: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`,
  `revert`. La descripción va en español, minúsculas, modo imperativo, sin punto final, idealmente
  ≤72 caracteres en la primera línea. El `<alcance>` entre paréntesis es opcional — usarlo cuando
  ayude a ubicar qué parte cambió (nombre de carpeta/módulo/SP/skill). Aplica tanto al resumen que
  se le da al usuario para que comitee él mismo, como al mensaje si Claude llega a comitear por
  petición explícita.

## Checklist rápido al empezar a construir un requerimiento nuevo

- [ ] `README.md` creado en la raíz, con las 5 secciones de la §1 + la sección "Requerimientos
      relacionados" de la §10 (aunque sea para decir explícitamente "independiente").
- [ ] `Compartidos\` creada, con la evidencia/HU/imágenes ya extraídas.
- [ ] `EchasXC\SCRIPTS\ORG\` y `EchasXC\SCRIPTS\MOD\{ALTERS,SP,FN,VI}\` creadas (solo las
      subcarpetas de tipo que ya se van a necesitar; agregar más bajo demanda, preguntando el
      nombre si el tipo de objeto no tiene carpeta dedicada todavía).
- [ ] Cada script nuevo lleva el código del requerimiento en comentario al inicio (§10).
- [ ] Cada sesión de trabajo termina con una entrada nueva en la Bitácora del `README.md`.
- [ ] Encoding de cada archivo tocado verificado antes y después de editar (§5).
- [ ] Cada hallazgo/decisión validado contra BD/código/HU real antes de darlo por hecho (§6),
      incluyendo si un objeto ya pertenece a otro requerimiento (§10).
- [ ] Si `README.md` supera ~200KB, archivar sesiones viejas en `README_historico_AAAA.md` antes
      de seguir agregando (§1).
- [ ] Si se re-entrega un documento de análisis ya enviado antes, la versión anterior se conservó
      con sufijo de versión (§11).
- [ ] Al terminar un cambio de código, se generó resumen para que el usuario comitee — no se hizo
      commit automático salvo petición explícita, y sin firma de coautoría de Claude (§12).
