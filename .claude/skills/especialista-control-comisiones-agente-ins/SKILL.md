---
name: especialista-control-comisiones-agente-ins
description: ESPECIALISTA CONTROL DE COMISIONES AGENTE INS (para OTROS agentes) — App web single-file, gratuita y sin backend, que JC ofrece a colegas agentes INS para que validen su pago de comisiones por quincena cargando el CSV oficial que entrega el INS (19 columnas, separador `;`, coma decimal, Latin-1/ISO-8859-1). Calcula totales y primas separando colones (MONEDA 01) de dólares (MONEDA 02), desglosa por línea (CLASE), grafica, detecta filas duplicadas y exporta Excel de resumen + detalle. Stack vanilla single-file `index.html` + Tailwind CDN + PapaParse + SheetJS + Chart.js + localStorage. Repo PRIVADO jhernandez-vibecode/control-comisiones-agente-ins, local C:\Users\segur\Desktop\control-comisiones-agente-ins. Marca "Powered by Seguros Digitales SDI". NO confundir con especialista-control-comisiones (app personal de ventas de JC, React+Google Sheets) ni con especialista-control-comisiones-jose (app de Jose Alonso, 3 dueños, 14 productos). Usar este skill cuando JC pida cualquier cambio, bug o mejora a la herramienta que les da a OTROS agentes para revisar el CSV de comisiones del INS.
---

# Especialista Control de Comisiones Agente INS (para otros agentes)

App web que JC les da a **otros agentes INS** (colegas, no él mismo) para que revisen su pago de comisiones por quincena sin tener que procesar el CSV a mano. Nació el **21 abr 2026**. Parte del ecosistema SDI.

**Por qué existe:** el INS entrega un CSV crudo de comisiones cada quincena. El agente promedio no sabe abrirlo bien (separador `;`, coma decimal, acentos rotos por el encoding). Esta app lo carga, lo cuadra y le dice cuánto le pagaron, por qué línea, y si hay filas repetidas.

🔴 **Este SKILL es la única fuente de verdad del proyecto.** No vive en el Claude Memory Engine (el engine es SASINS-only, regla permanente). El nodo de memoria que lo describía fue retirado y su contenido está acá.

## 🔴 Son TRES apps distintas de "comisiones" — no confundirlas

| Skill | Repo | De quién es | Stack |
|---|---|---|---|
| **`especialista-control-comisiones-agente-ins`** (**este**) | `jhernandez-vibecode/control-comisiones-agente-ins` | **OTROS agentes INS** (colegas de JC) | vanilla single-file + localStorage |
| `especialista-control-comisiones` | `jhernandez-vibecode/control-comisiones` | **JC**, sus propias ventas/comisiones | React 18 + Babel CDN + Google Sheets API |
| `especialista-control-comisiones-jose` | `vibecode-clients-lda/control-comisiones-agente-ins-jose` | **Jose Alonso** (reparto entre 3 dueños) | vanilla single-file + localStorage |

⚠️ Ojo especial con el repo de Jose: se llama `control-comisiones-agente-ins-jose`, o sea **este nombre + `-jose`**. Y su carpeta local está al lado de la de este proyecto en el Desktop. Verificar siempre el nombre completo.

🔴 **Si JC dice "Control de Comisiones" sin más contexto, PREGUNTAR cuál** antes de tocar nada.

## Identidad y recursos

- **Repo:** `jhernandez-vibecode/control-comisiones-agente-ins` — **PRIVADO** (según el nodo original; no verificado contra GitHub).
  - Publicado el 21 abr 2026 de noche. Camino que funcionó: JC creó el repo vacío en github.com/new y el push se hizo desde la línea de comandos. **GitHub Desktop falló silenciosamente** — no usarlo para este repo.
