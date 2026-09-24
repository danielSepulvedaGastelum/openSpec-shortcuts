# Artículo: Modificaciones avanzadas con Open Spec para evitar Spec Drift

Hasta ahora hemos trabajado con el camino rápido de OpenSpec: proponer un cambio con /`opsx:propose`, implementarlo con `/opsx:apply` y cerrarlo con `/opsx:archive`.

Ese flujo es suficiente para arrancar, pero se queda corto justo en el punto donde aparece el spec drift, cuando un cambio ya iniciado necesita modificaciones. Si esas modificaciones se hacen "a mano" (editando código sin tocar los artefactos, o retocando un artefacto sin regenerar los que dependen de él) la especificación y la implementación empiezan a contar historias distintas. Ese es exactamente el problema que vimos en la sesión anterior.

OpenSpec resuelve esto con un principio de diseño que conviene tener siempre presente: los comandos OPSX son acciones, no fases. No estás "en planificación" o "en implementación"; tienes un grafo de artefactos con dependencias (proposal → specs → design → tasks → código) y las herramientas te permiten volver a cualquier punto de ese grafo, modificarlo y propagar el cambio hacia adelante de forma controlada.

El perfil `core` esconde ese grafo detrás de un único comando; el perfil expandido te da acceso directo a cada acción. Para un equipo que quiere mantener las specs como fuente de verdad viva, el perfil extendido es la caja de herramientas anti-drift.

