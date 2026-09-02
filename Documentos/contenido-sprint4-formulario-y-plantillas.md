# Sprint 4 — Contenido listo para pegar

Tres bloques: el prompt para la IA de Fillout, y las dos plantillas de Google Docs.
Todo alineado a los nombres reales de campo de Airtable al 2 de septiembre de 2026.

---

# 1 · Prompt para la IA de Fillout

Pega esto tal cual en el generador de formularios de Fillout.

```
Crea un formulario interno en español para el equipo de EZER A.B.P., una asociación de
beneficencia en Nuevo León, México. Lo llena nuestro propio equipo justo después de tener
una reunión con una empresa interesada en hacer voluntariado corporativo. No lo llena la
empresa.

Título: Captura de datos — Voluntariado Corporativo
Descripción: Llena este formulario durante o justo después de la reunión con la empresa.
Con estos datos se genera automáticamente su cotización y su convenio.

Organiza el formulario en 5 páginas o secciones, en este orden exacto:

SECCIÓN 1 — El evento
1. "Nombre del evento" — texto corto, obligatorio
2. "Fecha estimada del evento" — selector de fecha, obligatorio
3. "¿En qué municipio será?" — desplegable de una sola opción, obligatorio.
   Opciones: los 51 municipios de Nuevo León.
4. "¿Cuántos voluntarios asistirán?" — desplegable de una sola opción, obligatorio.
   Opciones exactas, en este orden: 1-5, 6-10, 11-15, 16-20, 21-25, 26-30, 31-40,
   41-50, 51-75, 76-100, Más de 100
5. "Duración" — desplegable de una sola opción, obligatorio.
   Opciones exactas: Jornada Matutina, Jornada Vespertina, Jornada Completa,
   Más de una jornada
6. "Número de jornadas" — número entero, obligatorio, mínimo 1

SECCIÓN 2 — Para conectar con la asociación
Texto de ayuda de la sección: "Con estas respuestas el sistema busca qué asociación
hace match con lo que busca la empresa."
7. "¿A qué beneficiarios quieren apoyar?" — selección múltiple, obligatorio.
   Opciones exactas: Niños, Mujeres, Adultos Mayores, Educación, Discapacidad,
   Medio Ambiente, Comedores, Migrantes, Vivienda Digna
8. "¿En qué meses pueden hacerlo?" — selección múltiple, obligatorio.
   Opciones: los doce meses, de Enero a Diciembre.

SECCIÓN 3 — El coordinador de la empresa
Texto de ayuda de la sección: "Quien va a coordinar el evento por parte de la empresa.
No necesariamente es quien llenó el registro en la página."
9. "Nombre del coordinador" — texto corto, obligatorio
10. "Correo del coordinador" — correo electrónico, obligatorio
11. "Teléfono del coordinador" — teléfono, opcional

SECCIÓN 4 — Curso de sensibilización
12. "¿Incluye curso de sensibilización?" — opción única Sí / No, obligatorio

SECCIÓN 5 — Datos fiscales
Texto de ayuda de la sección: "Para poder emitir el recibo deducible."
13. "RFC" — texto corto, obligatorio
14. "Razón social" — texto corto, obligatorio
15. "Uso de CFDI" — texto corto, obligatorio

Mensaje al terminar: "Listo. Ya se están generando la cotización y el convenio de esta
empresa. En unos segundos aparecen en Airtable."

No agregues preguntas que no estén en esta lista.
```

## Después de que la IA lo genere — 3 ajustes a mano

La IA no puede configurar estas tres cosas. Son las que hacen que el formulario funcione con el resto del sistema.

**1 · Agregar el campo oculto `record_id`**
En la configuración del formulario, agrega un *hidden field* / *URL parameter* llamado exactamente
`record_id`. Es el que le dice al sistema **a qué evento** pertenece lo capturado. Sin él, `E-4` no
sabe dónde escribir.

