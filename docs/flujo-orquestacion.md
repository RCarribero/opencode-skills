# Flujo de Orquestación — Arquitectura de Agentes

## Visión General

```
         Usuario
            │
       ┌────┴────┐
       │ Sinsajo │  (agente default, operaciones sistema + dev directo)
       └────┬────┘
            │ tarea de desarrollo
       ┌────┴────┐
       │Orquestador│  (planifica, delega, NO escribe código)
       └────┬────┘
    ┌───────┼───────┬──────────┬──────────┬──────────┬──────────┐
    │       │       │          │          │          │          │
    ▼       ▼       ▼          ▼          ▼          ▼          ▼
 FE coord  BE coord refactor  test      review    crash     explorer
    │       │       coord.    coord.    coord.   analyzer
    │       │       │          │          │          │
    ▼       ▼       ▼          ▼          │          │
 FE wrkr  BE wrkr refactor  test wrkr     │          │
                    wrkr                  │          │
                                          ▼          │
                                       revisor  (gate calidad)
```

---

## 1. Punto de Entrada: Sinsajo

**Modo**: `primary` (agente por defecto)

**Propósito**: Copiloto del sistema para administración Linux y desarrollo. Es quien recibe los mensajes del usuario.

**Cuándo deriva al Orquestador**:
- Tareas de desarrollo complejas (features, refactors, tests, bugs)
- Múltiples archivos o módulos involucrados
- Necesidad de planificar y coordinar múltiples agentes

**Cuándo actúa directamente**:
- Operaciones del sistema (paquetes, procesos, red, discos, logs)
- Git simple (commits, push, status)
- Ediciones rápidas de archivos
- Comandos ad-hoc

---

## 2. El Orquestador — Ciclo de Vida de una Tarea

**Modelo**: `opencode-go/deepseek-v4-pro` — temperature 0.2

**Permisos**: `bash: deny`, `edit: deny`, `write: deny` — **no toca archivos**

### Ciclo Estándar (7 fases)

```
┌──────────────────────────────────────────────────────────────┐
│  1. PLAN                                                     │
│  ├─ ¿Proyecto nuevo o >2 archivos? → @explorer primero      │
│  ├─ Descompone en subtareas atómicas con dependencias        │
│  └─ Estima tiempo por subtarea → Presenta → Usuario confirma │
├──────────────────────────────────────────────────────────────┤
│  2. DISPATCH (con timeouts explícitos)                       │
│  ├─ Subtareas SIN dependencias → en PARALELO                 │
│  ├─ Subtareas CON dependencias → SECUENCIAL                  │
│  ├─ Cada subtarea lleva timeout según tipo:                  │
│  │  explorer=2min, worker=5min, coord=10min, crash=15min     │
│  └─ FE coord ↔ BE coord pueden comunicarse directamente      │
│     para acordar contratos de API (resultado documentado)    │
├──────────────────────────────────────────────────────────────┤
│  3. MONITOR + HEARTBEAT                                      │
│  ├─ Estados: PENDING → IN_PROGRESS → REVIEW → DONE/FAILED    │
│  │  / ROLLED_BACK                                            │
│  ├─ Si excede timeout → FAILED + compensación                │
│  └─ Si desviación >30% tiempo estimado → re-planificar       │
├──────────────────────────────────────────────────────────────┤
│  3.5 AGGREGATE                                               │
│  ├─ Reconciliar resultados parciales antes de validar        │
│  ├─ Detectar conflictos (archivos duplicados, contratos API  │
│  │  inconsistentes entre FE y BE, dependencias faltantes)    │
│  └─ Si hay conflictos → resolver antes de pasar a VALIDATE   │
├──────────────────────────────────────────────────────────────┤
│  4. VALIDATE                                                 │
│  ├─ Output agregado → @revisor                               │
│  ├─ APPROVED → estado DONE                                   │
│  └─ REJECTED → pasar a REINTENTOS o COMPENSACIÓN             │
├──────────────────────────────────────────────────────────────┤
│  5. CLOSE + RETROSPECTIVA                                    │
│  ├─ Todo DONE → sintetiza resultado al usuario               │
│  └─ Retrospectiva breve: qué funcionó, qué no, ajustes       │
├──────────────────────────────────────────────────────────────┤
│  6. ROLLBACK (Saga — opcional)                               │
│  └─ Si subtarea crítica falla sin recuperación → deshacer    │
│     dependientes completadas (borrar archivos, revertir      │
│     migraciones, restaurar config) y marcar ROLLED_BACK      │
└──────────────────────────────────────────────────────────────┘
```

