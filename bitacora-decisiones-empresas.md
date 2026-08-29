# Bitácora de decisiones — Flujo de Empresas

Documento vivo. Una entrada por decisión: qué se decidió, qué se descartó y por qué.
Se actualiza en el momento en que se decide, no al final del sprint.

---

## 2026-08-28 · S0-3 · Verificación de planes de las 4 herramientas

Resultado de la verificación hecha por Ethan el viernes 28 de agosto.

| Herramienta | Plan real | Consumo actual | Veredicto |
|---|---|---|---|
| Calendly | Standard | 2 tipos de evento (Empresas y Asociaciones), permite crear más | ✅ Sin bloqueo |
| Make | Core | ~2,300 de **10,000** operaciones · 10 escenarios (7 activos) | ✅ Margen suficiente |
| Airtable | **Free** | Sin asientos de pago. Intención: pagar 2 (sistemas + voluntariado) | ⚠️ Requiere decisión |
| Fillout | Free | 1,000 respuestas/mes · permite subir archivos | ✅ Sin bloqueo |

### D-001 · Calendly soporta los 3 tipos de evento que necesita el flujo

**Decidido:** se confirma el plan Standard y que ya existen los tipos "Empresas" y "Asociaciones".
Falta crear un tercero para el curso de sensibilización en el Sprint 8.

**Consecuencia:** se elimina el riesgo "Calendly no soporta el segundo o tercer tipo de evento"
del registro de riesgos. El respaldo previsto (compartir tipo de evento + Router de
discriminación, +2 h) ya no hace falta.

**Impacto en el plan:** el paso S3-1 se acorta — el tipo de evento de Empresas ya existe,
solo hay que configurarle el parámetro de identificación y el mínimo de 1 día de anticipación.

---

### D-002 · El `record_id` NO viaja hoy por Calendly — se decide agregarlo por UTM

**Situación encontrada:** en Asociaciones el `record_id` nunca pasó por Calendly. El escenario
`A-3` identifica a quién agendó **por el correo del invitado**, y por eso tiene un Router de
3 rutas (contacto existente con organización / sin organización / contacto nuevo). Las
preguntas que sí se piden en Calendly son teléfono, nombre de la asociación y sector que apoyan.

**Decidido:** en Empresas se pasa el `record_id` como parámetro de seguimiento en el link de
Calendly que va dentro del Correo #1 de Bienvenida — el link lo generamos nosotros, así que
podemos anexarlo. Método a probar: parámetro UTM (`utm_content=<record_id>`), que llega en el
objeto `tracking` del webhook de Calendly sin depender de campos ocultos.

**Alternativa descartada:** replicar el emparejamiento por correo de Asociaciones. Se descarta
porque es frágil: si el coordinador agenda con un correo distinto al que usó para registrarse
—cosa común cuando alguien reenvía la invitación a un colega— se crea un evento huérfano que
alguien tiene que reparar a mano.

**Por qué conviene:** además de ser más robusto, **simplifica** el Bloque 2. Con `record_id`
el escenario tiene una sola ruta en vez de las 3 del Router de `A-3`.

**Pendiente de verificar:** que el parámetro UTM efectivamente llegue en el payload del webhook.
Se prueba en el Sprint 3 antes de construir el resto del escenario. Si no llega, el respaldo es
el emparejamiento por correo (el patrón de `A-3`, ya probado) y se pierde la simplificación.

---

### D-003 · Make Core tiene margen suficiente

**Decidido:** no se requiere subir de plan.

**Números (corregidos el 28-ago con lectura directa por MCP):** la licencia Core es de **10,000
operaciones**, no 14,000. Consumo real sumado de los escenarios: ~2,300, es decir **23%**.
Empresas suma ~15 escenarios, de los cuales 5 son diarios y corren aunque no haya actividad.
Estimación de consumo adicional: ~2,000–3,000 operaciones al mes con volumen bajo de empresas,
lo que deja el total alrededor del 40% de la cuota.

**A vigilar:** el consumo se revisa en el Sprint Review de S6 (22 de noviembre), cuando ya
estén corriendo los escenarios diarios de recordatorio y seguimiento. Si para entonces se pasa
del 60%, se evalúa subir de plan antes de construir los Bloques 8 y 9.