**2 · Revisar las opciones de los desplegables**
La IA suele "mejorar" las listas. Verifica que las opciones de las preguntas **4, 5, 7 y 8** quedaran
**idénticas** a las de arriba, carácter por carácter. Si difieren, el motor de match del Sprint 8
devolverá 0% y parecerá un error del sistema cuando en realidad es de los datos.

**3 · Pasarme el ID del formulario**
Aparece en su URL: `https://forms.fillout.com/t/XXXXXXXXXX`. Con ese ID conecto `E-4`.

---

# 2 · Plantilla de Google Docs — Cotización

Crea un Doc nuevo en la carpeta **Plantillas**, nómbralo `PLANTILLA — Cotización Empresa` y pega
esto. Los marcadores `{{...}}` se sustituyen solos; **no cambies su escritura.**

---

**EZER A.B.P. — La Casa del Voluntario**
Programa de Voluntariado Corporativo

**COTIZACIÓN**

Folio: `{{folio}}`
Fecha de emisión: `{{fecha_emision}}`
Vigencia de esta cotización: 30 días naturales

---

**DATOS DE LA EMPRESA**

| | |
|---|---|
| Razón social | `{{razon_social}}` |
| RFC | `{{rfc}}` |
| Uso de CFDI | `{{uso_cfdi}}` |
| Coordinador del evento | `{{coordinador}}` |
| Correo | `{{correo_coordinador}}` |
| Teléfono | `{{telefono_coordinador}}` |

---

**DETALLE DEL EVENTO**

| | |
|---|---|
| Nombre del evento | `{{nombre_evento}}` |
| Fecha estimada | `{{fecha_estimada}}` |
| Municipio | `{{municipio}}` |
| Número de voluntarios | `{{numero_asistentes}}` |
| Duración | `{{duracion}}` |
| Número de jornadas | `{{numero_jornadas}}` |
| Beneficiarios a apoyar | `{{beneficiarios}}` |
| Curso de sensibilización | `{{incluye_curso}}` |

---

**QUÉ INCLUYE**

- Coordinación completa del evento de principio a fin, de la mano de nuestro equipo.
- Vinculación con una asociación beneficiaria cuyo trabajo corresponda a la causa que ustedes
  eligieron apoyar.
- Acompañamiento de personal de EZER el día del evento.
- Materiales y logística de la actividad.
- Reporte de impacto al cierre, con fotografías y resultados.
- `{{incluye_curso_texto}}`

---

**INVERSIÓN**

| Concepto | Importe |
|---|---|
| `{{concepto_1}}` | `{{importe_1}}` |
| `{{concepto_2}}` | `{{importe_2}}` |
| **Total** | **`{{monto_total}}`** |

Los importes están expresados en pesos mexicanos. EZER A.B.P. es una asociación de beneficencia
privada autorizada para emitir recibos deducibles de impuestos.

---

**SIGUIENTE PASO**

Si esta propuesta les hace sentido, en el correo que acompaña esta cotización encontrarán un botón
que los lleva a un formulario breve. Ahí están nuestros datos bancarios, los pasos a seguir, y el
espacio para subir su constancia de situación fiscal.

En cuanto recibamos sus datos, nuestro contador emite el recibo y les llega por correo.

---

Quedamos a sus órdenes.

**Equipo EZER A.B.P. — La Casa del Voluntario**
voluntariadocorporativo@ezer.org.mx · ezer.org.mx

*"Servir genera esperanza, comunidad y transformación."*

---

# 3 · Plantilla de Google Docs — Convenio EZER–Empresa

> ⚠️ **Este borrador NO ha pasado por revisión legal.** Es la tarea que sigue pendiente desde julio.
> Sirve para que `E-4` genere el documento y el flujo se pueda probar completo, pero **no debe
> mandarse a una empresa real** hasta que un abogado lo revise. Es una revisión de una sola vez;
> después se reutiliza.

Crea un Doc en la carpeta **Plantillas**, nómbralo `PLANTILLA — Convenio EZER-Empresa` y pega esto.

---

**CONVENIO DE COLABORACIÓN**
**Programa de Voluntariado Corporativo**