### Reglas de Paralelización

| Situación | ¿Paralelo? |
|---|---|
| @codegen-frontend escribe módulo A + @codegen-backend escribe módulo B | ✅ Sí |
| @codegen-frontend escribe UI + @test-coordinator testea módulo existente B | ✅ Sí |
| @review-coordinator audita + @explorer mapea otro módulo | ✅ Sí |
| FE coord y BE coord acuerdan contrato de API directamente | ✅ Sí (sin pasar por orchestrator) |
| @codegen-frontend termina ANTES de que @test-coordinator testee su código | ❌ No (secuencial) |
| @crash-analyzer activo + cualquier otra tarea | ❌ No (pausa todo) |
| @explorer antes de presentar el plan | ❌ No (debe terminar primero) |

### Timeouts por Tipo de Agente

| Agente | Timeout |
|---|---|
| @explorer | 2 min |
| @codegen-frontend-worker / @codegen-backend-worker | 5 min |
| @codegen-frontend-coordinator / @codegen-backend-coordinator | 10 min |
| @refactor-coordinator | 8 min |
| @refactor-worker | 4 min |
| @test-coordinator | 5 min |
| @test-worker | 3 min |
| @review-coordinator | 5 min |
| @crash-analyzer | 15 min |
| @revisor | 3 min |

### Sistema de Reintentos con Clasificación de Errores

**Antes de reintentar, clasificar el error:**

- **RETRYABLE**: timeout de red, rate-limit, recurso temporalmente no disponible, error transitorio → reintentar
- **NON-RETRYABLE**: error de sintaxis, lógica incorrecta, archivo no encontrado, permiso denegado, conflicto de tipos, dependencia faltante → NO reintentar, pasar a compensación o escalar

| Intento | Acción |
|---|---|
| 1 (solo si RETRYABLE) | Reenvía la tarea con contexto adicional del error |
| 2 (solo si RETRYABLE) | Divide la subtarea en partes más pequeñas |
| 3 (cualquier error) | Escala al usuario con diagnóstico, tipo de error y opciones |

### Compensación (Saga Pattern)

Cuando una subtarea falla y hay otras completadas que dependen de ella:
1. Identifica dependientes directas e indirectas de la que falló
2. Ejecuta compensación para cada una (borrar archivos, rollback migraciones, restaurar config)
3. Marca como ROLLED_BACK
4. Reporta al usuario qué se revertió y por qué
5. Solo entonces escala el error

No aplica compensación para subtareas independientes entre sí.

### Adaptive Re-Planning

| Desviación vs tiempo estimado | Acción |
|---|---|
| < 30% | Continuar, solo anotar |
| 30-60% | Re-planificar dependientes (ajustar tiempos, dividir, añadir recursos) |
| > 60% o bloqueo | Pausar dependientes, escalar al usuario con nuevo plan |

No re-planificar durante un crash (prioridad absoluta).

### Exploración Proactiva

El Orquestador DEBE delegar a `@explorer` **antes de planificar** si:

- La tarea afecta más de 2 archivos o módulos
- El usuario no especifica qué archivos modificar
- Es un fix y no se conoce la causa raíz
- Es la primera tarea en un proyecto nuevo o desconocido

El output de `@explorer` es **input obligatorio** del PLAN.

---

## 3. Subagentes del Orquestador — Coordinadores

### 3a. CodeGen Frontend Coordinator

| Atributo | Valor |
|---|---|
| **Modelo** | `opencode-go/kimi-k2.6` |
| **Temperatura** | 0.3 |
| **Spawnea** | `@codegen-frontend-worker` |

**Responsabilidades**:
- Componentes UI y páginas
- Estado y stores (Redux, Zustand, Pinia, etc.)
- Routing y navegación
- Llamadas a API desde el cliente
- Formularios y validación frontend
- Estilos y diseño responsive

**Stack por defecto**: React/Vue + TypeScript + Tailwind + Vite + Vitest

### 3b. CodeGen Backend Coordinator

| Atributo | Valor |
|---|---|
| **Modelo** | `opencode-go/kimi-k2.6` |
| **Temperatura** | 0.3 |
| **Spawnea** | `@codegen-backend-worker` |

**Responsabilidades**:
- APIs REST o GraphQL
- Modelos de datos y esquemas
- Lógica de negocio
- Autenticación y autorización
- Validación de datos del lado servidor
- Integración con bases de datos
- Middleware y pipes

**Stack por defecto**: Node.js + TypeScript + Express/Fastify/NestJS + Prisma + Vitest

### 3c. Refactor Coordinator

