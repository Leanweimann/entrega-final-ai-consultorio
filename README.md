# Consultorio Medico: reserva de turnos con IA y aprobacion humana

Entrega final · Ecosistema de Automatizacion IA Autonomo para Negocios
Autor: Leandro Weimann · Octubre de 2026

Sistema autonomo que recibe un pedido de turno medico por formulario, **consulta la agenda real en Google Calendar**, usa **Claude (Anthropic)** para decidir si el horario esta libre o proponer el proximo disponible, pide **aprobacion humana (HITL)** por mail y recien entonces **contacta al paciente**. Todo queda registrado en **Airtable**.

## Enlaces obligatorios

- **Base de datos (Airtable, solo lectura):** https://airtable.com/appXJnLgFXvdLNMBf/shr7PnFcvyw0hIEeK
- **Diagrama de arquitectura (PDF):** [docs/arquitectura.pdf](docs/arquitectura.pdf)
- **Logica del flujo (JSON de n8n):** [workflow/consultorio-reserva-turnos.json](workflow/consultorio-reserva-turnos.json)
- **Evidencias (capturas):** carpeta [evidencias/](evidencias/)

## Stack

| Capa | Tecnologia | Rol |

| Corazon (logica) | n8n Cloud | Trigger, validaciones, orquestacion, rutas de error y HITL |
| Cerebro (datos) | Airtable | Pacientes, Turnos (estados), Log de Errores y Configuracion |
| Motor de IA | Claude (Anthropic) con AI Agent | Elige el horario y devuelve JSON validado |
| Voz (salida) | Gmail y Google Calendar | Aprobacion, citacion, avisos, alertas y eventos |

## Como funciona

1. El paciente completa el formulario de n8n (nombre, email, telefono, fecha, horario y motivo).
2. Se validan los datos. Si faltan o el formato es invalido, se registra el error y se envia una alerta.
3. Se guarda el paciente y el turno (estado **Pendiente**) en Airtable.
4. Se leen la configuracion y los eventos de la agenda; el codigo calcula los dias de atencion validos.
5. El agente de Claude propone el turno: **Solicitado**, **Alternativo** o **Sin Disponibilidad**.
6. **HITL:** la secretaria o el medico recibe un mail con los botones *Aprobar turno* y *Rechazar*. El flujo queda en pausa.
7. Aprobado: se crea el evento, se envia la citacion y el estado pasa a **Aprobado por Humano**.
8. Rechazado o sin disponibilidad: el paciente recibe un aviso con el telefono y el horario de atencion de la clinica.

## Requisitos de la consigna

| Requisito | Como se cumple |


| Autonomo | Se dispara con el formulario y entrega el resultado sin intervencion manual, salvo la aprobacion |
| Resiliente (2+ manejos de error) | Datos invalidos y falla de la IA: registro en Airtable y alerta por mail |
| HITL | Gmail "enviar y esperar respuesta" antes de agendar y contactar al paciente |
| Limpio | Nodos con nombres claros, variables dinamicas y configuracion en Airtable (nada hardcodeado) |
| IA | Prompt dinamico, salida estructurada con valores permitidos y Max Tokens limitado |
| Base con estados y relaciones | Pendiente, Procesado por IA, Aprobado por Humano, Rechazado, Sin Disponibilidad y Error; tablas vinculadas |

## Pruebas realizadas

| Caso | Resultado |
|---|---|
| Horario libre, aprobado | Citacion enviada y evento creado |
| Horario ocupado | La IA propone el proximo horario libre |
| Rechazo del humano | Estado Rechazado y aviso al paciente |
| Domingo | La IA propone el lunes siguiente |
| Datos incompletos | Log de Errores y alerta por mail |
| Falla de la IA | Rama de error: registro y alerta por mail |
| Sin disponibilidad | Mail con el telefono de la clinica |

Mas de diez ejecuciones en el historial de n8n. Detalle y capturas en el PDF y en `evidencias/`.

## Como reproducirlo

1. Crear en Airtable una base con las tablas **Pacientes**, **Turnos**, **Log de Errores** y **Configuracion** (campos descritos en el PDF).
2. Cargar en **Configuracion** las claves: `telefono_clinica`, `email_aprobador`, `hora_inicio_atencion`, `hora_fin_atencion`, `duracion_turno_min`, `dias_busqueda_alternativa`, `dias_atencion` (1 = lunes ... 7 = domingo) y `dias_atencion_texto`.
3. En n8n, importar `workflow/consultorio-reserva-turnos.json` y crear las credenciales: Airtable (token personal), Google Calendar, Gmail y Anthropic.
4. Reasignar en cada nodo base, tabla, calendario y credencial.

## Seguridad

Las claves se guardan en las credenciales de n8n y el JSON solo referencia sus nombres. En las capturas se ocultaron datos de contacto de terceros y claves.

## Estructura del repositorio

```
README.md
docs/arquitectura.pdf
workflow/consultorio-reserva-turnos.json
evidencias/   (capturas numeradas)
```