---

### D-004 · Fillout Free alcanza para todo el flujo

**Decidido:** no se requiere subir de plan.

**Razón:** 1,000 respuestas al mes cubren de sobra los 4 formularios del flujo (captura interna,
comprobante de pago, registro de voluntarios y feedback), y **sí permite subir archivos**, que
era el requisito crítico del comprobante de pago en el Bloque 5.

---

### D-005 · Airtable en plan Free → el panel se rediseña como panel compartido

**Situación encontrada:** el workspace está en plan Free, sin asientos de pago. Solo se pagarán
2 cuentas: `sistemas@encuentromundialdevalores.org` y `voluntariadocorporativo@ezer.org.mx`.

**Decidido:** Mia, Adri, Ana Cris y Gabi trabajarán desde el **login compartido de voluntariado**.
En consecuencia, el Sprint 11 construye **un panel compartido con filtro manual por Responsable**
(menú desplegable) en vez de las 4 páginas auto-filtradas del diseño original.

**Alternativa descartada:** pagar 4–5 asientos para conservar el filtro por usuario conectado.
Se descarta porque Airtable se cobra por persona y el costo mensual recurrente no se justifica
por una comodidad de filtrado que un desplegable resuelve.

**Por qué funciona:** las condiciones de visibilidad de los botones ("Enviar cotización" solo
aparece si hay asociación ligada) son **por campo, no por usuario**, así que se comportan igual
con cuenta compartida. El reparto por turnos Mia/Adri tampoco se ve afectado: vive en el campo
`Responsable` del registro, no en quién inició sesión.

**Lo que se pierde, y hay que asumirlo:** no hay vista automática de "mis empresas", y no queda
rastro de quién apretó cada botón. La trazabilidad se conserva a nivel de registro (campo
`Responsable`), no a nivel de persona conectada.

**Impacto en el plan:** S11-1 baja de 3 h a 2.5 h y S11-2 sube de 1 h a 1.5 h — el total del
Sprint 11 no cambia. El manual de operación del Sprint 12 debe **abrir** explicando el filtro
de Responsable, porque con cuenta compartida es el primer gesto de cada sesión.

**Riesgo que sube de importancia:** "el equipo no adopta el panel". Sin filtro automático, si
nadie usa el desplegable el panel se vuelve una lista indistinta y el equipo regresa a la tabla.

**Pendiente de verificar (no bloquea hoy):** los límites del plan Free en **automation runs por
mes** y **registros por base**. Los botones nativos del panel son automatizaciones de Airtable y
el primero se construye en el Sprint 6 (9 de noviembre). Si el tope resulta bajo, el respaldo es
cambiar los botones a tipo "abrir URL" apuntando a un webhook de Make, que no consume cuota de
Airtable. Fecha límite para resolverlo: Review del Sprint 5, 8 de noviembre.

---
### D-006 · El campo `Fase de asociación` NO tiene el valor "Activa" — se corrige el filtro del Bloque 4

**Verificado contra la base real** (`appZA6fc9TRQz2upb`, tabla Organizaciones) el 28 de agosto.

La recomendación original del plan era filtrar las asociaciones sugeribles con
`Fase de asociación = "Activa"`. **Ese valor no existe.** Las opciones reales del campo son:

| Valor real de `Fase de asociación` | ¿Sugerible? |
|---|---|
| Asociación Sin Reunión | No — todavía no se ha reunido |
| Reunión agendada | No |
| Reunión realizada | No |
| **Asociación Registrada sin asignación de Empresa** | **Sí — es el estado exacto que buscamos** |
| Convenio enviado | No — falta que lo regrese firmado |
| Asociación registrada con empresa asignada | Depende (ver pregunta abierta) |
| Sin respuesta | No |

El campo `Estatus` (marcado legacy) sí tiene Activa/Inactiva, pero sigue descartado: es una
segunda fuente de verdad que habría que mantener sincronizada a mano.

**Decidido:** el filtro del Bloque 4 usa `Fase de asociación = "Asociación Registrada sin
asignación de Empresa"`. Es más preciso que un "Activa" genérico porque ya incorpora la
condición de que la asociación no tenga empresa.

