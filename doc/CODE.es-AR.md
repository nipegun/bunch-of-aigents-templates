# CODE - bunch-of-aigents-templates

Este CODE también está disponible en:\
[en-GB](CODE.en-GB.md), [en-US](CODE.md), es-AR, [es-ES](CODE.es-ES.md)

Referencia técnica de este repositorio para personas y modelos de lenguaje.
Saltá a la sección que necesites; cada una se entiende sola.

## Índice

1. [Arquitectura y decisiones de diseño](#1-arquitectura-y-decisiones-de-diseño)
2. [Mapa de módulos](#2-mapa-de-módulos)
3. [Índice de símbolos clave](#3-índice-de-símbolos-clave)
4. [Flujos principales](#4-flujos-principales)
5. [Mapa de entradas y rutas](#5-mapa-de-entradas-y-rutas)
6. [Análisis de impacto](#6-análisis-de-impacto)
7. [Puntos de extensión](#7-puntos-de-extensión)

---

## 1. Arquitectura y decisiones de diseño

Este repositorio no tiene código. Son datos que lee otro programa:
[Bunch of AIgents](https://github.com/nipegun/bunch-of-aigents) (la
«aplicación»), clonado a su lado como `../bunch-of-aigents`. Cada carpeta de
primer nivel que tiene un `agent.json` es una plantilla de agente, y la
aplicación las ofrece en **+ → Importar desde una plantilla de GitHub**.

**Por qué un repositorio propio.** Las plantillas venían dentro de la
aplicación, un archivo Markdown cada una. Acá afuera, una plantilla nueva o
mejorada llega a todas las instalaciones la próxima vez que alguien abre la
lista, sin actualizar la aplicación, y una instalación puede apuntar a un fork
o a un repositorio propio (Ajustes → Agentes en la aplicación).

**Por qué una sola descarga.** La aplicación no recorre este repositorio con
la API de GitHub. Descarga el único `.tar.gz` con el que GitHub sirve una rama,
`<repositorio>/archive/refs/heads/<rama>.tar.gz` (la misma dirección de la que
su instalador descarga la aplicación), y lo guarda cinco minutos. Un
pedido, sin límite de pedidos, y la misma estructura funciona desde una
ruta `file://` en una máquina sin salida.

**Por qué el mismo formato que una exportación.** Una carpeta de plantilla es
exactamente lo que produce la pestaña **Exportar** de un agente, descomprimido:
`agent.json` y `system-prompt.md` y, opcionalmente, `memory.md`, `home/...` y
una biblioteca en `rag/`. Así la aplicación instala una plantilla e importa un
`.zip` con el mismo código, y la forma más rápida de escribir una plantilla es
armar el agente en la aplicación y exportarlo.

**Por qué tan estricto.** Una plantilla es un conjunto de permisos tanto como
un prompt: nombra las herramientas con las que llega el agente y cuándo se
despierta. La aplicación comprueba cada plantilla antes de ofrecerla y otra
vez antes de instalarla, y rechaza en vez de adivinar: una clave desconocida en
`agent.json`, un horario que no sea de cinco campos de cron, un archivo oculto
en `home/`, una ruta que sale para arriba, un enlace. Una plantilla rechazada
se sigue listando, con su error, para que quien la escribió vea por qué.

**Lo que una plantilla no puede hacer nunca.** El formato no tiene dónde poner
`enabled`: toda plantilla se instala apagada y sin modelo, salvo que nombre
uno. No tiene dónde poner claves de API, tokens, credenciales de canales ni
suscripciones a canales. Sus horarios se convierten en líneas de crontab que
arrancan al agente y nada más.

## 2. Mapa de módulos

| Módulo | Ruta | Responsabilidad | Depende de | Usado por |
|---|---|---|---|---|
| Una plantilla | `<id>/` | Un agente: su identidad, sus permisos, su horario y su prompt | - | `agent_templates` y `agent_package` de la aplicación |
| Manifiesto | `<id>/agent.json` | Nombre, descripciones, herramientas, horarios, límites, ajustes de biblioteca | El formato 1 de la aplicación | `agent_package.fValidateManifest` |
| Prompt | `<id>/system-prompt.md` | El prompt de sistema, en inglés | - | Se instala como el `system-prompt.md` del agente |
| Descripción | `<id>/README.md` | Qué hace el agente y para qué sirve, para quien lee el repositorio; en en-US | `agent.json`, `system-prompt.md` | Personas; la aplicación lo ignora |
| Contenido opcional | `<id>/memory.md`, `<id>/home/...`, `<id>/rag/...` | Lo que además puede llevar una exportación | - | `agent_io.fInstallPackage` |
| Documentación | `README.md`, `doc/` | Este repositorio explicado | - | Personas |
| Reglas | `AGENTS.md`, `CLAUDE.md`, `RulesForThisProjectOnly.md`, `Rules/` | Cómo trabajar en este repositorio | - | Personas y asistentes |

## 3. Índice de símbolos clave

Los «símbolos» de un repositorio de datos son las claves de `agent.json`. La
lista que manda es `lManifestKeys`, en `backend/core/agent_package.py:80` de la
aplicación. Los números de línea son los de la aplicación cuando se escribió
esto; si alguno ya no coincide, buscá el nombre de la función en el mismo
archivo.

| Clave | Dónde se lee | Archivo:línea en la aplicación | Qué hace |
|---|---|---|---|
| `format` | `agent_package.fValidateManifest` | `backend/core/agent_package.py:404` | Tiene que ser `1`. Un paquete de otro formato se rechaza |
| `name` | `agents.fValidateAgentName` | `backend/core/agents.py:77` | De 2 a 40 letras, dígitos, espacios, puntos, guiones y guiones bajos. Acá, el nombre de la carpeta |
| `description` | `agent_package.fValidateDescription` | `backend/core/agent_package.py:359` | Texto, o un objeto de textos por idioma (`xx-YY`). Las plantillas llevan los quince |
| `tools` | `agent_package.fValidateManifest` | `backend/core/agent_package.py:404` | Nombres de herramientas con punto. Las que falten en la instalación se quitan y se enumeran |
| `skills` | `agent_package.fValidateManifest` | `backend/core/agent_package.py:404` | Nombres de habilidades; la instalación se queda con las que tiene |
| `schedules` | `agents.fValidateScheduleList` | `backend/core/agents.py:117` | Horarios cron de cinco campos, como mucho veinte y como mucho 288 arranques al día entre todos. Vacío: trabaja cuando se le pide |
| `limits` | `agent_package.fValidateManifest` | `backend/core/agent_package.py:404` | `max_tokens_per_run`, `max_steps_per_run`, `timeout_seconds`, `max_runs_per_day`, `max_memory_characters` |
| `rag` | `rag_settings.fValidateSettings` | `backend/core/rag_settings.py:53` | Ajustes de la biblioteca; `{"enabled": true}` la activa |
| `kanban_enabled` | `agent_package.fValidateManifest` | `backend/core/agent_package.py:404` | `false` para crearlo sin el tablero |
| `provider`, `fallback_provider` | `agent_package.fValidateProviderEntry` | `backend/core/agent_package.py:386` | `name`, `model`, `base_url`. Nunca una clave. Las plantillas normalmente no los llevan |
| `exported_at` | `agent_package.fValidateManifest` | `backend/core/agent_package.py:404` | Informativo, lo escribe una exportación |

## 4. Flujos principales

Listar, en la aplicación:

```
+ → Importar desde una plantilla de GitHub
  GET /api/admin/agent-templates?language=<idioma de la interfaz>
    agent_templates.fLoadTemplates → descarga <repo>/archive/refs/heads/<rama>.tar.gz (5 min en caché)
      agent_package.fReadRepositoryArchive    una fuente por carpeta de primer nivel con agent.json
    agent_package.fReadPackage                comprueba cada una; una rechazada se lista con su error
```

Instalar:

```
POST /api/admin/agents {name, template: <id>, language}
  agent_templates.fReadTemplate(<id>)        el servidor la descarga y la comprueba otra vez
  agent_io.fInstallPackage
    create_agent (apagado, herramientas, habilidades, límites, rag, horarios)
    memoria, archivos de la carpeta personal, documentos de la biblioteca   sólo si la carpeta los trae
    cualquier fallo vuelve a borrar el agente
```

Comprobar, mientras se escribe una:

```
cd ../bunch-of-aigents
PYTHONDONTWRITEBYTECODE=1 PYTHONPATH=.:tests python3 -m unittest test_tools.TestExampleAgents
  fLoadRepositoryTemplates → agent_package.DirSource(<este repositorio>/<id>) + fReadPackage
```

## 5. Mapa de entradas y rutas

| Qué | Dirección | Lo lee |
|---|---|---|
| El repositorio entero, una rama | `https://github.com/nipegun/bunch-of-aigents-templates/archive/refs/heads/main.tar.gz` | `agent_templates.fDownloadArchive` |
| Una copia para una máquina sin salida | `file:///<ruta>/archive/refs/heads/<rama>.tar.gz` | `agent_templates.fDownloadArchive` |
| El repositorio y la rama que se usan | Ajustes → Agentes (`templates_repo_url`, `templates_repo_branch`) | `agent_templates.fReadRepositorySettings` |

## 6. Análisis de impacto

| Componente | Si se cambia, afecta a |
|---|---|
| El nombre de una carpeta | El id de la plantilla. Los agentes ya instalados siguen funcionando (son copias), pero lo que nombre la plantilla (documentación, tests) tiene que cambiar con ella |
| `tools` de una plantilla | Lo que puede hacer cada agente que se instale a partir de ella desde ese momento. Una herramienta que desaparezca de la aplicación hace que la plantilla la liste como que falta |
| `schedules` de una plantilla | Cada cuánto se despiertan sus agentes, y por tanto lo que cuestan |
| `system-prompt.md` | Sólo los agentes instalados después del cambio. Un agente instalado tiene su propia copia. Su `README.md` tiene que decir lo mismo |
| El formato del paquete (`agent_package` de la aplicación) | Todas las plantillas de aquí y todos los `.zip` exportados. Una clave nueva necesita la versión de la aplicación que la lea; un cambio de significado necesita un `format` nuevo |

## 7. Puntos de extensión

### Una plantilla nueva

1. Armá y probá el agente en la aplicación, después **Exportalo** (sin memoria,
   carpeta personal ni biblioteca, salvo que la plantilla deba llevarlas) y
   descomprimilo en una carpeta nueva con el mismo nombre que su `name`.
2. Poné en `agent.json` una `description` en los quince idiomas y sacá lo que
   una plantilla no debe llevar (`provider`, `exported_at`).
3. Comprobá que `system-prompt.md` está en inglés, en líneas completas, trata
   como datos lo que lee y termina con la regla sobre el idioma de la respuesta.
4. Escribí su `README.md`, en en-US: para qué sirve, qué hace, qué necesita antes
   de ser útil, qué no hará, con qué viene y cómo se instala. Copiá la forma de
   uno que ya exista.
5. Corré `TestExampleAgents` en `../bunch-of-aigents`.
6. Agregá su fila, con un enlace a su README, a las tablas de los cuatro README de acá (`README.md` y
   `doc/README.*.md`) y de los quince MANUAL de la aplicación
   (`../bunch-of-aigents/doc/MANUAL*.md`).

### Una clave nueva en agent.json

Primero es un cambio de la aplicación (`agent_package.lManifestKeys` y su
validación) y después de las plantillas que la usen. Hasta que las
instalaciones tengan esa versión, rechazan una plantilla con la clave nueva.