- **Local:** `C:\Users\segur\Desktop\control-comisiones-agente-ins\` — verificado 22-jul-2026: existe, working tree limpio, `main` == `origin/main`.
- **Producción:** ⏳ **PENDIENTE.** JC planea conectarlo a Netlify. No hay `netlify.toml` ni configuración de deploy en el repo. Hoy se usa abriendo `index.html` con doble clic.
- **Título en pantalla:** "Control de Comisiones Agente INS" — subtítulo "Validá tu pago de comisiones por quincena".
- **Marca:** pie "Powered by **Seguros Digitales SDI**" → `https://segurosdigitales.com`. Header con degradado azul (`from-blue-800 to-blue-950`).
- **Archivos del repo:** solo `index.html` (~650 líneas), `README.md`, `.gitignore`. No hay build, ni npm, ni carpetas.
- **Credenciales:** el proyecto **no maneja ninguna**. Sin API keys, sin OAuth, sin backend. Todo corre en el navegador del agente y nada sale a ningún servidor. Si alguna vez se le agrega un servicio, la clave va en variable de entorno de Netlify, nunca en el HTML.

### Historial de commits (todos del 21 abr 2026)

```
03f385a  Discriminación de moneda (colones/dólares) + primas + totales   ← HEAD, pusheado
1e93ba4  Fix: chart altura fija para evitar crecimiento con muchas líneas
1a8ee36  Rename to control-comisiones-agente-ins                          ← nombre final del repo
efb8bec  Rename to control-comisiones-agente
b302b90  Rename app to Control de Comisiones INS
eeb6758  Initial commit: Revisor de Comisiones INS
```

## Stack

Single-file `index.html`, todo por CDN, sin build ni npm:

| Librería | Versión / URL |
|---|---|
| Tailwind | `https://cdn.tailwindcss.com` — **sin pin de versión** |
| PapaParse | `papaparse@5.4.1` (jsDelivr) — parseo del CSV |
| SheetJS | `xlsx@0.18.5` (`xlsx.full.min.js`) — export Excel |
| Chart.js | `chart.js@4.4.0` (`chart.umd.min.js`) — gráfico de barras |

Tipografía: `system-ui` (sin Google Fonts). Sin React, sin Babel.

## 🔴 El CSV oficial del INS — esquema y reglas del parser

Esto es el corazón del proyecto. **19 columnas, en este orden exacto** (verificado contra el archivo real):

```
AGENTE;MONEDA;SEDE;CLASE;POLIZA;MODULO;NOMBRE;VIG_DESDE;VIG_HASTA;FEC_PAGADO;FEC_EMISIO;PRIMA;PAGO_REINT;INDICADOR;RECIBO;REINT_4__B;CENTRO_COS;FREC_PAGO;CEDULA
```

| Columna | Qué es | Formato observado |
|---|---|---|
| `AGENTE` | Código del agente | 8 dígitos con ceros a la izquierda. En el único archivo de muestra visto (`110129CR.csv`, 4.363 filas) es constante (`00000129`), lo que **sugiere** que el INS lo entrega ya filtrado por agente — pero eso NO está confirmado por JC ni por el INS: no lo asumas al programar |
| `MONEDA` | **Código de moneda** | `01` = colones ₡ · `02` = dólares $ |
| `SEDE` | Sede | 2 dígitos (`01`, `14`, `20`, `21`…) |
| `CLASE` | **Línea / ramo** — es lo que la app llama "línea" | 3 letras (`VTC`, `PCR`, `IMR`, `AUT`, `ARV`…) |
| `POLIZA` | Número de póliza | texto; **si viene vacío la fila se descarta** |
| `MODULO` | Módulo | 2 dígitos. En el archivo de muestra (4.363 filas) la mayoría son `00`–`04`, pero también aparecen sueltos `17`, `21`, `27`, `28` y `32` — **no asumir el rango 00–04 al validar** |
| `NOMBRE` | Nombre del cliente | texto con acentos → **por esto hace falta Latin-1** |
| `VIG_DESDE` / `VIG_HASTA` / `FEC_PAGADO` / `FEC_EMISIO` | Fechas | `AAAAMMDD` (8 caracteres, sin separadores) |
| `PRIMA` | **Prima** | `287348,00` — coma decimal, **sin separador de miles** |
| `PAGO_REINT` | **La COMISIÓN pagada** — es la cifra que le importa al agente | igual formato que PRIMA |
| `INDICADOR` | Indicador | valores observados: `C` y `D`. **Significado NO confirmado — no inventarlo.** La app hoy no lo usa |
| `RECIBO` | Número de recibo | texto |
| `REINT_4__B` | Reintegro | numérico, casi siempre `,00` (nótese el doble guion bajo del nombre) |
| `CENTRO_COS` | Centro de costos | texto |
| `FREC_PAGO` | Frecuencia de pago | valores observados: `A`, `S`, `M`, `D`. **Significado NO confirmado.** La app hoy no lo usa |
| `CEDULA` | Cédula del cliente | texto |

