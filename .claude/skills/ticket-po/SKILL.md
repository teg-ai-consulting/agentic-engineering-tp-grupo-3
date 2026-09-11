---
name: ticket-po
description: >-
  Crea una card `tipo: feature` en To-Do a partir de un pedido de negocio del
  PO, con checklist de criterios de aceptación verificables. La usa un humano
  (vía Claude Code) para cargar una feature nueva al pipeline. No la uses para
  un fix de incidente (ver `crear-card`) ni para reflejar un paso del pipeline
  (ver `mover-card` / `registrar-en-card`).
---

Esta card es el **insumo cero** del pipeline: el `analista` la lee sin más
contexto que lo que escribas acá. Si el pedido queda ambiguo, el problema baja
en cascada a todo el resto.

## Cuándo

Cuando el PO (o alguien en su nombre) tiene una feature nueva para el
pipeline y todavía no existe card. Si ya existe, buscala por título y
comentá/editá esa en vez de duplicar.

## Cómo

```
set_active_board(board_id = <TRELLO_BOARD_ID>)
listas = get_lists()
todo   = next(l.id for l in listas if l.name == "To-Do")
card   = add_card(list_id = todo, name = <título>, desc = <descripción>)
# agregá el label "tipo: feature" (resolvé nombre → id como con las listas)
# creá un checklist llamado "Criterios de aceptación" con un ítem por criterio
```

Los nombres exactos de tool (`add_card`, `create_checklist`, ...) son del
servidor MCP de Trello — verificalos contra su README.

## Estructura

**Título**: corto, verbo + qué (ej. "Mostrar badge de novedad en items"). Nada
de nombres de función, tabla ni endpoint — eso lo decide después el
`arquitecto`.

**Descripción** (`desc`): el problema de negocio en 1-2 frases. Es lo que el
`analista` copia tal cual en "Qué pide el PO". Si hay algo fuera de alcance,
decilo acá explícito ("no incluye X").

**Checklist "Criterios de aceptación"**: un ítem por criterio, numerado,
**verificable** (se puede marcar cumplido/no cumplido mirando el resultado, no
"que funcione bien"). El `analista` los lee con `get_acceptance_criteria` y
los vuelca 1 a 1 en `contexto/feature-<slug>.md`.

```
1. Un item con menos de 30 días desde su alta muestra el badge "Nuevo".
2. Un item con 30 días o más no lo muestra.
3. El listado paginado no cambia su tiempo de respuesta de forma perceptible.
```

**Label**: `tipo: feature` es obligatorio — es lo que hace que el pipeline la
tome por el flujo de feature y no la ignore o la trate como incidente.

## Reglas

- Pedí el **qué**, nunca el **cómo**. Si escribís la solución técnica en el
  ticket, el `arquitecto` y el `revisor` no tienen margen para elegir el mejor
  camino — y si esa solución técnica tiene un problema (ej. un N+1), nadie lo
  va a cuestionar porque "así lo pidió el PO".
- Sin datos que no tengas confirmados. Si no sabés el volumen o la frecuencia,
  no inventes un número — dejalo para que el `analista` lo marque como señal
  pendiente.
- Si la feature toca plata, datos personales, imágenes o un listado, decilo
  en la descripción — son las señales que el `analista` necesita para no
  perderlas.
- Solo a `To-Do`. Nunca crees ni muevas la card más allá vos mismo.
