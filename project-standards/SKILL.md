---
name: project-standards
description: Estandares de proyecto para dependencias, estructura de tests y commits. Cargar antes de cualquier tarea que implique dependencias, configuracion o estructura de proyecto JS/TS.
---

# Project Standards

## GESTION DE PAQUETES (JavaScript / TypeScript)
- Usa SIEMPRE `pnpm`. Nunca `npm` ni `yarn`.
- Si el proyecto no tiene pnpm: ejecuta `pnpm init` y migra dependencias.
- Lockfile: `pnpm-lock.yaml`. Si existe `package-lock.json` o `yarn.lock`, eliminalos tras migrar.
- Instalar dependencia: `pnpm add <pkg>` / dev: `pnpm add -D <pkg>`

## ESTRUCTURA DE TESTS
- Tests unitarios: junto al archivo con sufijo `.test.ts` o en `__tests__/`
- Tests de integracion: en `tests/` en la raiz del proyecto
- Framework: vitest (ya presente en el proyecto, no instalar otro)
- Ejecutar: `pnpm test` o `pnpm vitest run`

## COMMITS
- Formato: `type(scope): descripcion` — feat, fix, refactor, test, docs, chore
- Un commit por subtarea completada y aprobada por @revisor
- No commits parciales ni work-in-progress sin marcar como WIP
