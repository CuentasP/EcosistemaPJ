# Ecosistema PJ Venezuela — Resumen de continuidad

Este documento es para pegar/adjuntar en una conversación nueva de Claude y seguir operando el proyecto sin perder contexto.

## Qué es esto

Un mapa interactivo de Venezuela que mide la actividad en Instagram de las cuentas institucionales de Primero Justicia (regionales, municipales, juveniles y nacionales), con drill-down por estado → municipio. Construido en 3 archivos HTML autocontenidos (Leaflet + GeoJSON embebidos, sin dependencias externas) y publicados como Artifacts.

## Los 3 artefactos (URLs — usar `url:` al republicar para mantener el mismo link)

**2026-09-03: reorganización de carpeta.** Todo el proyecto vive ahora en `/Users/andreseduardog/Code/ecosistema_pj/` (antes estaba suelto en `~/Code`). Además, existían dos versiones del mapa nacional (`mapa_venezuela.html` del 2-sep y `mapa_venezuela_v2.html` del 3-sep, esta última ya publicada como el artifact vigente `86dc890e`) — la v2 es la que manda; la v1 se archivó para no perder historial.

| Archivo local | Artifact URL | Contenido |
|---|---|---|
| `ecosistema_pj/mapa_venezuela.html` (era `mapa_venezuela_v2.html`) | https://claude.ai/code/artifact/86dc890e-6685-4da5-ade1-712e724f98e3 | Mapa nacional, 24 estados + Esequibo, dashboard, drill-down a municipios |
| `ecosistema_pj/mapa_zulia.html` | https://claude.ai/code/artifact/7787057a-fd71-4866-a6dd-c723e1048f43 | Detalle completo de Zulia (municipios + Regional/Juvenil) |
| `ecosistema_pj/mapa_miranda.html` | https://claude.ai/code/artifact/b3ed4e61-8619-4ee4-9e41-44f99894d083 | Detalle completo de Miranda (municipios + Regional/Juvenil) |

**Archivado (no usar):**
- `ecosistema_pj/archive/mapa_venezuela_v1_20260902.html` — versión nacional anterior a la reorganización, superada por la de arriba.
- Artifact `https://claude.ai/code/artifact/2764fe0c-1f63-4476-a87c-80a182c37a7f` — versión vieja del mapa nacional, quedó un paso atrás del `86dc890e` vigente. No republicar aquí.

**Nota:** el artifact `https://claude.ai/code/artifact/ca741d76-e41c-4d0c-a702-1ecc0991ba50` ("El Mapa de Primero Justicia") es un **proyecto aparte**, no relacionado con Ecosistema PJ Venezuela — no tocar ni confundir con los 3 de arriba.

**Favicon al republicar:** 🗺️ (mantener el mismo emoji siempre).

Solo Zulia y Miranda tienen página de detalle individual. Los otros 18 estados con datos solo existen dentro del drill-down del mapa nacional (no tienen artifact propio).

## Metodología de scraping (repetir exactamente así)

1. Actor de Apify: `apify/instagram-post-scraper` (MCP `989f86bd-...`, tool `call-actor`).
2. Input: `{"username": ["<handle_sin_@>"], "resultsLimit": 50, "onlyPostsNewerThan": "<fecha>", "dataDetailLevel": "basicData"}`
3. Ventana de fechas actual: **23–31 ago 2026**. Para una nueva corrida, cambiar `onlyPostsNewerThan` a la fecha de corte que corresponda.
4. **Truco clave**: al scrapear el perfil de una cuenta regional, el actor no solo trae sus posts propios — también trae posts de OTRAS cuentas que la etiquetaron. Así es como se descubren las cuentas municipales.
5. **Regla de oro confirmada por el usuario**: al calcular los totales de una cuenta específica (para el dashboard, para "Regional"/"Juvenil"/etc.), **contar solo los posts donde `ownerUsername` sea exactamente esa cuenta** — nunca sumar lo que otras cuentas etiquetadas publicaron.
6. Filtrar posts fuera de ventana (algunos "pinned" se cuelan con fecha vieja — excluirlos).
7. "Publicaciones" = posts + reels sumados sin distinguir (Instagram mezcla ambos en el grid del perfil; se identifican por `productType: "clips"` = reel). Esto quedó como pregunta abierta del usuario, sin resolver — si se retoma, decidir si separar.
8. Clasificación institucional: username contiene `pj`, `primero`, `justicia` o `jovenes` (con juicio para variantes estilizadas como `pr1mero`). Cuentas personales (activistas/candidatos sin ese patrón) se excluyen **siempre**, aunque sean muy activas. El usuario puede pedir excepciones explícitas (ej. sumar una cuenta aunque no matchee la regla).
9. La cuenta nacional `@pr1merojusticia` NO se suma a los totales de ningún estado/municipio — se trackea aparte en "Cuentas nacionales".

