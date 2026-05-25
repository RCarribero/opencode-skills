---
name: explorer-workflow
description: Skill de exploracion de codebases. Usar cuando @explorer necesite investigar y analizar rapidamente la estructura de un proyecto.
---

# Explorer Workflow

## PROPOSITO
Investigar y analizar codebases complejos rapidamente para dar contexto al equipo de desarrollo.

## TIPOS DE EXPLORACION

### 1. ESTRUCTURA
- Arquitectura del proyecto
- Modulos y dependencias
- Patrones de organizacion
- Convenciones de naming

### 2. FLUJOS
- Como se procesa una request
- Como fluyen los datos
- Puntos de entrada/salida
- Middlewares e interceptores

### 3. DEPENDENCIAS
- Librerias externas
- Modulos internos
- Acoplamiento entre componentes
- Dependencias circulares

### 4. CONFIGURACION
- Environment variables
- Archivos de config
- Build system
- CI/CD pipeline

## FLUJO DE TRABAJO

### 1. MAPEO INICIAL
- Leer estructura de directorios
- Identificar archivos clave
- Entender stack tecnologico

### 2. ANALISIS PROFUNDO
- Seguir flujos de datos
- Mapear dependencias
- Identificar patrones

### 3. DOCUMENTACION
- Crear mapa del codebase
- Documentar hallazgos clave
- Identificar areas de riesgo

### 4. ENTREGA
- Resumen estructurado
- Diagramas si aplica
- Recomendaciones

## PLANTILLA DE REPORTE
```
🗺️ EXPLORACION: [proyecto]
📊 NIVEL: quick | medium | very thorough

📁 ESTRUCTURA:
[descripcion de la arquitectura]

🔑 PUNTOS CLAVE:
- [hallazgo 1]
- [hallazgo 2]

🔗 DEPENDENCIAS:
[mapeo de dependencias criticas]

⚠️ AREAS DE RIESGO:
- [area 1]
- [area 2]

💡 RECOMENDACIONES:
- [sugerencia 1]
```

## HERRAMIENTAS
- `glob` para patrones de archivos
- `grep` para busquedas de contenido
- `read` para analisis de archivos
- `bash` para comandos de sistema

## REGLAS
1. Ser conciso pero completo
2. Priorizar informacion accionable
3. No asumir, verificar
4. Documentar hallazgos inesperados
5. Mantener contexto para otros agentes
