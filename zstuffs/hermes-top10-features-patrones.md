# Hermes Agent — Top 10 de features, patrones y lo más distintivo

Documento de referencia: qué separa este proyecto de “otro wrapper de OpenAI”.  
Orden aproximado por **impacto distintivo** (identidad técnica y palanca de producto).

**Nota sobre “silver bullet”:** en software rara vez existe una sola bala de plata. Aquí el mayor multiplicador suele ser la **combinación** del ítem **1** con el **2** (núcleo unificado + delegación seria). El resto son palancas que convierten el repo en **plataforma**.

---

## Top 10

### 1. Un solo núcleo de agente, muchas puertas (piedra angular)

Misma lógica `AIAgent` + herramientas + sesiones desde:

- CLI clásica y **TUI** (Ink + `tui_gateway`)
- **Gateway** de mensajería (Telegram, Discord, Slack, WhatsApp, etc.)
- **ACP** (editores)
- **Dashboard** (TUI embebida por PTY)
- **Batch** / otros runners

**Por qué importa:** menos duplicación y menos *drift* entre superficies; un comportamiento corregido beneficia a todos los canales que comparten el mismo stack.

---

### 2. Delegación de primer nivel (`delegate_task`)

Patrón documentado y central en `tools/delegate_tool.py`:

- Subagentes con **contexto limpio** (sin historial del padre)
- **Toolsets** acotados y herramientas bloqueadas por política
- Modo **batch paralelo** y roles **orchestrator** / **leaf** con profundidad acotada
- Solo el **resumen final** vuelve al padre

**Por qué importa:** evita saturar el contexto del agente principal y permite investigación o subtareas pesadas en paralelo; es de lo más “agentico” y escalable del proyecto.

---

### 3. Perfiles con `HERMES_HOME` aislado

Varias instancias completas (configuración, claves, sesiones, skills, logs) en la misma máquina, vía `get_hermes_home()` / `display_hermes_home()`.

**Por qué importa:** multi-cliente, entornos separados (dev/prod), demos y tests sin contaminar el hogar por defecto; regla explícita en la guía de desarrollo de no hardcodear `~/.hermes`.

---

### 4. Registro central de slash commands (`COMMAND_REGISTRY`)

En `hermes_cli/commands.py`: una lista de `CommandDef` de la que derivan CLI, gateway, help, menús de Telegram, mapeo Slack, autocomplete, etc.

**Por qué importa:** un alias o comando nuevo se propaga de forma coherente; patrón poco habitual y muy mantenible.

---

### 5. Cadena de herramientas auto-descubiertas

- `tools/registry.py` — registro al importar
- `model_tools.py` — descubrimiento y `handle_function_call()`
- `toolsets.py` — qué conjuntos están habilitados por política o plataforma

**Por qué importa:** no hace falta mantener listas manuales gigantes de cada tool; el árbol de `tools/*.py` escala con el equipo.

---

### 6. Humano en el bucle “de verdad” (aprobaciones + gateway)

- Detección de comandos peligrosos del terminal, colas por sesión, bloqueo hasta aprobación explícita donde aplica
- Modos **cron**, **yolo** acotado, distinción CLI vs gateway vs no interactivo

**Por qué importa:** encaja con uso serio en producción, no solo demos donde el agente ejecuta cualquier cosa sin fricción.

---

### 7. Compresión de contexto consciente de coste y de caché

`agent/context_compressor.py` (poda, cabeza/cola protegidas, resumen del medio) integrada en el bucle de `run_agent.py`, con continuación de sesión cuando corresponde.

La guía del proyecto enfatiza **no romper el prompt cache** (por ejemplo: contexto aportado por plugins en el mensaje de usuario, no mutar el system prompt a mitad de conversación salvo compresión gobernada).

**Por qué importa:** conversaciones largas + tool calling sin reventar ventana ni coste de forma ingenua.

---

### 8. Plugins y memoria desacoplados del núcleo

- Plugins generales: hooks sin meter lógica de terceros en `run_agent.py` / `cli.py` / `gateway/run.py` (política explícita en `AGENTS.md`)
- Proveedores de memoria plugables bajo `plugins/memory/`

**Por qué importa:** el ecosistema crece sin fork permanente del motor ni PRs monolíticos por cada integración.

---

### 9. Checkpoints de filesystem con git sombra

`tools/checkpoint_manager.py`: snapshots antes de operaciones que mutan archivos, usando un repositorio git **sombrero** bajo el perfil de Hermes, sin tocar el `.git` del proyecto del usuario.

**Por qué importa:** poco habitual en agentes de código; aumenta confianza y reversibilidad ante ediciones o parches agresivos.

---

### 10. Resiliencia operativa del stack LLM

- **Credential pool** y cadenas de **fallback** de modelo en el agente principal
- **`agent/auxiliary_client.py`**: router para tareas auxiliares (compresión, visión, etc.) con cadenas de proveedor y fallback ante errores de pago (**402**) o conexión cuando el modo es `auto`

**Por qué importa:** menos dependencia de “una sola API key y un solo modelo”; comportamiento más robusto en entornos reales.

---

## Casi en el podio (honoríficos)

| Tema | Dónde / qué es |
|------|----------------|
| **Cron + entrega a plataformas** | Tareas programadas con el mismo ecosistema de mensajería (`cron/`) |
| **MCP** | Extensión vía servidores Model Context Protocol |
| **Skills + optional-skills** | Procedimientos empaquetados; lo pesado/nicho en `optional-skills/` |
| **Evaluación de horizonte largo** | P. ej. **YC-Bench** en `environments/benchmarks/yc_bench/` — simulación multi-turno, coherencia estratégica; más investigación/madurez del agente que feature de usuario casual |

---

## Cierre

Lo más cercano a un **multiplicador único** es **(1) + (2)**: mismo agente en todos lados y **delegación** para no colapsar contexto ni mezclar riesgos. El resto (perfiles, aprobaciones, compresión consciente de caché, plugins, checkpoints, resiliencia de LLM) es lo que convierte Hermes en **plataforma** en lugar de un solo script de chat.

---

*Documento generado para el folder `zstuffs/`; no forma parte de la documentación oficial del producto.*
