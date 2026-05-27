---
name: crash-analyzer-workflow
description: Skill de diagnostico de crashes. Usar cuando @crash-analyzer necesite diagnosticar errores criticos y generar correcciones con tests de regresion.
---

# Crash Analyzer Workflow

## PROPOSITO
Diagnosticar la causa raiz de crashes, generar correcciones y crear tests de regresion para prevenir recurrencia.

## FLUJO DE TRABAJO

### 1. RECOLECCION DE INFORMACION
- Obtener stack trace completo
- Identificar contexto del crash
- Recopilar logs relevantes
- Determinar condiciones de reproduccion

### 2. DIAGNOSTICO
- Analizar stack trace
- Identificar variable/estado problematico
- Determinar causa raiz (no solo sintoma)
- Evaluar impacto

### 3. CORRECCION
- Crear fix minimalista
- No refactorizar adicionalmente
- Mantener cambio enfocado en el crash
- Incluir manejo de errores robusto

### 4. TEST DE REGRESION
- Crear test que reproduzca el crash
- Confirmar que el test falla sin el fix
- Confirmar que el test pasa con el fix
- Añadir a suite de tests

### 5. VALIDACION
- Ejecutar tests existentes
- Verificar que no hay regresiones
- Confirmar fix en entorno de prueba

## PATRONES COMUNES DE CRASH

### Null/Undefined
```typescript
// Causa: Acceso a propiedad de null
user.profile.name // user.profile es null

// Fix: Validacion o optional chaining
user?.profile?.name ?? 'default'
```

### Race Conditions
```typescript
// Causa: Estado compartido sin sincronizacion
let counter = 0
async function increment() { counter++ }

// Fix: Atomicidad o locks
const lock = new AsyncLock()
async function increment() {
  await lock.acquire(() => counter++)
}
```

### Memory Leaks
```typescript
// Causa: Listeners no removidos
element.addEventListener('click', handler)

// Fix: Cleanup
useEffect(() => {
  element.addEventListener('click', handler)
  return () => element.removeEventListener('click', handler)
}, [])
```

## PLANTILLA DE REPORTE
```
🚨 CRASH ANALYSIS
📍 ARCHIVO: [archivo]
📍 LINEA: [linea]
📍 ERROR: [mensaje de error]

🔍 CAUSA RAIZ:
[explicacion]

🔧 FIX APLICADO:
[descripcion del cambio]

🧪 TEST DE REGRESION:
[descripcion del test creado]

✅ VALIDACION:
- [ ] Tests existentes pasan
- [ ] Test de regresion pasa
- [ ] Crash no se reproduce
```

## REGLAS
1. Fix + test de regresion son indivisibles
2. No refactorizar adicionalmente
3. Causa raiz, no solo sintoma
4. Fix minimalista
5. Documentar para el equipo