| Atributo | Valor |
|---|---|
| **Modelo** | `opencode-go/glm-5.1` |
| **Temperatura** | 0.2 |
| **Spawnea** | `@refactor-worker` |

**Reglas**:
- Nunca cambia el comportamiento externo del código
- Si detecta bugs fuera del scope → los reporta al Orquestador, no los corrige
- Documenta cada cambio y su razón

### 3d. Test Coordinator

| Atributo | Valor |
|---|---|
| **Modelo** | `opencode-go/kimi-k2.6` |
| **Temperatura** | 0.2 |
| **Spawnea** | `@test-worker` |

**Recursos**: Acceso a `@mega-test-library` (+100 escenarios categorizados)

**Prioridad de tests**: Unit > Integration > E2E > Security > Performance > Edge Cases

**Obligatorio**: Toda nueva funcionalidad (feat) testeable debe incluir tests **cubriendo mínimo 3 escenarios**: happy path, un caso de error y un edge case. El Orquestador delega a `@test-coordinator` inmediatamente después del coordinador de código correspondiente.

### 3e. Review Coordinator

| Atributo | Valor |
|---|---|
| **Modelo** | `opencode-go/glm-5.1` |
| **Temperatura** | 0.1 |
| **Permisos** | `write: false`, `edit: false` (solo lee) |

**Output**: Reportes estructurados con severidad (CRITICAL / HIGH / MEDIUM / LOW), descripción, archivo:línea y sugerencia. No aplica correcciones.

### 3f. Explorer

| Atributo | Valor |
|---|---|
| **Modelo** | `opencode-go/kimi-k2.6` |
| **Temperatura** | 0.1 |

**Propósito**: Investigar codebases rápidamente usando grep, glob y lectura de archivos.

**Output**: Estructura de directorios, dependencias, puntos de entrada, patrones de diseño, funciones clave.

**No modifica nada**.

---

## 4. Manejador de Crash — Prioridad Máxima

```
FASE 1 ─ DIAGNÓSTICO
├── ROOT_CAUSE (una frase)
├── CATEGORY (LOGIC_ERROR | RUNTIME_ERROR | TYPE_ERROR | ASYNC_ERROR | DEPENDENCY_ERROR | UNKNOWN)
├── AFFECTED_FILES
├── CONFIDENCE (HIGH | MEDIUM | LOW)
└── Si LOW → pide más info al Orquestador

FASE 1.5 ─ DETERMINAR DOMINIO
├── Frontend (componentes, páginas, stores, fetch, src/, components/, pages/, app/)
└── Backend  (APIs, modelos, ORM, lógica, api/, routes/, controllers/, services/)

FASE 2 ─ CORRECCIÓN
┌─────────────────────┬────────────────────┬────────────────────┐
│ Categoría           │ Frontend           │ Backend            │
├─────────────────────┼────────────────────┼────────────────────┤
│ LOGIC / TYPE        │ @refactor-coord    │ @refactor-coord    │
│ RUNTIME / ASYNC /   │ @codegen-frontend  │ @codegen-backend   │
│ DEPENDENCY          │                    │                    │
│ UNKNOWN             │ @codegen-frontend  │ @codegen-backend   │
└─────────────────────┴────────────────────┴────────────────────┘
│
├── Incluye: diagnóstico + "no modificar nada fuera del ROOT_CAUSE"
├── Fallo ×2 → prueba coordinador alternativo
└── Fallo ×3 → escala al usuario (máximo 3 intentos)

FASE 3 ─ TEST DE REGRESIÓN
├── LOGIC/TYPE → test UNITARIO
├── ASYNC/DEPENDENCY → test de INTEGRACIÓN
├── UNKNOWN/mixto → AMBOS
└── Requisitos: reproduce el crash (falla sin fix), pasa con fix, incluye cabecera

FASE 4 ─ VALIDACIÓN
├── Corrección + test → unidad indivisible → @revisor
└── Máximo 2 rechazos de Revisor antes de escalar

FASE 5 ─ REPORTE
└── CRASH_RESOLVED, ROOT_CAUSE, FIX_SUMMARY, TEST_TYPE, TEST_LOCATION
```

**Importante**: Cuando hay un crash activo, el Orquestador PAUSA todas las demás tareas hasta que esté resuelto.

---

## 5. Workers (Capa de Ejecución)

### CodeGen Frontend Worker

**Modelo**: `opencode-go/kimi-k2.6` — temp 0.3

- Componentes <150 líneas, reutilizables
- TypeScript tipado (nunca `any`)
- Estados: loading, empty, error, success
- Diseño responsive por defecto

### CodeGen Backend Worker