### Reglas del parser (no romperlas)

```js
Papa.parse(file, {
  header: true,
  delimiter: ';',          // 🔴 punto y coma, NO coma
  encoding: 'ISO-8859-1',  // 🔴 Latin-1 — sin esto los acentos salen rotos
  skipEmptyLines: true,
  transformHeader: h => h.trim(),
  ...
})
```

- **Trim a todos los valores** de tipo string después de parsear.
- **`parseNum(v)`** — convierte el número del INS: quita **todos** los puntos (separador de miles, defensivo) y cambia la **coma por punto**. `NaN` → `0`. Se aplica a `PRIMA`, `PAGO_REINT` y `REINT_4__B`.
- **Filtro de filas:** se descarta toda fila **sin `POLIZA`**. Si no queda ninguna → error "El CSV no tiene filas válidas. Revisá que sea un reporte de comisiones INS."
- **`fmtDate(s)`** — de `AAAAMMDD` a `DD/MM/AAAA`. Si el largo no es exactamente 8, devuelve el valor tal cual.
- **Moneda:** `01` → `Intl` `es-CR`/`CRC` con **0 decimales**; `02` → `en-US`/`USD` con **2 decimales**. Cualquier otro código cae a un formateador genérico ("Otra", símbolo `?`). ⚠️ **`MONEDA` vacío NO se trata igual en todos lados:** en agregación y filtros sí se asume `01` (`r.MONEDA||'01'` en `index.html:262, 275, 292`), pero la tabla de detalle y el Excel leen `r.MONEDA` crudo (`index.html:468, 476, 477, 527`) y caen al genérico "Otra"/`??` con 2 decimales. Inconsistencia real, no la documentes como si fuera uniforme.

### Mapa de códigos de línea (`LINE_NAMES` en `index.html`)

**Confirmados contra el CSV real:** `VTC` Vida Temporal Colectiva · `PCR` Pensión Colectiva · `IMR` Incendio Riesgos (Hogar) · `AUT` Automóviles · `ARV` Asistencia al Viajero.

El resto del mapa (`HOG`, `VID`, `SAL`, `VIA`, `INV`, `ACC`, `RES`, `PYM`, `MED`, `FGA`, `PER`, `MAR`, `TRN`, `NAV`, `MXE`, `PCA`, `PCB`, `PVM`, `EDU`, `TEC`, `TOD`, `ROB`, `CUM`, `FDL`) **no está confirmado contra ningún catálogo oficial del INS** — son nombres puestos al construir la app. Si un código no está en el mapa, la app muestra el código crudo (comportamiento correcto, no es bug). **Antes de dar por buena una traducción de código, confirmarla con JC o con documento INS.**

## Qué hace la app

