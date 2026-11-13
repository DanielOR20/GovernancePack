# Governance Pack · guía paso a paso

Esta guía los lleva de principio a fin: qué van a entregar, cómo copiar la plantilla a su proyecto, cómo llenar cada archivo, cómo leer lo que dice el bot de verificación y qué hacer si algo falla. Síganla en orden. Si se pierden, vuelvan al **mapa** de abajo.

Se creó para el taller *Gobernanza de IA y de datos* del programa Business Solutions de Fundación Forward Costa Rica (13 de noviembre de 2026), pero sirve para cualquier proyecto.

---

## Contenido

1. [Qué van a entregar y por qué](#1-qué-van-a-entregar-y-por-qué)
2. [El mapa de la mañana](#2-el-mapa-de-la-mañana)
3. [Antes de empezar: lo que necesitan](#3-antes-de-empezar-lo-que-necesitan)
4. [Preparar el repo (pasos 1 a 4)](#4-preparar-el-repo-pasos-1-a-4)
5. [Llenar los archivos (pasos 5 a 10)](#5-llenar-los-archivos-pasos-5-a-10)
6. [El bot de verificación](#6-el-bot-de-verificación)
7. [Cómo leer el comentario del bot y qué corregir](#7-cómo-leer-el-comentario-del-bot-y-qué-corregir)
8. [Cerrar el reto (pasos 11 y 12)](#8-cerrar-el-reto-pasos-11-y-12)
9. [Problemas frecuentes](#9-problemas-frecuentes)
10. [Glosario](#10-glosario)
11. [Para ir más allá](#11-para-ir-más-allá)
12. [Referencias](#12-referencias)

---

## 1. Qué van a entregar y por qué

Al mediodía, en el repo de **su propio proyecto final**, va a haber un *Governance Pack*: seis archivos cortos que contestan por escrito cuatro preguntas sobre su sistema.

| Pregunta | Archivo que la contesta |
| --- | --- |
| ¿Quién decide: la IA o una persona? | `docs/adr/0002-que-decide-la-ia.md` |
| ¿Con qué datos trabaja el sistema? | `docs/gobernanza/inventario-de-datos.md` y `docs/adr/0003-donde-viven-los-datos.md` |
| ¿Quién responde por cada decisión? | El campo «Decide» de cada ADR y la columna «Responsable» del registro de riesgos |
| ¿Cómo se corrige cuando algo falla? | `docs/gobernanza/registro-de-riesgos.md` |

Además, dos archivos más:

- `docs/adr/0001-modelo-y-proveedor-de-ia.md`: qué modelo de IA usan y por qué.
- `AGENTS.md`: las reglas que cualquier agente de código (Claude Code, Codex, Copilot, Cursor) debe leer antes de tocar su repo.

**Reglas del reto**

- Se trabaja sobre su proyecto real, no sobre un caso inventado.
- Pueden usar IA para redactar. Pero **quien hace el commit responde por lo que dice**: si un ADR afirma algo que el código no hace, está mal.
- No es para nota. El reto está cumplido cuando el bot dice que la forma está completa, el checklist del PR está marcado y lo que escribieron es verdad en su código.
- Un pack corto y verdadero vale más que uno largo y genérico.

---

## 2. El mapa de la mañana

| Hora | Qué hacen | Pasos de esta guía |
| --- | --- | --- |
| 9:45 – 10:00 | Preparar el repo y abrir el PR | 1 a 4 |
| 10:00 – 10:25 | Inventario de datos | 5 |
| 10:25 – 11:05 | Los tres ADRs | 6 a 8 |
| 11:05 – 11:30 | Registro de riesgos | 9 |
| 11:30 – 11:45 | `AGENTS.md` | 10 |
| 11:45 – 12:00 | Revisar, corregir y cerrar | 11 y 12 |

**Si se atrasan**, prioricen en este orden: inventario, ADR 0002, registro de riesgos. Son los tres que más importan.

Barry y Steven pasan a revisar en tres momentos: a las 10:15 (inventario), a las 10:50 (ADR 0002) y a las 11:45 (el PR completo).

---

## 3. Antes de empezar: lo que necesitan

Revisen esta lista antes de las 9:45. Cada punto tiene cómo comprobarlo.

- [ ] **Su proyecto final está en GitHub, en su usuario.** Abran su repo en el navegador y confirmen que lo ven.
- [ ] **Tienen el repo clonado en su computadora.** Abran la terminal en la carpeta del proyecto y corran `git status`. Si responde algo como «On branch main», están en el lugar correcto.
- [ ] **Pueden hacer push.** Si ya subieron cambios a ese repo desde esa computadora, está listo.
- [ ] **Node.js instalado** (opcional, pero recomendado): `node -v` tiene que mostrar una versión, por ejemplo `v22.x`. Si no lo tienen, igual pueden hacer el reto: el bot de GitHub hace la misma revisión.
- [ ] **Si usan Windows:** usen **Git Bash** (viene con Git para Windows), no PowerShell ni CMD. Todos los comandos de esta guía están escritos para Git Bash, macOS y Linux.

---

## 4. Preparar el repo (pasos 1 a 4)

### Paso 1. Partir de la rama principal al día

En la terminal, dentro de la carpeta de su proyecto:

```bash
git status
```

Si dice que hay cambios sin guardar («Changes not staged» o «Untracked files»), hagan commit de ellos antes de seguir, o pregúntenle a un profe. Después:

```bash
git checkout main
git pull
```

Si su rama principal no se llama `main` (por ejemplo, `master`), usen ese nombre en todos los pasos.

**Cómo saben que salió bien:** `git status` dice «On branch main» y «nothing to commit, working tree clean».

### Paso 2. Crear la rama del pack

```bash
git checkout -b docs/governance-pack
```

Una rama es una copia de trabajo separada: lo que hagan aquí no toca su `main` hasta que ustedes lo decidan.

**Cómo saben que salió bien:** la terminal dice «Switched to a new branch 'docs/governance-pack'».

### Paso 3. Copiar la plantilla a su proyecto

Copien y peguen estas dos líneas, tal cual:

```bash
git clone --depth 1 https://github.com/gustmach-star/governance-pack-template /tmp/governance-pack
cp -Rn /tmp/governance-pack/docs /tmp/governance-pack/.github /tmp/governance-pack/AGENTS.md .
```

Qué hace cada una:

- La primera descarga esta plantilla a una carpeta temporal (`/tmp/governance-pack`), fuera de su proyecto.
- La segunda copia a su proyecto las carpetas `docs` y `.github` y el archivo `AGENTS.md`. La opción `-n` significa «no reemplazar»: si ya tenían un archivo con el mismo nombre, se queda el suyo.

Ahora comprueben qué se copió:

```bash
git status
```

Deberían ver archivos nuevos dentro de `.github/`, `docs/` y `AGENTS.md`.

**Si ya tenían alguno de estos archivos:**

| Ya tenían… | Qué pasa | Qué hacer |
| --- | --- | --- |
| `AGENTS.md` | No se reemplaza | Abran `/tmp/governance-pack/AGENTS.md` y copien sus secciones al final del suyo |
| `.github/pull_request_template.md` | No se reemplaza | En el paso 4 copien el checklist a mano en la descripción del PR (igual hay que hacerlo) |
| Otros workflows en `.github/workflows/` | Se mantienen | Nada: el del pack se suma a los suyos y no los toca |
| Una carpeta `docs/` | Se mantiene lo suyo | Nada: se agregan `docs/adr/` y `docs/gobernanza/` adentro |

**Si el comando `cp` les da error** (por ejemplo en Windows fuera de Git Bash): en esta página de GitHub, botón verde **Code → Download ZIP**, descomprímanlo y copien a mano las carpetas `docs` y `.github` y el archivo `AGENTS.md` a la raíz de su proyecto. Ojo: `.github` empieza con punto y puede estar oculta; en macOS se muestra con `Cmd + Shift + .`.

### Paso 4. Primer commit, push y abrir el PR desde ya

Abrir el PR al principio, aunque los archivos estén vacíos, tiene una ventaja: el bot les va a ir diciendo qué falta durante toda la mañana.

```bash
git add -A
git commit -m "Agregar plantilla del Governance Pack"
git push -u origin docs/governance-pack
```

La terminal va a mostrar un enlace que dice «Create a pull request for 'docs/governance-pack'». Ábranlo y:

1. Confirmen arriba: **base: `main`** ← **compare: `docs/governance-pack`**. El PR va dentro de su propio repo.
2. Título: `Governance Pack`.
3. **La descripción va a salir vacía.** Es normal: GitHub toma la plantilla de descripción de la rama principal, y ahí todavía no está. Abran el archivo [`.github/pull_request_template.md`](.github/pull_request_template.md) de esta plantilla, copien todo su contenido y péguenlo en la descripción.
4. Hagan clic en **Create pull request**.

**Cómo saben que salió bien:** en uno o dos minutos aparece abajo un check llamado «Verificar la forma del pack» en **rojo** y un comentario de **github-actions** con la etiqueta **Bot** que dice «faltan 12 cosa(s) por resolver». Que salga en rojo es lo esperado: todavía no llenaron nada. Si a los cinco minutos no aparece, vayan a [Problemas frecuentes](#9-problemas-frecuentes).

---

## 5. Llenar los archivos (pasos 5 a 10)

**Regla general para todos los archivos:** todo lo que está entre `<` y `>` es un **marcador** que tienen que reemplazar por su texto, borrando también los signos `<` y `>`. Por ejemplo, `Decide: <nombre y rol>` se convierte en `Decide: Ana Pérez, desarrolladora`. El texto entre `<!--` y `-->` son comentarios de ayuda: no se ven al publicar y pueden dejarlos o borrarlos.

Después de cada archivo, hagan commit y push (los comandos están en la [sección 8](#8-cerrar-el-reto-pasos-11-y-12)) y miren qué dice el bot.

### Paso 5. Inventario de datos (10:00 – 10:25)

**Archivo:** `docs/gobernanza/inventario-de-datos.md`

**Qué es:** una tabla con una fila por cada dato que su sistema guarda, recibe o envía a otro servicio, incluido el proveedor del modelo de IA. No se puede gobernar lo que no se sabe que se tiene.

**Cómo hacerlo:**

1. Recorran su base de datos (tablas y columnas), sus formularios y lo que le mandan al modelo de IA.
2. Por cada dato, llenen las 8 columnas. Ninguna puede quedar vacía: si algo no aplica, escriban «No» o «No aplica».
3. En la columna «¿Personal?» escriban **Sí** o **No**. Un dato es personal si identifica o puede identificar a una persona: nombre, correo, teléfono, cédula, foto, CV, ubicación.
4. Si es personal, en «Categoría» usen una de las cuatro de la Ley 8968 (artículo 9):

| Categoría | Qué incluye | Ejemplos |
| --- | --- | --- |
| Sensible | Origen racial o étnico, opiniones políticas, convicciones religiosas, salud, vida y orientación sexual. La ley también considera sensible la condición socioeconómica y la información biomédica o genética | Diagnóstico médico, ingresos del hogar, religión |
| Acceso restringido | De interés solo para la persona o la administración | Correo personal, teléfono, dirección, CV |
| Acceso irrestricto | Los que constan en bases públicas de acceso general | Nombre que aparece en un registro público |
| Crediticio | Comportamiento crediticio de la persona | Deudas, historial de pagos |

5. Debajo de la tabla, en «Lo que NO recolectamos», pongan al menos un dato que decidieron no pedir y por qué. Por ejemplo: «Cédula: no la necesitamos para recomendar ofertas». Esta sección muestra la **minimización de datos**: recolectar solo lo necesario.

**Preguntas que los ayudan:**

- ¿Qué le mando exactamente al proveedor de IA? Abran el código donde lo llaman y léanlo.
- ¿Los errores de mi aplicación se envían a algún servicio externo? ¿Pueden llevar correos o nombres?
- ¿Cuánto tiempo guardo cada dato? ¿Hay forma de borrarlo?

**Listo cuando:** la tabla tiene al menos 2 filas completas, cada dato personal tiene su categoría y la sección «Lo que NO recolectamos» tiene al menos una viñeta.

**Error común:** decir «mi proyecto no tiene datos personales». Si hay inicio de sesión, correo o un formulario de contacto, ya los tiene.

### Paso 6. ADR 0002: qué decide la IA (10:25 – 10:45)

Empiecen por este, porque es el más importante.

**Archivo:** `docs/adr/0002-que-decide-la-ia.md`

**Qué es un ADR:** un *Architecture Decision Record*, o registro de decisión de arquitectura. Es un documento corto que deja escrita una decisión: qué se decidió, por qué, qué se descartó y qué consecuencias tiene. Regla de oro: un ADR aceptado no se edita; si la decisión cambia, se escribe uno nuevo que lo reemplaza.

**Las partes de todo ADR:**

| Parte | Qué escribir | Lo que pide el bot |
| --- | --- | --- |
| Título | La decisión en una frase | Que no quede el marcador |
| Estado | `Propuesto` o `Aceptado` | Una de esas palabras (o «Reemplazado por ADR-XXXX») |
| Fecha | La de hoy | Formato exacto `2026-11-13` |
| Decide | Su nombre y su rol | No vacío |
| Contexto | El problema y las restricciones, en 3 a 5 líneas | Al menos una oración |
| Decisión | Qué se hace, en frases que se puedan comprobar | Al menos una oración |
| Alternativas descartadas | Otras opciones y por qué no | Al menos una viñeta que empiece con `- ` |
| Consecuencias | Qué se gana, qué se pierde, qué queda pendiente | Al menos una oración |
| Cómo se verifica | Dónde en el código o en las pruebas se ve que se cumple | Al menos una oración |

**Para el 0002, pregúntense:**

- ¿La IA ordena, filtra, puntúa, resume o recomienda algo sobre una persona?
- ¿Quién ve el resultado? ¿Puede corregirlo o reclamar?
- ¿Qué decisiones nunca se automatizan en mi sistema?

**Frases vagas contra frases verificables:**

| Vaga (está mal) | Verificable (está bien) |
| --- | --- |
| «Se usará la IA de forma responsable» | «La IA solo sugiere el texto; nada se publica sin que el usuario haga clic en Confirmar» |
| «Se cuidará la privacidad» | «Al modelo se envía solo el texto de la oferta, nunca el nombre ni el correo del usuario» |
| «Habrá supervisión humana» | «El botón Aprobar solo aparece para el rol administrador (ver `src/app/admin/...`)» |

**Ejemplo real**, de un portal de empleo en producción:

> La IA solo extrae campos a un esquema fijo; nunca puntúa, ordena, resume méritos ni descarta candidatos. El resultado se muestra para revisión y nada se publica sin confirmación de la persona.

**Si su proyecto no usa IA dentro del producto:** el 0002 se vuelve «qué decisiones del sistema nunca se automatizan».

### Paso 7. ADR 0001: modelo y proveedor de IA (10:45 – 10:55)

**Archivo:** `docs/adr/0001-modelo-y-proveedor-de-ia.md`

Mismas partes que el 0002. Contesten: ¿qué modelo y qué proveedor usan, para qué tareas, desde dónde se llama (servidor o navegador) y dónde está la llave de API? ¿Qué otras opciones consideraron? ¿Cuánto cuesta al mes, aproximadamente?

**Si su proyecto no usa IA dentro del producto:** el 0001 se vuelve «qué herramientas de IA usamos para programar y con qué reglas».

### Paso 8. ADR 0003: dónde viven los datos (10:55 – 11:05)

**Archivo:** `docs/adr/0003-donde-viven-los-datos.md`

Mismas partes. Contesten: ¿en qué servicio y en qué región se guardan los datos? ¿Qué se envía al proveedor de IA y qué nunca? ¿Cuánto se conservan y cómo se borran? ¿Quién puede leer cada tabla? Usen su inventario del paso 5: este ADR explica las decisiones detrás de esa tabla.

**Si en su proyecto hay una decisión más importante** (por ejemplo, cómo manejan el texto que un usuario sube y que llega al modelo), pueden escribir esa en lugar del 0003. Mantengan el número `0003` en el nombre del archivo y explíquenlo en la descripción del PR.

### Paso 9. Registro de riesgos (11:05 – 11:30)

**Archivo:** `docs/gobernanza/registro-de-riesgos.md`

**Qué es:** una tabla con al menos tres riesgos concretos de su sistema, cada uno con quién sale afectado y qué lo previene.

**Cómo llenar cada columna:**

| Columna | Qué escribir | Valores que acepta el bot |
| --- | --- | --- |
| # | R1, R2, R3… | Libre |
| Riesgo | Qué puede salir mal, de forma concreta | No vacío |
| Quién sale afectado | Personas u organización. No solo «qué se rompe» | No vacío |
| Probabilidad | Qué tan probable es | `B`, `M` o `A` (baja, media, alta) |
| Impacto | Qué tan grave sería | `B`, `M` o `A` |
| Mitigación | Qué lo previene o lo reduce | No vacío |
| Estado | Si la mitigación ya existe | `Hecho`, `En curso` o `Pendiente` |
| Responsable | Quién se encarga | No vacío |

**Riesgo genérico contra riesgo concreto:**

| Genérico (está mal) | Concreto (está bien) |
| --- | --- |
| «Hackeo» | «Un usuario sube un documento con instrucciones ocultas que manipulan al modelo» |
| «Fuga de datos» | «Los correos de los usuarios terminan en los logs de errores que se envían a un servicio externo» |
| «Costos» | «Un bucle en el chat dispara la factura del proveedor de IA» |

Para encontrar riesgos, usen las listas de OWASP (*Open Worldwide Application Security Project*, Proyecto Abierto y Mundial de Seguridad de Aplicaciones), que están en las [referencias](#12-referencias).

### Paso 10. AGENTS.md (11:30 – 11:45)

**Archivo:** `AGENTS.md`, en la raíz del repo.

**Qué es:** las reglas que lee cualquier agente de código antes de tocar su proyecto. Si una regla no está ahí, el agente no la conoce.

**Cómo hacerlo:**

1. Reemplacen los marcadores de la plantilla.
2. Agreguen en «Reglas propias de este proyecto» reglas que salgan de **sus** ADRs. Por ejemplo, si su ADR 0002 dice que la IA nunca publica sin confirmación, la regla es: «- Nunca agregar una ruta que publique contenido generado por IA sin el paso de confirmación del usuario».
3. Cada regla es una línea que empieza con `- `.

**Listo cuando:** el archivo tiene al menos 5 reglas y ningún marcador. Al menos algunas tienen que ser propias de su proyecto, no copiadas de la plantilla.

---

## 6. El bot de verificación

### Qué es

La plantilla trae un verificador automático. Técnicamente es un **workflow de GitHub Actions**: un archivo, `.github/workflows/governance-pack.yml`, que le dice a GitHub que corra un programa cada vez que pasa algo en su repo. Ese programa es `.github/governance-pack/verificar-pack.mjs`.

No tienen que instalar ni configurar nada. Se copió a su proyecto en el paso 3, y GitHub lo corre solo.

### Cuándo corre

Cada vez que **abren** el PR o **suben un cambio** (push) a la rama del PR, siempre que el cambio toque alguno de los archivos del pack. Tarda uno o dos minutos.

### Qué van a ver

En la página del PR:

1. **Un check** llamado «Verificar la forma del pack». En **rojo** (✗) si falta algo; en **verde** (✓) si la forma está completa.
2. **Un comentario** de **github-actions** con la etiqueta **Bot**. No es una persona: es GitHub publicando el resultado. El comentario tiene una sección por archivo, con lo que falta (casillas vacías) y lo que ya está bien (casillas marcadas).

Cada vez que suben un cambio, el bot deja un comentario nuevo. Lean siempre **el último**.

### Qué revisa y qué no

El bot revisa la **forma**: que esté lo que tiene que estar. No puede revisar la **verdad**: si lo que escribieron es cierto en su código.

| El bot SÍ revisa (la forma) | El bot NO revisa (la verdad) |
| --- | --- |
| Que existan los 6 archivos | Que lo que dicen sea cierto en su código |
| Que cada ADR tenga estado, fecha, quién decide y sus 5 secciones | Que la decisión sea buena |
| Que no queden marcadores como `<dato>` | Que el inventario tenga *todos* sus datos |
| Que los datos personales usen una categoría de la Ley 8968 | Que la categoría elegida sea la correcta |
| Que haya al menos 3 riesgos con afectados y mitigación | Que esos sean los riesgos importantes |
| Que `AGENTS.md` tenga al menos 5 reglas | Que las reglas sirvan para su proyecto |

**Ejemplo:** su ADR dice «el modelo solo se llama desde el servidor», pero en el código lo llaman desde el navegador. El bot lo marca en verde igual, porque la forma está completa. Por eso el verde del bot es **necesario, pero no suficiente**: la verdad la revisan ustedes y los profes.

### Probarlo en su computadora, sin esperar a GitHub

Si tienen Node.js, desde la raíz de su proyecto:

```bash
node .github/governance-pack/verificar-pack.mjs
```

Muestra en la terminal exactamente el mismo reporte que el bot comenta en GitHub, al instante. Úsenlo antes de cada push.

### Por qué es seguro tenerlo en su repo

El workflow sigue las reglas de seguridad vistas en el taller:

- Usa el evento `pull_request` y no `pull_request_target`, así que el código del PR nunca corre con los secretos de su repo.
- Tiene permisos mínimos: leer el código y comentar en el PR.
- La acción de terceros que usa (`actions/checkout`) está fijada a un commit exacto, no a una etiqueta que alguien podría mover.
- Nunca mete en un comando de terminal texto escrito por personas, como el título del PR.

Solo se dispara cuando cambian los archivos del pack, así que no interfiere con el resto de su proyecto. Cuando terminen pueden borrarlo sin ningún efecto.

---

## 7. Cómo leer el comentario del bot y qué corregir

Busquen en esta tabla el mensaje que les apareció:

| El bot dice… | Qué significa | Cómo se corrige |
| --- | --- | --- |
| «No existe el archivo» | Falta uno de los 6 archivos o está en otra carpeta | Revisen el paso 3 y que el nombre sea exacto |
| «Hay N ADR(s); se necesitan al menos 3» | Faltan ADRs o el nombre no tiene el formato `NNNN-nombre.md` | Los nombres empiezan con 4 dígitos: `0001-…md` |
| «Falta el ADR 0002» | No hay un archivo que empiece con `0002` | No le cambien el número al archivo |
| «la fecha debe tener formato AAAA-MM-DD» | La fecha falta o está en otro formato | Escriban `- Fecha: 2026-11-13` |
| «falta quién decide (nombre y rol)» | El campo «Decide» está vacío o con el marcador | `- Decide: Ana Pérez, desarrolladora` |
| «el estado debe ser Propuesto, Aceptado o Reemplazado…» | La palabra del estado no es una de esas | `- Estado: Propuesto` |
| «falta la sección «X»» | Borraron o renombraron un encabezado | Vuelvan a poner `## X` tal cual está en la plantilla |
| «la sección «X» está vacía o es demasiado corta» | Hay menos de una oración | Escriban al menos una oración completa |
| «Alternativas descartadas necesita al menos una opción en viñeta» | No hay ninguna línea que empiece con `- ` | `- Llamar al modelo desde el navegador: expone la llave.` |
| «queda un marcador de plantilla sin llenar: `<…>`» | Quedó un texto entre `<` y `>` | Busquen `<` en ese archivo y reemplácenlo |
| «Fila N: hay celdas vacías» | Una fila del inventario no tiene sus 8 columnas llenas | Llenen todas; si no aplica, escriban «No aplica» |
| «es dato personal, pero la categoría no es una de la Ley 8968» | Escribieron otra palabra en «Categoría» | Usen Sensible, Acceso restringido, Acceso irrestricto o Crediticio |
| «Falta la sección «Lo que NO recolectamos»» | Borraron ese encabezado | Vuelvan a poner `## Lo que NO recolectamos` con una viñeta |
| «la probabilidad / el impacto debe ser B, M o A» | Escribieron otra cosa | Usen una sola letra: B, M o A |
| «Fila N: falta riesgo / quién sale afectado / mitigación / responsable» | Esa celda está vacía | Llénenla |
| «Tiene N regla(s) en viñetas; se esperan al menos 5» | `AGENTS.md` tiene pocas reglas | Agreguen reglas, una por línea, empezando con `- ` |
| **Aviso**: «es dato sensible» | No es un error, es una alerta | Expliquen en un ADR por qué necesitan ese dato |
| **Aviso**: «No menciona `docs/adr/`» | No es un error | Agreguen la regla de leer los ADRs antes de cambiar código |

Los **avisos** no ponen el check en rojo. Los **errores** sí.

Después de corregir, suban el cambio (comandos de la sección 8) y esperen el nuevo comentario. No hace falta abrir otro PR.

---

## 8. Cerrar el reto (pasos 11 y 12)

### Cómo subir cada cambio (lo van a usar toda la mañana)

```bash
node .github/governance-pack/verificar-pack.mjs   # opcional: revisar antes de subir
git add -A
git commit -m "Completar inventario de datos"     # cambien el mensaje según lo que hicieron
git push
```

El PR se actualiza solo y el bot vuelve a revisar.

### Paso 11. Revisar la verdad (11:45 – 11:55)

Con el bot en verde, lean su pack una última vez contra su código y pregúntense, frase por frase: **¿esto es cierto hoy?** Si algo es una intención y no un hecho, márquenlo como **Pendiente** o cámbienle el estado al ADR a **Propuesto**.

### Paso 12. Marcar el checklist y avisar (antes de las 12:00)

1. En la descripción del PR, marquen cada casilla del checklist que se cumple.
2. Confirmen que el último comentario del bot dice «la forma del pack está completa».
3. Avísenle a Barry o a Steven que su PR está listo.
4. Después de la revisión, hagan **merge** del PR a su rama principal. Así el pack queda en `main` y, desde el próximo PR, la descripción ya trae el checklist sola.

---

## 9. Problemas frecuentes

**El check o el comentario del bot no aparece después de 5 minutos.**
1. Vayan a la pestaña **Actions** de su repo. Si ven el workflow «Governance Pack» corriendo o con error, hagan clic para ver el detalle y muéstrenselo a un profe.
2. Si la pestaña dice que Actions está desactivado: **Settings → Actions → General → Allow all actions and reusable workflows**, guardar, y suban un cambio cualquiera a un archivo del pack.
3. Confirmen que el PR es hacia la rama principal de **su propio** repo.

**El check está en rojo pero no hay comentario.**
Abran el check (clic en «Details»): el reporte completo aparece en el resumen del job. Sigan la tabla de la sección 7.

**`git push` me pide usuario y contraseña.**
GitHub no acepta la contraseña de su cuenta en la terminal. Si tienen GitHub CLI, corran `gh auth login`. Si no, creen un token en **GitHub → Settings → Developer settings → Personal access tokens** y úsenlo como contraseña.

**`node: command not found`.**
No tienen Node.js. No es grave: sáltense la verificación local y usen el comentario del bot en GitHub.

**`cp: … No such file or directory`.**
Probablemente el `git clone` del paso 3 falló o están en otra carpeta. Corran `ls /tmp/governance-pack`: tiene que mostrar `AGENTS.md`, `README.md` y `docs`. Si no, repitan la primera línea del paso 3.

**Ya existía `/tmp/governance-pack` y el clone dice «already exists».**
Bórrenla y repitan: `rm -rf /tmp/governance-pack`.

**Mi proyecto no usa IA dentro del producto.**
Igual la usaron para construirlo. El ADR 0001 se vuelve «qué herramientas de IA usamos para programar y con qué reglas», y el 0002, «qué decisiones del sistema nunca se automatizan».

**¿Puedo pedirle a una IA que llene el pack?**
Sí, pero quien hace el commit responde por lo que dice. Revisen cada frase contra su código: una IA no sabe qué hace su sistema hoy si no lo comprueba.

**Mi repo es privado. ¿Funciona igual?**
Sí. Los repos privados en cuentas personales tienen minutos gratis de GitHub Actions, y este workflow usa menos de uno por corrida.

---

## 10. Glosario

| Término | Qué significa |
| --- | --- |
| ADR | *Architecture Decision Record*, registro de decisión de arquitectura. Documento corto que deja escrita una decisión técnica |
| AGENTS.md | Archivo con las reglas que leen los agentes de código antes de tocar un repo. Es un estándar abierto |
| Branch (rama) | Copia de trabajo separada dentro de un repo. Lo que se hace en una rama no afecta a `main` hasta el merge |
| Bot | Programa que actúa solo en GitHub. Aquí, el verificador que comenta en su PR como **github-actions** |
| CI | *Continuous Integration*, integración continua: pruebas automáticas que corren con cada cambio |
| Commit | Un punto guardado en el historial de Git, con un mensaje que describe el cambio |
| GitHub Actions | El servicio de GitHub que corre workflows automáticamente |
| LLM | *Large Language Model*, modelo de lenguaje grande, como los que usan Claude, ChatGPT o Gemini |
| Ley 8968 | Ley de Protección de la Persona frente al tratamiento de sus datos personales de Costa Rica (2011) |
| Marcador | Texto entre `<` y `>` en la plantilla que hay que reemplazar |
| Merge | Unir los cambios de una rama a otra, normalmente a `main` |
| OWASP | *Open Worldwide Application Security Project*, Proyecto Abierto y Mundial de Seguridad de Aplicaciones. Publica listas de riesgos de seguridad |
| PR (pull request) | Solicitud para integrar los cambios de una rama a otra. Ahí se revisa y se discute antes del merge |
| Push | Subir sus commits de la computadora a GitHub |
| Workflow | Archivo en `.github/workflows/` que le dice a GitHub qué correr automáticamente y cuándo |

---

## 11. Para ir más allá

Si terminan antes, o para después de graduarse. Estas prácticas aparecen en proyectos con IA en producción:

| Práctica | Para qué sirve |
| --- | --- |
| PRD (*Product Requirements Document*, documento de requisitos de producto) con los límites de la IA | Deja escrito qué debe hacer el producto y, sobre todo, qué no debe hacer. Los ADRs pueden citar los requisitos del PRD por su código |
| Runbooks | Qué hacer, paso a paso, cuando algo falla: un pico de gasto en IA, una caída, una filtración |
| Bitácora de lecciones | Qué pasó y qué hacer en su lugar. Si una lección cambia una regla, la regla va al ADR o al `AGENTS.md` |
| Banderas de funcionalidad | La función con IA está apagada hasta que alguien con autoridad la aprueba |
| Proveedor detrás de una interfaz propia | Cambiar de modelo o de proveedor sin reescribir el sistema |
| Medir el costo de la IA | Gasto por usuario, presupuesto mensual y alerta |
| Revisiones automáticas periódicas | Vulnerabilidades y dependencias revisadas cada semana |

Retos extra para hoy:

- Un cuarto ADR sobre cómo manejan el texto que el usuario envía al modelo.
- Una prueba automatizada que compruebe alguna regla de su `AGENTS.md`.

---

## 12. Referencias

- Nygard, M. (2011). [Documenting Architecture Decisions](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions). El texto que dio origen a los ADRs.
- [MADR](https://adr.github.io/madr/) (*Markdown Architectural Decision Records*), plantilla abierta de ADR.
- [AGENTS.md](https://agents.md/), estándar abierto.
- [OWASP Top 10 for LLM Applications](https://genai.owasp.org/llm-top-10/) y [OWASP Top 10 for Agentic Applications for 2026](https://genai.owasp.org/resource/2025-12-09/owasp-top-10-for-agentic-applications-for-2026).
- [Ley 8968 de Costa Rica](https://www.micitt.go.cr/sites/default/files/marco_juridico_legal/08.%20Ley%20n.%C2%B0%208968%20Ley%20de%20Protecci%C3%B3n%20de%20la%20Persona%20frente%20al%20tratamiento%20de%20sus%20datos%20personales..pdf), artículos 3, 5 y 9.

Autor: Gustavo Machado Loría.