## Inventario completo de cuentas (estado actual, todas ya scrapeadas)

### Cuentas nacionales (sección "Cuentas nacionales" del mapa, siempre visible salvo que se haga clic en un estado)
| Cuenta | Rol | Posts | Likes | Coment. | Nota |
|---|---|---:|---:|---:|---|
| @pr1merojusticia | Primero Justicia (nacional) | 9 | 22,639 | 799 | Solo posts propios |
| @jovenespj | Jóvenes PJ (nacional) | 0 | 0 | 0 | Sin publicaciones propias en la ventana |
| @pjregiones | PJ Regiones | 0 | 0 | 0 | Funciona como cuenta de colaboraciones aceptadas, no publica contenido propio |
| @pr1merojusticia.mundo | Primero Justicia Mundo (diáspora) | 0 | 0 | 0 | Cuenta privada o vacía (`no_items`) |

### Zulia — Regional: @pjzulia (4/1194/79) · Juvenil: @jovenespjzulia (1/121/9)
| Municipio | Cuenta |
|---|---|
| Maracaibo | @maracaibopj |
| Jesús María Semprún | @pjsemprum |
| Santa Rita | @pjsantaritaz |
| Baralt | @pj_baralt |
| Rosario de Perijá | @pjlavilla |

### Miranda — Regional: @primerojusticiamiranda (1/414/19) · Juvenil: ninguna detectada
| Municipio | Cuenta |
|---|---|
| Guaicaipuro | @pr1merojusticiaguaicaipuro |
| Páez | @primerojusticia_paez |
| Brión | @primerojusticia.brion |
| Paz Castillo | @pazcastillopj |
| Plaza | @mpj_guarenas |
| Zamora | @primerojzamora |
| Carrizal | @pjcarrizal |
| Chacao | @pjchacao |

### Los otros 18 estados con datos
| Estado | Regional | Juvenil | Otras cuentas | Municipales |
|---|---|---|---|---|
| Amazonas | @amazonasprimerojusticia (2/10/3) | — | — | — |
| Anzoátegui | @primerojusticiaanzoategui (6/1262/33) | — | — | Bolívar: @primerojusticiabarcelona · Simón Rodríguez: @pr1merojusticiaeltigre001 |
| Apure | @comunic.apure (6/276/15) | @jovenespj_apure (2/79/4) | @activismoapurepj (1/52/0), @ddhhoficialapure (0/0/0) | — |
| Aragua | @pjaraguaoficial (2/320/4) | — | — | Girardot: @girardotprimerojusticia · Santiago Mariño: @pjmarino_aragua · José Angel Lamas: @pjlamas_oficial |
| Barinas | @pj_barinas (4/464/13) | — | — | — |
| Bolívar | @primerojusticiabolivar (8/425/13) | — | — | Angostura: @pjangosturadelorinoco |
| Carabobo | @pjcarabobo1 (1/117/2) | — | — | San Diego: @pr1merosandiego |
| Cojedes | @pjcojedesoficial (2/179/10) | — | — | — |
| Falcón | @pjfalcon (1/17/0) | — | — | — |
| Guárico | @pjguarico1 (3/48/1) | — | — | — |
| Lara | ninguna (0 posts propios) | — | — | Palavecino: @pj.palavecino |
| Mérida | ninguna (0 posts propios) | — | — | Alberto Adriani: @pj.vigia · Libertador: @pj.libertador · Tovar: @pjtovar |
| Nueva Esparta | @pjneregional (6/394/31) | — | — | García: @primeroj_garcia · Antolín del Campo: @justicieros_antolin_del_campo |
| Portuguesa | @pr1merojusticiaportuguesa (4/352/9) | — | — | — |
| Sucre | ninguna | @jovenespjsucre_ (2/83/6, municipio Sucre) | — | — |
| Vargas | @mpjvargas (7/260/8) — mismo dato que el único municipio "Vargas" | — | — | (estado de un solo municipio) |
| Yaracuy | @pjyaracuy1 (17/2352/59) | — | — | Bruzual: @pjmunicipiobruzual |
| Distrito Capital | @pr1merojusticia.ccs (1/181/3) — mismo dato que el único municipio "Libertador" | — | — | (estado de un solo municipio) |