1. **Carga del CSV** — botón o drag & drop sobre la zona de subida (solo `.csv`).
2. **Nombre y licencia del agente** en el header, persistidos en localStorage. Aparecen en el Excel exportado.
3. **4 tarjetas de resumen:** Colones (comisión + primas + N° pólizas), Dólares (idem), Registros totales, Duplicados.
4. **Desglose por línea Y moneda** — tabla con N°, primas, comisión y % sobre el total **de esa moneda**, con una fila `TOTAL <moneda>` al cierre de cada grupo.
5. **Gráfico de barras** comisión por línea, con **doble eje Y** (izquierda colones, derecha dólares). Los ejes/leyenda solo aparecen si esa moneda tiene datos.
6. **Detector de duplicados** — marca fila `DUP` en rojo cuando **dos filas son idénticas en todas sus columnas** (hash de todos los campos ordenados). No es dedupe por póliza.
7. **Filtros:** moneda · línea · "solo duplicados" · búsqueda por nombre o póliza. Todos se combinan.
8. **Tabla de detalle** — Línea, Mon, Póliza, Mód, Cliente, Pagado, Prima, Comisión, Recibo + bandera DUP. **Tope de 1000 filas renderizadas**; el pie dice cuántas hay realmente y los totales del filtro.
9. **Excel** (`Comisiones_<Agente>_<AAAA-MM-DD>.xlsx`) con 2 hojas: **Resumen** (encabezado SDI, agente, licencia, fecha, totales por moneda, desglose por línea) y **Detalle** (todas las columnas del CSV + `MONEDA_NOMBRE` + `DUPLICADO` SI/NO). **Respeta los filtros activos.**
10. **Botón Limpiar** — pide confirmación, borra el CSV de la quincena y resetea filtros. **Conserva nombre y licencia.**

### localStorage — claves exactas

| Clave | Contenido |
|---|---|
| `sdi_agente_nombre` | Nombre del agente |
| `sdi_agente_licencia` | Licencia del agente |
| `sdi_csv_data` | **El CSV completo parseado**, en JSON |

## Decisiones tomadas (con fecha)

- **21 abr 2026 — Público objetivo: OTROS agentes, no JC.** Deliberadamente separado del repo personal `control-comisiones` de JC.
- **21 abr 2026 — Simple para agente NO técnico.** Toda mejora se juzga contra esto. Nada de jerga, nada de configuración.
- **21 abr 2026 — 🔴 JC RECHAZÓ EXPLÍCITAMENTE la comparación con la quincena anterior.** No proponerla de nuevo salvo que él la pida.
- **21 abr 2026 (`03f385a`) — Discriminación de moneda.** Colones y dólares **nunca se suman entre sí**: tarjetas separadas, filas separadas en el desglose, ejes separados en el gráfico, filtro de moneda propio. Invariante del proyecto.
- **21 abr 2026 (`1e93ba4`) — Altura fija del gráfico.** El canvas vive en un contenedor `relative h-64` con `maintainAspectRatio: false`. Antes crecía sin control con muchas líneas. **No quitar la altura fija ni volver a `maintainAspectRatio: true`.**
- **21 abr 2026 — Datos sensibles fuera del repo.** `.gitignore` bloquea `*.csv`, `*.xlsx`, `*.xls`. Los CSV traen nombres y cédulas de clientes reales.
- **21 abr 2026 — Todo en el navegador.** Sin backend, sin envío de datos. Es el argumento de venta ante el colega que confía su cartera al archivo.

## Gotchas

