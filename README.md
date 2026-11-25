# UX Findings · Agente Integrador (Checkpoint 4 · Módulo 4)
_Una línea: asistente multi-agente que atiende el buzón de consultas sobre findings de user testing, con CRM, borradores con revisión humana y avisos al equipo._

## 1. Qué hace
[Qué resuelve: buzón de equipo de producto (no e-commerce). Recibe consultas/solicitudes
sobre findings, responde desde la planilla, deja borrador para aprobación y avisa por Slack.]

## 2. Por qué sobre el integrador (y no un mini flujo)
[Decisión: el profe permitía un mini flujo nuevo; seguimos sobre el flujo del M3 para
tener un proyecto integrador que crece módulo a módulo.]

## 3. Arquitectura
[Manager (Triage) + W1 (Consultar findings) + W2 (Solicitud de cambio). Memoria en Sheets.
Diagrama de bloques. Nota: el diagrama de la consigna es monolítico; el nuestro es multi-agente.]

## 4. Los 4 guardrails que evalúa la rúbrica
[El corazón del entregable. Para cada uno: qué, dónde en el flujo, por qué.]
### ① Barrera anti auto-reply + spam
[IF determinista (auto-reply/out of office/undeliverable/no-reply@) + clasificador LLM de spam.
Descartados = no se hace nada, sin HITL. Corta el bucle infinito.]
### ② Look up antes de crear en el CRM
[Buscar contacto por email → IF existe → Actualizar / Crear. Evita el 409 (duplicados).]
### ③ Create Draft (Human-in-the-loop)
[Gmail Draft en vez de Reply. Nada se envía sin que un humano apruebe. Subject + Thread ID.]
### ④ Set de limpieza de payload antes de Slack
[El Set "Auditoría" (campos planos, sin binarios) alimenta el aviso a Slack. Evita el
"pasillo de la muerte" y el 400.]

## 5. El CRM como directorio del equipo
[Hilo conductor: importa el rol, no la marca. El CRM no es de ventas; es la ficha de quien
escribe (dev/QA/PO/PM/diseño). Convivencia Sheets (memoria) + HubSpot (ficha).]

## 6. Las 3 integraciones (OAuth2 + mínimo privilegio)
[HubSpot (CRM) + Gmail (correo) + Slack (canal). OAuth2, semáforo en verde. Cada nodo mapea
solo los campos necesarios. El .json no lleva secretos, solo la referencia a la credencial;
por eso se adjunta la captura.]
[CAPTURA 1: sección Credentials de n8n con las 3 conexiones activas]

## 7. Decisiones técnicas y errores resueltos
[Las "historias de guerra" que muestran criterio.]
- **GDPR / hs_legal_basis** [el portal EU exige base legal al crear contactos → `Legitimate interest - other`]
- **Clasificador de spam que devolvía vacío** [Haiku gastaba tokens en razonamiento → prompt corto + On Error Continue]
- **Fechas en HubSpot (INVALID_DATE)** [date exige medianoche UTC exacta → `toUTC().startOf('day').toMillis()`]
- **Limpieza de firma y texto citado** [corte en Normalizar email para que la consulta no arrastre la firma]
- **Convivencia Sheets + HubSpot** [por qué conviven y no se reemplazan]
[CAPTURA 2 (opcional): ejecución completa en verde, o el borrador en el hilo de Gmail]
[CAPTURA 3 (opcional): mensaje posteado en #contactos-ux-findings]

## 8. Cómo importar y probar
[Los 3 archivos. Pasos: importar los 3, re-vincular los nodos Call W1/W2 al importar
(n8n asigna IDs nuevos), asignar las credenciales propias, Execute step hasta ver verde.]

## 9. Contenido del repo
[Lista de archivos: checkpoint4_agustin_pasztetnik.json (Manager) + los 2 workers.]