### Estados sin actividad esta semana (rojo — cuenta existe, se scrapeó, salió vacía)
- **Monagas** — @pjmonagas (solo 1 post y estaba fuera de la ventana de fechas)
- **Trujillo** — @somospjtrujillo (`no_items`, privada o vacía)
- **Táchira** — @pjtachira (0 posts propios). Antes se usaba la cuenta personal de Karim Vera (@kar1mvera) como voz operativa, pero el usuario pidió quitarla — Táchira quedó sin dato real.

### Sin cuenta conocida (gris — nunca se ha scrapeado, no hay handle)
- **Delta Amacuro**

### Guayana Esequiba
No se scrapea (no es territorio administrado por Venezuela). Se muestra en el mapa con relleno de rayas diagonales, sin comparar en la escala de actividad.

## Capas adicionales descubiertas el 2026-09-03 (no documentadas hasta ahora)

Al editar `mapa_venezuela.html` se encontraron dos objetos JS que ya existían en el archivo pero nunca se documentaron aquí — evidentemente se agregaron en alguna sesión anterior sin actualizar este resumen:

- **`stateParroquiasData`** — nivel parroquia, debajo del municipio. Solo tiene datos para Zulia por ahora (`Maracaibo`: 9 cuentas, `Cabimas`: 3 cuentas). Sus posts/likes/comments **sí suman** al total del municipio correspondiente en `stateMuniData` (y por tanto al total del estado en `statesData`) — son cuentas institucionales de nivel parroquial, no una capa aparte.
- **`stateDirigentesData`** — cuentas personales de dirigentes/activistas del partido, una lista por estado. A diferencia de las parroquias, estas **NO suman** a `statesData` ni a la escala de color del mapa ni al dashboard nacional — es una capa puramente informativa, consistente con la regla original de excluir cuentas personales de los totales institucionales.

Cuentas agregadas en la sesión del 2026-09-03 (pedidas explícitamente por el usuario). **Ojo:** la primera pasada usó mal la ventana (23-ago→hoy en vez de 1-31 ago) — la tabla ya refleja los números corregidos con la ventana correcta:
| Cuenta | Nombre | Ubicación | Capa | Posts | Likes | Coment. |
|---|---|---|---|---:|---:|---:|
| @pj.juanadeavila | Primero Justicia \| Juana de Ávila | Maracaibo, Zulia (parroquia) | `stateParroquiasData.Zulia.Maracaibo` | 9 | 442 | 25 |
| @nohegutierrezm | Carmen Nohelia Gutierrez | Altagracia de Orituco, Guárico | `stateDirigentesData.Guárico` (dirigente) | 17 | 2057 | 140 |
| @eciogut | Ecio Gutierrez | Municipio Bolívar, Yaracuy | `stateDirigentesData.Yaracuy` (dirigente, coordinador municipal según su bio) | 2 | 56 | 2 |

Nohelia resultó mucho más activa de lo que la ventana corta había mostrado (17 posts reales en agosto vs. 4 detectados con la ventana equivocada) — el usuario lo notó al ver el número de likes demasiado bajo.

Cuentas agregadas en la sesión del 2026-09-05 (pedidas explícitamente por el usuario, vía links de Instagram):
| Cuenta | Nombre | Ubicación | Capa | Posts | Likes | Coment. | Nota |
|---|---|---|---|---:|---:|---:|---|
| @amarcosvoy | Marcos Montilla | San Rafael de Carvajal, Trujillo | `stateDirigentesData.Trujillo` (dirigente, exalcalde de Carvajal) | 13 | 33 | 1 | El usuario pidió explícitamente contar **solo publicaciones de política** de esta cuenta — de 140 posts en la ventana ago-2026, la mayoría son deportes, reflexiones religiosas, condolencias e historia local; se contaron solo la columna de opinión "Moviendo la Esperanza" (10 entregas) y 3 notas de prensa de PJ Trujillo |
| @mariluzrojaspj | Mariluz Rojas | Estado Bolívar | `stateDirigentesData.Bolívar` (dirigente, Presidenta Adjunta de PJ Bolívar) | 2 | 55 | 4 | Identificada a partir de un link a un post suyo (`DcFA1otRijz`) que el usuario pasó sin username — se resolvió con el scraper de post directo. De 11 posts donde aparece etiquetada, solo 2 tienen `ownerUsername` = mariluzrojaspj; el resto son de @primerojusticiabolivar/@pjangosturadelorinoco (no se suman, regla de oro) |

