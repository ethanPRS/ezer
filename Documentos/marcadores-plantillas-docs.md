# Marcadores para los dos Docs — reemplazo obligatorio

## Por qué hay que cambiarlos

Dos motivos, y el segundo es el importante.

**1 · Los acentos y las llaves.** Los marcadores deben ir sin acentos y **sin dobles llaves**.

**2 · `{{ }}` es el delimitador de expresiones de Make.** En el campo "buscar" del módulo de Google
Docs, Make no puede recibir `{{Nombre_Evento}}` como texto literal — lo interpretaría como una
expresión suya. Por eso el blueprint de `A-4` tiene guardado `Nombre_Asociacion` **sin llaves**.

> **Sobre Asociaciones:** se verificó que sus convenios generados salen limpios, sin llaves. Sus
> plantillas ya usan el formato correcto. Lo que hay que cambiar son las **dos plantillas nuevas de
> Empresas**, que se pegaron con marcadores en minúsculas y entre llaves porque así venían en el
> borrador entregado.

## Qué hacer

En cada uno de los dos Docs, usa **Editar → Buscar y reemplazar** con esta tabla.
Son reemplazos de texto plano, sin formato.

### Cotización
`docs.google.com/document/d/1TNIdjuNAyvROXV2TXrRtlnOzVo53L9hM1SUFYvVN-k4`

| Buscar | Reemplazar por |
|---|---|
| `{{folio}}` | `Folio_Cotizacion` |
| `{{fecha_emision}}` | `Fecha_Emision` |
| `{{razon_social}}` | `Razon_Social` |
| `{{rfc}}` | `RFC_Empresa` |
| `{{uso_cfdi}}` | `Uso_CFDI` |
| `{{coordinador}}` | `Coordinador_Nombre` |
| `{{correo_coordinador}}` | `Coordinador_Correo` |
| `{{telefono_coordinador}}` | `Coordinador_Telefono` |
| `{{nombre_evento}}` | `Nombre_Evento` |
| `{{fecha_estimada}}` | `Fecha_Estimada` |
| `{{municipio}}` | `Municipio_Evento` |
| `{{numero_asistentes}}` | `Numero_Voluntarios` |
| `{{duracion}}` | `Duracion_Evento` |
| `{{numero_jornadas}}` | `Numero_Jornadas` |
| `{{beneficiarios}}` | `Beneficiarios_Apoyo` |
| `{{incluye_curso}}` | `Incluye_Curso` |
| `{{monto_total}}` | `Monto_Total` |

**Y borra la línea** que dice `{{incluye_curso_texto}}` en la sección "QUÉ INCLUYE".
Se resolvió más simple: `Incluye_Curso` ya dice "Sí" o "No" en la tabla del detalle.

### Convenio EZER–Empresa
`docs.google.com/document/d/1iMvXL6WjcRHtVEp_71vVh8fCawmP65OpLeRKwlozonA`

| Buscar | Reemplazar por |
|---|---|
| `{{razon_social}}` | `Razon_Social` |
| `{{rfc}}` | `RFC_Empresa` |
| `{{coordinador}}` | `Coordinador_Nombre` |
| `{{nombre_evento}}` | `Nombre_Evento` |
| `{{fecha_estimada}}` | `Fecha_Estimada` |
| `{{municipio}}` | `Municipio_Evento` |
| `{{numero_asistentes}}` | `Numero_Voluntarios` |
| `{{duracion}}` | `Duracion_Evento` |
| `{{numero_jornadas}}` | `Numero_Jornadas` |
| `{{monto_total}}` | `Monto_Total` |
| `{{folio}}` | `Folio_Cotizacion` |
| `{{fecha_emision}}` | `Fecha_Emision` |

---

## Cuidado al escribir marcadores nuevos

El reemplazo es **texto plano y literal**. Dos reglas:

- **Que no aparezcan como palabra normal en la prosa.** Por eso van con guión bajo: `Monto_Total`
  jamás aparece en una oración, pero "Monto" solo, sí.
- **Que Google Docs no los parta.** Si al pegar el texto queda con formato mixto —media palabra en
  negritas, por ejemplo— Docs lo guarda en fragmentos separados y el buscar-y-reemplazar de la API
  no lo encuentra. Si un marcador no se sustituye, casi siempre es esto: bórralo y reescríbelo de
  corrido, sin formato.
