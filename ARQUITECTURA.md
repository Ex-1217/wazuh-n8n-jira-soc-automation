# Arquitectura

## Monitorización (Wazuh)

- Wazuh Manager como servidor central.
- Dos máquinas virtuales monitorizadas (VirtualBox):
  - **Kali Linux** — agente Wazuh: FIM (integridad de archivos), rootcheck, logs del sistema.
  - **Windows** — agente Wazuh: logs del Visor de Eventos, cambios de configuración, servicios.
- Cuando una alerta supera cierto nivel de severidad, se dispara una notificación vía webhook hacia n8n.

## Automatización de respuesta (n8n)

Tres flujos, exportados en [`flows/`](./flows):

### 1. Wazuh → Jira (`flows/wazuh-to-jira.json`)

Punto de entrada. Recibe la alerta de Wazuh por webhook (POST), filtra por severidad (`rule.level >= 3`) con un nodo **If**, y si la supera, crea automáticamente un ticket en Jira (tipo Historia) con el resumen y la descripción de la alerta: agente origen, regla disparada y un fragmento del log.

### 2. Agente SOC - Personal (`flows/agente-soc-personal.json`)

Se ejecuta semanalmente (lunes 9:00). Recupera hasta 5 tickets del proyecto Jira, y por cada uno los pasa a un agente de IA (Groq, `llama-3.3-70b-versatile`) con rol de analista SOC nivel 1, que identifica el tipo de incidente, evalúa la severidad, propone próximos pasos y determina si requiere escalado a nivel 2. En esta versión el análisis no se escribe de vuelta en Jira.

### 3. Análisis IA - Alertas Wazuh (`flows/analisis-ia-alertas-wazuh.json`)

Variante que cierra el ciclo: se ejecuta cada 10 minutos, recupera tickets del mismo proyecto, y el agente de IA (Groq, `llama-3.1-8b-instant`) da un veredicto (CERRAR o ESCALAR) con justificación breve. Ese veredicto se añade automáticamente como **comentario en el propio ticket de Jira**.

## Gestión de incidentes (Jira)

Cada alerta queda registrada, documentada y con un primer análisis automático, sin revisión manual una por una.

## Limitaciones conocidas

- El webhook de recepción de alertas no tiene autenticación ni validación de firma; solo es accesible desde la red local. Pendiente de asegurar antes de cualquier exposición externa. (El UUID real del webhook se ha eliminado de los JSON publicados.)
- El modelo de lenguaje usado en el análisis SOC requiere revisión periódica (los proveedores retiran modelos con el tiempo).
- Con volúmenes altos de tickets, el análisis por lotes requiere varias ejecuciones (limitación de tokens del proveedor de IA).

## Stack

Wazuh · n8n · Jira · LLM vía Groq (análisis de tickets) · VirtualBox (Kali Linux, Windows)
