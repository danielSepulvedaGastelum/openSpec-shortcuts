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


---
---
---
# CONFIGURAR EL ARCHIVO DE CONSTITUCIÓN: CONFIG.YML

Todo lo que le pidamos al agente usando OpenSpec inyectará de forma **Depertiminista** lo que se encuentra en el archivo `config.yml` en el apartado `context:`

Así es como lo da por Default:
```yml
schema: spec-driven

# Project context (optional)
# This is shown to AI when creating artifacts.
# Add your tech stack, conventions, style guides, domain knowledge, etc.
# Example:
#   context: |
#     Tech stack: TypeScript, React, Node.js
#     We use conventional commits
#     Domain: e-commerce platform

# Per-artifact rules (optional)
# Add custom rules for specific artifacts.
# Example:
#   rules:
#     proposal:
#       - Keep proposals under 500 words
#       - Always include a "Non-goals" section
#     tasks:
#       - Break tasks into chunks of max 2 hours

# Per-operation guidance (optional)
# Add advisory guidance for how apply and archive work should be conducted.
# This is separate from artifact rules above.
# Example:
#   operations:
#     apply:
#       guidance:
#         - Keep test summaries concise
#     archive:
#       guidance:
#         - Summarize the archive outcome before finishing

```

Este es un ejemplo de como puede ser un archivo de configuración `config.yml` para un proyecto:

```yml
schema: spec-drivenschema: spec-driven

# Se inyecta en TODAS las generaciones de artefactos. Conciso a propósito.
context: |
  CartaYa: carta digital con pedidos para una cafetería de barrio española
  (La Estación). Cliente: Andrés, el dueño, sin perfil técnico.

  Stack técnico (fijado; no se renegocia en propuestas):
  - Backend: Node.js 22 con Express y better-sqlite3 (base de datos SQLite
    en un único fichero, suficiente para una cafetería).
  - Frontend: React 19 con Vite, servido como estáticos por el propio
    Express. CSS propio, sin frameworks CSS pesados.
  - Tiempo real: Server-Sent Events (no polling, no WebSockets).
  - Tests: suite automatizada; cada escenario de las specs debe tener un
    test trazable por el nombre del escenario.
  - Autenticación: sesión simple con contraseña de establecimiento (no hay
    multiusuario). Sin Docker en desarrollo.

  Principios innegociables:
  1. Idioma y mercado: todo el producto (interfaz, textos, specs, commits,
     documentación) en español de México. Precios en pesos mexicanos con IVA incluido.
  2. Simplicidad ante todo: es una aplicación para una cafetería de barrio,
     no una plataforma. Ante dos soluciones, siempre la más simple.
  3. Privacidad por diseño: el cliente final NUNCA se registra ni aporta
     datos personales. Un pedido se asocia a una mesa, jamás a una persona.
  4. La spec es la fuente de verdad: todo cambio de comportamiento
     observable del sistema debe entrar como cambio de OpenSpec (propuesta
     + deltas) o regularizarse como tal. Un comportamiento que
     openspec/specs/ no describe es un defecto, aunque el código funcione.
  5. Accesibilidad real: la carta la usan personas mayores en un móvil a
     contraluz: tipografía legible, contraste alto, botones grandes. Los
     alérgenos se muestran siempre de forma visible (obligación legal).

# Reglas por artefacto: solo se inyectan al generar ese artefacto.
rules:
  specs:
    - Redacta en español conservando la palabra normativa inglesa de cada
      requisito (MUST/SHALL), p. ej. "El sistema MUST mostrar todos los
      platos activos"
    - Reutiliza los términos ya definidos en las specs vivas; no introduzcas
      sinónimos nuevos para conceptos existentes
  proposal:
    - Registra cada decisión de negocio en proposal.md con su fecha
  design:
    - Ninguna dependencia nueva sin justificación escrita
```

La sección de **'rules'** solo se inyecta en el contexto de la conversación cuando se genera un artefacto en concreto, por ejemplo:
- spec: 
- proposal:
- desig: 

---
---
---
# GENERAR UNA PROPUESTA

Lo que implementa siempre *openspec* son **'CAMBIOS'** por la misma metodologia de *Spec Driven* que estamos usando de **spec anchored**

- Se sugiere seguir el mismo esquema de preespicificación:
    - Que queremos y porque
    - Reglas del Negocio
    - Fuera del alcance