Que celebran por una parte **EZER A.B.P.**, en adelante "EZER", y por la otra
**`{{razon_social}}`**, en adelante "LA EMPRESA", al tenor de las siguientes declaraciones y
cláusulas.

---

**DECLARACIONES**

**I. Declara EZER:**

a) Ser una Asociación de Beneficencia Privada legalmente constituida conforme a la legislación del
Estado de Nuevo León, con más de 27 años de trayectoria en la vinculación y capacitación de
voluntarios.

b) Que dentro de su objeto social se encuentra la coordinación de programas de voluntariado que
enlazan a empresas con organizaciones de beneficio social.

c) Que cuenta con la capacidad, la experiencia y el personal para coordinar el evento objeto de
este convenio.

**II. Declara LA EMPRESA:**

a) Ser una persona moral legalmente constituida, con Registro Federal de Contribuyentes
`{{rfc}}`.

b) Que es su voluntad participar en el Programa de Voluntariado Corporativo de EZER, aportando a
sus colaboradores como voluntarios.

c) Que designa como coordinador del evento a `{{coordinador}}`, quien fungirá como enlace con EZER.

**III. Declaran ambas partes:**

Que se reconocen mutuamente la personalidad con la que comparecen y que es su voluntad obligarse
conforme a las siguientes:

---

**CLÁUSULAS**

**PRIMERA — Objeto.** EZER coordinará para LA EMPRESA un evento de voluntariado corporativo con las
siguientes características:

| | |
|---|---|
| Evento | `{{nombre_evento}}` |
| Fecha estimada | `{{fecha_estimada}}` |
| Municipio | `{{municipio}}` |
| Número de voluntarios | `{{numero_asistentes}}` |
| Duración | `{{duracion}}` |
| Número de jornadas | `{{numero_jornadas}}` |

**SEGUNDA — Obligaciones de EZER.** EZER se obliga a: coordinar la logística del evento; vincular a
LA EMPRESA con una asociación beneficiaria cuyo trabajo corresponda a la causa elegida; acompañar
con personal propio el día del evento; y entregar un reporte de impacto al cierre.

**TERCERA — Obligaciones de LA EMPRESA.** LA EMPRESA se obliga a: aportar a sus colaboradores como
voluntarios en el número y fechas convenidos; cubrir la aportación económica pactada en la
cotización que forma parte integrante de este convenio; designar un coordinador que funja como
enlace; y **recabar de cada voluntario participante la carta de deslinde de responsabilidad** que
EZER proporcionará.

**CUARTA — Aportación.** LA EMPRESA cubrirá la cantidad de `{{monto_total}}` conforme a la
cotización folio `{{folio}}`. EZER emitirá el recibo deducible correspondiente.

**QUINTA — Responsabilidad y seguridad de los voluntarios.** Los voluntarios participan de manera
libre y voluntaria. LA EMPRESA manifiesta que sus colaboradores participan bajo su propia
responsabilidad y que ha recabado de cada uno la carta de deslinde correspondiente. EZER no asume
responsabilidad laboral alguna respecto de los voluntarios, quienes en ningún caso se considerarán
sus trabajadores.

*[Cláusula pendiente de revisión legal — el alcance exacto de la responsabilidad, así como la
conveniencia de contratar una póliza de accidentes personales por evento, debe definirlo el
abogado.]*

**SEXTA — Uso de imagen.** LA EMPRESA autoriza a EZER a tomar fotografías y video durante el evento
y a utilizarlas con fines de documentación del impacto y difusión institucional, sin fines
comerciales. LA EMPRESA podrá manifestar su negativa por escrito antes del evento.

**SÉPTIMA — Cancelación.** Cualquiera de las partes podrá cancelar el evento dando aviso por escrito
con al menos **[N] días naturales** de anticipación. *[Plazo y condiciones de reembolso pendientes
de definir con Dirección y revisión legal.]*

**OCTAVA — Vigencia.** Este convenio surte efectos a partir de su firma y hasta la conclusión del
evento y la entrega del reporte de impacto.

