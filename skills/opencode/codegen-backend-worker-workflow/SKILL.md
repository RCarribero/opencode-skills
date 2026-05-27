---
name: codegen-backend-worker-workflow
description: Skill de worker de generacion de codigo backend. Usar cuando @codegen-backend-worker necesite implementar APIs o logica de negocio.
---

# CodeGen Backend Worker Workflow

## PROPOSITO
Implementar APIs, modelos y logica de negocio asignados por `@codegen-backend-coordinator` con calidad y siguiendo buenas practicas.

## RESPONSABILIDADES
- Escribir codigo limpio y mantenible
- Seguir las convenciones del proyecto
- Incluir comentarios solo cuando sea necesario para explicar el "por que"
- Manejar errores de forma robusta
- Escribir codigo testeable

## FLUJO DE TRABAJO

### 1. COMPRENSION DE LA TAREA
- Leer cuidadosamente los requerimientos del coordinator
- Identificar archivos a modificar/crear
- Entender el contexto y dependencias

### 2. IMPLEMENTACION
- Escribir el codigo siguiendo las especificaciones
- Mantener funciones pequenas (<50 lineas idealmente)
- Usar nombres descriptivos para variables y funciones
- Aplicar principios SOLID

### 3. VALIDACION LOCAL
- Verificar sintaxis correcta
- Asegurar que los imports son correctos
- Confirmar que no hay variables sin usar
- Revisar manejo de errores

### 4. ENTREGA
- Reportar que archivos se crearon/modificaron
- Incluir breve explicacion de los cambios
- Sugerir tests necesarios si no estan especificados

## PATRONES DE CODIGO

### Funciones
```typescript
// Mal
function processData(data: any): any { ... }

// Bien
function parseUserInput(raw: string): ValidationResult { ... }
```

### Manejo de errores
```typescript
// Siempre validar inputs
if (!input) throw new ValidationError('Input required')

// Usar Result pattern cuando aplique
function safeParse(json: string): Result<ParsedData, Error> { ... }
```

## REGLAS
1. Nunca usar `any` en TypeScript sin justificacion
2. Preferir composicion sobre herencia
3. Usar `pnpm` para cualquier dependencia
4. No hardcodear valores magicos (usar constantes)
5. Escribir codigo que se pueda testear facilmente