**Modelo**: `opencode-go/kimi-k2.6` — temp 0.3

- Validación de inputs siempre server-side
- Manejo de errores estructurado (códigos HTTP)
- Consultas optimizadas (N+1, índices, paginación)

### Refactor Worker

**Modelo**: `opencode-go/glm-5.1` — temp 0.2

- NUNCA cambia comportamiento externo
- Cambios atómicos, una responsabilidad
- Reporta bugs fuera de scope (no los arregla)

### Test Worker

**Modelo**: `opencode-go/kimi-k2.6` — temp 0.2

- Tests independientes y reproducibles
- Nombres descriptivos del escenario
- Ejecuta y reporta resultados (pasan/fallan)

---

## 6. Gate de Calidad: El Revisor

**Modelo**: `opencode-go/deepseek-v4-pro` — temperature 0.1

**Permisos**: `write: deny`, `edit: deny`, `bash: deny` — solo evalúa

```
┌─────────────────────────────────────────────────────────┐
│                    REVISOR                               │
│                                                         │
│  VERDICT: APPROVED | REJECTED                           │
│  ISSUES: lista de problemas                             │
│  CRITERIA_MET: qué pasó                                 │
│  CRITERIA_FAILED: qué falló (detalle accionable)        │
├─────────────────────────────────────────────────────────┤
│  Para código:                                           │
│  ✅ Correctitud: ¿hace lo que se pidió?                 │
│  ✅ Completitud: ¿cubre edge cases?                     │
│  ✅ Calidad: ¿legible y mantenible?                     │
│  ✅ Sin regresiones: ¿no rompe nada?                    │
├─────────────────────────────────────────────────────────┤
│  Para tests de nuevas feats:                            │
│  ✅ Cobertura: happy path + caso de error + edge case?  │
│  ✅ Aislamiento: sin dependencia externa                │
│  ✅ Nomenclatura: describe comportamiento, no impl.     │
│  ✅ Determinismo: mismo resultado siempre               │
│  ✅ Caso de error: prueba una situación de fallo real   │
│  ✅ Edge case: el caso límite elegido tiene sentido     │
├─────────────────────────────────────────────────────────┤
│  REJECTED automático si:                                │
│  ❌ Solo hay 1 test para la funcionalidad               │
│  ❌ Test solo verifica que la función existe            │
│  ❌ Test depende del orden de ejecución                 │
│  ❌ Nombre genérico ('should work', 'test 1')           │
│  ❌ (Crash) Test no reproduce el crash original         │
└─────────────────────────────────────────────────────────┘
```

**Regla de oro**: Ninguna tarea es DONE sin aprobación del Revisor.

---

## 7. Agente Independiente: Doc Architect

| Atributo | Valor |
|---|---|
| **Modelo** | `opencode-go/deepseek-v4-pro` |
| **Temperatura** | 0.4 |
| **Modo** | `all` (invocable directamente) |

**NO forma parte de la orquestación**. Opera de forma independiente y **no pasa por el ciclo de validación del Revisor**.

**Flujo**: Recopilar requisitos → Analizar proyecto → Generar salida (README, docs, presentación, diagramas).

---

## 8. Tabla Resumen de Asignación

| Tipo de tarea | Coordinador | Worker | ¿Pasa por Revisor? |
|---|---|---|---|
| **Nueva funcionalidad frontend** | `@codegen-frontend-coordinator` | `@codegen-frontend-worker` | ✅ Sí |
| **Nueva funcionalidad backend** | `@codegen-backend-coordinator` | `@codegen-backend-worker` | ✅ Sí |
| **Refactorización** | `@refactor-coordinator` | `@refactor-worker` | ✅ Sí |
| **Tests** | `@test-coordinator` | `@test-worker` | ✅ Sí |
| **Code review** | `@review-coordinator` | — | ✅ Sí |
| **Crash / bug crítico** | `@crash-analyzer` | (delega a FE/BE coord según dominio) | ✅ Sí |
| **Documentación** | `@doc-architect` | — | ❌ No |

---

## 9. Skills Disponibles

