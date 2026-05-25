---
name: review-coordinator-workflow
description: Skill de coordinacion de revision de codigo. Usar cuando @review-coordinator necesite auditar codigo en busca de bugs y problemas de calidad.
---

# Review Coordinator Workflow

## PROPOSITO
Auditar codigo generado o modificado en busca de bugs, problemas de calidad, seguridad y mantenibilidad.

## AREAS DE REVISION

### 1. BUGS
- Logica incorrecta
- Condiciones de carrera
- Memory leaks
- Unhandled exceptions
- Edge cases no manejados

### 2. SEGURIDAD
- Inyeccion SQL
- XSS
- CSRF
- Auth bypass
- Datos sensibles expuestos
- Dependencias vulnerables

### 3. CALIDAD
- Complejidad ciclomatica alta
- Codigo duplicado
- Naming confuso
- Funciones muy largas
- Acoplamiento excesivo

### 4. MANTENIBILIDAD
- Documentacion faltante
- Tests insuficientes
- Hardcoded values
- Magic numbers
- Comentarios obsoletos

## FLUJO DE TRABAJO

### 1. ANALISIS ESTATICO
- Revisar estructura del codigo
- Identificar code smells
- Evaluar arquitectura

### 2. ANALISIS DINAMICO
- Verificar tests existentes
- Evaluar cobertura
- Confirmar manejo de errores

### 3. REVISION DE SEGURIDAD
- Inputs no validados
- Auth/Authorizacion
- Datos sensibles
- Dependencias

### 4. REPORTE
- Listar problemas encontrados
- Priorizar por severidad
- Sugerir soluciones
- Aprobar o rechazar

## ESCALA DE SEVERIDAD

| Nivel | Descripcion | Accion |
|---|---|---|
| Critical | Bug de seguridad o crash | Bloquear merge |
| High | Bug funcional | Corregir antes de merge |
| Medium | Code smell significativo | Corregir pronto |
| Low | Mejora menor | Opcional |

## PLANTILLA DE REPORTE
```
🔍 REVISION: [archivo/modulo]
✅ APROBADO / ❌ RECHAZADO

🚨 CRITICAL:
- [problema]

⚠️ HIGH:
- [problema]

📝 MEDIUM:
- [problema]

💡 LOW:
- [problema]

📋 RECOMENDACIONES:
- [sugerencia]
```

## REGLAS
1. Ser especifico en los problemas
2. Sugerir soluciones concretas
3. Priorizar por impacto real
4. No ser nitpicky con estilo
5. Aprobar solo si no hay Critical/High
