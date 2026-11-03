# MANUAL - bunch-of-aigents-templates

Este MANUAL también está disponible en:\
[en-GB](MANUAL.en-GB.md), [en-US](MANUAL.md), [es-AR](MANUAL.es-AR.md), es-ES

Cómo usar estas plantillas, y cómo escribir, comprobar y publicar una.

## Contenido

1. [Instalar una plantilla](#instalar-una-plantilla)
2. [Usar otro repositorio](#usar-otro-repositorio)
3. [Una máquina sin salida a GitHub](#una-máquina-sin-salida-a-github)
4. [Escribir una plantilla](#escribir-una-plantilla)
5. [Qué puede llevar agent.json](#qué-puede-llevar-agentjson)
6. [Comprobarla](#comprobarla)
7. [Cuando una plantilla no aparece](#cuando-una-plantilla-no-aparece)

## Instalar una plantilla

En Bunch of AIgents, pulsa **+** en la barra lateral y elige **Importar desde
una plantilla de GitHub**. La lista muestra todas las plantillas de este
repositorio, con su descripción en el idioma de la interfaz, las familias de
herramientas que traen, cada cuánto se despiertan y si activan su biblioteca.
Elige una, dale un nombre (viene puesto el suyo) y se crea.

Llega **apagada y sin modelo**. Abre sus ajustes, elige un proveedor y un
modelo, lee su system prompt (varias dicen qué tienes que rellenar antes de
que sirvan de algo) y enciéndela.

## Usar otro repositorio

**Ajustes → Agentes** en la aplicación guarda la dirección del repositorio de
plantillas y su rama. Vacío significa este, rama `main`. Sirve cualquier
repositorio con la misma estructura: un fork de este, con tus cambios, o uno
tuyo. La dirección tiene que ser `https://` (o `file://`, abajo).

## Una máquina sin salida a GitHub

La aplicación descarga `<dirección>/archive/refs/heads/<rama>.tar.gz`. Para una
máquina sin Internet, deja ese archivo en ella y dale a la aplicación una
dirección `file://`:

```
mkdir -p /opt/templates-mirror/archive/refs/heads
cd /ruta/a/una/copia/de
tar czf /opt/templates-mirror/archive/refs/heads/main.tar.gz bunch-of-aigents-templates
```

y pon en **Ajustes → Agentes → Repositorio de plantillas**
`file:///opt/templates-mirror`. El archivo tiene que tener una carpeta arriba
del todo con las plantillas dentro, que es lo que sirve GitHub y lo que hace el
comando de arriba.

## Escribir una plantilla

Lo más rápido es montarla primero en la aplicación:

1. Crea un agente vacío, escribe su system prompt, elige sus herramientas, su
   horario y sus límites, y pruébalo hasta que haga lo que debe.
2. Abre su pestaña **Exportar** y descárgalo **sin** memoria, archivos de la
   carpeta personal, biblioteca ni modelo, salvo que la plantilla deba
   llevarlos.
3. Descomprímelo en una carpeta nueva de este repositorio, con el nombre del
   agente: minúsculas, dígitos y guiones, como `invoice-reader`.
4. Edita `agent.json`: pon en `name` el nombre de la carpeta, da a
   `description` los quince idiomas y quita `exported_at`.
5. Revisa el system prompt: en inglés, un párrafo por línea, tratando lo que
   lee (páginas, correos, documentos) como datos y nunca como instrucciones, y
   terminado con: *Answer in the language this prompt is written in. If the
   user writes to you in another language, answer in theirs.*
6. Escribe su `README.md`, en inglés (en-US): para qué sirve el agente, qué hace,
   qué necesita antes de ser útil, qué no hará nunca, con qué viene
   (herramientas, horario, límites) y cómo se instala. La aplicación no lo lee;
   lo leen personas, así que tiene que decir lo mismo que el prompt y
   `agent.json`. Las carpetas que ya hay muestran la forma.

Una carpeta lleva `agent.json`, `system-prompt.md` y `README.md`, y puede
llevar `memory.md`, `home/...` y `rag/documents.json` con `rag/files/...`.
Cualquier otra cosa hace que la aplicación la rechace.

## Qué puede llevar agent.json

```json
{
  "format": 1,
  "name": "invoice-reader",
  "description": {
    "de-DE": "...", "en-GB": "...", "en-US": "Reads the invoices you drop in its folder.",
    "es-AR": "...", "es-ES": "...", "fr-FR": "...", "he-IL": "...", "hi-IN": "...", "it-IT": "...",
    "ja-JP": "...", "ko-KR": "...", "pt-BR": "...", "pt-PT": "...", "ru-RU": "...", "zh-CN": "..."
  },
  "tools": ["bash.run", "kanban.add_card"],
  "schedules": ["0 8 * * 1-5"],
  "limits": {"max_tokens_per_run": 12000, "max_steps_per_run": 15,
             "timeout_seconds": 240, "max_runs_per_day": 4}
}
```

- **tools**: nombres con punto, como los enumera la aplicación en Ajustes →
  Herramientas. Una que la instalación no tenga se deja fuera, y la lista lo
  dice.
- **schedules**: horarios cron de cinco campos, como mucho veinte. Una lista
  vacía significa que sólo trabaja cuando se le pide.
- **limits**: lo que puede gastar una ejecución. Techos, no objetivos.
- **rag**: `{"enabled": true}` si el agente se basa en su biblioteca de
  documentos, como `rag-consultant`.
- Nunca `enabled`, claves, tokens ni credenciales: el formato no tiene dónde
  ponerlos, y toda plantilla llega apagada.

## Comprobarla

Con la aplicación clonada junto a este repositorio, ejecuta desde
`../bunch-of-aigents`:

```
PYTHONDONTWRITEBYTECODE=1 PYTHONPATH=.:tests python3 -m unittest test_tools.TestExampleAgents
```

Lee todas las plantillas de aquí con el mismo código con el que la aplicación
las instala, y falla ante cualquier cosa que rechazaría, una herramienta que
no existe, un idioma que falta o un prompt cortado a una columna fija.

## Cuando una plantilla no aparece

La lista de la aplicación dice cuántas plantillas no se han podido leer. Su
API, `GET /api/admin/agent-templates`, devuelve cada una con su `error`, que
dice qué arreglar: una clave desconocida, un horario que no es de cinco campos
de cron, un archivo que el formato no conoce, una ruta de `home/` oculta o que
sale hacia arriba. La aplicación guarda el repositorio cinco minutos; con
`?refresh=1` la API lo vuelve a descargar.