| Skill | ¿Cuándo se usa? |
|---|---|
| `orchestration-workflow` | Framework de descomposición y planificación |
| `project-standards` | Antes de tocar dependencias/configuración JS/TS |
| `mega-test-library` | El Test Coordinator selecciona escenarios de aquí |
| `codegen-frontend-coordinator-workflow` | Flujo de generación frontend |
| `codegen-backend-coordinator-workflow` | Flujo de generación backend |
| `codegen-frontend-worker-workflow` | Instrucciones para workers frontend |
| `codegen-backend-worker-workflow` | Instrucciones para workers backend |
| `refactor-coordinator-workflow` | Flujo de refactorización |
| `refactor-worker-workflow` | Instrucciones para workers de refactor |
| `test-coordinator-workflow` | Flujo de testing |
| `test-worker-workflow` | Instrucciones para workers de test |
| `review-coordinator-workflow` | Flujo de auditoría |
| `crash-analyzer-workflow` | Flujo de diagnóstico de crashes |
| `revisor-workflow` | Flujo de validación |
| `explorer-workflow` | Flujo de exploración |
| `doc-architect-workflow` | Flujo de documentación |

## Skills Globales Instaladas (~/.agents/skills/)

| Skill Global | Agentes que la usan | Propósito |
|---|---|---|
| `@web-design-guidelines` (Vercel) | FE coord, FE worker | Guías de diseño web, UI patterns, responsive |
| `@react-patterns` | FE coord, FE worker | Patrones de componentes React, hooks, composición |
| `@design-system` | FE coord, FE worker | Sistemas de diseño, atomic design, design tokens |
| `@shadcn-ui` | FE coord, FE worker | Componentes shadcn/ui, personalización |
| `@tailwind-css-patterns` | FE coord, FE worker | Patrones avanzados de Tailwind CSS |
| `@tailwind-theme-builder` | FE coord, FE worker | Temas Tailwind, paletas, configuración |
| `@vitest` | FE coord, FE worker, test coord, test worker | Configuración y patrones de testing con Vitest |
| `@api-design-principles` | BE coord, BE worker | Diseño de APIs REST, versionado, documentación |
| `@neon-postgres` | BE coord, BE worker | PostgreSQL, consultas, optimización, escalado |
| `@database-migration` | BE coord, BE worker | Migraciones de BD, estrategias, rollbacks |
| `@prisma-database-setup` | BE coord, BE worker | Prisma ORM, schemas, queries |
| `@web-security` | BE coord, BE worker | Seguridad web, OWASP, auth, validación |
| `@refactoring-patterns` | Refactor coord, refactor worker | Patrones de refactorización, code smells, técnicas |
| `@code-review-excellence` | Review coordinator | Revisión profunda, mejores prácticas, antipatrones |
| `@code-review-and-quality` | Review coordinator | Revisión de calidad, estándares por lenguaje |
| `@code-quality` | Revisor | Métricas de calidad, cobertura, complejidad ciclomática |
| `@systematic-debugging` | Crash analyzer | Debugging sistemático, aislamiento de causa raíz |
| `@debugging-strategies` | Crash analyzer | Estrategias por tipo de error (runtime, async, memoria) |
| `@debugging-and-error-recovery` | Crash analyzer | Recuperación de errores, logging, stack traces |
| `@workflow-orchestration-patterns` | Orchestrator | Patrones de orquestación, coordinación paralela/secuencial |
| `@multi-agent-orchestration` | Orchestrator | Orquestación multi-agente, comunicación entre agentes |
| `@find-skills` | — (CLI tool) | Herramienta de descubrimiento de skills |

**Nota**: Las skills globales se cargan mediante `load` en el prompt del agente. Las skills de `~/.config/opencode/skills/` se referencian en `opencode.jsonc`. Ambos sistemas conviven.

---

## 10. Ejemplos de Flujo Completo

### Feature completa (FE + BE + tests)

```
1. @explorer mapea el proyecto (si es nuevo o >2 archivos)
2. Orquestador presenta plan → usuario confirma
3. ┌─ @codegen-frontend-coordinator → @codegen-frontend-worker  (paralelo)
   └─ @codegen-backend-coordinator  → @codegen-backend-worker
4. @test-coordinator escribe tests (min. happy path + error + edge case) para FE y BE  (después de 3)
5. @review-coordinator audita código            (opcional, después de 4)
6. @revisor valida todo                         (gate final)
7. Orquestador cierra la tarea
```

### Fix de crash

```
1. Orquestador PAUSA todo
2. @crash-analyzer diagnostica (Fase 1)
3. Determina dominio FE/BE (Fase 1.5)
4. Delega corrección al coordinador correspondiente (Fase 2)
5. Crea test de regresión (Fase 3)
6. @revisor valida corrección + test (unidad indivisible) (Fase 4)
7. Reporte final (Fase 5)
8. Orquestador reanuda tareas pausadas
```

### Refactor simple

```
1. @explorer si es necesario
2. @refactor-coordinator → @refactor-worker
3. @test-coordinator corre tests que pudieron romperse
4. @revisor valida
5. Cierre
```
