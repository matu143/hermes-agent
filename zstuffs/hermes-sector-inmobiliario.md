# Hermes Agent en el sector inmobiliario

Documento de referencia: casos de uso, sugerencias operativas e ideas innovadoras.  
**Matiz:** Hermes es un asistente con herramientas y canales múltiples; no sustituye tasación legal, notaría, registro de la propiedad ni asesoramiento fiscal o jurídico vinculante. Las decisiones de precio, contrato y cumplimiento normativo siguen siendo humanas (y, donde corresponda, profesionales colegiados).

---

## Parte A — Sugerencias base (agencias, administración, promoción, inversión)

### A.1 Agencias y comercialización

| Idea | Qué hace Hermes | Piezas típicas |
|------|------------------|----------------|
| Primer contacto 24/7 | FAQs (zonas, tipología, visitas), calificación de leads (presupuesto, timing, financiación), proponer franjas de visita y recordatorios | Gateway (WhatsApp, Telegram, web), sesiones |
| Fichas y anuncios | A partir de notas internas o PDFs, redactar o uniformar descripciones (tono, idiomas); siempre con revisión humana antes de publicar | Tools de lectura/archivo, skills con plantillas |
| Investigación de mercado | Sintetizar comparables públicos (portales, precios orientativos) sin saturar el contexto del agente principal | `delegate_task`, búsqueda web (si está habilitada) |
| Seguimiento post-visita | Resúmenes de conversación; memoria/sesiones para no repetir preguntas al cliente | `hermes_state` / memoria opcional |

### A.2 Administración de fincas y alquileres

| Idea | Qué hace Hermes | Piezas típicas |
|------|------------------|----------------|
| Incidencias por chat | Clasificar urgencia (fuga, calefacción), pedir fotos, generar borrador de ticket para mantenimiento o comunidad | Gateway, estructuración en JSON |
| Comunicación con inquilinos | Avisos de obra, lecturas, subida de documentación; usar aprobaciones donde haya acciones sensibles (terminal, comandos) | `approval.py`, políticas de toolsets |
| Recordatorios operativos | Renovaciones de contrato, revisiones de IPC, revisiones anuales | `cron/` |

### A.3 Promotores y obra nueva

| Idea | Qué hace Hermes | Piezas típicas |
|------|------------------|----------------|
| Documentación repetitiva | Checklists de entrega; listas de defectos a partir de texto libre → estructurar en tablas o tickets | Tools + formato estructurado |
| Atención a compradores | Estado de obra genérico, cita con comercial (sin comprometer plazos no respaldados por datos oficiales) | System prompt acotado, RAG sobre docs internos |

### A.4 Inversión y asset management (ligero)

| Idea | Qué hace Hermes | Piezas típicas |
|------|------------------|----------------|
| Screening de oportunidades | Resumir memorandums largos, extraer supuestos y riesgos para validación humana | Contexto largo, compresión de contexto |
| Datos abiertos / investigación | Complementar con fuentes citadas y revisión humana; solo donde la extracción sea lícita | Web/navegador si configurado |

### A.5 Transparencia y cumplimiento (diseño)

- **Antidiscriminación y cláusulas:** el modelo no “certifica” legalidad; sirve para borradores y checklist frente a plantillas aprobadas por abogacía.
- **Trazabilidad:** sesiones SQLite pueden apoyar auditoría interna si la política de datos y RGPD lo permiten (minimización, base jurídica, retención).

### A.6 Mapa rápido Hermes ↔ necesidad

| Necesidad | Encaje en Hermes |
|-----------|------------------|
| WhatsApp / Telegram / Slack con clientes | Gateway + adaptadores de plataforma |
| Automatizar tareas recurrentes | `cron/` |
| Muchas conversaciones y contexto | Sesiones + memoria opcional |
| Tareas pesadas en paralelo | `delegate_task` |
| CRM, calendario, ticketing | MCP, webhook (`gateway/platforms/webhook.py`), plugins |

---

## Parte B — Cinco ideas más innovadoras

### B.1 Gemelo conversacional del edificio (B2B comunidades)

Un agente por finca con **memoria y skills** cargados con el reglamento de la comunidad, actas recientes (siempre versionadas y aprobadas) y FAQs del administrador. Los propietarios preguntan por WhatsApp; Hermes responde solo con **citas a documento y artículo**; lo dudoso escala a administración. Innovación: **reduce fricción** sin sustituir la junta ni el administrador.

### B.2 Simulador de “primera visita” para comerciales

Sesiones de entrenamiento donde el agente **interpreta** perfiles de comprador (script) y hace preguntas incómodas típicas; el comercial practica en Telegram/CLI. Innovación: **formación continua** con feedback estructurado (sin grabar datos reales de clientes si no es necesario).

### B.3 Due diligence climática y energética (asistida)

Pipeline que combine **delegación** para leer ficheros técnicos (ITE, certificados energéticos, subsidios) y producir un **informe ejecutivo** de riesgos/oportunidades (CAPEX orientativo, plazos) para inversores. Innovación: acoplar documentación dispersa a **una narrativa de decisión**; cifras finas siempre validadas por técnico.

### B.4 Coordinación multilingüe en obra (promotora + subcontratas)

Canal Slack/Discord del obra con Hermes como **moderador operativo**: resume acuerdos diarios, extrae entregables y fechas, traduce entre equipos. Innovación: **memoria de obra** vinculada a hitos, no solo chat libre.

### B.5 Copiloto de cumplimiento publicitario (pre-publicación)

Antes de subir un anuncio a portales, el agente contrasta el texto con una **base de reglas** (metraje, certificación energética obligatoria, lenguaje prohibido en la jurisdicción) y devuelve **diff sugerido** + checklist. Innovación: tratar la publicación como **CI/CD de contenido** con trazabilidad de quién aprobó qué versión.

---

## Cierre

Hermes aporta valor inmobiliario sobre todo en: **respuesta rápida multicanal**, **ordenación de información**, **borradores y recordatorios**, **investigación paralela** y **integración** (MCP, webhooks, plugins). Las **garantías legales y económicas** permanecen en procesos y personas responsables.
