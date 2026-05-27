---
name: refactor-worker-workflow
description: Skill de worker de refactoring. Usar cuando @refactor-worker necesite aplicar cambios concretos de mejora de codigo.
---

# Refactor Worker Workflow

## PROPOSITO
Aplicar cambios de refactoring concretos y atomicos sin alterar el comportamiento externo del codigo.

## TIPOS DE REFACTORING

### 1. RENAMING
- Variables, funciones, clases con nombres confusos
- Hacer explicito el proposito

### 2. EXTRACTION
- Funciones muy largas → funciones pequenas
- Codigo duplicado → funcion compartida
- Condicionales complejos → funciones con nombre

### 3. SIMPLIFICATION
- Anidamiento excesivo → guard clauses
- Logica compleja → patrones mas simples
- Dependencias innecesarias → eliminacion

### 4. REORGANIZATION
- Archivos muy grandes → modulos separados
- Responsabilidades mezcladas → separacion clara
- Imports desorganizados → estructura limpia

## FLUJO DE TRABAJO

### 1. COMPRENSION
- Leer el codigo a refactorizar
- Entender su proposito y comportamiento
- Identificar tests existentes

### 2. IMPLEMENTACION
- Aplicar un solo tipo de refactoring a la vez
- Mantener cambios pequenos (<50 lineas)
- Preservar interfaz publica

### 3. VALIDACION
- Ejecutar tests existentes
- Verificar que nada se rompio
- Confirmar mejora de legibilidad

### 4. ENTREGA
- Reportar archivos modificados
- Explicar el cambio realizado
- Confirmar que tests pasan

## PATRONES COMUNES

### Guard Clauses
```typescript
// Antes
function process(user: User) {
  if (user) {
    if (user.active) {
      // logica
    }
  }
}

// Despues
function process(user: User) {
  if (!user?.active) return
  // logica
}
```

### Extract Function
```typescript
// Antes
function calculateOrder(order: Order) {
  const subtotal = order.items.reduce(...)
  const tax = subtotal * 0.21
  const shipping = subtotal > 50 ? 0 : 5.99
  return subtotal + tax + shipping
}

// Despues
function calculateOrder(order: Order) {
  return calculateSubtotal(order) + calculateTax(order) + calculateShipping(order)
}
```

## REGLAS
1. Un solo tipo de refactoring por tarea
2. Tests deben pasar siempre
3. No añadir funcionalidad nueva
4. Mantener compatibilidad hacia atras
5. Documentar el "por que" del cambio
