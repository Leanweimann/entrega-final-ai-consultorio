# Guion del video demo (3 minutos)

**Antes de grabar:** tené el flujo abierto en n8n, el formulario en otra pestaña, Gmail, Google Calendar y Airtable abiertos. No abras la sección de credenciales ni muestres claves o tokens. Usá un mail tuyo en el campo Email para ver los avisos.

| Tiempo | Qué mostrar | Qué decir |
|---|---|---|
| 0:00 – 0:20 | El diagrama de arquitectura (PDF) | "Es un sistema autónomo de reserva de turnos para un consultorio. Tiene tres capas: n8n como corazón, Airtable como cerebro y Gmail como voz, con Claude como motor de IA." |
| 0:20 – 1:05 | Tocá **Execute workflow**, completá y enviá el formulario con **fecha de domingo**. Mostrá cómo avanzan los nodos y el turno nuevo en Airtable (estado Pendiente) | "El trigger es el formulario. El flujo valida los datos, guarda al paciente y al turno, lee la configuración y la agenda de Google Calendar." |
| 1:05 – 1:30 | Abrí el nodo **Agente Agenda Claude** y mostrá la respuesta (tipo Alternativo, lunes, mensaje) | "El código calcula los días de atención y la IA elige el próximo horario libre. La salida es un JSON con valores permitidos." |
| 1:30 – 2:05 | Mail de aprobación en Gmail; apretá **Aprobar turno**; mostrá el evento en Google Calendar, el mail de citación y el estado en Airtable | "Acá está el HITL: el flujo se frena hasta que una persona aprueba. Recién entonces se crea el evento y se contacta al paciente." |
| 2:05 – 2:40 | Enviá el formulario con horario `10.30`. Mostrá la rama de error, la fila en Log de Errores y el mail de alerta. Si podés, mostrá también la ejecución con la falla de IA | "El camino infeliz: datos inválidos se registran y avisan al administrador. Lo mismo pasa si falla la API de la IA." |
| 2:40 – 3:00 | La tabla Configuración y el cuadro de costos del PDF | "No hay datos fijos: teléfono, horarios y días de atención salen de Airtable. Uso un modelo de razonamiento solo donde hace falta y limito los tokens para controlar el costo." |

**Consejos:** ensayá una vez sin grabar, porque el mail de aprobación tarda unos segundos. Si algo demora, cortá y volvé a empezar solo esa parte.
