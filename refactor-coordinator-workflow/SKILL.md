---
name: refactor-coordinator-workflow
description: Skill de coordinacion de refactoring. Usar cuando @refactor-coordinator necesite planificar mejoras de codigo sin cambiar comportamiento externo.
---

# Refactor Coordinator Workflow

## PROPOSITO
Planificar y coordinar mejoras de codigo existentes sin alterar su comportamiento externo, delegando tareas especificas a `@refactor-worker`.

## CUANDO USAR
- Deuda tecnica acumulada
- Codigo duplicado
- Funciones/metodos muy largos
- Acoplamiento excesivo
- Naming confuso
- Arquitectura desorganizada

## FLUJO DE TRABAJO

### 1. AUDITORIA INICIAL
- Identificar areas con deuda tecnica
- Analizar complejidad ciclomatica
- Detectar code smells
- Evaluar acoplamiento y cohesion

### 2. PLANIFICACION
- Priorizar por impacto y riesgo
- Definir alcance exacto del refactoring
- Establecer que NO debe cambiar (contratos publicos)
- Crear plan de rollback si es necesario

### 3. ASIGNACION
- Delegar cambios atomicos a `@refactor-worker`
- Cada cambio debe ser independiente y reversible
- Ejecutar en paralelo cuando sea posible

### 4. VALIDACION
- Ejecutar tests existentes (deben pasar todos)
- Verificar que el comportamiento no cambio
- Confirmar mejora en metricas de calidad

### 5. DOCUMENTACION
- Documentar cambios realizados
- Explicar el "por que" del refactoring
- Actualizar documentacion si aplica

## METRICAS DE EXITO
- Tests pasan al 100%
- Complejidad ciclomatica reducida
- Duplicacion de codigo eliminada
- Naming mas expresivo
- Arquitectura mas limpia

## REGLAS
1. Nunca cambiar comportamiento externo
2. Tests deben pasar antes y despues
3. Cambios pequenos y reversibles
4. Un problema a la vez
5. Documentar el "por que" del cambio
