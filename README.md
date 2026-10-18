# Detección y respuesta automatizada con Wazuh + n8n + Jira

Proyecto personal de ciberseguridad: un pequeño SOC en máquinas virtuales que detecta ataques con un SIEM, abre un ticket en Jira y usa una IA para explicar la alerta y recomendar si cerrarla o escalarla. **La IA solo recomienda: la decisión es del analista.**

Circuito: **detección (Wazuh) → ticket (Jira) → análisis con IA (comentario en el ticket) → decisión del analista**.

**Prueba real:** 9 inicios de sesión fallidos en Windows → alerta 60204 (MITRE ATT&CK T1110, fuerza bruta) → ticket en Jira con el análisis de la IA como comentario.

Detalle técnico en [ARQUITECTURA.md](./ARQUITECTURA.md). Flujo de n8n exportado en [`flows/`](./flows) (sin claves ni la ruta real del webhook).

## Stack

Wazuh 4.14 · Sysmon · n8n (Docker) · Jira Cloud · Groq (openai/gpt-oss-120b) · VirtualBox (Kali Linux, Windows 10, Ubuntu, Metasploitable 2)

---

*Proyecto personal con fines de aprendizaje y práctica en detección, automatización y respuesta a incidentes.*
