---
name: codegen-frontend-worker-workflow
description: Skill de worker de generacion de codigo frontend. Usar cuando @codegen-frontend-worker necesite implementar componentes o paginas.
---

# CodeGen Frontend Worker Workflow

## PROPOSITO
Implementar componentes frontend y paginas asignados por `@codegen-frontend-coordinator` con calidad y siguiendo buenas practicas.

## RESPONSABILIDADES
- Escribir codigo limpio y mantenible
- Seguir las convenciones del proyecto
- Incluir comentarios solo cuando sea necesario para explicar el "por que"
- Manejar errores de forma robusta (estados vacio, error, loading)
- Escribir codigo testeable

## FLUJO DE TRABAJO

### 1. COMPRENSION DE LA TAREA
- Leer cuidadosamente los requerimientos del coordinator
- Identificar archivos a modificar/crear
- Entender el contexto y dependencias

### 2. IMPLEMENTACION
- Escribir el codigo siguiendo las especificaciones
- Mantener componentes pequenos (<150 lineas idealmente)
- Usar nombres descriptivos para variables y componentes
- Aplicar principios SOLID y de composicion

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

### Componentes
```typescript
// Mal
function Card(props: any) { ... }

// Bien
interface CardProps { title: string; children: React.ReactNode; }
function Card({ title, children }: CardProps) { ... }
```

### Manejo de estados
```typescript
type Status = 'idle' | 'loading' | 'success' | 'error'
const [status, setStatus] = useState<Status>('idle')
```

## REGLAS
1. Nunca usar `any` en TypeScript sin justificacion
2. Preferir composicion sobre herencia
3. Usar `pnpm` para cualquier dependencia
4. No hardcodear valores magicos (usar constantes)
5. Escribir codigo que se pueda testear facilmente
