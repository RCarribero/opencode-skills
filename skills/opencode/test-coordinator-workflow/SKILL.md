---
name: test-coordinator-workflow
description: Skill de coordinacion de testing. Usar cuando @test-coordinator necesite disenar estrategias de testing exhaustivas usando la mega-test-library.
---

# Test Coordinator Workflow

## PROPOSITO
Disenar y coordinar estrategias de testing completas, seleccionando casos de la `mega-test-library` y delegando implementacion a `@test-worker`.

## FLUJO DE TRABAJO

### 1. ANALISIS DE COBERTURA
- Identificar que necesita ser testeado
- Evaluar cobertura actual
- Detectar gaps criticos
- Priorizar por riesgo de negocio

### 2. SELECCION DE TESTS
- Consultar `mega-test-library` para casos aplicables
- Seleccionar IDs relevantes segun contexto
- Adaptar casos genericos al stack especifico
- Definir prioridad (unit > integration > e2e)

### 3. PLANIFICACION
- Agrupar tests por categoria y archivo
- Establecer orden de implementacion
- Delegar a `@test-worker` con contexto claro
- Ejecutar en paralelo cuando sea posible

### 4. EJECUCION Y VALIDACION
- Ejecutar tests creados
- Verificar cobertura (>80% objetivo)
- Confirmar que tests son deterministicos
- Validar que tests fallan cuando deben

### 5. REPORTAR
- Cobertura alcanzada
- Tests creados por categoria
- Gaps identificados
- Recomendaciones

## PLANTILLA DE TAREA PARA WORKER
```
🧪 TAREA: [descripcion de tests a crear]
📁 ARCHIVOS: [donde crear los tests]
📚 LIBRARY IDS: [IDs de mega-test-library aplicables]
🔧 FRAMEWORK: [jest/vitest/etc]
📋 CASOS:
- [caso 1]
- [caso 2]
⚠️ MOCKS: [que necesita mockearse]
```

## CATEGORIAS DE TESTS

| Categoria | Prioridad | Cobertura |
|---|---|---|
| Unit Tests | Alta | Funciones puras, utils, helpers |
| Integration Tests | Media | APIs, DB, servicios externos |
| E2E Tests | Media | Flujos completos de usuario |
| Security Tests | Alta | Inputs, auth, vulnerabilidades |
| Performance Tests | Baja | Benchmarks, limites |

## REGLAS
1. Tests deben ser independientes entre si
2. Usar `pnpm` para dependencias de testing
3. Tests deterministicos (sin flakiness)
4. Naming claro: `describe('X', () => it('should Y', ...))`
5. Un assert por test cuando sea posible