- 🔴 **`.gitignore` bloquea CSV y Excel — nunca forzar un `git add -f` de esos archivos.** Traen nombre, cédula y prima de clientes reales de otro agente.
- 🔴 **El CSV de muestra vive en `C:\Users\segur\Downloads\110129CR.csv`** (682 KB, 4.363 filas, todas colones). **Downloads lo barren los limpiadores de disco** — si desapareció, pedirle a JC un CSV nuevo, no reconstruirlo. Nunca copiar su contenido a un skill, a un commit ni al chat.
- ⚠️ **El camino de dólares (`MONEDA` = `02`) nunca se probó con datos reales.** El CSV de muestra es 100% colones (`01`). La lógica USD está escrita pero sin verificar contra un archivo real del INS.
- ⚠️ **`sdi_csv_data` puede reventar la cuota de localStorage.** `saveData()` atrapa el error, escribe un `console.warn` y **sigue de largo sin avisarle al usuario** → el agente recarga la página y su quincena desapareció en silencio. Con CSV grandes (miles de filas) es plausible. Si JC reporta "se me borran los datos", mirar acá primero. **El localStorage no es fuente de la verdad: el CSV del INS lo es.**
- ⚠️ **Tailwind se carga desde `cdn.tailwindcss.com` sin pin de versión.** Un cambio mayor del CDN puede desmaquetar la app. Si aparece "se ve todo raro", sospechar del CDN antes que del código.
- ⚠️ **La tabla de detalle corta en 1000 filas.** Es a propósito (rendimiento), y el pie lo dice. **El Excel sí exporta todo lo filtrado** — no confundir un reclamo de "faltan filas" con un bug del export.
- ⚠️ **Duplicado = fila 100% idéntica.** Dos pagos legítimos de la misma póliza en fechas distintas NO se marcan. Si JC quiere detectar dobles pagos de la misma póliza, es una regla nueva, no un fix.
- ⚠️ **La comisión es la columna `PAGO_REINT`, no `PRIMA`.** Error fácil de cometer al tocar cálculos.
- ⚠️ **Los valores del CSV se inyectan con `innerHTML` sin escapar** (nombre de cliente, póliza, recibo). Observado en el código el 22-jul-2026, no estaba en el nodo original. Riesgo bajo hoy (el agente carga su propio archivo, todo es local), pero **si el proyecto sale a Netlify o llega a recibir archivos de terceros, escapar antes**.
- ⚠️ **GitHub Desktop falló silenciosamente con este repo.** Publicar y pushear por línea de comandos.

## Pendientes abiertos

1. **Deploy en Netlify** — JC lo planeaba desde el arranque y sigue sin hacerse. No hay subdominio asignado. Antes de publicarlo, resolver el escape de `innerHTML`.
2. **Confirmar el catálogo de códigos de línea del INS** — hoy solo 5 de 29 están verificados.
3. **Probar el camino de dólares** con un CSV real que traiga `MONEDA = 02`.
4. **Distribuir la herramienta a los colegas** — no hay registro de a quién se le entregó ni cómo.

## Reglas de trabajo en este proyecto

- **Español de Costa Rica.** JC es agente de seguros, **no programador**: entregar el código completo listo para copiar y pegar, o hacer el commit y push directamente desde el clon local (`C:\Users\segur\Desktop\control-comisiones-agente-ins\`) — eso último es lo que JC prefiere.
- **Simple.** El usuario final es un agente de seguros que no sabe qué es un CSV. Si una mejora agrega un paso, un concepto o una pantalla, probablemente no va.
- **Un solo archivo.** No partir `index.html` en módulos ni meter un build. La app tiene que funcionar con doble clic.
- **No inventar datos del INS.** Códigos, significados de columna y nombres de ramo se confirman con JC o con documento oficial; si no, se muestran crudos.
- **Antes de agregar algo, releer la sección donde va y fusionar ahí.** No apilar secciones nuevas al final de este skill.

---

> **Ubicaciones canon (desde el 6 sep 2026, decisión 10-C):** `jhernandez-vibecode/control-comisiones-agente-ins` → `.claude/skills/especialista-control-comisiones-agente-ins/SKILL.md` y `C:\Users\segur\.claude\skills\especialista-control-comisiones-agente-ins\SKILL.md`, byte-idénticas. Se edita en el repo, se commitea y se copia al user-level (o al revés, pero siempre las dos en el mismo día).
