---
name: reporte-qa
description: >-
  Genera reporte-qa.md, el reporte estandarizado de tests del agente `qa`: qué
  criterio de aceptación cubre cada test, el resultado real de pytest y, para
  incidentes, la prueba de que el test rojo ataca la causa raíz. No publica ni
  mueve la card — eso es del `documentador`.
---

## Cuándo

Al cerrar el paso de QA, sea `tipo: feature`/`fix` (cobertura de criterios de
aceptación) o `tipo: incidente` (test rojo contra la causa raíz).

## Formato de `reporte-qa.md`

```
# QA — <slug>

Veredicto: PASS | FAIL
Comando: `pytest -q ...`
Resultado: <línea real de pytest, ej. "7 passed, 0 failed in 1.2s">

## Cobertura de criterios de aceptación
| # criterio | test | resultado |
|---|---|---|
| 1 | test_criterio_1_... | PASS |
| 2 | test_criterio_2_... | FAIL — <por qué> |
| 3 | — | SIN COBERTURA |

## Casos borde agregados
- <caso no cubierto por el criterio> → `test_...` → <resultado>

## (solo incidente) Test rojo contra la causa raíz
- Cita de la causa raíz (de `docs/diagnostico-<slug>.md`): "..."
- Test: `test_...`
- Corrida contra el código actual (pre-fix): FALLÓ ✓ confirma el diagnóstico
  | NO FALLÓ ✗ el diagnóstico no está confirmado
```

## Reglas

- El resultado es el output real de correr `pytest`, nunca "debería pasar" ni
  un número inventado.
- **Un test rojo que no falla contra el código actual no confirma nada.**
  Decilo explícito como `NO FALLÓ`, no lo disfraces de PASS. Es la regla dura
  de `GOBERNANZA.md` #3: un test que no falla contra el bug es un placebo, no
  se publica la propuesta.
- Un criterio de aceptación sin test asociado se marca `SIN COBERTURA` en la
  tabla, no se omite.
- Un solo `FAIL` (o `SIN COBERTURA` en un criterio obligatorio) baja el
  veredicto general a `FAIL` — no hay veredicto "parcial".
- El archivo queda en la raíz del árbol de trabajo, listo para que el
  `documentador` lo adjunte (skill `registrar-en-card`) y, si es `FAIL`
  (pull-back), para que el `desarrollador-*` lo lea.
