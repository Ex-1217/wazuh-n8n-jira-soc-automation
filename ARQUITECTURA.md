# Arquitectura

## Monitorización (Wazuh)

- Wazuh Manager en Kali Linux, dentro de una red interna de VirtualBox.
- Endpoints con agente Wazuh:
  - **Windows 10 + Sysmon**: inicios de sesión, procesos lanzados y conexiones de red.
  - **Ubuntu**: logs del sistema y SSH.
- **Metasploitable 2** como objetivo de práctica, sin salida a internet.
- Integración `custom-n8n` en `/var/ossec/etc/ossec.conf`: las alertas de **nivel ≥ 10** se envían por webhook a n8n con el script `/var/ossec/integrations/custom-n8n.py`, que manda la alerta completa en `all_fields`.

## Automatización (n8n) — un solo flujo

`flows/3 SOC Personal - Wazuh crea ticket en Jira.json`

1. **Webhook** (POST): recibe la alerta de Wazuh.
2. **If**: segundo filtro por nivel (`rule.level >= 5`).
3. **Create an issue** (Jira): ticket con resumen = título de la alerta y una descripción legible: qué ha pasado, gravedad (Baja / Media / Alta / Crítica), equipo e IP, regla, fecha y un fragmento del log.
4. **Explicar alerta (IA)**: agente con rol de analista SOC nivel 1 (Groq, `openai/gpt-oss-120b`). Responde en cuatro partes: QUÉ HA PASADO, DATOS CLAVE (incluida la técnica MITRE), CAUSA PROBABLE y RECOMENDACIÓN (CERRAR o ESCALAR y qué comprobar). Si un dato no está en la alerta, lo dice; no inventa.
5. **Comentar en Jira**: añade el análisis como comentario del ticket.

Las credenciales (Jira y Groq) están en el gestor de credenciales de n8n; el JSON publicado solo lleva sus nombres.

## Limitaciones conocidas

- El webhook no tiene autenticación ni validación de firma; solo es accesible desde la red interna. En el JSON publicado la ruta real se ha sustituido por `TU-RUTA-DEL-WEBHOOK`.
- El SIEM está en la misma máquina que se usa para atacar, por falta de recursos del PC. En un entorno real estaría separado.
- Los proveedores de IA retiran modelos con el tiempo: hay que revisar el modelo periódicamente.

## Pila

Wazuh 4.14 · Sysmon · n8n (Docker) · Jira Cloud · Groq · VirtualBox (Kali Linux, Windows 10, Ubuntu, Metasploitable 2)