**Pregunta abierta para el equipo:** ¿una asociación puede atender **más de una empresa a la vez**?
Si sí, también debe sugerirse la fase "Asociación registrada con empresa asignada" y el filtro
se amplía a las dos. Si no, se queda como está. Resolver antes del Sprint 5 (26 de octubre).

---

### D-007 · La tabla Eventos no guarda la causa que la empresa quiere apoyar — BLOQUEA el Bloque 4

**Verificado contra la base real** el 28 de agosto.

El escenario de sugerencias debe cruzar el **sector de apoyo** de la asociación contra **la causa
del evento**. La tabla Organizaciones sí tiene `Sector de apoyo` (multipleSelects, 9 opciones:
Niños, Mujeres, Adultos Mayores, Educación, Discapacidad, Medio Ambiente, Comedores, Migrantes,
Vivienda Digna). **La tabla Eventos no tiene ningún campo equivalente.** No hay dónde esté escrito
qué causa le interesa a la empresa, así que hoy el cruce es imposible.

**Decidido:** agregar a Eventos un campo `Sector de interés` (multipleSelects, con **exactamente
las mismas 9 opciones** que `Sector de apoyo`, para que el cruce sea directo y no requiera
traducción), y capturarlo como pregunta del **formulario interno del Bloque 3**.

**Impacto en el plan:** el Bloque 3 (Sprint 4) suma una pregunta al Fillout y un campo a Airtable.
Es trabajo menor —15 minutos— pero **tenía que descubrirse antes del Sprint 5**, porque sin ese
campo el escenario de sugerencias no se puede construir. Se habría topado con pared el 26 de octubre.

**Criterio secundario de sugerencia:** `Municipio` existe en ambas tablas y puede usarse como
desempate cuando varias asociaciones compartan sector — sugerir primero las del mismo municipio
del evento.

---
### D-008 · El formulario de Calendly solo pide datos del invitado

**Confirmado por Ethan el 28 de agosto.** Una sola cuenta de Calendly con dos tipos de evento
("calendarios"): uno para Asociaciones y otro para Empresas. En el formulario del invitado se
piden **únicamente sus propios datos** — nada de asociación ni de fase.

**Es el diseño correcto y no se cambia.** La asociación no la elige la empresa: la liga el equipo
a mano en el Bloque 4, después de que el sistema sugiere hasta 3. Y la Fase es un concepto interno
de Airtable que el invitado no tiene por qué conocer ni podría contestar.

**Consecuencia sobre D-002:** esto *refuerza* la decisión de pasar el `record_id` por UTM. Si el
formulario no captura nada que ligue al registro de Airtable, el único vínculo disponible sería el
correo del invitado — justo el emparejamiento frágil que D-002 descarta.

**A verificar en el Sprint 3:** qué campos exactos pide hoy el tipo de evento de Empresas. Si no
incluye el nombre de la empresa, el respaldo por correo queda aún más débil y el UTM pasa de
"mejora" a "necesario".

---

### D-009 · El feedback y las fotos van en un solo correo a la empresa — PENDIENTE RESUELTO

**Resuelto el 28 de agosto.** Cierra la pregunta abierta desde julio sobre qué significa
"vincular el feedback de las empresas".

**Decidido:** después del evento, una automatización manda **un solo correo a la empresa** que
incluye la liga del formulario de feedback **y** la liga de la carpeta de fotos.

**Cambio respecto del plan original:** el plan tenía dos correos separados — el #11 con la carpeta
de fotos y el #12 con el feedback. El contenido se fusiona en el correo a la empresa.

**Lo que NO se fusiona:** el aviso interno al Responsable el día del evento se conserva. Tiene otro
destinatario y otro propósito: recordarle que **suba** las fotos. Sin ese aviso, el correo a la
empresa saldría apuntando a una carpeta vacía.

**Ajuste de tiempo necesario:** el plan mandaba el feedback a las 19:00 del mismo día del evento.
Con las fotos incluidas eso ya no funciona — nadie alcanza a subirlas. Se recomienda:

