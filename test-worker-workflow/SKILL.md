---
name: test-worker-workflow
description: Skill de worker de testing. Usar cuando @test-worker necesite implementar tests unitarios y de integracion especificos.
---

# Test Worker Workflow

## PROPOSITO
Implementar tests especificos asignados por `@test-coordinator` siguiendo las mejores practicas de testing.

## ESTRUCTURA DE TESTS

### Unit Tests
```typescript
describe('functionName', () => {
  it('should return expected value with valid input', () => {
    // Arrange
    const input = 'valid'
    
    // Act
    const result = functionName(input)
    
    // Assert
    expect(result).toBe(expected)
  })
  
  it('should throw error with invalid input', () => {
    // Arrange
    const input = null
    
    // Act & Assert
    expect(() => functionName(input)).toThrow('Error message')
  })
})
```

### Integration Tests
```typescript
describe('API Endpoint', () => {
  it('should return 200 with valid request', async () => {
    // Arrange
    const payload = { valid: 'data' }
    
    // Act
    const response = await request(app)
      .post('/endpoint')
      .send(payload)
    
    // Assert
    expect(response.status).toBe(200)
    expect(response.body).toHaveProperty('expectedField')
  })
})
```

## FLUJO DE TRABAJO

### 1. COMPRENSION
- Leer requerimientos del coordinator
- Identificar que se debe testear
- Entender dependencias y mocks necesarios

### 2. IMPLEMENTACION
- Seguir patron Arrange-Act-Assert
- Un concepto por test
- Naming descriptivo
- Mocks minimalistas

### 3. VALIDACION
- Ejecutar tests localmente
- Confirmar que pasan
- Verificar que fallan cuando deben (probar negativo)

### 4. ENTREGA
- Reportar tests creados
- Incluir cobertura del archivo
- Confirmar que todos pasan

## PATRONES DE MOCKING

### Funciones externas
```typescript
vi.mock('./externalModule', () => ({
  externalFunction: vi.fn().mockReturnValue('mocked')
}))
```

### Servicios
```typescript
const mockService = {
  method: vi.fn().mockResolvedValue({ data: 'mocked' })
}
```

## REGLAS
1. Tests deben ser independientes
2. No tests que dependan de orden de ejecucion
3. Usar `pnpm` para dependencias
4. Tests rapidos (<1s cada uno idealmente)
5. Cobertura significativa, no solo lineas