**NOVENA — Jurisdicción.** Para la interpretación y cumplimiento de este convenio, las partes se
someten a las leyes y tribunales de Monterrey, Nuevo León, renunciando a cualquier otro fuero.

---

Leído que fue por las partes, y enteradas de su contenido y alcance, lo firman de conformidad.

Monterrey, Nuevo León, a `{{fecha_emision}}`.

| **POR EZER A.B.P.** | **POR LA EMPRESA** |
|---|---|
| | |
| _________________________ | _________________________ |
| Nombre y firma | `{{coordinador}}` |
| EZER A.B.P. — La Casa del Voluntario | `{{razon_social}}` |

---

# 4 · Marcadores y de dónde sale cada uno

`E-4` sustituye estos valores al generar los documentos.

| Marcador | Origen |
|---|---|
| `{{nombre_evento}}` | Eventos · `Nombre del evento` |
| `{{fecha_estimada}}` | Eventos · `Fecha estimada` |
| `{{municipio}}` | Eventos · `Municipio` |
| `{{numero_asistentes}}` | Eventos · `Número de asistentes` |
| `{{duracion}}` | Eventos · `Duración` |
| `{{numero_jornadas}}` | Eventos · `Número de jornadas` |
| `{{beneficiarios}}` | Eventos · `Beneficiarios de interés` |
| `{{incluye_curso}}` | Eventos · `Incluye curso de sensibilización` → "Sí" / "No" |
| `{{incluye_curso_texto}}` | Texto condicional: si hay curso, la línea que lo describe; si no, vacío |
| `{{coordinador}}` | Eventos · `Coordinador empresa` |
| `{{correo_coordinador}}` | Eventos · `Correo coordinador` |
| `{{telefono_coordinador}}` | Eventos · `Teléfono del coordinador` |
| `{{razon_social}}` | Organizaciones · `Razón social` |
| `{{rfc}}` | Organizaciones · `RFC` |
| `{{uso_cfdi}}` | Organizaciones · `Uso CFDI` |
| `{{fecha_emision}}` | Fecha en que corre `E-4` |
| `{{folio}}` | 🔴 **Sin definir** |
| `{{concepto_1}}` `{{importe_1}}` `{{concepto_2}}` `{{importe_2}}` `{{monto_total}}` | 🔴 **Sin definir** |

---

# 🔴 Lo que falta decidir: de dónde sale el precio

**El plan nunca lo definió, y sin eso la cotización sale en blanco.** Tres caminos:

**A · Lo captura el equipo en el formulario.**
Se agregan dos o tres preguntas más a la sección 1 (monto por voluntario, monto del curso, o
directamente el total) y un campo de moneda en Airtable. Es lo más simple y lo más flexible: cada
cotización puede tener su precio. Requiere que el equipo ya sepa el precio al terminar la reunión.

**B · Se calcula solo.**
Una tarifa base por voluntario o por jornada, más el costo del curso si aplica, guardadas en la
tabla `Config`. `E-4` hace la multiplicación. Más elegante y sin errores de dedo, pero **exige que
exista un tabulador de precios**, y que cambiarlo sea un proceso.

**C · Se llena a mano en el Doc.**
La cotización se genera con los espacios en blanco y el equipo escribe los importes antes de
enviarla. Cero desarrollo, pero el monto **nunca queda en Airtable** — así que no se puede reportar
cuánto se ha cotizado ni cuánto se ha cobrado, y la cláusula CUARTA del convenio queda vacía.

**Recomendación: A.** Es la que menos supone sobre cómo cobran hoy, deja el monto en Airtable para
poder reportarlo, y llena la cláusula del convenio sin trabajo extra. Si más adelante el precio se
estandariza, migrar de A a B es fácil; al revés no.

**Lo mismo con el folio.** Puede ser un autonumber de Airtable con prefijo — por ejemplo
`COT-2026-0001` — igual que el número de referencia del Sprint 6. Si te parece, lo creo con ese
formato y queda resuelto.