```sh
$openspec-propose 

add-carta-digital — La carta digital pública de CartaYa para
la cafetería La Estación. Es la pantalla que ve un cliente al escanear el
QR de su mesa (el flujo de pedido será otro cambio; este cubre solo la
consulta de la carta y la administración del catálogo).

Qué queremos y por qué: sustituir las cartas plastificadas, siempre
desactualizadas, por una carta digital que el dueño mantiene al día desde
su panel y que el cliente consulta desde el móvil sin instalar nada.

Reglas de negocio:
- La carta se organiza en categorías definidas por el dueño (desayunos,
  bocadillos, raciones, bebidas, postres...), en el orden que él decida y
  con orden manual de platos dentro de cada categoría.
- La carta muestra todos los platos activos de cada categoría. Un plato
  "activo" es un plato que el dueño no ha eliminado del catálogo; los
  platos eliminados no aparecen jamás, pero se conservan archivados en el
  sistema (los pedidos históricos los referencian).
- Cada plato muestra: nombre, precio (pesos, IVA incluido), descripción
  corta, foto opcional y sus alérgenos. Los alérgenos se muestran SIEMPRE
  y de forma visible en todos los platos, con los 14 alérgenos de
  declaración obligatoria de la normativa europea; un plato sin alérgenos
  declara explícitamente "sin alérgenos" (la ausencia de información no es
  una opción legal).
- La carta es pública: no requiere identificación para consultarse, y
  carga en menos de 2 segundos en un móvil de gama media con 4G.
- El dueño gestiona el catálogo (crear, editar, archivar platos y
  categorías, subir fotos con límite de tamaño) desde una pantalla de
  administración protegida con la sesión de establecimiento, pensada para
  usarse desde un móvil.

Fuera de alcance: el pedido desde la mesa (siguiente cambio), el panel de
cocina, precios por franjas horarias, múltiples idiomas, y cualquier forma
de cuenta o registro del cliente final (prohibido por el principio 3
de config.yaml).
```

Que archivos principales genera este comando:
- proposal.md
- design.md
- tasksmd

## proposal.md
Este archivo es el que nos indica el **Porque** y **que** se debe implementar en lenguaje de negocio, igual de como leeriamos una especificación, esto es lo que nos pidió nuestro cliente.

- **Why**: el porque se está haciendo este Feature
- **What Changes**: Que cambios tiene que hacer
- **Capabilities**: Capacidades que tiene que implementar
- **Impact**: el impacto que tiene este cambio
    - Crear un nuevo Backend
    - Crear un nuevo Frontend
    - Implementar Autenticación
    - etc.

## design.md

Este archivo es el enfoce técnico que debe seguir todo el Stack tecnologico en nuestro config.yml, es el equivalente al **plan** de *Github-Speckit*.

- **Context**: El el contexto general
- **Goals**: Los objetivos 
- **Decisions**: las desiciones tecnicas
- **Migration Plan**: el Plan de migración


## tasks.md

Este archivo es el equivalente a las **tasks** que genera tambien *Github-Speckit*, es el listado de implementación de [], son todos los pasos en nun solo paso.

## /changes/'Nombre de la Spec'/specs/'Cambio a realizar'

En esta carpeta se generaran el *Delta*, los cambios que se aplicaran en las especificaciones vivas existentes

- **spec.md**:  Son los requerimientos que propone que añadir, por eso siempre empiexan con **'Add'**

> ⚠️ **Advertencia:** 
> Haz commit con los cambios.

---
---
---
# PREVIO a Implementación

Antes de implementar es recomendable que se realice algo similar al **Clarify**, pero en *OpenSpec* no existe, por lo que se recomienda este prompt:

```sh
Antes de implemtentar, lista las anbigüedades de la propuesta que requieren desición de negocio
```
Te enlistará, preguntará o te mostrará las propuestas de ambigüedades, las cuales es recomendable que se analicen 1 por 1 y responderle cuales se deben modificar en los archivos que ya tenemos genrados como: proposal.md, design.md, tasks.md, etc.

Además si no ejecuta el sigueinte comando es recomendable que se ejecuta, para que valide el formato, la estructura
- **openspec validate**: es el comando
- **add-carta-digital**: es el  nombre de nuestra carpeta de la spec
- **--strict**:  flag para que lo haga en modo estricto
```sh
openspec validate add-carta-digital --strict
```
> ⚠️ **Importante:** Recuerda que puede ser que el agente lo haya ejecutado por ti, verifica eso antes de lanzar este comando.


> ⚠️ **Advertencia:** 
> Haz commit con los cambios.

Se recomienda que en el archivo 'AGENTS.md' o 'CLAUDE.md' se agrege instrucciones para que despues de ejecutar el comando **$openspec-propose** se genere un commit en GIT

---
---
---
# IMPLEMENTACIÓN

1. Primero que nada es necesario Abrir una nueva conversación para realizar la implementación con el siguiente comando:

```sh
$openspec-apply-change
```

Este proceso puede tardar un poco ya que es cuando genera todos los archivos de código de implemetación.

> ⚠️ **Advertencia:** 
> Haz commit con los cambios.

---
2. Despues de una implementación grande, es remondable usar este comando para que verifique la implementación:

```sh
$openspec-verify-change
```
 
---
3. Si la tarea es correcta y no se va a iterar con algo de esta especificación se procede a archivar la especificación con el siguiente comando:

```sh
$openspec-archive-change
```
[$openspec-verify-change](C:\\Desarrollo\\sdd_curso\\tema4\\cartaya\\.agents\\skills\\openspec-verify-change\\SKILL.md) 
> ⚠️ **Advertencia:** 
> Esto probablemente se deba hacer antes, o el agente lo puede sugerir:
```text
Es probable que pregunte si antes de archivar se sincronicen las especificaciones Delta hacia las Specs Activa, que se hace con el comando:
```

```sh
$openspec-sync-specs
```
> ⚠️ **Advertencia:** 
> Haz commit con los cambios.