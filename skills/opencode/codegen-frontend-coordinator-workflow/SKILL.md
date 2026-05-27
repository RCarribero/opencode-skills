---
name: codegen-frontend-coordinator-workflow
description: Skill de coordinacion de generacion de codigo frontend. Usar cuando @codegen-frontend-coordinator necesite estructurar la creacion de nuevas funcionalidades.
---

# CodeGen Frontend Coordinator Workflow

## PROPOSITO
Coordinar la generacion de codigo frontend nuevo de manera estructurada, asignando tareas especificas a `@codegen-frontend-worker` y asegurando calidad.

## FLUJO DE TRABAJO

### 1. ANALISIS DE REQUERIMIENTOS
- Identificar que UI/componentes se necesita construir
- Determinar stack tecnologico y convenciones del proyecto frontend
- Evaluar dependencias externas necesarias
- Definir interfaz publica esperada (props, eventos, etc.)

### 2. PLANIFICACION
- Descomponer la funcionalidad en subtareas atomicas
- Cada subtarea debe ser lo suficientemente pequena para un worker
- Establecer orden de dependencias entre subtareas
- Identificar archivos a crear o modificar

### 3. ASIGNACION A WORKERS
- Delegar cada subtarea a `@codegen-frontend-worker` con contexto claro
- Incluir: archivos objetivo, interfaz esperada, constraints
- Ejecutar en paralelo cuando no haya dependencias

### 4. INTEGRACION
- Validar que los fragmentos generados funcionen juntos
- Resolver conflictos de integracion
- Verificar imports y exports

### 5. VALIDACION
- Ejecutar tests existentes para asegurar no regresiones
- Crear tests para la nueva funcionalidad (obligatorio)
- Pasar a `@review-coordinator` para auditoria

## PLANTILLA DE TAREA PARA WORKER
```
📝 TAREA: [descripcion breve]
📁 ARCHIVOS: [lista de archivos a crear/modificar]
🔧 STACK: [tecnologias/frameworks frontend]
📋 REQUERIMIENTOS:
- [req 1]
- [req 2]
⚠️ CONSTRAINTS:
- [constraint 1]
- [constraint 2]
🧪 TESTS: [que debe testearse]
```

## REGLAS
1. Nunca generar codigo sin tests correspondientes
2. Seguir convenciones del proyecto existente
3. Documentar decisiones arquitectonicas
4. Usar `pnpm` para dependencias JS/TS
5. Mantener componentes pequenos y con responsabilidad unica
