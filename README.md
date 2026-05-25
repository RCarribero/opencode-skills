# 🤖 OpenCode Multi-Agent Skills

Sistema de **14 skills especializadas** para orquestacion de desarrollo con agentes de IA. Diseñado para [OpenCode](https://github.com/opencode-ai/opencode), este conjunto de workflows coordina un equipo de agentes que trabajan en paralelo para **generar codigo, testear, refactorizar, revisar y desplegar** siguiendo un pipeline de calidad.

---

## 📐 Arquitectura

```
┌──────────────────────────────────────────────────────────────┐
│                     @Orchestrator                             │
│            Orquestador central del sistema                    │
└──────┬──────────┬──────────┬──────────┬──────────────────────┘
       │          │          │          │
       ▼          ▼          ▼          ▼
┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐
│ @codegen │ │ @refacto│ │  @test   │ │ @review  │
│ -coord.. │ │ -coord.. │ │ -coord.. │ │ -coord.. │
└────┬─────┘ └────┬─────┘ └────┬─────┘ └────┬─────┘
     │            │            │            │
     ▼            ▼            ▼            ▼
┌──────────┐ ┌──────────┐ ┌──────────┐ ┌──────────┐
│ @codegen │ │ @refacto│ │  @test   │ │ @crash   │
│ -worker  │ │ -worker  │ │ -worker  │ │ -analyzer│
└──────────┘ └──────────┘ └──────────┘ └──────────┘

     ▼            ▼            ▼            ▼
┌──────────────────────────────────────────────────────────────┐
│                      @Revisor                                │
│              Gate de calidad final (APROBAR/RECHAZAR)        │
└──────────────────────────────────────────────────────────────┘

     │
     ▼
┌──────────┐   ┌──────────┐   ┌──────────┐   ┌──────────┐
│ @doc     │   │ @explorer│   │ @project │   │ @mega    │
│-architect│   │          │   │-standards│   │-test-lib │
└──────────┘   └──────────┘   └──────────┘   └──────────┘
   Independientes del pipeline principal
```

---

## 📦 Skills incluidas (14)

### 🎯 Coordinadores — Planifican y delegan

| Skill | Agente | Funcion |
|-------|--------|---------|
| [`orchestration-workflow`](orchestration-workflow/) | `@orchestrator` | Descompone tareas complejas, asigna agentes, trackea progreso |
| [`codegen-coordinator-workflow`](codegen-coordinator-workflow/) | `@codegen-coordinator` | Planifica generacion de codigo nuevo, delega a workers |
| [`refactor-coordinator-workflow`](refactor-coordinator-workflow/) | `@refactor-coordinator` | Planifica refactorings, audita deuda tecnica |
| [`test-coordinator-workflow`](test-coordinator-workflow/) | `@test-coordinator` | Diseña estrategias de testing con la mega-test-library |
| [`review-coordinator-workflow`](review-coordinator-workflow/) | `@review-coordinator` | Audita codigo en busca de bugs, calidad y seguridad |

### 🔧 Workers — Ejecutan tareas atomicas

| Skill | Agente | Funcion |
|-------|--------|---------|
| [`codegen-worker-workflow`](codegen-worker-workflow/) | `@codegen-worker` | Implementa fragmentos de codigo especificos |
| [`refactor-worker-workflow`](refactor-worker-workflow/) | `@refactor-worker` | Aplica cambios de refactoring atomicos |
| [`test-worker-workflow`](test-worker-workflow/) | `@test-worker` | Implementa tests unitarios y de integracion |

### 🛡️ Calidad y Seguridad

| Skill | Agente | Funcion |
|-------|--------|---------|
| [`crash-analyzer-workflow`](crash-analyzer-workflow/) | `@crash-analyzer` | Diagnostica crashes, genera fix + test de regresion |
| [`revisor-workflow`](revisor-workflow/) | `@revisor` | Gate de calidad final: aprueba o rechaza outputs |

### 📚 Soporte

| Skill | Agente | Funcion |
|-------|--------|---------|
| [`doc-architect-workflow`](doc-architect-workflow/) | `@doc-architect` | Crea READMEs, docs tecnicas y presentaciones |
| [`explorer-workflow`](explorer-workflow/) | `@explorer` | Explora y mapea codebases rapidamente |
| [`project-standards`](project-standards/) | — | Estandares de proyecto (pnpm, tests, commits) |
| [`mega-test-library`](mega-test-library/) | — | +100 casos de prueba categorizados para cobertura exhaustiva |

---

## 🔄 Flujo de trabajo tipico

```
Tarea: "Añadir endpoint de login con JWT"

1. @orchestrator analiza y descompone:
   ├── @codegen-coordinator → @codegen-worker: genera el endpoint
   ├── @test-coordinator → @test-worker: crea tests unitarios + integracion
   └── @review-coordinator: audita codigo y seguridad

2. @revisor valida todo:
   ├── Requerimientos cumplidos ✓
   ├── Tests pasando ✓
   ├── Sin regresiones ✓
   └── APRUEBA ✅
```

---

## ⚡ Paralelizacion

Los agentes se ejecutan en paralelo cuando no hay dependencias entre ellos:

```yaml
Paralelo valido:
  - @codegen-coordinator escribe modulo A
  - @test-coordinator escribe tests del modulo B (ya existente)
  - @review-coordinator audita mientras @explorer mapea otro modulo

Secuencial obligatorio:
  - @codegen-coordinator → @test-coordinator (testear lo generado)
  - @crash-analyzer → resto de agentes (prioridad maxima)
  - @explorer → @orchestrator (primero mapear, luego planificar)
```

---

## 🚀 Instalacion

Clona este repo en tu directorio de skills de OpenCode:

```bash
git clone https://github.com/RCarribero/opencode-skills.git ~/.config/opencode/skills/
```

O enlaza skills individuales:

```bash
ln -s $(pwd)/orchestration-workflow ~/.config/opencode/skills/orchestration-workflow
```

---

## 📋 Estandares del proyecto

- **Package manager**: `pnpm` (nunca `npm` ni `yarn`)
- **Testing**: `vitest` (unitarios junto al codigo, integracion en `tests/`)
- **Commits**: `type(scope): descripcion` — feat, fix, refactor, test, docs, chore
- **Revision**: todo debe ser aprobado por `@revisor` antes de merge

---

## 🧪 Cobertura de tests

Usando la [mega-test-library](mega-test-library/) con **+100 escenarios**:

| Categoria | Casos | IDs |
|-----------|-------|-----|
| Unit Tests | 30 | 1-30 |
| Integration Tests | 20 | 31-50 |
| E2E / Flujo | 15 | 51-65 |
| Security Tests | 15 | 66-80 |
| Performance | 10 | 81-90 |
| Edge Cases | 15 | 91-105 |

---

## 🤝 Contribuir

1. Fork el repo
2. Crea tu skill en una carpeta nueva con su `SKILL.md`
3. Sigue el formato: frontmatter YAML con `name` y `description`, luego el contenido
4. PR con descripcion clara del proposito de la skill

---

## 📄 Licencia

MIT — usa, modifica y comparte libremente.
