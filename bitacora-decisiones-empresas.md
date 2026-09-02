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
## 2026-08-29 · ✅ `E-1` CONSTRUIDO Y ACTIVO — Fase A

Escenario **5537665** reconstruido como `E-1) Registro de Empresa`, conservando el hook 2521498.
`isinvalid: false`, `isActive: true`. **Los registros del sitio ya no se pierden.**

### Estructura final

```
gateway:CustomWebHook (hook 2521498)
  └─ filtro: tipo = "registro_empresa"
→ airtable:ActionSearchRecords  (Contactos, por Correo normalizado)
→ builtin:BasicRouter
   ├─ Ruta 1 · filtro "Ya existe"  → ActionUpdateRecords (Contactos: nombre + teléfono)
   ├─ Ruta 2 · filtro "No existe"  → ActionCreateRecord (Organizaciones, Tipo=Empresa)
   │                                → ActionCreateRecord (Contactos, vinculado)
   └─ Ruta 3 · SIN filtro          → ActionSearchRecords (Contactos, re-lectura)
                                    → ActionGetRecord (Config)
                                    → ActionCreateRecord (Eventos)
                                    → ActionUpdateRecords (Config)   ← retry:false
```

### D-012 · Se descarta `If-Else + Merge`: la API de Make lo rechaza

Se intentaron **tres** estructuras de `BasicIfElse` + `BasicMerge` y la API las rechazó todas:

1. Merge al nivel superior tras el If-Else → *"BasicMerge found after BasicIfElse with no merged branches"*
2. Merge dentro de cada rama → *"BasicMerge must immediately follow a BasicIfElse"*
3. Merge arriba + segunda rama como "else" puro sin filtro → mismo error que (1)

**Solución adoptada:** `BasicRouter` con **tres** rutas, donde la tercera **no tiene filtro** y por
tanto siempre corre al final. Ahí vive todo el flujo compartido.

**Por qué es mejor que la alternativa:** logra el objetivo original —**cero duplicación** de la
lógica compartida— sin depender del Merge. `A-3` resuelve lo mismo duplicando todo en cada rama;
este patrón no. Es el patrón a reusar en los escenarios siguientes.

⚠️ Requiere `sequential: true` en el metadata del escenario para garantizar que las rutas corran
en orden y la tercera vea el resultado de las dos primeras.

### D-013 · `useColumnId: true` obliga a referenciar la salida por ID de columna

La primera prueba dejó el vínculo a Organización vacío. Causa: se mapeó
`{{8.Organización[].id}}` usando el **nombre** del campo, pero con `useColumnId: true` la salida
del módulo viene **por ID de columna**. Lo correcto es `` {{8.`fldxMJhApdpUOnZNe`}} `` — pasando el
array completo, sin `[].id`.

Aplica a **todos** los escenarios que se construyan con `useColumnId: true`.

### Resultado de las pruebas (3 POST reales al webhook)

| Caso | Resultado |
|---|---|
| Empresa nueva (Cemex) | ✅ Organización + Contacto + Evento creados, Fase `01 Contacto` |
| Empresa nueva (FEMSA) | ✅ Vínculo a Organización correcto tras el fix D-013 |
| **Mismo correo reenviado** | ✅ **NO duplicó el contacto** — lo actualizó con nombre y teléfono nuevos |
| Reparto por turnos | ✅ Mia → Adri → Mia, y Config avanzó en cada corrida |
| `capacitacion: "Sí"` | ✅ marcó la casilla "Incluye curso de sensibilización" |
| Nombre del evento vacío | ✅ cayó al respaldo `"Voluntariado " + empresa` |

Registros de prueba eliminados y Config restaurado a "Adri".

### 🔴 Hallazgo abierto: el reenvío SÍ duplica el Evento

El contacto no se duplica, pero **cada POST crea un Evento nuevo**. Si una empresa manda el
formulario dos veces, quedan dos Eventos en Fase 01 para la misma organización.

Puede ser correcto (una empresa puede hacer varios eventos al año) o puede ser un problema.
**Decisión pendiente:** ¿se agrega un candado que evite crear un Evento si ya existe uno en Fase
`01 Contacto` para esa organización? Bloquea el cierre de la tarea S2-6.

### Lo que falta de `E-1` (Fase B)

Los dos correos. **Y hay una complicación que descubrimos hoy:** `api/interest.js` **ya manda un
correo de bienvenida** al interesado, con copia a voluntariado y con el link de Calendly incluido
(`calendly.com/voluntariadocorporativo-ezer/reunion-de-voluntariado-corporativo`, línea 94).

Si `E-1` manda su propio Correo #1, la empresa recibe **dos correos**. Además, el correo del sitio
sale **antes** de que exista el registro en Airtable, así que no puede llevar el `record_id` en el
link de Calendly — lo que choca con la decisión D-002.

**Tres opciones, pendientes de decidir:**
1. Quitar el link de Calendly del correo del sitio y que `E-1` mande el suyo con `record_id`
2. Dejar el correo del sitio como está y renunciar al `record_id` (emparejar por correo, patrón `A-3`)
3. Que el sitio deje de mandar correo y `E-1` se encargue de todo

---
### D-014 · Una empresa SÍ puede tener varios eventos — no se pone candado

**Decidido por Ethan el 29-ago.** El hallazgo de que cada POST crea un Evento nuevo, aun con el
mismo correo, **es el comportamiento correcto**: una empresa puede hacer varios eventos de
voluntariado. No se agrega candado sobre la creación del Evento.

El candado que sí importa —y ya funciona— es el del **Contacto**: reenviar el formulario actualiza
el contacto existente en vez de duplicarlo. Eso queda probado.

### D-015 · Se quita el Calendly del correo del sitio; `E-1` es el único que lo manda

**Situación:** `api/interest.js` mandaba un correo de bienvenida con el link de Calendly. Como ese
correo sale **antes** de que exista el registro en Airtable, no podía llevar el `record_id`, y
además duplicaba el Correo #1 que `E-1` iba a mandar.

**Decidido:** quitar el bloque de Calendly del correo de registro del sitio. `E-1` manda el suyo,
ya con el registro creado, y por tanto **puede anexar `?utm_content={{record_id}}`** — que es lo
que exige D-002 para identificar quién agendó sin depender del correo del invitado.

**Alcance del cambio de código:** solo las variantes de **registro** (texto y HTML). El bloque de
Calendly del flujo de **catálogo/evento** se dejó intacto, porque ese camino no pasa por `E-1`
(el filtro del escenario solo deja pasar `tipo = registro_empresa`).

**Efecto secundario feliz:** el correo de registro ya decía *"no necesita hacer nada más: nosotros
le contactaremos muy pronto"* y aun así mostraba un botón de agendar. Quitarlo **resuelve una
contradicción** que ya existía en el texto, no solo el duplicado.