## Cómo activar el perfil extendido
Nosotros ya activamos el perfil extendido al comienzo del tema. El perfil global por defecto tras instalar OpenSpec es `core`, con cuatro comandos: `propose`, `explore`, `apply` y `archive`. Para habilitar los comandos del flujo expandido hay que hacer dos cosas:
`
```sh
openspec config profile   # seleccionar los workflows adicionales
openspec update           # regenerar los archivos de instrucciones en el proyecto
```

El primer comando modifica la configuración global (qué workflows quieres disponibles); el segundo regenera las skills y slash commands que OpenSpec instala para tu herramienta de IA (`.claude/skills/`, `.cursor/commands/`, etc.). Este segundo paso es fácil de olvidar y es la causa más habitual de que "los comandos nuevos no aparezcan". La sintaxis exacta varía según la herramienta: en Claude Code es `/opsx:comando`, mientras que Cursor, Windsurf o Copilot usan `/opsx-comando`. La intención de cada comando es idéntica en todas.

Con el perfil expandido activo, además de los cuatro comandos base disponemos de siete más: `new`, `continue`, `ff`, `verify`, `sync`, `bulk-archive` y `onboard`. Vamos con cada uno.

## /opsx:new - separar la creación del cambio de la generación de artefactos
`/opsx:new nombre-del-cambio` crea únicamente el andamiaje: el `directorio openspec/changes/<nombre>/` con su archivo de metadatos `.openspec.yaml` (que registra el schema del workflow y la fecha de creación). No genera ningún artefacto de planificación.

¿Por qué es útil? Porque `/opsx:propose` es en realidad dos decisiones fusionadas: "existe este cambio" y "genera ya toda su planificación". Al separarlas con `new`, el cambio queda registrado en el sistema desde el minuto cero (aparece en `openspec list`, tiene identidad, tiene schema) pero tú decides el ritmo y la profundidad de la planificación. Esto importa para el drift porque el registro temprano evita el patrón peligroso de "empiezo a tocar código y ya documentaré después", el cambio existe como entidad rastreable antes de que exista una sola línea de implementación.

**Cuándo usarlo**: siempre que quieras control incremental sobre los artefactos (seguido de `continue`) o cuando tengas clara la idea pero quieras revisar cada pieza de planificación antes de generar la siguiente. Un consejo práctico para tus proyectos, nombres descriptivos en kebab-case (`add-dark-mode`, `fix-login-redirect`) y nunca genéricos (`update`, `wip`), porque el nombre es lo que verás en los listados y en el archivo histórico. Admite además la opción `--schema` para usar un workflow distinto del `spec-driven` por defecto.

## /opsx:continue - el comando central para introducir modificaciones controladas
`/opsx:continue` es probablemente el comando más importante de todo el perfil expandido para el tema que nos ocupa.

**Su mecánica**: consulta el grafo de dependencias de artefactos del cambio, te muestra cuáles están completos, cuáles están listos para crearse y cuáles están bloqueados por dependencias pendientes, y crea el siguiente artefacto disponible leyendo antes los artefactos de los que depende.

Ese último detalle es la clave anti-drift. Cuando `continue` genera las specs, lo hace leyendo el proposal actual; cuando genera las tasks, lee las specs actuales. Esto significa que el flujo natural de trabajo con `continue` es: se crea un artefacto, tú lo revisas y lo editas si hace falta, y el siguiente artefacto se genera ya sobre la versión corregida. Las modificaciones se propagan hacia adelante por construcción, en lugar de quedar huérfanas en un documento que nadie vuelve a leer.

**Cuándo usarlo**: cambios complejos donde quieres validar cada artefacto antes de avanzar, situaciones donde el proposal necesita iteración con el equipo antes de derivar specs, o correcciones a mitad de camino. Si durante la implementación descubres que una spec estaba mal planteada, el procedimiento correcto no es editar el código y seguir: es corregir el artefacto y dejar que la cadena de dependencias regenere lo que corresponda. continue es la herramienta que hace ese procedimiento barato en lugar de tedioso.

## /opsx:ff - fast-forward cuando el alcance está claro
`/opsx:ff` (fast-forward) genera de golpe todos los artefactos de planificación pendientes, en orden de dependencias, leyendo cada dependencia antes de crear el siguiente artefacto, y se detiene cuando todo lo necesario para `apply` está completo.

La combinación `new` + `ff` produce un resultado equivalente a `propose`, pero con una diferencia práctica sutil: con `new` + `ff` el punto de entrada y la generación están desacoplados, así que puedes crear el scaffold, ajustar configuración o contexto, y lanzar la generación después.

También es el comando natural para "rellenar lo que falta": si un cambio tiene el proposal escrito (quizá editado a mano tras una revisión) y quieres generar el resto de la planificación de una vez, `ff` completa los artefactos pendientes respetando lo que ya existe.

**Cuándo usarlo**: cambios pequeños y medianos con requisitos claros, o cuando hay presión de tiempo y puedes describir el alcance completo de antemano. La regla que da la propia documentación es buena, si puedes describir el alcance completo desde el principio, usa `ff`; si lo vas descubriendo sobre la marcha, usa `continue`.

## /opsx:verify - el detector de spec drift
Si `continue` previene el drift durante la planificación, `/opsx:verify` lo detecta después de la implementación. Compara el código real con los artefactos del cambio buscando evidencia en el codebase, y evalúa tres dimensiones:

**Completitud**: todas las tasks marcadas, todos los requisitos de las specs con código correspondiente, todos los escenarios cubiertos (incluida cobertura de tests).

**Corrección**: la implementación respeta la intención de la spec, los casos límite de los escenarios están manejados, los estados de error coinciden con lo especificado.

**Coherencia**: las decisiones del design.md se reflejan en la estructura del código y las convenciones son consistentes.

El resultado se clasifica en CRITICAL, WARNING y SUGGESTION. Un ejemplo típico de warning de coherencia: "el design menciona variables CSS pero la implementación usa clases de Tailwind". Eso, literalmente, es spec drift capturado antes de archivar: o refactorizas el código para cumplir el design, o actualizas el design para reflejar la decisión real. Ambas salidas son legítimas; lo que no es legítimo es dejar la discrepancia viva y archivarla.

Importante para no generar falsas expectativas: `verify` no bloquea el archivado. Informa. La disciplina de actuar sobre sus avisos es del equipo. Cuándo usarlo: siempre antes de `archive`, y también como revisión del trabajo del agente antes de hacer commit. El flujo de cierre recomendado es `apply` → `verify` → `archive`.

## /opsx:sync - fusionar delta specs en las specs principales sin cerrar el cambio
Aquí conviene recordar la arquitectura de OpenSpec: las specs de un cambio no son copias completas, son delta specs con secciones ADDED, MODIFIED, REMOVED y RENAMED respecto a las specs principales de `openspec/specs/`.

`/opsx:sync` lee esos deltas y los fusiona en las specs principales de forma inteligente (puede añadir escenarios a un requisito existente sin duplicarlo y preserva todo el contenido no mencionado en el delta) dejando el cambio activo, sin archivarlo.

Es un comando marcado como opcional porque `archive` ya ofrece sincronizar si detecta deltas pendientes, y la mayoría de usuarios nunca lo invoca directamente. Pero hay tres escenarios donde llamarlo manualmente evita drift real. Primero, cambios de larga duración: si un cambio va a estar semanas abierto, sus specs ya validadas pueden fusionarse a las principales para que el resto del equipo (y el resto de agentes) trabajen sobre la fuente de verdad actualizada. Segundo, cambios paralelos que necesitan la base de specs actualizada: si el cambio B depende de requisitos introducidos por el cambio A, sincronizar A antes de planificar B evita que B se planifique contra specs obsoletas. Tercero, cuando quieres revisar la fusión como paso separado del archivado, por ejemplo en un pull request dedicado.

## /opsx:bulk-archive - archivado masivo con resolución de conflictos entre cambios
Cuando se trabaja con varios cambios en paralelo (algo que OpenSpec soporta explícitamente, cada uno aislado en su directorio) aparece un riesgo de drift particular: dos cambios completados que tocan la misma spec. Archivarlos uno a uno a mano puede hacer que el segundo pise contenido del primero.

`/opsx:bulk-archive` gestiona esto. Lista los cambios completados, valida cada uno, detecta conflictos de specs entre ellos y los resuelve de forma agéntica: inspecciona el codebase para comprobar qué está realmente implementado y fusiona en orden cronológico de creación, pidiendo confirmación antes de sobrescribir contenido.

Cuándo usarlo: al cerrar un sprint o una tanda de trabajo paralelo con varios cambios terminados. Es preferible a una secuencia manual de `archive` precisamente porque el análisis de conflictos se hace sobre el conjunto, no cambio a cambio.

## ¿Modificar el cambio existente o abrir uno nuevo?
Las herramientas anteriores resuelven el cómo modificar; queda el cuándo. La documentación oficial ofrece un criterio de decisión que encaja directamente con nuestra discusión sobre drift.

**Actualiza el cambio existente** (editando artefactos y propagando con `continue`/`ff`, o corrigiendo tras un `verify`) cuando la intención es la misma y solo se refina la ejecución: el alcance se recorta (primero el MVP), el codebase resultó no ser como esperabas, o la implementación reveló ajustes de diseño.

**Abre un cambio nuevo** cuando la intención ha cambiado de fondo, el alcance ha explotado hacia trabajo distinto, o el cambio original puede considerarse terminado por sí solo. Las preguntas guía: ¿es el mismo problema?, ¿hay más de un cincuenta por ciento de solape con el alcance original?, ¿podría archivarse el cambio original como "hecho" sin estas modificaciones? Si la respuesta a la última es sí, ciérralo (con `verify` + `archive` o dentro de un `bulk-archive`) y arranca uno nuevo con new.

## El flujo completo, de un vistazo
Con el perfil expandido, el ciclo de vida disciplinado de un cambio queda así: `explore` para investigar si hay dudas, `new` para registrar el cambio, `continue` (revisando cada artefacto) o `ff` (si el alcance está claro) para planificar, `apply` para implementar, `verify` para auditar la correspondencia entre artefactos y código, `sync` si el cambio es largo o hay trabajo paralelo que necesita las specs actualizadas, y `archive` (o `bulk-archive` para tandas) para cerrar fusionando los deltas en la fuente de verdad.

Cada comando del perfil expandido existe para que modificar un artefacto, propagar esa modificación y verificar que el código la respeta cueste un solo comando, no una tarde. Si toda modificación sobre un cambio en curso pasa por estas herramientas, las specs de `openspec/specs/` siguen siendo lo que prometimos que serían: la descripción fiel del sistema tal y como es.