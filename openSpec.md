# Gúia de Shorcuts y manual para el uso de OpenSpec
---

# INSTALACIÓN

1. Buscar en Google el repositorio de GitHub Spec kit
```text
https://github.com/Fission-AI/openspec
```

---
2. Ahora instalar openSpec con este comando, cambiando el X.Y.Z por la versión mas actual en el repositorio
```sh
npm install -g @fission-ai/openspec@latest
```

---
3. para verificar que se haya instalado bien ejecutar comando
```sh
openspec 
```

Nos muestra toda la ayuda de comandos para esta herramienta
```sh
Usage: openspec [options] [command]

AI-native system for spec-driven development

Options:
  -V, --version                      output the version number
  --no-color                         Disable color output
  -h, --help                         display help for command

Commands:
  init [options] [path]              Initialize OpenSpec in your project
  update [options] [path]            Update OpenSpec instruction files
  list [options]                     List items (changes by default). Use --specs to list specs.
  view [options]                     Display an interactive dashboard of specs and changes
  change                             Manage OpenSpec change proposals
  archive [options] [change-name]    Archive a completed change and update main specs
  spec                               Manage and view OpenSpec specifications
  config [options]                   View and modify global OpenSpec configuration
  schema                             Manage workflow schemas [experimental]
  store                              Create and manage stores - standalone OpenSpec repos you register on this machine
  doctor [options]                   Report relationship health for the resolved OpenSpec root
  context [options]                  Print the working context for the resolved OpenSpec root
  workset [options]                  Compose, keep, and open personal working views (purely local)
  validate [options] [item-name]     Validate changes and specs
  show [options] [item-name]         Show a change or spec
  feedback [options] <message>       Submit feedback about OpenSpec
  completion                         Manage shell completions for OpenSpec CLI
  status [options]                   Display artifact completion status for a change
  instructions [options] [artifact]  Output enriched instructions for artifacts, apply, or archive
  templates [options]                Show resolved template paths for all artifacts in a schema
  schemas [options]                  List available workflow schemas with descriptions
  new                                Create new items
  help [command]                     display help for command
```

---
4. Crear una carpeta del proyecto e ir a esa carpeta
```sh
mkdir tema4
cd  /c/Desarrollo/curso-sdd/tema4
```

---
5. iniciar el proyecto con git
```sh
git init
```

---
6. iniciar el proyecto con lo siguiente con openspec
```sh
openspec init --tools codex
```

```sh
✔ Setup complete for Codex

OpenSpec Setup Complete

Created: Codex
6 skills in .agents/
Commands skipped for: codex (uses skills)
Config: openspec/config.yaml (schema: spec-driven)

Getting started:
  Start your first change: $openspec-propose "your idea"

Note: 6 more workflows are available (new, continue, ff, bulk-archive, verify, onboard).
Add them with `openspec config profile`.

Learn more: https://github.com/Fission-AI/OpenSpec
Feedback:   https://github.com/Fission-AI/OpenSpec/issues
```
> ⚠️ **Importante:** 
> El resultado indica que se crearon 6 skills en .agents/

.

> ⚠️ **Advertencia:** 
> Tambien recomiendo que se reinicie el IDE de trabajo

---
7. Configurar openspec, para configurar un perfil avanzado, para que nos de un par de comandos de skills adicionales, seleccionar:

```sh
? What do you want to configure?
❯ Delivery and workflows
  Delivery only
  Workflows only
  Keep current settings (exit)
```

```sh
? What do you want to configure?
  Delivery and workflows
❯ Both (skills + Commands) [current]
  Skills only
  Commands only
```

```sh
 [x] Propose change
 [x] Explore ideas
 [x] New change
 [x] Continue change
 [x] Apply tasks
 [x] Update change
 [x] Fast-forward
 [x] Sync specs
 [x] Archive change
 [x] Bulk archive
 [x] Verify change
 [x] Onboard
```

```sh
? Apply changes to this project now? (Y/n)
```

---
8. Para forzar a una actualización mas reciente OpenSpec:

```sh
openspec update
```
> ⚠️ **Advertencia:** 
> Esto no es necesario, solo es una anotación