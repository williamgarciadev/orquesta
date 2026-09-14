# orquesta — Agent Plugin

Reglas de trabajo compartidas para **varios agentes de código operando sobre el
mismo workspace al mismo tiempo**.

Empaquetadas en **[Agent Plugins 1.0](https://agent-plugins.org)**, el estándar
abierto publicado el 2026-08-06, para que cualquier cliente que lo implemente las
cargue igual: Claude Code, Antigravity, MiniMax Code — y el que se sume mañana.

## Qué resuelve

Cuando dos o más agentes trabajan el mismo repositorio, los fallos que aparecen
no son de código: son de **coordinación**. Dos escritores a la vez sobre un
recurso que no tiene lock. Mediciones tomadas durante el build de otro. Y
conclusiones de ausencia sacadas de lecturas recortadas.

Las seis reglas de la skill salieron de incidentes reales, y **cada una lleva
escrito el precio que pagó**. Una regla sin su costo se borra en la primera
discusión.

| # | Regla |
|---|---|
| 1 | Eres solo lectura. Un único escritor |
| 2 | Corre el gate antes de medir |
| 3 | Toda afirmación lleva su nivel de evidencia |
| 4 | Nunca concluyas una ausencia desde una lectura recortada |
| 5 | Todo chequeo lleva un control |
| 6 | El ambiente se cae, y no es tu culpa |

## Estructura

```
plugin.json                                 manifiesto canónico (esquema cerrado)
.minimax-plugin/plugin.json                 el mismo, donde lo busca MiniMax Code
skills/coordinacion-multiagente/SKILL.md    las seis reglas
mcp.json                                    pendiente — el servidor de orquesta
```

> El manifiesto está duplicado a propósito: la especificación lo ubica en la raíz,
> y el cargador de MiniMax Code exige `.minimax-plugin/plugin.json`. Es una verruga
> del cliente, no del formato.

## Instalación

**MiniMax Code** — `Plugins` → `+ Create` → `Import plugin from GitHub`, y pega
la URL de este repositorio.

**Claude Code · Antigravity** — toman configuración local: clona el repo y
apunta tu cliente a la carpeta.

## Estado

| Pieza | Estado |
|---|---|
| Skill de coordinación | listo |
| `mcp.json` con el servidor de orquesta | **pendiente** — el servidor no existe todavía |

Sin `mcp.json`, el plugin aporta las reglas pero ninguna herramienta. Esa mitad
llega cuando el servidor esté construido y se sepa **qué tools se usan de verdad**,
no antes.
