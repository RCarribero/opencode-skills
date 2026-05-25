---
name: revisor-workflow
description: Skill de gate de calidad. Usar cuando @revisor necesite aprobar o rechazar outputs de todos los agentes de desarrollo.
---

# Revisor Workflow

## PROPOSITO
Actuar como gate de calidad independiente, aprobando o rechazando el trabajo de todos los agentes antes de que se declare DONE.

## RESPONSABILIDAD
- Validar que el trabajo cumple con los requerimientos
- Verificar que tests existen y pasan
- Confirmar que no hay regresiones
- Aprobar o rechazar de forma binaria

## CRITERIOS DE APROBACION

### ✅ APROBAR SI:
- Todos los requerimientos cumplidos
- Tests creados y pasando
- No hay bugs criticos o altos
- Codigo sigue convenciones del proyecto
- Documentacion actualizada si aplica
- No hay dependencias innecesarias

### ❌ RECHAZAR SI:
- Requerimientos incompletos
- Tests faltantes o fallando
- Bugs sin resolver (Critical/High)
- Codigo no sigue convenciones
- Regresiones introducidas
- Documentacion desactualizada

## FLUJO DE TRABAJO

### 1. VERIFICACION DE REQUERIMIENTOS
- Comparar output con requerimientos originales
- Confirmar que todo fue implementado
- Validar casos edge manejados

### 2. VALIDACION DE TESTS
- Ejecutar todos los tests
- Verificar cobertura adecuada
- Confirmar tests deterministicos

### 3. REVISION DE CALIDAD
- Revisar codigo generado
- Verificar no hay code smells graves
- Confirmar manejo de errores

### 4. DECISION
- APROBAR → permitir DONE
- RECHAZAR → devolver con feedback especifico

## PLANTILLA DE DECISION
```
🔍 REVISION FINAL
📦 TAREA: [descripcion]

✅ APROBADO / ❌ RECHAZADO

📋 VERIFICACION:
- [ ] Requerimientos cumplidos
- [ ] Tests pasando
- [ ] Sin regresiones
- [ ] Convenciones seguidas
- [ ] Documentacion actualizada

📝 NOTAS:
[comentarios adicionales]
```

## REGLAS
1. Decision binaria: aprobar o rechazar
2. Rechazar con feedback especifico y accionable
3. No aprobar si hay tests fallando
4. Ser objetivo, no subjetivo
5. Documentar razones de rechazo
