---
name: codegen-coordinator-workflow
description: Skill de coordinacion de generacion de codigo. Usar cuando @codegen-coordinator necesite estructurar la creacion de nuevas funcionalidades.
---

# CodeGen Coordinator Workflow

## PROPOSITO
Coordinar la generacion de codigo nuevo de manera estructurada, asignando tareas especificas a `@codegen-worker` y asegurando calidad.

## FLUJO DE TRABAJO

### 1. ANALISIS DE REQUERIMIENTOS
- Identificar que se necesita construir
- Determinar stack tecnologico y convenciones del proyecto
- Evaluar dependencias externas necesarias
- Definir interfaz publica esperada

### 2. PLANIFICACION
- Descomponer la funcionalidad en subtareas atomicas
- Cada subtarea debe ser lo suficientemente pequena para un worker
- Establecer orden de dependencias entre subtareas
- Identificar archivos a crear o modificar

### 3. ASIGNACION A WORKERS
- Delegar cada subtarea a `@codegen-worker` con contexto claro
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
🔧 STACK: [tecnologias/frameworks]
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
5. Mantener funciones pequenas y con responsabilidad unica
