# Especificación — Formulario interno de captura (Empresas)

**Sprint 4 · tarea S4-1** · Lo llena el equipo de EZER **después** de la reunión con la empresa.
Su envío dispara el escenario `E-4`, que genera la Cotización y el Convenio como Docs editables.

> Esta especificación existe para que la captura en Fillout sea mecánica: cada pregunta ya trae su
> tipo, sus opciones exactas y a qué campo de Airtable escribe. No hay que decidir nada sobre la
> marcha. **Las opciones de los campos de selección deben quedar idénticas** a las de Airtable, o el
> motor de match del Sprint 8 devolverá 0%.

---

## Campo oculto (obligatorio)

| Parámetro | Valor |
|---|---|
| `record_id` | ID del registro de **Eventos**. Llega en la URL, precargado por el correo interno. |

La liga que el equipo recibe tiene esta forma:
`https://forms.fillout.com/t/<ID-del-formulario>?record_id=recXXXXXXXXXXXXXX`

⚠️ `E-4` **valida este ID antes de tocar nada**. Es el patrón que resolvió el incidente de `A-4`,
donde un ID inválido apagaba el escenario completo.

---

## Sección 1 — El evento

Escribe en **Eventos** (`tblpxjGTQuC3zS4U6`).

| # | Pregunta | Tipo en Fillout | Campo destino |
|---|---|---|---|
| 1 | Nombre del evento | Texto corto | `Nombre del evento` |
| 2 | Fecha estimada del evento | Fecha | `Fecha estimada` |
| 3 | ¿En qué municipio será? | Desplegable · 51 opciones | `Municipio` |
| 4 | ¿Cuántos voluntarios asistirán? | Desplegable | `Número de asistentes` |
| 5 | Duración | Desplegable | `Duración` |
| 6 | Número de jornadas | Número entero | `Número de jornadas` |

**Opciones exactas — pregunta 4 (Número de asistentes):**
`1-5` · `6-10` · `11-15` · `16-20` · `21-25` · `26-30` · `31-40` · `41-50` · `51-75` · `76-100` · `Más de 100`

**Opciones exactas — pregunta 5 (Duración):**
`Jornada Matutina` · `Jornada Vespertina` · `Jornada Completa` · `Más de una jornada`

**Opciones — pregunta 3 (Municipio):** los 51 municipios de Nuevo León, tal como están en Airtable.
Se pueden copiar directo del campo `Municipio` de la tabla Eventos.

---

## Sección 2 — Para conectar con la asociación

Aquí se levantan las variables que alimentan el **motor de match** del Sprint 8. Escribe en **Eventos**.

| # | Pregunta | Tipo en Fillout | Campo destino |
|---|---|---|---|
| 7 | ¿A qué beneficiarios quieren apoyar? | Selección múltiple · 9 opciones | `Beneficiarios de interés` |
| 8 | ¿En qué meses pueden hacerlo? | Selección múltiple · 12 opciones | `Meses de interés` |

**Opciones exactas — pregunta 7:**
`Niños` · `Mujeres` · `Adultos Mayores` · `Educación` · `Discapacidad` · `Medio Ambiente` ·
`Comedores` · `Migrantes` · `Vivienda Digna`

**Opciones exactas — pregunta 8:** los doce meses, `Enero` a `Diciembre`.

> **Las otras dos variables del match ya se capturaron arriba:** el municipio (pregunta 3) y el
> número de voluntarios (pregunta 4). Son cuatro variables en total, no seis.

⚠️ **Ojo con el número de voluntarios.** La escala de Eventos (`1-5`, `6-10`, …) **no coincide** con
la de Organizaciones (`1-10`, `11-20`, …). El motor de match tendrá que convertir ambas a número
antes de compararlas — está anotado en la tarea del Sprint 7, no hay que resolverlo aquí.

---

## Sección 3 — Curso de sensibilización

| # | Pregunta | Tipo en Fillout | Campo destino |
|---|---|---|---|
| 9 | ¿Incluye curso de sensibilización? | Sí / No | `Incluye curso de sensibilización` (Eventos) |

Esta casilla decide, mucho más adelante, si el flujo manda la liga del curso o se salta esa etapa.
También debe reflejarse en el monto de la cotización.

---

## Sección 4 — Datos fiscales

⚠️ **Estas tres escriben en Organizaciones** (`tblTGoIoCoRRr6dPf`), no en Eventos. `E-4` tiene que
resolver el vínculo Evento → Organización antes de escribirlas.

| # | Pregunta | Tipo en Fillout | Campo destino |
|---|---|---|---|
| 10 | RFC | Texto corto | `RFC` |
| 11 | Razón social | Texto corto | `Razón social` |
| 12 | Uso de CFDI | Texto corto | `Uso CFDI` |

---

## Qué pasa al enviar el formulario

1. `E-4` valida el `record_id`. Si no existe, **no toca nada** y avisa al equipo.
2. Actualiza el Evento y la Organización con lo capturado.
3. Pasa el Evento a Fase **`03 Captura de datos`**.
4. Genera **dos Google Docs** desde plantilla: la Cotización y el Convenio EZER–Empresa.
5. Los mueve a la carpeta de Drive del evento y guarda sus ligas en Airtable.

**Candado de idempotencia:** si `Cotizacion Doc ID` ya tiene valor, `E-4` **no vuelve a generar** los
documentos. Reenviar el formulario actualiza los datos sin duplicar archivos.

---

## Campos que ya quedaron creados en Airtable (2 de septiembre)

Ninguno de estos existía; sin ellos `E-4` y `E-5` no se pueden construir.

| Campo | Tipo | Para qué |
|---|---|---|
| `Cotizacion Doc ID` | Texto | Candado de idempotencia de `E-4` |
| `Cotización (Doc editable)` | URL | Liga al Doc editable |
| `Convenio Empresa Doc ID` | Texto | Igual, para el convenio |
| `Convenio Empresa (Doc editable)` | URL | Liga al Doc editable |
| `Fecha de cotizacion enviada` | Fecha | **Candado contra doble envío** en `E-5` |
| `Cotizacion en proceso de envio` | Casilla | Candado temporal, patrón de `A-5` |
| `Cotización PDF` | URL | PDF que se adjunta al correo |
| `Convenio Empresa PDF` | URL | PDF que se adjunta al correo |

Los que Make **evalúa en filtros** van deliberadamente **sin acentos** — es la regla que salió de
D-017, donde los nombres acentuados rompían las expresiones y un buscador devolvía la tabla entera
ignorando su filtro.

---

## Pendiente de definir antes de construir `E-4`

**¿Quién es el "coordinador" del evento?** El flujo manda la cotización "al coordinador", pero hoy
el único contacto que existe es quien llenó el registro en el sitio. Dos opciones:

- **Son la misma persona** → no hace falta ningún campo, se usa el contacto existente
- **Puede ser alguien distinto** → hay que agregar nombre y correo del coordinador al formulario, y
  un campo en Airtable para guardarlos

Afecta a quién se le manda la cotización, así que conviene resolverlo antes de armar el formulario.
