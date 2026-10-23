---
name: analizar-requerimiento
description: Usar al ANALIZAR (no construir) un requerimiento nuevo de cualquier proyecto/cliente, a partir de su HU/documento de reglas de negocio — extracción de objetivo/alcance/reglas, inconsistencias internas del documento, comparación contra código ya existente, preguntas abiertas pendientes, inventario de objetos afectados, y entregables (por defecto solo el xlsx de estimación como archivo). Dispara con frases como "analiza este requerimiento", "léeme esta HU", o al recibir un documento de HU/reglas de negocio nuevo. No aplica a la fase de construcción (ver skill implementar-requerimiento).
---

# Análisis de requerimientos

Indicaciones generales a aplicar cuando se recibe un requerimiento (HU + Reglas de Negocio, si
aplica) **para analizarlo** — no para construirlo (eso es la skill `implementar-requerimiento`).
Portable: aplica igual sin importar en qué proyecto/cliente se use.

## 1. Lectura y extracción

- Leer el documento completo y extraer: objetivo, alcance, actores, flujos, reglas de negocio
  explícitas.
- Señalar cualquier inconsistencia **dentro del propio documento** (ej. algo mencionado en el
  Objetivo pero nunca detallado después, o una regla que se contradice con otra parte del texto).
- Revisar también **todas** las imágenes/evidencias embebidas (layouts, pantallas, dashboards) —
  no solo las obvias. Suelen contener detalles que no están en el texto y pueden resolver un hueco
  que el texto deja pendiente.

## 2. Comparación con lo ya existente (si aplica)

- Si el requerimiento toca o se parece a un módulo/proceso/funcionalidad ya construido,
  compararlo contra el código real del proyecto actual para detectar qué se puede reutilizar vs.
  qué debe ser nuevo.
- Si implica modificar algo que ya está en uso (otro módulo, otra integración, otro flujo
  compartido), evaluar el riesgo de esa modificación sobre lo existente y preferir aislar lo nuevo
  en vez de alterar el comportamiento actual, salvo que el requerimiento pida explícitamente
  cambiar ese comportamiento.
- Verificar los hallazgos contra la base de datos real DEV antes de darlos por hecho — no asumir
  esquema/datos, consultarlo. Servidor y credenciales: pedir al usuario si no se tienen ya en la
  conversación; **no reutilizar credenciales encontradas en comentarios/código sin autorización
  explícita del usuario**.

## 3. Preguntas abiertas

- Listar explícitamente cada punto ambiguo, no especificado, o que dependa de una decisión de
  negocio/cliente (ej. condiciones de una negociación, montos, plazos, umbrales, permisos,
  excepciones).
- Marcar cada uno como **Pendiente** hasta tener respuesta; cuando el cliente responda, documentar
  la respuesta y pasar a **Resuelto**.
- No incluir en la estimación nada que dependa de una pregunta que aún no se pueda responder.

## 4. Inventario

Lista completa de objetos de BD, pantallas, reglas de negocio/validaciones y reportes afectados o
nuevos, indicando **(Nuevo)** o **(Modificación)** para cada uno.

## 5. Entregables

- `Analisis_Impacto_[CÓDIGO].docx` — documento de impacto para enviar al equipo de desarrollo del
  cliente/proveedor.
- `Estimacion_tiempos_[CÓDIGO].xlsx` — horas por objeto/tarea, con total.
- `Plan_Implementacion_[CÓDIGO].docx` — plan por fases con dependencias entre sí (no todo en una
  fase).
- **Por defecto, al analizar un requerimiento solo se genera como archivo
  `Estimacion_tiempos_[CÓDIGO].xlsx`.** `Analisis_Impacto_[CÓDIGO].docx` y
  `Plan_Implementacion_[CÓDIGO].docx` **no se generan automáticamente** como archivo — su contenido
  (objetivo, alcance, reglas de negocio, inconsistencias, comparación con lo existente, preguntas
  abiertas, inventario, fases del plan) se desarrolla igual y se mantiene disponible en la
  conversación, listo para volcarse a documento en cuanto el usuario lo pida explícitamente (p. ej.
  "generame el Análisis de Impacto" o "pásamelo a Word").
- `Estimacion_tiempos_[CÓDIGO].xlsx` debe construirse **a partir de una plantilla existente** (si
  ya hay una en la carpeta del requerimiento, copiarla, no recrear la tabla desde cero; si no existe
  ninguna todavía en el proyecto, preguntar al usuario si tiene una plantilla estándar antes de
  inventar el formato). Formato esperado salvo que el usuario indique otro: banda de título +
  tabla con columnas **N° | Actividad | Hrs. | Días** (columna Actividad ancha, una fila por
  actividad, fila Total al final con fórmula de suma y `Días = Hrs./8`). No inventar columnas
  adicionales (Tipo, Notas, desglose Análisis/Desarrollo/Pruebas por separado, etc.) — si se quiere
  detallar tipo (Nuevo/Modificación) o notas, incluirlo dentro del texto de la celda "Actividad".
- **Horas realistas:** acreditar la reutilización ya confirmada en el paso 2 (no estimar como si
  todo fuera nuevo), no usar heurísticas parejas para todas las tareas, y preguntar la capacidad
  real del equipo si hace falta para traducir horas a fechas.

## 6. Estado y bloqueantes

Al final, resumir: qué está confirmado, qué sigue pendiente, y qué bloquea empezar a construir
(documentación faltante, respuestas de cliente/negocio pendientes, aprobaciones, etc.).
