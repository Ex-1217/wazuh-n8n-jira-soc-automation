# Detección y Respuesta Automatizada con Wazuh + n8n + Jira

Proyecto personal de ciberseguridad: un entorno de detección (SIEM) integrado con automatización de respuesta a incidentes y triaje asistido por IA.

## Arquitectura

**Monitorización (Wazuh)**
- Wazuh Manager como servidor central.
- Dos máquinas virtuales monitorizadas (VirtualBox):
  - Kali Linux — agente Wazuh: FIM (integridad de archivos), rootcheck, logs del sistema.
  - Windows — agente Wazuh: logs del Visor de Eventos, cambios de configuración, servicios.
- Cuando una alerta supera cierto nivel de severidad, se dispara una notificación vía webhook.

**Automatización de respuesta (n8n)**
- **Wazuh → Jira**: recibe la alerta por webhook y crea automáticamente un ticket en Jira con toda la información (agente, regla, nivel, descripción). Verificado end-to-end (tickets creados automáticamente a partir de alertas simuladas y reales).
- **Agente SOC (IA)**: toma cada ticket, lo evalúa con un LLM (rol de analista SOC nivel 1) que determina tipo de incidente, severidad, próximos pasos y si requiere escalado a nivel 2. El veredicto se añade como comentario en el propio ticket de Jira.

**Gestión de incidentes (Jira)**
- Cada alerta queda registrada, documentada y con un primer análisis automático, sin revisión manual una por una.

## Resultado

Un pipeline completo detección → ticket → análisis IA → decisión (cerrar/escalar), reduciendo el trabajo manual de un analista SOC nivel 1.

## Limitaciones conocidas

- El webhook de recepción de alertas no tiene autenticación ni validación de firma; solo es accesible desde la red local. Pendiente de asegurar antes de cualquier exposición externa.
- El modelo de lenguaje usado en el análisis SOC requiere revisión periódica (los proveedores retiran modelos con el tiempo).
- Con volúmenes altos de tickets, el análisis por lotes requiere varias ejecuciones (limitación de tokens del proveedor de IA).

## Stack

Wazuh · n8n · Jira · LLM (análisis de tickets) · VirtualBox (Kali Linux, Windows)

---

*Proyecto personal con fines de aprendizaje y práctica en detección, automatización y respuesta a incidentes.*
