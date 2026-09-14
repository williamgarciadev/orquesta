---
name: coordinacion-multiagente
description: Reglas obligatorias cuando varios agentes trabajan a la vez sobre el mismo workspace y la misma Knowledge Base. Cubre quien puede escribir, como se marca la evidencia, y que control lleva cada medicion. Cargala ANTES de medir o proponer nada.
---

# Coordinación entre agentes

Trabajas sobre un workspace compartido. **Hay otros agentes trabajando el mismo
entorno al mismo tiempo**, y ninguno te avisa cuando toca algo.

---

## REGLA 1 — Eres SOLO LECTURA. Sin excepciones

No escribes en la KB. No corres builds. No corres deploys. No tocas el servidor
de aplicaciones.

Prohibido: `genexus_edit`, `genexus_create`, `genexus_lifecycle`,
`genexus_apply_pattern`, los scripts de deploy, y cualquier escritura dentro del
directorio del webapp.

**Tu entregable es un archivo de texto con hallazgos.** Otro agente aplica.
Si crees que hay que cambiar algo, lo escribes como propuesta. No lo haces.

> **Por qué:** el worker del MCP es de un solo hilo; un deploy toma un lock que
> bloquea toda escritura; y el IDE abierto pisa en silencio lo que escribe el MCP.
> Un solo escritor no es burocracia: es la capacidad real del sistema.

## REGLA 2 — Primero corre el gate

```
pwsh -NoProfile -File <workspace>\gate.ps1 -SelfTest
```

Debe dar 13/13 y exit 0. Si no, **para y repórtalo**: tu entorno no es comparable
con el de los demás y nada de lo que midas va a servir.

Después corre `gate.ps1` sin flags y pega la salida al inicio de tu entregable.

> **Por qué:** es el único mecanismo que corre igual en todas las herramientas.
> Los hooks de un IDE concreto solo los ejecuta ese IDE.

## REGLA 3 — Toda afirmación lleva su nivel de evidencia

| Marca | Significa |
|---|---|
| `MEDIDO` | Corriste un comando. Pegas el comando **y** su salida |
| `INFERIDO` | Lo deduces de algo que sí mediste. Di de qué |
| `NO VERIFICADO` | No lo comprobaste. Decirlo es válido; mentir no |

**Si no lo leíste en ESTA sesión, no lo afirmas.** Lo lees, o lo formulas como
pregunta.

> **Por qué:** un `INFERIDO` presentado como hecho frenó un build una tarde entera.

## REGLA 4 — Nunca concluyas una ausencia desde una lectura recortada

Un `.slice(0,500)`, un `head -n`, un `top_k` chico o un resultado truncado **no
prueban que algo no está: prueban que no lo viste.** Para afirmar que algo falta,
lo leíste entero o barriste sin recorte.

Y `grep -c` cuenta **líneas**, no ocurrencias. El código generado viene casi en
una sola línea: usa `grep -o <patrón> | wc -l`.

> **Por qué:** el inventario de master pages salió de lecturas recortadas y
> acertó por casualidad. El conteo que se propagó sin verificar dejó fuera una
> master page que cubría 115 pantallas.

## REGLA 5 — Todo chequeo lleva un control

Un control es algo cuyo resultado **ya conoces**. Si buscas X y da 0, prueba
también con algo que sí existe. **Si el control también falla, tu método está
roto, no el sistema.**

> **Por qué:** tres conclusiones falsas en una sola sesión — 660 recursos "rotos"
> por una comilla suelta, 51 iconos "faltantes" por apuntar a la carpeta
> equivocada, y un `404` que no significaba nada porque el control daba `000`.

## REGLA 6 — El ambiente se cae, y no es tu culpa

Otro agente puede estar buildeando. Síntomas: el servidor timeoutea, el worker
del MCP devuelve `WorkerBusy`, una operación tarda minutos.

- **No reintentes en bucle.** Anota "ambiente ocupado" y sigue con otra cosa.
- **Una medición hecha durante un build no es confiable aunque devuelva un número.**

> **Por qué:** sobre los mismos archivos, en diez minutos:
> `200` → `timeout` → `200` → `404` → `000`. Ninguna servía, y solo el control
> lo delataba.

---

## Antes de medir: el semáforo

Lee `.coord/AMBIENTE.md`. Si dice `BUILD` o `DEPLOY`, no midas.
Al terminar tu propio build, **devuélvelo a `LIBRE` en el mismo movimiento**.

> No es un mutex: leer un archivo y después escribirlo no es atómico, y nadie
> impide que otro agente buildee sin anunciarlo. Es cortesía entre agentes que ya
> acordaron usarla. **El control es lo único que no miente.**
