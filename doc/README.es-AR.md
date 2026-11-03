# Bunch of AIgents: plantillas de agentes

Este README también está disponible en:\
[en-GB](README.en-GB.md), [en-US](../README.md), es-AR, [es-ES](README.es-ES.md)

Agentes listos para usar en [Bunch of AIgents](https://github.com/nipegun/bunch-of-aigents),
la aplicación autoalojada que ejecuta agentes de IA en tu propio servidor
Debian o Alpine. Cada carpeta de este repositorio es un agente: sus
instrucciones, las herramientas que necesita, cada cuánto se despierta y lo
que puede gastar.

## Cómo se usan

No tenés que descargar nada de acá. En Bunch of AIgents, apretá **+** en la
barra lateral y elegí **Importar desde una plantilla de GitHub**: la
aplicación descarga este repositorio, enumera sus agentes (con lo que hace
cada uno, las herramientas que trae y su horario) e instala el que elijas.
Llega apagado y sin modelo, así que no se ejecuta nada hasta que le pongas uno
y lo enciendas.

| Plantilla | Qué hace |
|---|---|
| [**backup-watcher**](../backup-watcher/README.md) | Comprueba que tus copias de seguridad se hicieron, son recientes y no son sospechosamente chicas. |
| [**cert-watcher**](../cert-watcher/README.md) | Avisa cuando un certificado TLS está por vencer, mientras todavía hay tiempo. |
| [**disk-cleaner**](../disk-cleaner/README.md) | Encuentra qué está llenando el disco y dice qué se podría borrar. Propone; nunca borra. |
| [**kanban-watcher**](../kanban-watcher/README.md) | Lee el tablero cada mañana de lunes a viernes y escribe un resumen corto. |
| [**log-watcher**](../log-watcher/README.md) | Lee los registros que le indiques y cuenta lo que es nuevo o se volvió frecuente. |
| [**mail-watcher**](../mail-watcher/README.md) | Vigila una cuenta de correo y actúa según las reglas que escribas en su prompt. |
| [**news-watcher**](../news-watcher/README.md) | Lee los feeds RSS que pongas en su prompt y cuenta lo que encaje con tus reglas. |
| [**os-watcher**](../os-watcher/README.md) | Vigila la máquina: disco, memoria, swap, carga y si los servicios están andando. |
| [**rag-consultant**](../rag-consultant/README.md) | Responde preguntas con los documentos de su biblioteca y cita el fragmento que respalda cada afirmación. |
| [**site-watcher**](../site-watcher/README.md) | Comprueba que los sitios que le des responden, responden rápido y siguen diciendo lo que decían. |
| [**web-navigator**](../web-navigator/README.md) | Maneja su propio navegador para hacer lo que le pidas en un sitio, y se frena antes de cualquier contraseña o pago. |

## Cómo usar las tuyas

En **Ajustes -> Agentes** podés apuntar una instalación a otro repositorio
(un fork de este o uno tuyo con la misma estructura) y a otra rama. Una
máquina sin salida a GitHub puede usar una ruta `file://` que contenga
`archive/refs/heads/<rama>.tar.gz`, la misma estructura que acepta el
instalador.

## Cómo se escribe una plantilla

Una plantilla es una carpeta con tres archivos: `agent.json`,
`system-prompt.md` y un `README.md` que la describe. Los dos primeros son el
mismo formato que descarga la pestaña **Exportar** de un agente, así que lo
más rápido es armar el agente en la aplicación, exportarlo, descomprimirlo
acá y agregarle su README. El formato, qué se comprueba y por qué está
en [doc/CODE.es-AR.md](CODE.es-AR.md); cómo escribir y publicar una, paso
a paso, en [doc/MANUAL.es-AR.md](MANUAL.es-AR.md).

## Licencia

MIT, como la aplicación. Mirá [LICENSE](../LICENSE).