Como ambas son cuentas personales de dirigentes, no se tocaron `stateMuniData`/`statesData` (la capa de dirigentes nunca suma a los totales institucionales).

Como Juana de Ávila sí suma al total institucional, se actualizaron también `stateMuniData.Zulia` (Maracaibo: 71→80 posts, 5205→5647 likes, 509→534 comentarios) y `statesData` (Zulia: 116→125 posts, 12247→12689 likes, 946→971 comentarios). Los dirigentes de Guárico y Yaracuy no requirieron tocar esos agregados.

**Ventana de fechas correcta para este proyecto: 1-31 de agosto de 2026** (no 23-31 ago como decía una versión anterior de este documento) — usar `onlyPostsNewerThan: "2026-08-01"` y filtrar manualmente cualquier post con timestamp posterior al 31 de agosto, ya que el actor no tiene un filtro de fecha superior.

**Pendiente:** `mapa_zulia.html` (el detalle dedicado de Zulia) probablemente tiene su propia copia de los datos de Maracaibo/parroquias — no se actualizó en esta sesión, así que puede quedar desalineado con el mapa nacional hasta que se revise.

## Dashboard actual (números vigentes al cierre de esta sesión)
- Cuentas regionales activas: **17**
- Cuentas municipales activas: **27**
- Likes totales: **34,038**
- Publicaciones totales: **133**
- Ecosistema más activo: **Yaracuy** (19 posts · 2,456 likes · 65 coment.)

## Detalles de diseño/técnicos por si hay que tocar el código
- Leaflet 1.9.4 inlineado completo (CSS+JS) en cada archivo — los Artifacts no pueden cargar CDNs externos.
- GeoJSON de estados/municipios de Venezuela sacado de GADM (vía gist `CamonZ/3384966`), simplificado y "limpiado" con buffer morfológico para evitar líneas internas espurias.
- Colores: escala amarilla `#fde68a → #fde047 → #eab308 → #ca8a04` (activo, relativa al máximo/mínimo de engagement), gris `#4b5563` (sin datos), rojo `#b91c1c` (sin actividad esta semana), Esequibo con patrón de rayas SVG (`#3b4d63`/`#9fb4d1`).
- Bordes blancos, grosor 0.6 (1.5 en hover).
- Mapa no interactivo (sin drag/zoom de usuario) — el zoom solo ocurre programáticamente al hacer clic en un estado (drill-down) o al volver ("Ver Venezuela completa").
- Hay un bug conocido y ya resuelto: `requestAnimationFrame` para el `fitBounds` inicial podía quedarse colgado en pestañas en segundo plano → se cambió a `setTimeout(fitMapToBounds, 30)`.
- La columna central del panel lateral es dinámica: por defecto muestra "Cuentas nacionales" (Primero Justicia + Jóvenes PJ + PJ Regiones + PJ Mundo); al hacer clic en un estado cambia a "[Estado] — Regional y Juvenil" (+ "Otras cuentas" si aplica); "Volver a Venezuela completa" restaura la vista nacional.

## Para continuar en la próxima sesión
1. Pega este documento como contexto inicial.
2. Si quieres una nueva corrida (ventana de fechas nueva), hay que re-scrapear **cada** cuenta de la tabla de arriba con el mismo método, actualizar los objetos JS embebidos (`statesData`, `stateMuniData`, `stateRegionalData`, `stateJuvenilData`, `stateOtherData`, `stateParroquiasData`, `stateDirigentesData` — estos dos últimos no se actualizan solos, hay que sumar sus cambios manualmente a `stateMuniData`/`statesData` solo en el caso de parroquias, nunca para dirigentes) y republicar con `Artifact` pasando la `url` correspondiente para no perder el link.
3. Pendientes que quedaron abiertos sin resolver: separar posts de reels (o no), decidir si Delta Amacuro tiene alguna cuenta que aún no se ha buscado, y si vale la pena crear páginas de detalle individuales (como Zulia/Miranda) para los otros 18 estados.