| Cuándo | A quién | Qué |
|---|---|---|
| Día del evento | Responsable (interno) | Link de la carpeta: "sube las fotos hoy" |
| Día siguiente | Empresa | Un correo con feedback + carpeta de fotos |

**Pendiente de confirmar con Ethan:** si el día siguiente es suficiente margen, o conviene mandarlo
a los 2 días. Alternativa más precisa pero más cara de construir: disparar el correo cuando alguien
marque una casilla "Fotos subidas", en vez de por tiempo.

---
## 2026-08-28 · CAMBIO DE ALCANCE — transcripción del video de back office

Ethan compartió la transcripción del video que describe el proceso real de back office.
**No coincide con el flujo sobre el que se construyó el plan del 26 de agosto.** Este registro
documenta la diferencia antes de re-planear.

### C-1 · La asociación se conecta DESPUÉS del pago, no antes

**Plan del 26 de agosto:** Contacto → Reunión → Captura → **Conectar asociación** → Cotización →
Pago. La conexión con la asociación era un candado duro: "la cotización no se puede enviar hasta
que alguien ligue una asociación a mano".

**Proceso real:** Contacto → Reunión → Captura → **Cotización** → Datos fiscales → Recibo →
Comprobante de pago → **Confirmar pago** → **Conectar asociación** → Segunda reunión.

**Consecuencia:** se invalida por completo el Bloque 4 tal como estaba diseñado, incluido el
escenario de desbloqueo (`E-6`) y la condición de visibilidad del botón "Enviar cotización".
La empresa paga primero y se le asigna asociación después.

### C-2 · El recibo fiscal va ANTES del pago, no después

**Plan:** pago confirmado → adjuntar recibo.
**Proceso real:** la empresa sube su constancia de situación fiscal → el contador elabora y sube
el recibo → se le manda a la empresa → **entonces** la empresa sube su comprobante de pago.

Queda invertido, y el Bloque 6 desaparece como etapa independiente.

### C-3 · Número de referencia — concepto nuevo y transversal

Se genera cuando la empresa llena sus datos fiscales y **acompaña todo el proceso** de esa empresa
con esa asociación o evento. Aparece en el correo al contador, al subir el recibo y al subir el
comprobante de pago. Es la llave que hila los tres formularios y los seis correos de esa etapa.
No existe hoy en Airtable: hay que crearlo como campo generado automáticamente.

### C-4 · El contador es un actor nuevo del flujo

Recibe copia del correo de datos fiscales, y tiene **su propio formulario** para subir el recibo
con el número de referencia. No estaba contemplado en el plan.

### C-5 · El motor de match es mucho más grande de lo planeado

**Plan:** `Search Records` que escribe hasta 3 nombres en un campo de texto.

**Proceso real:** una interfaz donde se busca la empresa, se ven sus datos, y un botón "busca qué
asociación hace match" devuelve un **ranking con porcentaje de compatibilidad y semáforo por
variable**. Ejemplo del video: Cardio Chavitos 90% — municipio Santa Catarina ✅, 10 voluntarios ✅,
beneficiario niños ✅, fechas ❌ (ellos junio, la empresa mayo). Segunda opción 85%, y así.

**Las 4 variables de cruce son:** municipio, número de voluntarios, tipo de beneficiario y fechas.
Esto responde y amplía D-007: no es un campo de sector, son cuatro.

### C-6 · Hay una SEGUNDA reunión por Calendly

Después de escoger asociación, la empresa agenda una segunda cita para definir el evento: fecha y
lugar del curso de sensibilización (si se pagó) y la ficha técnica con el minuto a minuto.
Calendly pasa de necesitar 2 tipos de evento a 3 o 4.

### C-7 · Etapas previas al evento que el plan no contemplaba

- Recordatorio de materiales: qué llevar, qué se juntó, quién asiste
- Captura de datos de los voluntarios que van a ir
- Firma del contrato de deslinde **antes** del evento (el plan lo tenía en el correo de pasos a seguir)
- **Llamada telefónica de confirmación con registro** de quién marcó y en qué fecha
- **Asignar quién de EZER acompaña el evento**

### C-8 · Tres feedbacks por QR, no uno

