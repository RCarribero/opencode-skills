---
name: doc-architect-workflow
description: Skill de creacion de documentacion. Usar cuando @doc-architect necesite crear documentacion tecnica, READMEs o presentaciones.
---

# Doc Architect Workflow

## PROPOSITO
Crear documentacion completa, READMEs y presentaciones tecnicas de alta calidad, operando de forma independiente al ciclo de orquestacion.

## TIPOS DE DOCUMENTACION

### 1. README
- Descripcion del proyecto
- Instalacion y configuracion
- Uso basico
- Contribucion
- Licencia

### 2. DOCUMENTACION TECNICA
- Arquitectura del sistema
- API documentation
- Guías de desarrollo
- Decisiones arquitectonicas (ADRs)
- Troubleshooting

### 3. PRESENTACIONES TECNICAS
- Overview del proyecto
- Arquitectura y diseno
- Demostraciones
- Roadmap

## FLUJO DE TRABAJO

### 1. RECOPILACION
- Entender el proyecto a documentar
- Identificar audiencia objetivo
- Recopilar informacion tecnica
- Clarificar dudas

### 2. ESTRUCTURA
- Definir formato y estilo
- Crear outline
- Establecer jerarquia de informacion
- Planificar diagramas

### 3. CREACION
- Escribir contenido claro y conciso
- Incluir ejemplos de codigo
- Crear diagramas si aplica
- Usar markdown o formato solicitado

### 4. REVISION
- Verificar precision tecnica
- Confirmar claridad
- Validar ejemplos
- Revisar ortografia y gramatica

### 5. ENTREGA
- Organizar en carpeta estructurada
- Incluir todos los archivos necesarios
- Confirmar que cumple requerimientos

## ESTANDARES DE DOCUMENTACION

### README Template
```markdown
# Project Name

Brief description of what this project does.

## Installation
[Steps to install]

## Usage
[Basic usage examples]

## Contributing
[How to contribute]

## License
[License information]
```

### API Documentation
```markdown
## Endpoint Name
`METHOD /path/to/endpoint`

Description of what the endpoint does.

### Request
- Headers
- Body/Params
- Query params

### Response
- Success (200)
- Errors (4xx, 5xx)

### Example
[Request/Response example]
```

## REGLAS
1. Documentacion siempre actualizada
2. Ejemplos de codigo funcionales
3. Lenguaje claro y accesible
4. Diagramas cuando ayuden
5. Organizar en carpetas limpias
