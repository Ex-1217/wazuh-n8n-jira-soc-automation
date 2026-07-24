# Detección y Respuesta Automatizada con Wazuh + n8n + Jira

Proyecto personal de ciberseguridad: un entorno de detección (SIEM) integrado con automatización de respuesta a incidentes y triaje asistido por IA.

Pipeline: **detección (Wazuh) → ticket (Jira) → análisis con IA → decisión (cerrar/escalar)**, sin revisión manual una por una.

Detalle técnico completo en [ARQUITECTURA.md](./ARQUITECTURA.md).
Flujos de n8n exportados en [`flows/`](./flows).

## Stack

Wazuh · n8n · Jira · LLM vía Groq · VirtualBox (Kali Linux, Windows)

---

*Proyecto personal con fines de aprendizaje y práctica en detección, automatización y respuesta a incidentes.*