⚠️ **Requiere redesplegar el sitio en Vercel** para surtir efecto. Mientras no se despliegue, el
correo viejo con Calendly sigue saliendo.

---
## 2026-08-29 · ✅ `E-1` FASE B — los dos correos, desplegados y activos

Escenario 5537665 completo: **11 módulos**, `isinvalid: false`, `isActive: true`.

Ruta 3 final: `Search Contactos` → `Get Config` → `Create Evento` → `http:DownloadFile` (flyer)
→ **Correo #1 a la empresa** → **Correo #2 interno a EZER** → `Update Config` (`retry:false`).

### Los dos correos

**#1 a la empresa** — saludo personalizado, botón de Calendly con
`?utm_content={{10.id}}` (el record ID del Evento, que es lo que exige D-002), el flyer del
Catálogo 2026 adjunto, y el nombre del Responsable asignado. Paleta EZER (#1A2E6C / #E8401C).

**#2 interno a voluntariadocorporativo@** — tabla con todos los datos del registro, el Responsable
que tocó por turno, y el record ID del Evento para poder buscarlo.

Ambos con `Break, retry: false` — **reintentar un correo es lo que lo duplica**.

### D-016 · Versiones de módulo que hay que recordar

`http:DownloadFile` es **versión 4**, no 3. Se descubrió por error de despliegue y se confirmó con
`apps_recommend`. El blueprint de `A-1` no expone la versión de forma localizable, así que **no
sirve como fuente para versiones de módulo** — hay que consultarlas explícitamente.

Confirmadas hasta ahora: `airtable:*` v3 · `google-email:sendAnEmail` v4 · `http:DownloadFile` v4
· `builtin:*` v1.

También: el parámetro `description` de `scenarios_update` tiene **límite de 240 caracteres**.

### 🔴 Hallazgo de la prueba end-to-end: el correo NO es único en Contactos

La prueba se mandó con `sistemas@encuentromundialdevalores.org` y reveló que **8 contactos de
prueba comparten ese mismo correo**. El escenario tomó la ruta "Ya existe", eligió uno
arbitrariamente (`maxRecords: 1`), lo actualizó, y ligó el Evento a **la organización equivocada**.

**No es un bug del escenario** — se comportó como está diseñado. Pero expone un supuesto que hay
que hacer explícito: **la deduplicación asume que el correo identifica de forma única a un
contacto**. Con correos repetidos, elige uno al azar.

**Dos implicaciones a resolver:**
1. Limpiar los contactos de prueba duplicados antes de migrar datos reales
2. Definir qué pasa si una persona se registra con un correo ya ligado a **otra** empresa
   (¿cambió de trabajo? ¿es un despacho que representa a varias?). Hoy el Evento nuevo se cuelga
   de la empresa vieja.

Registro restaurado (`rec9GOvTOUUkR6IKr` → "Contacto VINC-1"), Evento de prueba eliminado y
Config de vuelta en "Adri".

---
## 2026-08-29 · Sprint 3 · Campos de Airtable + `E-3` construido y probado

### Campos nuevos en Eventos

| Campo | Tipo | Para qué |
|---|---|---|
| `Beneficiarios de interés` | multipleSelects, 9 opciones | Cruce del motor de match contra `Sector de apoyo` |
| `Meses de interés` | multipleSelects, 12 meses | Cruce contra `Meses disponibles` |
| `Días desde el contacto` | formula `DATETIME_DIFF(NOW(), CREATED_TIME(), 'days')` | Dispara `E-3` |
| `Seguimiento 5 días enviado` | checkbox | Candado del recordatorio |
| `Correo del contacto` | lookup vía Organización | Destinatario de `E-3` |
| `Accion E3` | formula, **nombre sin acentos** | Encapsula toda la decisión de `E-3` (ver D-017) |

**Corrección a S3-7:** el plan pedía crear 4 campos de match. **Dos ya existían** — `Municipio` y
`Número de asistentes`. Solo faltaban beneficiarios y meses.

### 🔴 Hallazgo crítico para el motor de match: las escalas de voluntarios NO coinciden

| Organizaciones · *Voluntarios que puede recibir* | Eventos · *Número de asistentes* |
|---|---|
| 1-10, 11-20, 21-30, 31-40, 41-50, 50+ | 1-5, 6-10, 11-15, 16-20, 21-25, 26-30, 31-40, 41-50, 51-75, 76-100, Más de 100 |

Son **rangos distintos**: no se pueden cruzar directamente. La fórmula de puntuación (S7-5) tiene
que convertir ambos a un número —por ejemplo el punto medio del rango— antes de comparar. Es
justo el riesgo que anticipaba S7-6, ahora confirmado con datos reales.

### D-017 · Los nombres de campo con acentos rompen las expresiones de Make

**Síntoma:** el `Search Records` de `E-3` con la fórmula
`AND({Fase} = "01 Contacto", {Días desde el contacto} >= 5)` **devolvió los 3 registros de la
tabla**, ignorando por completo el filtro. Los filtros del Router tampoco frenaron el envío, y el
correo reventó con `[400] Recipient address required`.

**Se probaron tres variantes** antes de dar con el patrón: filtros por ID de columna, filtros por
nombre acentuado, y finalmente la solución.

**Solución adoptada:** un campo calculado en Airtable con **nombre estrictamente ASCII**
(`Accion E3`) que encapsula toda la lógica de decisión y devuelve `"recordar"`, `"inactivar"` o
vacío. Del lado de Make **ninguna expresión contiene acentos**:
- fórmula de búsqueda: `{Accion E3} != ""`
- filtro ruta A: `{{1.`Accion E3`}} = "recordar"`
- filtro ruta B: `{{1.`Accion E3`}} = "inactivar"`

Los acentos viven **dentro** de la fórmula de Airtable, donde sí funcionan.

**Regla para los escenarios que faltan:** cuando la lógica de un escenario dependa de campos con
acentos, encapsularla en un campo calculado de nombre ASCII y que Make solo lea ese. Como beneficio
lateral, la condición se puede **verificar a ojo en Airtable** antes de correr nada.

⚠️ La base está llena de nombres acentuados (`Organización`, `Asociación asignada`,
`Sugerencias de asociación`, `Días…`). Esto va a reaparecer.

### `E-3` probado — ambas rutas verificadas

Escenario **6093624**, diario a las 15:00 UTC (9:00 Monterrey), activo.

| Prueba | Resultado |
|---|---|
| Sin registros en Fase 01 | ✅ corrida limpia, no manda nada |
| Ruta "recordar" | ✅ correo enviado + casilla marcada; `Accion E3` se vació sola |
| Idempotencia | ✅ con la casilla marcada ya no vuelve a entrar |
| Ruta "inactivar" | ✅ Fase → `Inactivo` + aviso interno; `Accion E3` se vació sola |

La prueba se hizo bajando el umbral de la fórmula temporalmente (de `>=5` a `>=0`), ya restaurado.
Registros de prueba eliminados.

---
### D-018 · Los webhooks de Calendly no se pueden filtrar por tipo de evento

**Verificado el 29-ago con una reserva real de Ethan.**

Las suscripciones de webhook de Calendly se hacen a nivel **usuario u organización**, nunca por
tipo de evento. Consecuencia: **toda reserva llega a todos los escenarios** que escuchen
`invitee.created`, sin importar de qué tipo de evento sea.

**Evidencia del daño:** Ethan agendó una reunión del tipo de evento de **Empresas**. `A-3`
(Asociaciones) la procesó igual, emparejó su correo con el contacto `rec9GOvTOUUkR6IKr` y
le escribió `Fecha de reunión = 31-ago 16:00` a la organización **`recIxPA5vRKSOJPTY`
("PRUEBA VINC-1")**, que es una **Asociación**. Una reserva de Empresas corrompió un registro
de Asociaciones.

**Lo bueno:** el esquema de salida del trigger **sí incluye `tracking.utm_content`**, así que el
mecanismo de D-002 es viable. Falta confirmar que se popule con una reserva que lleve el
parámetro.

**Nota técnica:** el trigger `calendly:watchInvitees` (app versión **2**, no 4) **no expone el tipo
de evento**. Solo trae `event` como URI. Para conocer el tipo hay que encadenar `getAnEvent`
— que es exactamente por lo que `A-3` tiene ese módulo en segunda posición.

### D-019 · Excepción autorizada a D-011: un filtro en `A-3`

**Autorizado por Ethan el 29-ago.** Se permite **una sola modificación** a `A-3`: agregar un filtro
de tipo de evento después del módulo `getAnEvent`. No se tocan módulos, mapeos ni correos.

**El resto de D-011 sigue vigente:** `A-1`, `A-2.1`, `A-2.2`, `A-4`, `A-5` y `A-6` intactos, y de
`A-3` solo esta condición.

**Cómo se aplica — y por qué a mano:** editar `A-3` por API obligaría a reemplazar su blueprint
completo (28 módulos), porque `scenarios_update` no hace ediciones quirúrgicas. Reescribir un
escenario estable en producción para agregar una condición es riesgo desproporcionado. **Se aplica
en la interfaz de Make**, que lo hace de forma segura en dos minutos.

---
### `E-2` construido — escenario 6093786, activo

Webhook propio: **2750604** (`E-2 Calendly Empresas`). Tipos de evento confirmados por Ethan:
`Reunión Ezer Voluntariado Corporativo (Asociaciones)` y `… (Empresas)`.

```
calendly:watchInvitees (hook 2750604)
→ calendly:getAnEvent
   └─ filtro: {{2.name}} contiene "(Empresas)"     ← ASCII puro, evita D-017
→ airtable:ActionSearchRecords (Eventos, maxRecords 1)
→ airtable:ActionUpdateRecords (Fase "02 Reunión" + Fecha de reunión)
→ google-email  (confirmación a la empresa)
→ google-email  (aviso interno a EZER)
```

### D-020 · Emparejamiento dual en una sola búsqueda, sin ramificar

En vez de un Router entre "hay `utm_content`" y "no hay", la fórmula de Airtable cubre **ambos
caminos a la vez**:

```
OR(
  RECORD_ID() = "{{1.tracking.utm_content}}",
  AND(LOWER({Correo del contacto} & "") = LOWER("{{1.email}}"), {Fase} = "01 Contacto")
)
```

Si el UTM viene, empareja por ID. Si viene vacío, `RECORD_ID()=""` es falso para todos y cae al
emparejamiento por correo restringido a Fase 01. **Un solo camino, sin Router, sin duplicar nada.**

Todos los nombres de campo de la fórmula son ASCII (`Correo del contacto`, `Fase`), respetando D-017.

El correo interno reporta **por cuál de los dos caminos emparejó**, así que la prueba de D-002 se
verifica leyendo ese correo, sin tener que inspeccionar el payload.

**Limitación conocida:** si un mismo correo tiene varios Eventos en Fase 01, `maxRecords: 1` elige
uno arbitrariamente. Es la misma familia del hallazgo de unicidad de correo del 29-ago.

**Pendiente:** la tarea de Google Tasks con la liga del formulario interno (patrón de `A-3`) se
agrega en el Sprint 4, cuando ese formulario exista.

---
### Aplicada la excepción D-019 — filtro en `A-3`, por API

**Hecho el 29-ago.** Se agregó al módulo 4 de `A-3` (`ActionSearchRecords`, el que va justo después
de `getAnEvent`):

```json
"filter": {"name": "Solo Asociaciones",
  "conditions": [[{"a": "{{3.name}}", "b": "(Asociaciones)", "o": "text:contains"}]]}
```

Al ir sobre el módulo 4, **corta todo el flujo aguas abajo** cuando la reserva no es de Asociaciones.

**Verificado tras aplicarlo:** 28 módulos intactos, las 3 rutas del Router, el `else: 1`, las
conexiones, las posiciones del canvas y las etiquetas. `isinvalid: false`, activo.

**Respaldo del blueprint original** en `respaldos-make/A-3_blueprint_2026-08-30_ANTES-del-filtro.json`.

**Cómo se hizo con seguridad:** `scenarios_update` reemplaza el blueprint completo, y el de `A-3`
pesa 106 KB — demasiado para transcribir sin riesgo. Se redujo a **16 KB** quitando solo las tres
claves que Make regenera solo (`expect`, `interface`, `samples`), conservando `designer` y `restore`
para no descuadrar el canvas ni perder las etiquetas.

**Lo que se descubrió al leer `A-3` y sube la gravedad de D-018:** su Router tiene ruta *else*
(`parameters: {"else": 1}` → la ruta 2). Si una empresa agendaba con un correo **que no estaba en
Contactos**, `A-3` no solo escribía encima: **creaba una Asociación nueva desde cero**, con
`Tipo = Asociación` y `Fase de asociación = Reunión agendada`. Peor que el caso que se detectó.

**Reparación:** se restauró `recIxPA5vRKSOJPTY` (PRUEBA VINC-1) a su `Fecha de reunión` original
del 3 de agosto, que la reserva de prueba había sobrescrito.

---
### D-021 · `text:contains` no funciona como filtro; usar `text:equal`

**Detectado el 29-ago con evidencia cruzada.** Se filtró `A-3` y `E-2` con
`{"o": "text:contains", "b": "(Asociaciones)"}` y `"(Empresas)"` respectivamente. Al agendar una
reserva **de Empresas**, ambos escenarios corrieron con **2 operaciones** y se detuvieron:
`A-3` bloqueó (correcto) y `E-2` **también bloqueó** (incorrecto — debía procesarla).

Se confirmó vía `rpc_execute` / `listMyEvents` que el nombre real del evento **sí** es
`Reunión Ezer Voluntariado Corporativo (Empresas)`. O sea: el nombre estaba bien y el operador no.

**Conclusión:** `text:contains` evalúa siempre falso. El operador probado y funcional es
**`text:equal`** — es el que usa el filtro `tipo = registro_empresa` de `E-1`, que sí funciona.

**Consecuencia grave mientras estuvo mal:** `A-3` bloqueaba **todas** las reservas, incluidas las
legítimas de Asociaciones. Estuvo así unos minutos.

**Corregido en ambos:**

| Escenario | Filtro |
|---|---|
| `E-2` | `{{2.name}}` **igual a** `Reunión Ezer Voluntariado Corporativo (Empresas)` |
| `A-3` | `{{3.name}}` **distinto de** `Reunión Ezer Voluntariado Corporativo (Empresas)` |

**Por qué `A-3` va invertido:** filtrar por "es Asociaciones" falla cerrado — si el nombre cambia,
`A-3` deja de procesar todo. Filtrar por "no es Empresas" falla **abierto**: ante cualquier
imprevisto `A-3` sigue trabajando como siempre, y a lo más deja pasar una de Empresas, que es el
comportamiento que ya tenía antes. En un escenario en producción, fallar abierto es lo correcto.

**Regla para lo que falta:** usar solo operadores verificados. `text:equal` y `exist`/`notexist`
están probados en producción. Cualquier otro, probarlo antes de confiar en él.

### Estado del Sprint 3 al cierre del 29-ago

| Tarea | Estado |
|---|---|
| S3-1 Tipo de evento de Empresas en Calendly | ✅ ya existía; nombre confirmado por API |
| S3-2 Probar el `utm_content` | ⏳ **pendiente de una reserva más** |
| S3-3 Escenario `E-2` | ✅ construido y activo (6093786) |
| S3-4 Correos de confirmación | ✅ incluidos en `E-2` |
| S3-5 Escenario diario `E-3` | ✅ construido, activo y **probado en ambas rutas** |
| S3-6 Pruebas del Bloque 2 | ⏳ depende de S3-2 |
| S3-7 Campos de match en Airtable | ✅ hecho (2 de 4 ya existían) |

**Para cerrar el sprint** falta una reserva de prueba con la liga que lleva `utm_content`. El evento
`recAetb0HbVtNs2h7` sigue en Fase 01 esperando. El correo interno de `E-2` reporta por cuál camino
emparejó, así que esa sola reserva confirma o descarta D-002.

---
## 2026-09-02 · ✅ D-002 CONFIRMADO — Sprint 3 cerrado

### El `utm_content` sí viaja en el webhook de Calendly

Probado **sin necesidad de agendar otra vez**, reproduciendo (`scenarios_replay`) la ejecución de la
reserva del 30 de agosto, ya con el filtro corregido de D-021.

| Reproducción | Operaciones | Resultado |
|---|---|---|
| 1ª | 6 | El evento pasó de Fase `01 Contacto` → **`02 Reunión`**, con la fecha del 31-ago |
| 2ª | 6 | Corrió completa **aunque el evento ya estaba en Fase 02** |

**La segunda es la prueba concluyente.** La rama de emparejamiento por correo exige
`Fase = "01 Contacto"`, condición que ya era falsa. La única vía posible para que el
`Search Records` devolviera el registro era `RECORD_ID() = "{{1.tracking.utm_content}}"`.

**Consecuencia:** D-002 queda confirmado. `E-2` empareja por ID de registro, que es robusto ante
correos distintos, invitaciones reenviadas y contactos duplicados. La rama por correo se conserva
como respaldo para quien agende desde una liga sin el parámetro.

**Técnica que vale la pena recordar:** `scenarios_replay` re-ejecuta con los datos originales del
trigger. Sirve para probar correcciones de escenarios con webhook **sin pedirle al usuario que
repita la acción**, y permite diseñar pruebas discriminantes cambiando el estado de los datos entre
reproducciones — que es exactamente como se aisló aquí cuál de las dos ramas emparejó.

### Estado final del Sprint 3 — 7 de 7

| Tarea | Estado |
|---|---|
| S3-1 Tipo de evento de Empresas | ✅ |
| S3-2 Probar el `utm_content` | ✅ **confirmado** |
| S3-3 Escenario `E-2` | ✅ activo (6093786) |
| S3-4 Correos de confirmación | ✅ |
| S3-5 Escenario diario `E-3` | ✅ activo (6093624), ambas rutas probadas |
| S3-6 Pruebas del Bloque 2 | ✅ |
| S3-7 Campos de match en Airtable | ✅ |

Registros de prueba eliminados. **Tres escenarios de Empresas en producción: `E-1`, `E-2`, `E-3`.**

---
## 2026-09-02 · Sprint 4 arrancado — 8 campos creados y spec del formulario

### 8 campos nuevos en Eventos, sin los cuales `E-4` y `E-5` no se podían construir

| Campo | Tipo | Para qué |
|---|---|---|
| `Cotizacion Doc ID` | texto | **Candado de idempotencia** de `E-4` |
| `Cotización (Doc editable)` | url | Liga al Doc |
| `Convenio Empresa Doc ID` | texto | Idem, para el convenio |
| `Convenio Empresa (Doc editable)` | url | Liga al Doc |
| `Fecha de cotizacion enviada` | fecha | **Candado contra doble envío** de `E-5` |
| `Cotizacion en proceso de envio` | casilla | Candado temporal, patrón de `A-5` |
| `Cotización PDF` | url | Adjunto del correo |
| `Convenio Empresa PDF` | url | Adjunto del correo |

**Convención de nombres aplicada:** los campos que Make **evalúa en filtros** van sin acentos
(`Cotizacion Doc ID`, `Fecha de cotizacion enviada`, `Cotizacion en proceso de envio`); los que Make
solo **escribe por ID de campo** conservan su acentuación correcta. Sale de D-017.

**Nota de nomenclatura:** el convenio de Empresas se llama `Convenio Empresa …` a propósito, para no
confundirlo con `Convenio Doc ID` de Organizaciones, que es el de Asociaciones y tiene otro ciclo.

### El formulario escribe en DOS tablas

Hallazgo al revisar el esquema: los datos fiscales (`RFC`, `Razón social`, `Uso CFDI`) viven en
**Organizaciones**, no en Eventos. `E-4` tiene que resolver el vínculo Evento → Organización antes
de escribirlos. El plan original no lo contemplaba.

### Especificación lista

`Documentos/spec-formulario-interno-empresas.md` — 12 preguntas en 4 secciones, cada una con su tipo,
sus opciones exactas y su campo destino. La captura en Fillout queda mecánica.

**Corrección al plan:** el plan pedía capturar seis variables de match. Son **cuatro**, y dos de
ellas —municipio y número de voluntarios— ya se capturan como datos del evento. No hay preguntas
duplicadas.

### 🔴 Pendiente que bloquea el diseño de `E-4`

**¿El "coordinador" es la misma persona que registró la empresa en el sitio?** El flujo manda la
cotización "al coordinador", pero el único contacto que existe hoy es quien llenó el registro. Si
pueden ser distintos, hacen falta dos preguntas más en el formulario y un campo en Airtable.

---
### D-022 · Son dos coordinadores distintos; el de la empresa va en el Evento

**Resuelto por Ethan el 2 de septiembre.**

| Coordinador | Quién es | Dónde vive |
|---|---|---|
| **De EZER** | Quien acompaña el evento; cambia de un evento a otro | Ya planeado como tarea del Sprint 13 |
| **De la empresa** | Se captura en el formulario interno. **No necesariamente quien se registró en el sitio** | Tres campos nuevos en Eventos |

Campos creados: `Coordinador empresa`, `Correo coordinador` (sin acentos, Make lo evalúa antes de
enviar) y `Teléfono del coordinador`.

**A este correo se manda la cotización** y, más adelante, los pasos a seguir tras confirmar el pago.
Si va vacío, `E-5` cae al correo del contacto que se registró.

### Por qué el coordinador NO se guarda como un Contacto más

Se consideró crearlo como registro en **Contactos** ligado a la Organización — más normalizado y
reusa el patrón de deduplicación de `E-1`. **Se descartó por un efecto secundario grave:**

`Correo del contacto principal` de Organizaciones es un **rollup sobre todos sus contactos**. Un
segundo contacto haría que devolviera **dos correos**, y eso rompería el envío de `E-3`, que lee ese
valor a través del lookup `Correo del contacto`. Es **exactamente** el problema que hoy tiene a
`A-2.1` con 13 errores y 6 ejecuciones atoradas en Asociaciones.

**Razón adicional, y de modelo:** el coordinador es **por evento**, no por empresa. La misma empresa
puede hacer dos eventos con coordinadores distintos. Pertenece al Evento.

**Regla que queda:** mientras `Correo del contacto principal` siga siendo un rollup sin agregador,
**una organización debe tener un solo contacto**. Cualquier persona adicional se guarda como campo
del Evento, no como Contacto. Revisar esto si algún día se corrige el patrón multi-contacto.

---
### Contenido del Sprint 4 listo — prompt de Fillout y las dos plantillas

`Documentos/contenido-sprint4-formulario-y-plantillas.md` trae:

1. **Prompt para la IA de Fillout** con las 15 preguntas, sus tipos y sus opciones exactas, más los
   **3 ajustes que la IA no puede hacer**: el campo oculto `record_id`, verificar que no haya
   "mejorado" las listas de opciones, y pasar el ID del formulario.
2. **Plantilla de Cotización** con marcadores `{{...}}`.
3. **Plantilla de Convenio EZER–Empresa**, marcada explícitamente como **pendiente de revisión
   legal** y con dos cláusulas señaladas como incompletas (responsabilidad/seguro, y plazo de
   cancelación).
4. **Tabla de marcadores** con el origen exacto de cada valor.

### 🔴 D-023 · El plan nunca definió de dónde sale el precio de la cotización

Al redactar la plantilla salió a la luz: la tabla `Cotizaciones` tiene `Monto`, `Folio` y `Vigencia`,
pero **nada en el flujo los llena**. El formulario no pregunta el precio y `E-4` no crea el registro
de Cotización. La cotización saldría en blanco y la cláusula CUARTA del convenio también.

Tres opciones planteadas: capturarlo en el formulario (A), calcularlo desde un tabulador en `Config`
(B), o llenarlo a mano en el Doc (C).

**Recomendación: A.** Supone lo mínimo sobre cómo cobran hoy, deja el monto en Airtable para poder
reportarlo, y llena la cláusula del convenio sin trabajo extra. Migrar de A a B después es fácil; al
revés no.

**Bloquea:** cerrar la plantilla de Cotización y, por tanto, la construcción de `E-4`.

**Relacionado:** el `{{folio}}` tampoco está definido. Se propone un autonumber con prefijo
(`COT-2026-0001`), mismo criterio que el número de referencia del Sprint 6.

---
### D-023 RESUELTO · Monto fijo de $20,000, editable

**Decisión de Ethan el 2 de septiembre.** Implementado en **tres capas**, para que nunca salga una
cotización en blanco y a la vez el precio sea flexible:

1. **Precargado en el formulario** — la pregunta 13 llega con `20000` puesto; el equipo lo cambia al
   capturar si ese evento se cotizó distinto.
2. **Respaldo en `E-4`** — si llega vacío de todas formas, el escenario lo pone en 20000.
3. **Editable en Airtable** — el campo `Monto` se puede corregir a mano antes de enviar la
   cotización, sin volver a llenar el formulario.

| Campo creado | Tipo | Detalle |
|---|---|---|
| `Monto` | Moneda MXN, 2 decimales | Predeterminado $20,000 |
| `Folio cotizacion` | Fórmula | `COT-AAAAMMDD-XXXX`, p. ej. `COT-20260902-S2H7` |

El folio se compone de la fecha de creación del evento más los últimos 4 caracteres de su ID: único,
ordenable por fecha y rastreable al registro. **Si se prefiere un consecutivo real**
(`COT-2026-0001`), Airtable tiene el tipo *Autonumber*, pero **solo se puede crear desde la interfaz**
— la API no lo expone. Son dos clics cuando se quiera.

El formulario pasa de 15 a **16 preguntas**.

### 🟡 Observación abierta: la tabla `Cotizaciones` quedó redundante

Existe desde antes, con `Folio`, `Monto`, `Vigencia`, `PDF` y `Link Convenio`, ligada a Eventos.
**Nada del flujo la llena**, y con `Monto` y `Folio cotizacion` ahora en Eventos, su información
está duplicada.

**La única razón para conservarla** sería que una empresa pudiera tener **varias cotizaciones para
el mismo evento** — una con curso y otra sin él, o una revisión de precio con histórico. Si eso pasa
en la práctica, la tabla tiene sentido y habría que usarla en serio; si no, sobra.

**No decidir esto solo:** preguntar al equipo antes de borrar nada. No bloquea el Sprint 4.

---
## 2026-09-02 · `E-4` construido — escenario 6134761, activo

Webhook Fillout **2766332** sobre el formulario `tPVivQZG99us`. Plantillas y carpeta ya conectadas.

```
fillout:newSubmission
→ airtable:ActionSearchRecords (Eventos, RECORD_ID() = record_id)
→ builtin:BasicRouter
   ├─ Ruta 1 · "record_id valido"
   │    → Update Eventos (13 campos + Fase 03 + Monto)
   │    → Update Organizaciones (RFC, Razón social, Uso CFDI)   ← filtro: tiene organización ligada
   │    → [filtro: sin cotización aún]  createAFolder (carpeta del evento)
   │    → createADocumentFromTemplate  (Cotización)
   │    → createADocumentFromTemplate  (Convenio)
   │    → Update Eventos (Doc IDs, ligas y ID de carpeta)
   └─ Ruta 2 · "record_id invalido" → correo de alerta al equipo
```

### D-024 · Los marcadores `{{...}}` chocan con la sintaxis de Make

`{{ }}` es el delimitador de expresiones de Make, así que **no puede buscar `{{razon_social}}` como
texto literal** dentro del Doc. `A-4` ya lo había resuelto usando palabras con guión bajo
(`Nombre_Asociacion`); se adopta la misma convención.

Lista de reemplazo en `Documentos/marcadores-plantillas-docs.md`. **Hasta que se apliquen, `E-4`
genera los documentos con los marcadores sin sustituir.**

**Dos reglas para marcadores nuevos:** que no aparezcan como palabra normal en la prosa (por eso el
guión bajo), y que se escriban de corrido sin formato mixto — si Docs parte la palabra en fragmentos
con distinto formato, el reemplazo de la API no la encuentra.

### D-017 refinado · el problema de los acentos es más acotado de lo que se pensó

`A-3` usa `{{4.`Organización`}}` en un filtro y **funciona**. Lo que falla es el acento **dentro de
la cadena de fórmula de Airtable** que se manda por Make, no las referencias `{{}}` a la salida de
un módulo.

Enunciado corregido: **las fórmulas de Airtable que se pasan por Make deben ser ASCII puro.** Las
referencias `{{módulo.`Campo Acentuado`}}` sí resuelven bien. `E-4` usa
`{{2.`Organización`[1]}}` con confianza sobre esta base.

### Detalles del build

- **Validación defensiva:** la búsqueda por `RECORD_ID()` va antes de tocar nada, y el Router manda
  a un correo de alerta si el ID no existe — con **los datos capturados dentro del correo**, para no
  perder lo que el equipo ya escribió.
- **Candado de idempotencia:** carpeta y documentos van detrás del filtro `Cotizacion Doc ID` vacío.
  Reenviar el formulario actualiza los datos **sin duplicar archivos**.
- **`record_id` con respaldo:** `ifempty(urlParams.record_id; answers.2hFN)` — sirve tanto si Fillout
  lo entrega como parámetro de URL como si lo entrega desde el campo oculto.
- **Datos fiscales:** el filtro "tiene organización ligada" evita que truene un evento huérfano; su
  handler es `Resume`, no `Break`, porque no poder guardar el RFC no debe frenar la generación de
  documentos.

### 🟡 Pendientes de este build

1. **Falta la pregunta del monto en el formulario.** Se armó con la versión de 15 preguntas. `E-4`
   pone $20,000 por defecto y se edita en Airtable. Si se agrega la pregunta, es remapear un campo.
2. **La carpeta padre `1dmaUIEttm6HAOOXpaoUcNHuFIJ8QtsAk`** se asumió como raíz de eventos. Si es la
   carpeta de Plantillas, las carpetas de evento quedarían anidadas donde no va.

---
### ✅ DESCARTADO · Bug de llaves sobrantes en Asociaciones — no existe

**Detectado el 2 de septiembre**, al aclarar la convención de marcadores con Ethan.

Ethan indica que las plantillas de Asociaciones usan `{{Nombre_Asociacion}}`, con dobles llaves. Pero
el blueprint de `A-4` tiene guardado en su buscar-y-reemplazar:

```json
{"text": "Nombre_Asociacion", "replaceText": "{{4.answers.k3ee9MKrBtfgufXGpwsyRD}}"}
```

**Sin llaves** — y no puede ser de otra forma, porque `{{ }}` es el delimitador de expresiones de
Make y no admite llaves literales en ese campo.

**Consecuencia:** `replaceAllText` de Google Docs sustituye **por subcadena**. Encuentra
`Nombre_Asociacion` dentro de `{{Nombre_Asociacion}}`, lo reemplaza, y **deja las llaves**. El
convenio generado diría `{{Fundación Vida Nueva Monterrey A.C.}}`.

**VERIFICADO POR ETHAN EL MISMO DÍA: los convenios de Asociaciones salen limpios, sin llaves.**
La hipótesis era incorrecta y queda descartada. Las plantillas de Asociaciones ya usan los
marcadores en el formato que Make espera.

**Lo que sí queda en pie:** las dos plantillas nuevas de Empresas se pegaron con marcadores
`{{minusculas}}`, porque así venían en el borrador que se entregó. Esos sí hay que cambiarlos a
palabras con guión bajo — la lista está en `Documentos/marcadores-plantillas-docs.md`.

**Lección que sí vale:** el módulo de Google Docs busca **por subcadena**, así que un marcador debe
ser una cadena que no aparezca en la prosa del documento. El guión bajo lo garantiza.

---
## 2026-09-02 · Re-baseline por velocidad medida — entrega al 8 de noviembre de 2026

### La medición

| | |
|---|---|
| Trabajo del plan completado | **28.5 h** (S0 parcial, S2, S3, y ~5 h de S4) |
| Horas reales invertidas | **~9 h** en 4 sesiones (28-ago, 29-ago, 30-ago, 2-sep) |
| **Velocidad medida** | **≈ 3.2× lo estimado** |

### Por qué el resto NO se acelera igual

Proyectar 3.2× sobre todo lo que falta sería deshonesto: la velocidad se midió justamente en la
parte donde la skill de Make más ayuda. Se aplicaron factores por tipo de trabajo:

| Tipo | Plan | Factor | Real | Razón |
|---|---|---|---|---|
| Automatización | 37.25 h | 0.30 | 11.2 h | Es donde la skill construye por API |
| Configuración | 27.25 h | 0.70 | 19.1 h | Fillout, Calendly, Docs: clics humanos |
| Pruebas | 23.50 h | 0.55 | 12.9 h | El `replay` ayuda, verificar sigue siendo humano |
| Diseño | 7.50 h | 1.00 | 7.5 h | Criterio humano, no baja |
| Otros | 6.75 h | — | 3.1 h | |
| **Total** | **102.25 h** | | **≈ 54 h reales** | |

A 7 h/semana: **≈ 7.7 semanas**.

### Calendario nuevo — sprints de 1 semana

La cadencia pasa de 2 semanas a **1 semana por sprint**, que es lo que la velocidad real sostiene.

| | Antes | Ahora |
|---|---|---|
| Sprints restantes | 11 de 2 semanas | 10 de 1 semana |
| **Entrega** | **14 de marzo de 2027** | **8 de noviembre de 2026** |

**Cuatro meses de adelanto.** Los pendientes de Asociaciones (`S1`) pasan a ser un track paralelo de
baja prioridad, no un sprint que bloquee: 5 de sus 6 tareas ya existían de julio o están limitadas
por D-011.

### Lo que puede echar atrás esta fecha

1. **El motor de match (S8) es trabajo novedoso**, no copiar un patrón. Es donde la skill ayuda
   menos y donde la estimación es más incierta. **Conviene re-medir después del Sprint 8** antes de
   comprometer la fecha con Dirección.
2. **Las decisiones de contenido pasaron a ser el cuello de botella**, no la programación. Por eso
   se armó el documento de delegación a Ana Cristina.
3. **La revisión legal** sigue pendiente desde julio y ahora bloquea noviembre en vez de marzo.

### Documento de delegación a Ana Cristina

`Documentos/delegacion-ana-cristina.html` — 12 decisiones agrupadas en documentos, correos, reglas
de negocio y contenido nuevo. Cada una con **fecha límite y qué bloquea si no llega**.

La más crítica: **los pesos de las cuatro variables del motor de match**, antes del 4 de octubre. No
requiere una fórmula, basta con ordenarlas por importancia y decir cuál es inaceptable si no coincide.

---

---

### D-025 · `E-2` se apagaba entero cuando la cita no tenía expediente

**2 de septiembre de 2026.** Una reserva de Calendly tumbó el escenario completo. Fueron dos fallas
encadenadas, y las dos son de diseño, no de datos.

**La primera:** `E-2` buscaba el evento en Airtable y pasaba directo a actualizarlo **sin verificar
que lo hubiera encontrado**. Cuando la búsqueda no devuelve nada, el módulo de actualización recibe
un ID vacío y Airtable contesta `[422] parameter validation failed`.

**La segunda, más grave:** el escenario tenía apagado *"permitir ejecuciones incompletas"*
(`dlq: false`) mientras sus manejadores de error usaban `Break` con reintento. `Break` **necesita**
poder guardar la ejecución incompleta para reintentarla después. Al no poder, Make no reintentó:
**desactivó el escenario**. Un solo error de un solo registro apagó la automatización entera.

**Cómo quedó:** un `BasicRouter` después de la búsqueda, con dos rutas —
- *Sí existe el expediente* → actualiza a Fase 02, confirma a la empresa, avisa al equipo.
- *No existe* → **no toca Airtable** y manda un correo interno con quién agendó, cuándo, y qué hacer
  a mano. La cita no se pierde: se convierte en un aviso.

Más `dlq: true` en la configuración del escenario.

> **Regla que sale de aquí, aplicable a todos los escenarios:** después de cualquier búsqueda que
> alimente a un módulo de escritura, va un filtro que verifique que hubo resultado. Y **todo
> escenario que use `Break` necesita `dlq: true`**, si no el manejador de error hace lo contrario de
> lo que se espera de él.

**Pendiente:** `E-1`, `E-3` y `E-4` tienen el mismo `dlq: false`. Se prende desde la configuración
del escenario en la interfaz de Make — no se hizo por API para no reescribir blueprints grandes.

---

### D-026 · PENDIENTE DE DECISIÓN · `E-1` descarta en silencio todo lo que no sea "Registro"

**2 de septiembre de 2026.** Descubierto al investigar D-025.

`api/interest.js` línea 65 decide el tipo así:

```js
const isRegistration = eventName.startsWith('Registro');
```

y manda al webhook `tipo: "registro_empresa"` solo en ese caso. Todo lo demás llega como
`tipo: "evento"`. El módulo 2 de `E-1` filtra por `text:equal "registro_empresa"`, así que **una
empresa que se interesa en un evento del catálogo, o que pide un evento en su municipio, entra al
webhook y muere ahí**: sin expediente, sin aviso, y con la ejecución marcada como exitosa.

Es la misma clase de falla que el webhook huérfano de julio, y **está viva en producción**.

**La decisión que falta** — cuál de las dos:
1. Abrir expediente igual que un registro, guardando en el Evento qué evento del catálogo pidió.
2. Solo mandar un aviso interno al equipo, sin crear expediente.

La duda de fondo es si esos formularios los usan **empresas** o también voluntarios individuales. Si
son individuales, la opción 1 llenaría Organizaciones de personas físicas marcadas como "Empresa".

**Mientras se decide:** para probar el flujo hay que usar el formulario de **Registro** del sitio.
Es el único camino que `E-1` procesa hoy.

---

### D-027 · Dónde vive el motor de match — no se mueve nada a Supabase

**2 de septiembre de 2026.** Llegó una especificación técnica externa
(*"Motor de Match Asociación ↔ Empresa"*) con un prototipo en HTML autónomo. Se revisó contra lo que
ya existe. Se acepta la lógica completa y **se rechaza la recomendación de infraestructura**.

**Lo que se acepta:** los 7 criterios, las fórmulas de score por criterio, y los pesos por defecto
(causa 25, municipio 20, voluntarios 15, mes 15, actividad 12, presupuesto 8, accesibilidad 5). El
`evalAssoc` del prototipo se porta 1:1, sin tocar la lógica.

**Lo que se rechaza:** *"mover la base de asociaciones de localStorage a Postgres/Supabase"*. Las
asociaciones ya viven en Airtable y ahí corre todo el back office — A-1 a A-6, E-1 a E-4, convenios,
seguimientos. Duplicarlas a Supabase crea **dos fuentes de verdad** que el equipo tendría que
mantener a mano en dos lados. Se desincronizan en semanas. **Airtable se queda como única fuente.**

**Dónde corre el cálculo — dos caminos, misma lógica:**
1. **Script dentro de una automatización de Airtable.** El JS del prototipo se pega casi tal cual.
   No hay endpoint, no hay pieza extra que se caiga. *Intentar este primero.* Verificar en el plan
   Free que la acción de script exista y el tope de corridas mensuales.
2. **`api/match.js` en el Vercel que ya existe**, junto a `api/interest.js`. Make le manda un JSON y
   recibe el ranking. No es una página: nadie la abre en el navegador. Lleva llave compartida en el
   header.

**No se necesita ninguna herramienta nueva.** Airtable + Make + Vercel, las tres ya pagadas.

**No se necesita apartado web.** La pantalla donde se decide es la ficha del evento en Airtable, que
el equipo ya usa. El prototipo junta cálculo y pantalla en un archivo porque así se demuestra
rápido; en producción solo se necesita el cerebro.

**El motor sugiere, no asigna.** El vínculo final lo hace una persona — asignar mal una asociación
cuesta una relación, y el sistema no sabe lo que el equipo sabe.

#### Estado real de los campos (revisado el 2 de septiembre)

De los 7 criterios, **4 ya existen en ambos lados con opciones idénticas**:

| Criterio | Organizaciones | Eventos |
|---|---|---|
| Causa / beneficiarios | `fldnXJWhefLRh1fkp` (9) | `fldyfn8hzBXwu6i7L` (9) |
| Municipio | `fld75px4qXGUE7XcQ` (51) | `fldGAset0my8u0rYp` (51) |
| # voluntarios | `fldIAzag1KNz6Anyl` rangos | `fldIwXgx6WYViJ6nU` rangos |
| Mes | `fldPjZm4HGdS2toNc` (12) | `flda6AkeaCdGIuF60` (12) |

**Faltan 3:** `cuota` y `accesibilidad` no existen de ningún lado; `actividad` existe en
Organizaciones (`fldnFZt142s15YK9P`, 5 opciones) pero **no en Eventos** ni como pregunta en Fillout.

⚠️ **Las dos escalas de voluntarios no coinciden.** Organizaciones usa `1-10 / 11-20 / 21-30 /
31-40 / 41-50 / 50+`; Eventos usa `1-5 / 6-10 / 11-15 / …`. Hay que convertir ambas a número antes
de comparar o el criterio devuelve basura en silencio.

#### El verdadero cuello de botella: la base está vacía

De 37 registros con Tipo = Asociación, ~21 son reales. **Ninguna asociación real tiene los campos
del match capturados** — solo tienen nombre. Los 4 registros con datos completos son de prueba, y
uno de ellos trae *las 9 causas, los 12 meses y las 5 actividades* marcadas, que es relleno, no
información.

**Un motor de match sobre una base vacía devuelve 0% para todas.** El código puede quedar perfecto y
no servir de nada.

**Cómo llenarla sin quemar al equipo:** un formulario de Fillout para que **la asociación capture su
propio perfil**, enviado por correo a las 21. Convierte ~5 h de llamadas en un envío, y es la misma
pieza que se necesita de todos modos para dar de alta asociaciones nuevas.

#### Esfuerzo estimado

| Bloque | Horas |
|---|---|
| Campos nuevos en Airtable + fórmulas de conversión de rangos | 1.5 |
| 3 preguntas nuevas al formulario de captura + que `E-4` las escriba | 1.0 |
| El cálculo (script de Airtable o `api/match.js`) + pesos desde Config | 2.5 |
| `E-6`: leer asociaciones → calcular → escribir top 3 + avisar | 2.5 |
| Campos de salida en Eventos (top 3 y desglose) | 0.5 |
| Pruebas con datos reales | 1.5 |
| Formulario de perfil para asociaciones + envío a las 21 | 2.0 |
| **Total** | **~11.5 h** |

La fecha no la marcan esas horas: la marca **cuánto tardan las asociaciones en responder**.

#### Efecto sobre la delegación a Ana Cristina

La especificación **ya trae los pesos por defecto**, que era la decisión marcada como más crítica del
proyecto y con fecha límite del 4 de octubre. Si Ana Cristina avala esos números, **queda desbloqueada
sin junta**. Lo único que hay que confirmarle es que el orden refleje cómo deciden hoy a mano — sobre
todo que *causa* pese más que *municipio*.

---

### D-028 · Cómo elige el equipo la asociación: correo → confirmación → candado

**2 de septiembre de 2026.** Diseño acordado, pendiente de construir junto con `E-6`.

**El recorrido.** Cuando el evento entra a *04 Conectar asociación*, el match corre solo y escribe el
top 3 en la ficha. Al responsable le llega un correo con **tres botones, uno por asociación
sugerida**, cada uno con su porcentaje. El botón lleva a un **formulario corto de Fillout** ya
precargado que solo dice *"Vas a asignar Cardiochavitos al evento de Cemex. ¿Confirmas?"*. Al
confirmar, un escenario vincula la asociación, avanza la fase y avisa al equipo.

**Por qué Fillout y no una liga directa a Airtable.** El momento en que se elige asociación casi nunca
es frente a la computadora. Un botón en el correo se responde en el momento; *"entra a Airtable"* se
pospone. Además **quien elige no ocupa un lugar de colaborador** — el plan Free trae 5 y entre Mia,
Adri, Ana Cris, Gabi y Ethan ya están los 5. Y queda registro de **quién eligió y cuándo**, que hoy
no se guarda en ningún lado.

**Por qué hay paso de confirmación y no un solo clic.** Los correos se reenvían y algunos filtros de
seguridad abren las ligas solos para revisarlas. Si el botón asignara al abrirse, tarde o temprano se
asigna una asociación que nadie eligió y nadie se entera. **Ninguna acción con consecuencia hacia
afuera puede ejecutarse por abrir un link.**

**Abrir el link no guarda nada.** Se puede abrir diez veces, cerrar y reenviar sin efecto. Lo que hay
que blindar es **enviar**.

#### El candado: gana la primera respuesta, no la última

Sin protección gana la última y nadie se entera. El daño no es el dato: al vincular una asociación
**le sale el convenio a esa asociación**. Si Mia pica Cardiochavitos y Adri pica Manos Unidas diez
minutos después, **dos organizaciones reciben el convenio del mismo evento**. Airtable se queda con
una; afuera quedan dos creyendo que les tocó. Eso no lo arregla un campo — hay que hablarle a una
para decirle que no.

**Campo sello:** `Asociacion elegida en` (fecha y hora, nombre sin acentos por D-017), más quién
eligió. Antes de escribir, el escenario lo lee:

- **Vacío** → vincula, sella fecha y persona, avanza fase, avisa.
- **Con valor** → **no toca nada** y le contesta al que llegó tarde: *"Este evento ya tenía asociación
  asignada: Cardiochavitos, elegida por Mia el 2 de septiembre a las 18:04. No se cambió nada. Si hay
  que cambiarla, háblalo con Mia primero — a Cardiochavitos ya le salió el convenio."*

Es el mismo patrón de `E-4` y `E-5`, pero aquí importa más porque del otro lado hay una asociación
real esperando.

**Envíos simultáneos:** cubierto por el **procesamiento secuencial** que ya traen todos los
escenarios. Make los forma en fila y termina uno antes de empezar el siguiente, así que el segundo no
puede leer el campo antes de que el primero lo selle. No hay ventana de carrera.

**Cambiar a propósito:** el candado no es eterno, **pero tampoco es un botón**. Alguien con acceso a
Airtable borra el sello y el evento vuelve a aceptar elección. El estorbo es intencional: obliga a que
exista la conversación con la primera asociación antes del cambio, que es algo que ninguna
automatización puede hacer.

#### Pantalla de exploración — opcional, solo si la piden

Único caso que justificaría una pantalla web: consultar sin que exista un evento (*"si llega una
empresa de 40 voluntarios en García pidiendo medio ambiente, ¿a quién tenemos?"*). Se resuelve con el
**mismo prototipo HTML** leyendo Airtable en vez de `localStorage` — no hace falta meterlo al portal
ni construir un admin. **Primero el cálculo automático dentro del flujo**, que es lo que quita
trabajo; la pantalla solo si alguien la pide de verdad.