De la asociación, de la empresa **y de los participantes**. El plan solo contemplaba el de la
empresa. También queda por definir quién toma las fotografías y a dónde se suben.

### Pendientes explícitos que el propio video deja abiertos

1. Qué datos exactos se le piden a la empresa en la reunión para poder hacer el match
2. Cómo escoge la empresa la asociación: ¿reporte por correo, liga a la base, o llamada por Calendly?
3. Quién toma las fotos y dónde se suben

---
### D-010 · Los escenarios de Make los construye Claude con una skill — recálculo de esfuerzo

**Confirmado el 28 de agosto:** la skill existe y Ethan la probó — creó un escenario de Make.
Esto invalida el supuesto central del plan del 26 de agosto, que decía textualmente que "el cuello
de botella no es redactar contenido, es dar los clics reales en Make, y eso nadie más que tú lo
puede hacer".

**Recálculo aplicado** (factores por tipo de tarea, no un descuento parejo):

| Tipo de trabajo | Factor | Razón |
|---|---|---|
| Escenarios de Make | ×0.6 | Construcción por API; el 40% que queda es **revisión y prueba**, no construcción |
| Campos y vistas de Airtable | ×0.5 | El MCP de Airtable ya está disponible en la sesión |
| Documentación | ×0.5 | La redacta el agente |
| Fillout, Calendly, plantillas de Docs | ×0.9 | No hay camino por API; siguen siendo UI manual |
| Pruebas | ×0.9 | El disparo real (reservar en Calendly, revisar el correo) sigue siendo humano |
| Diseño y gestión | ×1.0 | Criterio humano; no baja |
| Sprints S0 a S3 | ×1.0 | Sin ahorro: es el periodo de calibración de la skill |

**Resultado:** 163 h → **125.75 h** (−23%). De 17 sprints a **14**. Entrega del 25 de abril al
**14 de marzo de 2027**.

**Observación de planeación:** la skill compensa casi exactamente el crecimiento de alcance que
trajo el video. El plan original cerraba el 28 de febrero; el proceso real lo empujó al 25 de
abril; la skill lo regresa al 14 de marzo.

**Lo que NO se automatiza y hay que seguir asumiendo:** las conexiones OAuth de Gmail, Airtable,
Drive y Calendly dentro de Make requieren autorización humana; los formularios de Fillout, los
tipos de evento de Calendly y las plantillas de Docs se siguen armando a mano; y las decisiones
de diseño (fórmula del match, formato del número de referencia) no bajan ni un minuto.

**Pendiente de verificar antes de dar el ahorro por bueno:** que el plan Make Core incluya acceso
a la API. Las 24.6 h de ahorro dependen enteramente de poder crear escenarios por blueprint.

**Calibración pendiente:** el factor ×0.6 es una estimación, no una medición. El Sprint 2 (E-1,
primer escenario real de Empresas) debe tratarse como **sprint de calibración**: registrar horas
reales contra estimadas y ajustar el factor para los 12 sprints restantes con dato duro.

---
### D-011 · Los ESCENARIOS DE MAKE de Asociaciones son de solo lectura (Airtable sí se puede)

**Instrucción de Ethan, 28 de agosto:** no editar ningún **escenario de Make** de Asociaciones
(`A-1` a `A-6`), aunque tengan errores. Sirven **como referencia** para construir los de Empresas.

**Alcance preciso de la restricción (aclarado el mismo día):** aplica **solo a Make**. En Airtable
sí se puede modificar lo que haga falta, incluido el lado de Asociaciones — campos, vistas e
interfaces. Eso desbloquea la tarea S1-5.

**Consecuencia inmediata sobre el Sprint 1.** Dos de sus seis tareas quedan bloqueadas porque
implican editar escenarios existentes:

| Tarea | Qué implicaba | Estado |
|---|---|---|
| S1-1 Probar `A-3` end-to-end | Solo probar | ✅ Se puede |
| S1-2 Re-probar `A4-4` | Solo probar | ✅ Se puede |
| S1-3 Re-probar `A4-5` | Probar **y agregar filtro a `A-4`** | ⚠️ Solo la parte de probar |
| S1-4 Patrón multi-contacto en `A-2.1` | **Editar `A-2.1`** | 🔴 Bloqueada |
| S1-5 Campo Convenio firmado | Interfaz de Airtable | ✅ Se puede — la restricción es solo Make |
| S1-6 Cerrar reporte de pruebas | Documentación | ✅ Se puede |

