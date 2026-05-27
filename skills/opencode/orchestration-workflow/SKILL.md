---
name: orchestration-workflow
description: Skill de orquestacion para gestion de flujos de trabajo, tracking de progreso y coordinacion de agentes. Usar cuando el Orchestrator necesite estructurar tareas complejas.
---

# Orchestration Workflow Skill

## PROPOSITO
Proporcionar al `@orchestrator` un framework estructurado para descomponer tareas complejas, asignar agentes, y mantener tracking de progreso.

## FLUJO DE TRABAJO ESTANDAR

### 1. ANALISIS INICIAL
- Identificar el alcance de la tarea
- Determinar si es una feat, fix, refactor, o doc
- Evaluar dependencias y riesgos

### 2. DESCOMPOSICION
- Dividir en subtareas independientes cuando sea posible
- Asignar cada subtarea al agente especializado adecuado
- Establecer orden de ejecucion (paralelo vs secuencial)

### CRITERIOS DE PARALELIZACION
Dos subtareas pueden ir en paralelo si y solo si:
- No escriben en los mismos archivos (leer el mismo archivo es ok)
- El output de una no es input de la otra
- No modifican la misma interfaz o contrato

Paralelo valido:
- @codegen-frontend-coordinator escribe modulo A mientras @codegen-backend-coordinator escribe modulo B
- @codegen-frontend-coordinator escribe UI mientras @test-coordinator escribe tests del modulo B (ya existente)
- @review-coordinator audita mientras @explorer mapea otro modulo

Secuencial obligatorio:
- @codegen-frontend-coordinator o @codegen-backend-coordinator deben terminar antes de que @test-coordinator testee lo que genero
- @crash-analyzer debe terminar antes de reanudar cualquier otra tarea
- @explorer debe terminar antes de que el Orquestador presente el plan

### 3. ASIGNACION DE AGENTES
| Tipo de Tarea | Agente Responsable |
|---|---|
| Nueva funcionalidad frontend | `@codegen-frontend-coordinator` → `@codegen-frontend-worker` |
| Nueva funcionalidad backend | `@codegen-backend-coordinator` → `@codegen-backend-worker` |
| Refactorizacion | `@refactor-coordinator` → `@refactor-worker` |
| Tests | `@test-coordinator` → `@test-worker` |
| Revision de calidad | `@review-coordinator` |
| Crash/bug critico | `@crash-analyzer` |
| Documentacion | `@doc-architect` (independiente) |

### 4. EJECUCION
- Lanzar subtareas independientes en paralelo
- Esperar resultados antes de continuar con dependencias
- Validar cada paso antes de avanzar

### 5. VALIDACION
- Todo debe pasar por `@revisor` antes de DONE
- Verificar que tests estan creados para nuevas feats
- Verificar que los tests cubren happy path + caso de error + edge case como minimo
- Confirmar que no hay regresiones

### 6. CIERRE
- Actualizar estado de la tarea
- Documentar decisiones clave
- Notificar al usuario

## PLANTILLAS DE RESPUESTA

### Tarea Nueva
```
📋 TAREA: [descripcion]
🎯 OBJETIVO: [que se quiere lograr]
📊 ALCANCE: [archivos/modulos afectados]
⚠️ RIESGOS: [posibles problemas]
🔄 FLUJO: [orden de agentes]
```

### Actualizacion de Progreso
```
🔄 PROGRESO: [X/Y subtareas completadas]
✅ HECHO: [lista de completadas]
⏳ PENDIENTE: [lista de en progreso]
🚫 BLOQUEADO: [si aplica]
```

### Cierre de Tarea
```
✅ TAREA COMPLETADA
📝 RESUMEN: [que se hizo]
🧪 TESTS: [cobertura alcanzada]
📄 DOCS: [documentacion actualizada]
🔍 REVISION: [aprobada por @revisor]
```

## REGLAS DE ORO
1. Nunca saltarse la validacion del `@revisor`
2. Tests son obligatorios para nuevas funcionalidades
3. Cada funcionalidad testeable debe tener minimo 3 tests: happy path, error, edge case
4. Paralelizar siempre que no haya dependencias
5. Documentar decisiones arquitectonicas clave
6. Mantener al usuario informado del progreso
