# opencode-skills

Sistema de orquestación multi-agente para OpenCode.

## Estructura

```
opencode-skills/
├── agents/              # Definiciones de agentes (.md)
│   ├── orchestrator.md           # Orquestador principal
│   ├── codegen-frontend-*.md     # Frontend (coord + worker)
│   ├── codegen-backend-*.md      # Backend (coord + worker)
│   ├── crash-analyzer.md         # Diagnóstico de crashes
│   ├── refactor-*.md             # Refactoring (coord + worker)
│   ├── review-coordinator.md     # Code review
│   ├── test-*.md                 # Testing (coord + worker)
│   ├── explorer.md               # Exploración de codebase
│   ├── doc-architect.md          # Documentación
│   └── revisor.md                # Gate de calidad
├── skills/
│   ├── opencode/        # Skills built-in de OpenCode (26)
│   └── global/          # Skills globales instaladas (22)
├── docs/
│   └── flujo-orquestacion.md     # Documentación del flujo
├── opencode.jsonc       # Configuración de OpenCode
└── README.md
```

## Arquitectura

Jerarquía de 3 niveles:
- **Orquestador**: planifica, delega y supervisa — no toca archivos
- **Coordinadores**: dirigen trabajadores especializados (FE, BE, refactor, test, review)
- **Workers**: ejecutan tareas concretas (<150 líneas, una responsabilidad)

## Instalación

Los agentes y skills van en `~/.config/opencode/`:

```bash
cp -r agents/* ~/.config/opencode/agents/
cp -r skills/opencode/* ~/.config/opencode/skills/
cp opencode.jsonc ~/.config/opencode/opencode.jsonc
```

Las skills globales van en `~/.agents/skills/`:

```bash
cp -r skills/global/* ~/.agents/skills/
```