**Reencuadre del Sprint 1:** deja de ser "cerrar pendientes de Asociaciones" y pasa a ser
**"extraer y documentar los patrones que Empresas va a reusar"** — lectura de blueprints por MCP,
sin tocar nada. Libera alrededor de 3 h del sprint (solo S1-4 queda fuera; S1-5 sí se hace).

**Riesgo técnico que esto introduce, y es el punto importante:** los bugs conocidos se quedan
en producción. `A-2.1` lleva **13 errores y 6 ejecuciones en la cola de fallidos**; `A-2.2`,
3 errores y 1 atorada. Si al construir Empresas se copia el patrón de un escenario que falla,
**se replica el bug**. Regla al construir: tomar `A-1` como referencia de envío multi-contacto
(tiene Iterator y Aggregator), **nunca `A-2.1`**, y verificar en el blueprint antes de copiar,
no de memoria.

**Nota de estado (verificado por MCP el 28-ago):** de los 10 escenarios del team, 2 están marcados
`isinvalid` y desactivados — `Contacto Empresas` (el webhook viejo de empresas) e
`Integration Calendly, Airtable`. Ninguno es de Asociaciones, así que no los cubre esta restricción,
pero conviene decidir si se reusan o se archivan antes del Sprint 2.

---
## 2026-08-28 · S1 · Patrones extraídos de `A-1` (lectura, sin editar)

Leído por MCP el blueprint completo de `A-1` (217 KB, 18 módulos). Estos son los patrones
verificados que Empresas va a reusar. **Versiones tomadas de un escenario real en producción**,
no de documentación — es la referencia más confiable disponible.

### Módulos y versiones confirmadas

| Módulo | Versión |
|---|---|
| `airtable:ActionSearchRecords` | 3 |
| `airtable:ActionGetRecord` | 3 |
| `airtable:ActionUpdateRecords` | 3 |
| `google-email:sendAnEmail` | 4 |
| `builtin:BasicRouter` · `BasicFeeder` · `BasicAggregator` · `Break` | 1 |
| `http:DownloadFile` | (usado para bajar el flyer del catálogo) |

### Patrón de manejo de errores

Handler `builtin:Break` en cada módulo, con dos variantes según el riesgo:

- **`retry: true, interval: "5"`** — en Search, Get, DownloadFile y los envíos de Gmail.
- **`retry: false`** — en el `ActionUpdateRecords` que **marca la casilla de candado**, después
  del Aggregator. Correcto: reintentar el marcado del candado es justo lo que podría duplicar.

### Patrón multi-contacto (el que SÍ se copia)

`BasicFeeder` → `BasicRouter` → `sendAnEmail` (uno por contacto) → `BasicAggregator` →
`ActionUpdateRecords` con `retry: false` para marcar la casilla una sola vez.

⚠️ Este es el patrón correcto. **`A-2.1` no lo tiene bien aplicado** y por eso acumula 13 errores
y 6 ejecuciones atoradas. Al construir Empresas se copia de `A-1`, nunca de `A-2.1` (D-011).

### Hallazgo de diseño que hay que resolver antes de construir `E-1`

En Make, **un Router no puede volver a converger**. `A-3` lo resuelve **duplicando** todo lo que
va después en cada una de sus 3 rutas — por eso tiene 3 copias de `createATask` y `sendAnEmail`.

Para `E-1` eso significa duplicar el reparto por turnos, la creación del Evento y los dos correos
en ambas ramas (empresa existente / empresa nueva). Es el patrón probado de la casa, pero **duplica
el mantenimiento**: cada cambio futuro en un correo hay que hacerlo dos veces, y olvidar una es
exactamente cómo se producen inconsistencias.

**Alternativa a evaluar:** `If-Else` + `Merge`, que sí converge y deja una sola copia de todo lo
que sigue. Hay que verificar disponibilidad antes de comprometerla.

---
## 2026-08-28 · PUNTO DE RETOMADA — construcción de `E-1`

### 🔴 Hallazgo operativo: el sitio manda registros a un escenario apagado

`api/interest.js` línea 224 hace POST a `https://hook.us2.make.com/gk3msgktwdcxack4cb718h5d6a5ntfsd`
con `tipo: "registro_empresa"`. Ese webhook es el hook **2521498 ("Nuevo Prospecto")**, y pertenece
al escenario **`Contacto Empresas` (5537665), que está marcado inválido y DESACTIVADO**.

**Consecuencia:** hoy, cada empresa que se registra en el sitio dispara un webhook que nadie
procesa. La cola marca 0, así que no se están acumulando — se pierden. Esto lleva así desde al
menos el 20 de julio, fecha del último edit del escenario.

**Camino recomendado para `E-1`:** reconstruir el escenario `Contacto Empresas` (5537665) como
`E-1`, **conservando el hook 2521498**. Ventajas: no hay que tocar `api/interest.js`, la URL del
sitio sigue funcionando, y es un escenario de Empresas, así que la restricción D-011 no aplica.
Requiere confirmación de Ethan por ser una modificación sobre un escenario existente.

### Decisiones tomadas para `E-1` (aprobadas por Ethan el 28-ago)

| Punto | Decisión |
|---|---|
| Estructura de ramas | **`builtin:BasicIfElse` + `builtin:BasicMerge`** — verificados como disponibles. Una sola copia del reparto por turnos, la creación del Evento y los dos correos. |
| Conexión de Gmail | **"My Gmail connection", ID 10032499** (la misma de `A-1` y `A-2.1`) |
| Conexión de Airtable | ID 9689432 — es la única del team ⚠️ **expira el 23-oct-2026** |

### Composición propuesta de `E-1`

```
gateway:CustomWebHook (hook 2521498)
  └─ filtro: tipo = "registro_empresa"
→ airtable:ActionSearchRecords v3   (Organizaciones, por correo, normalizado a minúsculas + trim)
→ builtin:BasicIfElse
   ├─ Sí existe → airtable:ActionUpdateRecords v3   (refrescar datos del contacto)
   └─ No existe → airtable:ActionCreateRecord       (Organizaciones, Tipo=Empresa)
                → airtable:ActionCreateRecord       (Contactos, vinculado)
→ builtin:BasicMerge
→ airtable:ActionGetRecord v3        (Config → "Último responsable asignado")
→ airtable:ActionCreateRecord        (Eventos: Fase="01 Contacto", Responsable alternado)
→ google-email:sendAnEmail v4        (Correo #1 → empresa: flyer + liga de Calendly)
→ google-email:sendAnEmail v4        (Correo #2 → EZER: nuevo interesado)
→ airtable:ActionUpdateRecords v3    (Config: guardar el nuevo turno)  ← retry:false
```

**Error handlers** (patrón copiado de `A-1`): `builtin:Break` con `retry: true, interval: "5"` en
todos los módulos, **salvo** el `ActionUpdateRecords` final de Config, que va con `retry: false`
para no arriesgar un doble avance de turno.

**IDs de Airtable ya identificados:** base `appZA6fc9TRQz2upb` · Organizaciones `tblTGoIoCoRRr6dPf`
· Contactos `tbldFi4FjJj0u7eVl` · Eventos `tblpxjGTQuC3zS4U6` · Config `tblc3ef3CHaAMLe32`.

### 🔴 Lo que falta para poder construir

1. **La URL del tipo de evento de Calendly para Empresas.** Va dentro del Correo #1, con
   `?utm_content=<record_id>` anexado. Solo Ethan la tiene — no hay conector de Calendly.
2. **Confirmación** para reconstruir el escenario 5537665 en vez de crear uno nuevo.
3. **Activar las notificaciones push** (`/config`). Sin eso no hay forma de consultar a Ethan
   a mitad de una construcción, que es justo lo que el flujo de trabajo acordado requiere.
4. **El límite diario de ClickUp** se reinicia ~29-ago 18:00 para poder marcar tareas.

---
