# PocketFlow: manual en español, desde cero

**Para aprender a hacer música sin haber usado LGPT, LSDJ ni M8.**

Este manual acompaña a **PocketFlow**, el fork de PocketTracker de este repositorio. Conserva los nombres ingleses que aparecen en las pantallas para que puedas encontrarlos en la app, pero explica su significado en español. No necesitas saber programar ni aprenderte todos los comandos antes de empezar.

**Base revisada:** código 0.9.8 del commit [`0ee080c`](https://github.com/imLeGEnDco55/PocketFlow/tree/0ee080c9136452e5be5377f9c4f7510ae6452a73). Las instrucciones de Android se refieren a esa base con el branding PocketFlow. Algunas opciones dependen de la plataforma o de la compilación. La revisión de este documento se hizo contra el código y la documentación del repositorio; no es una prueba de todos los controles en un teléfono físico.

> **Si hoy solo quieres hacer algo que suene:** lee del 1 al 7. Después consulta las demás secciones cuando encuentres una palabra desconocida. TABLE, MIDI y los efectos avanzados pueden esperar.

## Índice

1. [Qué es un tracker y cómo pensar en PocketFlow](#concepto)
2. [La estructura: canción, cadenas, frases e instrumentos](#estructura)
3. [Números raros, notas y tiempo musical](#numeros)
4. [Botones, navegación y reproducción](#controles)
5. [Preparar Android, carpetas y sonidos](#preparacion)
6. [Tu primer beat, paso a paso](#primer-beat)
7. [Añadir bajo, variaciones y una canción](#primera-cancion)
8. [Copiar, clonar, rellenar y deshacer](#edicion)
9. [SONG, CHAIN y PHRASE a fondo](#secuenciador)
10. [Instrumentos: sampler, SoundFont y externo](#instrumentos)
11. [Editar audio, ajustar loops y cortar slices](#samples)
12. [MODS: envolventes y movimiento](#modulacion)
13. [TABLE: un secuenciador dentro del instrumento](#tablas)
14. [GROOVE y SCALE: ritmo y tonalidad](#groove-escalas)
15. [MIXER, reverb, delay y EQ](#mezcla)
16. [Referencia completa de comandos FX](#efectos)
17. [Recetas musicales para practicar](#recetas)
18. [PROJECT: guardar, exportar y resamplear](#proyecto)
19. [Archivos, copias de seguridad y plantillas](#archivos)
20. [SETTINGS, temas y controles físicos](#ajustes)
21. [MIDI explicado desde cero](#midi)
22. [LIVE: tocar con las cadenas](#live)
23. [Problemas frecuentes y cómo resolverlos](#problemas)
24. [Ruta de aprendizaje y chuleta](#aprendizaje)
25. [Glosario](#glosario)
26. [Fuentes y alcance de esta revisión](#fuentes)

<a id="concepto"></a>
## 1. Qué es un tracker y cómo pensar en PocketFlow

Un **tracker** es un secuenciador musical en el que escribes eventos en una tabla que se lee **de arriba hacia abajo**. Cada fila representa un momento. Una fila puede decir: «toca este bombo», «toca esta nota del bajo», «cambia el filtro» o «no hagas nada nuevo».

En una piano roll el tiempo suele avanzar hacia la derecha y dibujas barras. Aquí el tiempo avanza hacia abajo y escribes notas como `C4`, acompañadas de números. La música sigue siendo la misma: ritmo, notas, sonidos, intensidad y duración.

PocketFlow está inspirado en la tradición de trackers. **No necesitas otro aparato ni un LGPT o M8 para usarlo.** Tampoco debes asumir que sus proyectos, atajos o comandos sean intercambiables con los de esos equipos. Este manual describe PocketFlow.

### Si vienes de FL Studio

Las equivalencias ayudan a orientarse, aunque no son idénticas:

| En un DAW como FL Studio | Aquí se parece a… | Diferencia que debes recordar |
|---|---|---|
| Playlist / arreglo | `SONG` | Las celdas llaman a cadenas; no son clips de audio dibujados. |
| Un grupo de patrones consecutivos | `CHAIN` | Ordena hasta 16 referencias a frases. |
| Un patrón / pasos de Channel Rack | `PHRASE` | Tiene 16 filas, una columna de nota y tres de efectos. |
| Sampler / instrumento del Channel Rack | `INSTRUMENT` | Un slot de sonido reutilizable por muchas frases y pistas. |
| Piano roll | Columna de notas de `PHRASE` | La duración se decide mediante eventos posteriores, el sample y las envolventes. |
| Canal del mezclador | Pista en `MIXER` | Hay ocho pistas de secuenciación; una puede usar distintos instrumentos. |
| Automation clip | Comandos FX y `AUS`/`AUF` | Los escribes en filas de la secuencia. |
| Envolvente / LFO de un sintetizador | `MODS` | Modifica parámetros del instrumento mientras suena. |
| Canal auxiliar de reverb o delay | Envíos `REV` y `DEL` | Varios instrumentos comparten los mismos buses de efecto. |
| Consolidar / renderizar un fragmento | Resampling | Convierte un tramo de la canción en un nuevo sample. |

### Las seis ideas que te ahorran más confusiones

1. **Una pista no es un instrumento.** La pista 1 puede tocar bombo en una fila y caja en otra. Para golpes simultáneos, al principio usa pistas diferentes.
2. **Una celda con `03` suele apuntar a algo.** No significa «repite tres veces» ni «volumen tres». Su significado depende de la columna.
3. **Reutilizar un número reutiliza el mismo objeto.** Si varias cadenas llaman a la frase `02`, editar la frase `02` modifica todas esas apariciones.
4. **Una fila sin nota no corta automáticamente el sonido.** Significa «no disparar una nota nueva». Una nota anterior puede seguir sonando.
5. **Guardar el proyecto y exportar audio son acciones diferentes.** El proyecto conserva las instrucciones; el WAV conserva el sonido ya renderizado.
6. **No hace falta usar todas las pantallas.** Con SONG, CHAIN, PHRASE, INSTRUMENT y PROJECT puedes terminar música.

<a id="estructura"></a>
## 2. La estructura: canción, cadenas, frases e instrumentos

Piensa en cuatro niveles:

| Nivel | Qué contiene | Ejemplo |
|---|---|---|
| `SONG` | Ocho columnas de pistas, con referencias a cadenas. | Pista 1, fila `00`: reproduce cadena `00`. |
| `CHAIN` | Una lista de hasta 16 frases, con transposición por entrada. | Cadena `00`: frases `00`, `00`, `00`, `03`. |
| `PHRASE` | 16 pasos con nota, intensidad, instrumento y efectos. | Fila `04`: `C4`, `V=7F`, `I=00`. |
| `INSTRUMENT` | El sonido y sus ajustes. | Instrumento `00`: `kick.wav`, centrado, sin reverb. |

El instrumento consulta un archivo de audio, un SoundFont o una salida MIDI. La frase dice **cuándo y cómo** tocarlo. La cadena ordena frases. SONG organiza las cadenas por pista.

### Los números pertenecen a bancos distintos

La cadena `00`, la frase `00` y el instrumento `00` son **tres cosas distintas**. Que tengan el mismo número no los conecta automáticamente. Los conectas escribiendo las referencias correspondientes.

| Banco del proyecto | Cantidad | IDs disponibles |
|---|---:|---|
| Pistas | 8 | Las ocho columnas de SONG |
| Cadenas | 256 | `00`–`FF` |
| Frases | 256 | `00`–`FF` |
| Instrumentos | 128 | `00`–`7F` |
| Tablas | 128 | `00`–`7F` |
| Grooves | 128 | `00`–`7F` |
| Presets de EQ | 128 | `00`–`7F` |
| Escalas | 16 | `00`–`0F` |

**Pool** significa banco de objetos disponibles. Un instrumento puede existir en su pool aunque todavía ninguna frase lo use.

### Ejemplo: cuatro compases de bombo

1. Creas la frase `00` con un patrón de bombo de 16 pasos.
2. En la cadena `00`, escribes `00` en sus filas `00`, `01`, `02` y `03`.
3. En SONG, pista 1, fila `00`, escribes la cadena `00`.

En el ritmo normal, esa cadena dura cuatro compases. Solo existe **una frase de bombo**, utilizada cuatro veces. Si cambias uno de sus golpes, cambia en los cuatro compases.

Para que únicamente el cuarto compás tenga un fill, crea una **copia independiente** de la frase y referencia esa copia en la cuarta entrada. Ver [copiar y clonar](#edicion).

<a id="numeros"></a>
## 3. Números raros, notas y tiempo musical

### 3.1 Hexadecimal sin sufrir

Muchas celdas usan **hexadecimal**, una forma de contar con 16 símbolos: `0 1 2 3 4 5 6 7 8 9 A B C D E F`. Después de `09` vienen `0A`, `0B`, `0C`, `0D`, `0E`, `0F` y después `10`.

| Hex | Decimal | Te servirá para… |
|---|---:|---|
| `00` | 0 | Primer ID; cero; silencio, según el campo. |
| `04` | 4 | Segundo tiempo de un compás de 16 pasos. |
| `08` | 8 | Tercer tiempo. |
| `0C` | 12 | Cuarto tiempo; también 12 ticks. |
| `0F` | 15 | Última fila de una frase. |
| `10` | 16 | Un grupo completo de 16. |
| `20` | 32 | Un valor bajo en un campo de 0 a 255. |
| `40` | 64 | Aproximadamente un cuarto de `FF`. |
| `7F` | 127 | Máximo de la columna `V` de la frase. |
| `80` | 128 | Centro del panorama; aproximadamente media amplitud en un fader. |
| `C0` | 192 | Aproximadamente tres cuartos de `FF`. |
| `FF` | 255 | Máximo de un byte sin signo. |

**No todo se muestra en hexadecimal.** El tempo se lee en BPM decimales; la EQ muestra Hz y dB; algunos campos son nombres o listas. Mira la etiqueta y el rango.

Tampoco todos los campos tienen el mismo máximo: `V=7F` ya es intensidad máxima en PHRASE; el volumen de instrumento o mezclador llega a `FF`.

### 3.2 Un número completo frente a dos números pequeños

En los efectos verás dos notaciones:

- **`XX`**: el valor completo. En `VOL80`, `80` es un único valor de volumen.
- **`XY`**: cada dígito hace algo diferente. En `ARP37`, `3` y `7` son dos intervalos. No significa 55 semitonos.

Algunos comandos usan un byte **con signo**: `00` es cero, `01` es +1, `7F` es +127, `FF` es −1, `FE` es −2 y `F4` es −12. Se indicará cuáles. **No apliques esta regla a PAN:** `PANFF` va a la derecha, no a −1.

### 3.3 Las notas

| Nombre en pantalla | Nombre habitual |
|---|---|
| C | Do |
| D | Re |
| E | Mi |
| F | Fa |
| G | Sol |
| A | La |
| B | Si |
| `#` | Sostenido: un semitono por encima. |

El número indica la octava: `C3` es una octava más grave que `C4`. A igual instrumento, subir una octava duplica la frecuencia. Algunos DAW numeran las octavas de otra manera; compara el sonido o la nota MIDI, no solo el nombre. En esta app, `C4` corresponde a la nota MIDI 60.

En un sampler normal, cambiar la nota cambia también la **velocidad del audio**. Un bombo en `C5` puede sonar más corto y agudo que en `C4`. Eso no es time-stretch automático.

### 3.4 Filas, beats, compases y ticks

Con el groove normal:

- Una frase tiene **16 pasos**.
- Un paso equivale a una **semicorchea**.
- Cuatro pasos forman un tiempo o beat.
- Dieciséis pasos forman un compás de **4/4**.
- Un paso normal dura **12 ticks**; un beat dura 48 ticks en la referencia habitual.

| Cuenta del compás | Filas |
|---|---|
| Tiempo 1 y sus subdivisiones | `00`, `01`, `02`, `03` |
| Tiempo 2 y sus subdivisiones | `04`, `05`, `06`, `07` |
| Tiempo 3 y sus subdivisiones | `08`, `09`, `0A`, `0B` |
| Tiempo 4 y sus subdivisiones | `0C`, `0D`, `0E`, `0F` |

A 120 BPM, un beat dura 0,5 segundos, el compás 2 segundos y cada paso 0,125 segundos. La fórmula de un compás de 4/4 es `240 / BPM` segundos.

Un **tick** es una subdivisión interna útil para retrasos, retriggers y tablas. `LAT06` retrasa medio paso normal; `KIL06` corta después de medio paso. Los grooves y saltos pueden cambiar la duración de una frase: «16 filas = un compás» es el punto de partida, no una ley para cualquier configuración.

<a id="controles"></a>
## 4. Botones, navegación y reproducción

Los nombres `A`, `B`, `L`, `R`, `START` y `SELECT` se refieren a los botones de la interfaz o del mando. La cruceta son las cuatro flechas. En una combinación, mantén pulsado el modificador y pulsa la otra tecla.

### 4.1 Los controles que necesitas primero

| Acción | Control | Qué esperar |
|---|---|---|
| Mover el cursor | Flechas | Elige celda o parámetro. |
| Insertar / aceptar / abrir | `A` | En una celda vacía suele insertar el último valor usado. |
| Cambiar un valor poco | Mantener `A` + izquierda/derecha | −1 / +1, según el campo. |
| Cambiar un valor mucho | Mantener `A` + abajo/arriba | Normalmente −16 / +16; en notas, una octava. |
| Borrar / restablecer una celda | `A+B` | No confundas esto con pulsar B solo. |
| Crear siguiente cadena/frase libre | Doble toque rápido de `A` | En una referencia de SONG o CHAIN, busca un ID sin usar. |
| Cambiar de pantalla | Mantener `R` + dirección | Navega por el mapa de pantallas. |
| Reproducir / parar | `START` | El punto de partida depende de la pantalla. |
| Ayuda contextual | `SELECT` | Explica los controles de la pantalla actual, si HELP está activo. |
| Cerrar un navegador o cuadro | `B` | Hay excepciones, como el teclado de texto. |

**La nota OFF se introduce con `A+B` sobre una celda de nota vacía.** Si hay una nota, primero la borras; repites sobre el hueco para poner OFF. Sobre OFF, la combinación lo vuelve a borrar.

En una celda numérica, un solo `A` puede reutilizar un ID existente. Si querías un objeto nuevo y acabas editando otro patrón, probablemente reutilizaste una referencia.

### 4.2 Mapa de pantallas

La fila principal, de izquierda a derecha, es:

**SONG → CHAIN → PHRASE → INSTRUMENT → TABLE**

Te mueves entre ellas con `R+izquierda/derecha`. Las pantallas adicionales se abren así:

| Desde | Control | Destino |
|---|---|---|
| SONG o CHAIN | `R+arriba` | PROJECT |
| PHRASE | `R+arriba` | GROOVE |
| GROOVE | `R+arriba` | SCALE |
| INSTRUMENT | `R+arriba` | MODS |
| MODS | `R+arriba` | INST.POOL |
| TABLE | `R+arriba` | PROJECT |
| Cualquier pantalla de la fila principal | `R+abajo` | MIXER |
| MIXER | `R+abajo` | EFFECTS |
| PROJECT | `A` en SETTINGS o MIDI | La pantalla elegida |
| INSTRUMENT | `A` en EDIT | SAMPLE EDITOR, para samples |
| Una celda EQ | `A` | Editor de EQ |

**Si R+derecha no te deja entrar desde SONG o CHAIN**, comprueba que la celda seleccionada tenga una cadena o frase asignada. No hay contenido que abrir en una referencia vacía.

INST.POOL tiene navegación contextual: desde él puedes abrir el instrumento seleccionado con `R+derecha`; al volver con `R+izquierda`, regresas al pool. Los cuadros superpuestos, el navegador y el editor de muestras tienen sus propios controles de salida.

### 4.3 START no siempre significa «toda la canción desde el principio»

| Dónde estás, con la reproducción detenida | Qué inicia START |
|---|---|
| SONG | La canción desde la **fila del cursor**. |
| CHAIN | La cadena actual como preescucha. |
| PHRASE | La frase actual como preescucha. |
| INSTRUMENT, MODS, TABLE, GROOVE y pantallas de edición relacionadas | La preescucha de la frase actual. |
| PROJECT, MIXER, EFFECTS, SETTINGS o MIDI | La canción desde la fila `00`. |
| Navegador de samples | Preescucha del archivo seleccionado. |
| SAMPLE EDITOR | Preescucha del audio que estás editando. |

- **`R+START`** desde CHAIN, PHRASE, INSTRUMENT, INST.POOL, MODS, TABLE, GROOVE o SCALE permite oír el conjunto desde la fila de SONG recordada.
- **`A+START`** en INSTRUMENT, INST.POOL, MODS o TABLE permite probar el instrumento seleccionado.
- Con reproducción normal activa, `START` detiene. **LIVE es una excepción**: allí START lanza cadenas; se explica en su sección.

Si escuchas solo el bombo al editar su frase, no significa que se hayan borrado las otras pistas: estás preescuchando esa frase. Vuelve a SONG para oír el arreglo.

### 4.4 Navegar con B: contexto o pool

Con **SETTINGS → NAV = SONG**, que es el modo inicial:

- En CHAIN, `B+izquierda/derecha` busca otra pista ocupada en la misma fila de SONG; `B+arriba/abajo` busca otra fila ocupada de esa pista.
- En PHRASE, `B+izquierda/derecha` cambia a una pista vecina con frase en esa posición de cadena; `B+arriba/abajo` cambia de entrada ocupada de la cadena.
- Rebasar arriba o abajo los extremos de una frase también puede llevarte a la entrada vecina de su cadena.

Con **NAV = POOL**, `B+izquierda/derecha` recorre los IDs globales de cadenas o frases. Sirve para buscar objetos que aún no están colocados en SONG. En instrumentos, tablas y grooves, B con izquierda/derecha recorre su banco; en instrumentos, MODS y TABLE, B con arriba/abajo permite saltos de 16 IDs.

En **SONG**, `B+izquierda/derecha` cambia entre SONG y LIVE. No lo uses allí esperando cambiar un número.

### 4.5 Escribir nombres

En el teclado de texto de la app:

- Flechas: escoger carácter; `A`: escribirlo.
- `B`: borrar un carácter. **Aquí B no es «cerrar».**
- `R+izquierda/derecha`: mover el cursor dentro del texto.
- `R+arriba/abajo`: alternar grupos de caracteres.
- `START`: aplicar el nombre; `SELECT`: cancelar.
- También puedes elegir las acciones `APPLY` o `ABORT` del propio teclado.

<a id="preparacion"></a>
## 5. Preparar Android, carpetas y sonidos

### 5.1 Elegir una carpeta

La app necesita acceso a una carpeta donde guardar proyectos y leer audio. Abre un navegador de archivos de la app, por ejemplo desde **INSTRUMENT → LOAD**, y busca **ADD FOLDER…**. Al pulsar A aparece el selector de carpetas de Android. Elige una carpeta que puedas localizar después y concede el acceso.

La carpeta base puede seguir llamándose **PocketTracker**. Eso es normal en este fork: el nombre visible PocketFlow no obliga a renombrar las carpetas ni el identificador de Android.

Dentro de la carpeta de trabajo encontrarás directorios como `Projects`, `Samples`, `Soundfonts` y `Renders`. La ruta exacta depende de la carpeta que autorizaste; no hay que adivinar una ruta fija del teléfono.

Si añades más carpetas, puedes acceder a ellas desde el navegador. Antes de cambiar la carpeta principal, guarda el proyecto y verifica dónde quedan sus archivos. Una autorización a una carpeta no es una copia de su contenido.

### 5.2 Preparar un kit pequeño

Para el tutorial necesitas archivos propios o que tengas permiso de usar:

- Un bombo corto: `kick.wav`.
- Una caja o clap: `snare.wav`.
- Un hi-hat cerrado: `hat.wav`.
- Opcional: un bajo con afinación conocida o un SoundFont con un bajo.

Ponlos en una subcarpeta de `Samples`, por ejemplo `Samples/MiKit`. No necesitas una biblioteca enorme. Empieza con archivos WAV cortos y reconocibles.

El sampler también admite MP3, FLAC, OGG, Opus y M4A en las rutas de carga compatibles. Los archivos comprimidos se decodifican para reproducirse; **cargar uno no crea automáticamente un WAV nuevo en tu carpeta**. El reproductor SoundFont admite SF2 y SF3.

### 5.3 Lo que debes guardar

Un proyecto `.ptp` guarda la secuencia y los ajustes, pero **referencia los archivos de sonido**. Si borras o mueves el sample, tener el `.ptp` no basta para recuperar el audio. Tu respaldo debe incluir el proyecto y los samples/SoundFonts que utiliza.

Antes de actualizar, desinstalar o cambiar de APK, haz una copia de esa carpeta. Una APK es el programa, no tu canción ni tu biblioteca.

<a id="primer-beat"></a>
## 6. Tu primer beat, paso a paso

Objetivo: escuchar **bombo + caja + hi-hat** en un loop de un compás. No usaremos efectos, tablas ni MIDI. El ejemplo supone un proyecto nuevo y vacío; si tienes una plantilla con contenido, utiliza IDs libres equivalentes.

### Paso 1: poner nombre y tempo

1. Entra en PROJECT con `R+arriba` desde SONG.
2. En NAME, pulsa A y escribe `MiPrimerBeat`. Confirma con START.
3. Ajusta TEMPO a **100 BPM** usando A y las flechas. Este número es decimal.
4. Regresa a SONG con la navegación de pantalla.

No hace falta cambiar TRANSPOSE. Deja el resto como está.

### Paso 2: conectar la primera pista

1. En SONG, coloca el cursor en **pista 1, fila `00`**.
2. Inserta la cadena `00` con A; si aparece otro número, ajústalo con A+flechas.
3. Entra con `R+derecha`: ahora estás dentro de CHAIN `00`.
4. En su fila `00`, columna de frase, inserta la frase `00`. Deja vacía la fila `01`.
5. Entra con `R+derecha`: estás dentro de PHRASE `00`.

Acabas de construir el recorrido SONG → cadena `00` → frase `00`. Todavía no hay sonido porque falta un instrumento y sus notas.

### Paso 3: cargar el bombo

1. En la primera fila de PHRASE, establece **I = `00`**.
2. Ve a INSTRUMENT con `R+derecha` y confirma que el encabezado indique el instrumento `00`.
3. Deja TYPE en el tipo sampler y selecciona LOAD.
4. Pulsa A, abre la carpeta de tu kit y elige `kick.wav` con A.
5. Prueba el instrumento con `A+START`.
6. Deja ROOT en `C4` para este ejercicio y vuelve a PHRASE.

Si al entrar no estás en el instrumento esperado, selecciónalo con B+izquierda/derecha y revisa el ID. Lo esencial es que el archivo esté cargado en el mismo instrumento que escribirás en la columna I.

### Paso 4: escribir el bombo

En PHRASE `00`, coloca estas cuatro filas:

| Fila | Nota | V | I | FX |
|---|---|---|---|---|
| `00` | `C4` | `7F` | `00` | Vacíos |
| `04` | `C4` | `7F` | `00` | Vacíos |
| `08` | `C4` | `7F` | `00` | Vacíos |
| `0C` | `C4` | `7F` | `00` | Vacíos |

Para cada celda, muévete hasta ella, pulsa A si está vacía y ajusta con A+flechas. Los demás pasos se quedan vacíos. Es normal que la app recuerde el último valor que pusiste y te ahorre algunas ediciones.

Pulsa START desde PHRASE. Debes oír un bombo en cada tiempo: **uno, dos, tres, cuatro**. Vuelve a pulsar START para parar.

### Paso 5: añadir la caja en otra pista

1. Regresa a SONG con R+izquierda dos veces.
2. En **pista 2, fila `00`**, asigna la **cadena `01`**.
3. Dentro de esa cadena, asigna la **frase `01`** en su fila `00`.
4. Desde esa frase, carga `snare.wav` en el **instrumento `01`**.
5. En PHRASE `01`, escribe `C4`, `V=7F`, `I=01` en las filas **`04` y `0C`**.

No reutilices la cadena `00` para este paso: esa cadena ya apunta al patrón del bombo. Cada nueva referencia del tutorial está indicada a propósito.

### Paso 6: añadir hi-hat

1. En **pista 3, fila `00`** de SONG, usa cadena `02`.
2. En CHAIN `02`, fila `00`, usa frase `02`.
3. Carga `hat.wav` en instrumento `02`.
4. En PHRASE `02`, pon `C4`, `I=02` en las filas **`00`, `02`, `04`, `06`, `08`, `0A`, `0C`, `0E`**.
5. Alterna la intensidad: `V=60` en `00`, `04`, `08`, `0C`; `V=40` en `02`, `06`, `0A`, `0E`.

Esto hace que el hi-hat tenga acentos en vez de ocho golpes idénticos. Es una primera forma de dar movimiento sin ningún efecto especial.

### Paso 7: escuchar todo y guardar

Vuelve a SONG, pon el cursor en la fila `00` y pulsa START. Las tres pistas deben sonar juntas. Al tener una sola frase por cadena y el mismo groove, las tres duran lo mismo y repiten coordinadas.

En MIXER baja un poco el hi-hat si tapa la caja. Para guardar, entra en **PROJECT → PROJECT SAVE** y pulsa A. Comprueba el mensaje de guardado.

**Resultado esperado:** un loop de un compás con bombo en negras, caja en 2 y 4 e hi-hat en corcheas. Todavía no es un arreglo largo, pero ya utilizaste toda la estructura necesaria para crearlo.

Si no suena, ve a [problemas frecuentes](#problemas). No necesitas rehacer el proyecto completo.

<a id="primera-cancion"></a>
## 7. Añadir bajo, variaciones y una canción

### 7.1 Un bajo que toque las notas correctas

Usa la **pista 4**, cadena `03`, frase `03` e instrumento `03`, siguiendo el mismo procedimiento del tutorial. Carga un sample de bajo cuya nota original conozcas, o un SoundFont con un patch de bajo.

**ROOT es la nota original del sample.** Si el archivo fue grabado tocando C3, pon ROOT en C3. Así, escribir C3 lo reproduce a su velocidad original y escribir D3 lo transpone dos semitonos. Poner ROOT en C4 no convierte mágicamente un sample desafinado en un Do.

Prueba esta frase sencilla en La menor, sin efectos:

| Fila | Nota | V | I | Función |
|---|---|---|---|---|
| `00` | `A2` | `60` | `03` | Tónica: La. |
| `04` | `A2` | `50` | `03` | Repite con menos intensidad. |
| `08` | `E3` | `60` | `03` | Quinta: Mi. |
| `0C` | `G2` | `50` | `03` | Sol, para regresar a La. |

La octava adecuada depende del sonido; si queda demasiado grave o agudo, transpón el patrón una octava. Un archivo muy corto no se sostiene por dejar filas vacías: necesitas un sample largo, un loop o un instrumento apropiado.

### 7.2 Cómo hacer una nota corta o larga

Hay tres situaciones diferentes:

| Situación | Qué ocurre |
|---|---|
| Sample de bombo corto, sin loop | Termina por sí solo al acabarse el archivo. |
| Sample largo o en loop | Puede continuar sobre filas vacías. |
| Instrumento con envolvente ADSR | Se mantiene según SUS hasta recibir OFF; después ejecuta REL. |

Para detener una nota puedes escribir **OFF en la columna de nota**. Para un corte brusco y programable, usa **`KIL00`** en una columna FX. Un KIL no espera una cola de release.

Ejercicio: toca `A2` en `00`, deja `01` y `02` vacías, y coloca OFF en `03`. Con una envolvente apropiada, la nota comienza en `00` y empieza a soltarse en `03`.

**OFF en la columna de nota no es lo mismo que el efecto `OFFxx`.** El efecto cambia el punto de inicio del sample. Es una coincidencia de nombres que conviene aprender desde el principio.

### 7.3 Convertir el loop de un compás en cuatro

En cada una de las cuatro cadenas, repite su frase en las entradas `00`, `01`, `02` y `03`:

| Entrada de CHAIN | Cadena `00` / bombo | Cadena `01` / caja | Cadena `02` / hat | Cadena `03` / bajo |
|---|---|---|---|---|
| `00` | Frase `00` | Frase `01` | Frase `02` | Frase `03` |
| `01` | Frase `00` | Frase `01` | Frase `02` | Frase `03` |
| `02` | Frase `00` | Frase `01` | Frase `02` | Frase `03` |
| `03` | Frase `00` | Frase `01` | Frase `02` | Frase `03` |

Deja la entrada `04` vacía. Ahora la fila `00` de SONG contiene cuatro cadenas de cuatro compases cada una.

**Una fila de SONG no tiene una duración fija de un compás.** Su contenido puede durar uno, cuatro o más, según las cadenas, frases, grooves y saltos. Para aprender, mantén todas las cadenas de una sección con la misma duración.

### 7.4 Crear un fill sin modificar los otros compases

1. Abre la cadena de caja `01`.
2. Pon el cursor en su cuarta referencia, fila `03`, que ahora apunta a la frase `01`.
3. Usa **`L+B+A`** para clonar esa frase a un ID libre y reemplazar esa referencia.
4. Anota el ID nuevo que aparece; no tiene por qué coincidir con un número concreto del ejemplo.
5. Entra en esa frase y añade golpes de caja en `0E` y `0F`, con V más baja, por ejemplo `40` y `50`.

Los tres primeros compases seguirán usando la frase `01`; el cuarto usará la nueva. Eso es una **variación independiente**.

### 7.5 Hacer intro, parte principal y cierre

Primero decide cuánto dura cada sección. Por ejemplo, cuatro compases por fila de SONG. Crea cadenas con esa duración y colócalas en filas consecutivas.

| Fila SONG | Bombo | Caja | Hat | Bajo | Idea |
|---|---|---|---|---|---|
| `00` | Cadena de cuatro compases | Cadena de silencio | Cadena de cuatro compases | Cadena de silencio | Intro. |
| `01` | Cadena de cuatro compases | Cadena de cuatro compases | Cadena de cuatro compases | Cadena de cuatro compases | Entra el conjunto. |
| `02` | Igual o una variación | Cadena con fill final | Variación | Variación | Cambio. |
| `03` | Cadena de cierre | Cadena de cierre | Cadena de cierre | Cadena de cierre | Salida. |

Las descripciones de esta tabla no son IDs para teclear: crea o reutiliza las cadenas correspondientes y escribe sus números.

**Para programar un compás de silencio, utiliza una cadena que contenga una frase vacía.** Si necesitas cuatro compases de silencio, referencia esa frase cuatro veces. Un hueco en SONG delimita el recorrido de la pista; una referencia vacía dentro de CHAIN se omite. Ninguno equivale a un clip vacío de un compás.

Si un sonido de la sección anterior sigue sostenido, una frase sin eventos no necesariamente lo apaga. Pon OFF o KIL al comienzo de la frase de silencio según la cola que quieras conservar.

### 7.6 El primer acorde

Para tocar varias notas simultáneas puedes repartirlas entre pistas, o usar el comando **CHD**. Prueba con un piano o pad:

| Fila | Nota | I | FX1 |
|---|---|---|---|
| `00` | `C4` | Tu instrumento | `CHD00` |
| `08` | `A3` | Tu instrumento | `CHD10` |

`CHD00` pide un acorde mayor desde la nota escrita; `CHD10`, uno menor. El resultado usa hasta cuatro voces. La siguiente nota sin CHD vuelve a ser una nota individual. Baja el volumen si el acorde suena mucho más fuerte que una nota sola.

<a id="edicion"></a>
## 8. Copiar, clonar, rellenar y deshacer

### 8.1 Copiar un bloque

1. Coloca el cursor al principio del bloque.
2. Pulsa **L+B** para iniciar selección.
3. Amplía con las flechas.
4. Pulsa **B** para copiar y salir de la selección.
5. Ve al destino y pulsa **L+A** para pegar.

Repetir L+B rápidamente, dentro de aproximadamente medio segundo, amplía la selección de **celda a fila y después a pantalla**. Dentro de una selección, L+A **corta**; fuera de ella, L+A **pega**. A+B borra el bloque seleccionado sin copiarlo.

### 8.2 Copiar referencias no duplica sus objetos

| Operación | Resultado |
|---|---|
| Copiar una celda `02` de SONG | Otra celda apunta a la **misma cadena 02**. |
| Copiar una referencia `05` de CHAIN | Otra entrada apunta a la **misma frase 05**. |
| Copiar notas dentro de una frase | Copias esos eventos al destino. |
| Clonar en CHAIN con `L+B+A` | Crea otra frase y cambia la referencia seleccionada. |
| Clonar en SONG con `L+B+A` | Crea otra cadena y copias de sus frases referenciadas. |

En una clonación de cadena, las repeticiones de una misma frase siguen compartiendo una sola copia dentro de la cadena nueva. No se crea una frase distinta por cada repetición.

**Clonar la secuencia no duplica los instrumentos, sus archivos ni todos los recursos compartidos.** Si ambas versiones usan instrumento `03`, cambiar el filtro de ese instrumento cambia su sonido en ambas. Para sonidos independientes, prepara otro slot de instrumento o automatiza los cambios por nota.

Para hacer variaciones de forma predecible, clona desde la referencia de SONG o CHAIN y comprueba el nuevo ID antes de editar.

### 8.3 Fill: escribir muchos eventos de una vez

En PHRASE, **L+dirección** ofrece herramientas de relleno. Trabajan sobre el contexto o la selección actual; conviene practicar en una frase de prueba.

| Control | Función |
|---|---|
| `L+derecha` | Recorre rellenos periódicos: cada 8, 6, 4, 3, 2 o 1 pasos; según el campo y los extremos, también una rampa. |
| `L+izquierda` | Distribuciones de 3, 5, 6, 7, 9, 11 o 13 eventos espaciados a lo largo del rango. |
| `L+arriba` | Aleatoriza valores tomando como referencia el rango de valores existente. |
| `L+abajo` | Aleatoriza posiciones de eventos. |

La secuencia de variaciones incluye el estado original mientras permanezcas en ese ciclo de edición. Al mover el cursor o hacer otra edición puedes perder ese punto de retorno: **no es un historial general de Undo**.

En efectos, el relleno puede trabajar con sus valores; no debes esperar que invente tipos de efectos aleatorios. En notas, el resultado también depende de la escala.

### 8.4 ¿Dónde está Undo?

No hay un deshacer global equivalente al historial de un DAW para cualquier acción.

- **L+R** limpia estados como mute/solo recientes, selección o portapapeles; puede requerir más de una pulsación si hay varios estados. No recupera cualquier nota borrada.
- El ciclo de Fill permite volver a su original bajo las condiciones anteriores.
- El editor de samples tiene **UNDO para una operación destructiva**, no una pila ilimitada.
- Guardar versiones del proyecto es tu mecanismo más claro para volver atrás después de cambios grandes.

Una costumbre útil: antes de experimentar, guarda `Tema_01`; cambia NAME a `Tema_02` y guarda otra vez. Comprueba el nombre del archivo creado.

<a id="secuenciador"></a>
## 9. SONG, CHAIN y PHRASE a fondo

### 9.1 SONG: ocho recorridos en paralelo

Cada columna es una pista. Una celda ocupada contiene un ID de cadena. Las pistas recorren sus propias cadenas y sus propias columnas: no son ocho clips obligados a esperar una barra global de duración fija.

Con cadenas de igual duración todo resulta sencillo. Con longitudes distintas puedes obtener ciclos que se cruzan y polirritmos. Eso es una herramienta musical, pero también explica muchos patrones que parecen «descuadrarse».

**Al llegar a un hueco en su columna, una pista vuelve al inicio del bloque contiguo que estaba recorriendo.** Una pista inicialmente vacía no inicia una cadena por sí sola. Una fila completamente vacía sirve para separar partes o bocetos, no para introducir un compás de silencio.

Para empezar desde una sección concreta, sitúa el cursor en su fila y pulsa START. Para reproducir desde el comienzo, usa la fila `00` o arranca desde PROJECT/MIXER.

### 9.2 CHAIN: lista de frases y transposición

Cada cadena ofrece 16 entradas, `00`–`0F`. Una entrada contiene una referencia de frase y su transposición. El recorrido utiliza las entradas ocupadas y **salta las referencias vacías sin añadir tiempo**; al terminar las ocupadas, completa la cadena. Para aprender, colócalas consecutivas desde `00`.

**La entrada `00` debe tener una referencia para que SONG considere reproducible esa cadena.** Una cadena con la primera entrada vacía y contenido más abajo puede no arrancar desde SONG. Una referencia a una frase sin notas sí es válida y permite programar silencio con duración.

La transposición desplaza las notas en semitonos sin reescribir la frase. Por ejemplo, una frase de bajo puede repetirse con otro desplazamiento para seguir una progresión. Los instrumentos con **TSP=OFF** quedan fuera de la transposición de cadena/proyecto y del ajuste a escala.

En preescucha, la cadena se repite. Durante SONG, al completarla la pista continúa según el arreglo. Un comando HOP puede alterar ese recorrido.

### 9.3 PHRASE: leer una fila correctamente

| Columna | Qué significa |
|---|---|
| Número de fila | Momento dentro de la frase, `00`–`0F`. No se edita como una nota. |
| Nota | Altura a tocar, `---` sin nota nueva u OFF para soltar la actual. |
| `V` | Intensidad/velocidad, `00`–`7F`. Es un factor por evento, distinto del VOL del instrumento. |
| `I` | ID del instrumento, `00`–`7F`. |
| `FX1`, `FX2`, `FX3` | Hasta tres comandos con sus valores. |

Una fila sin nota puede cambiar el sonido que ya está sonando mediante efectos. Escribir un instrumento en una fila vacía **no equivale por sí solo a tocarlo**.

Como regla de trabajo, escribe I y V de forma explícita en las notas importantes hasta que conozcas qué valores conserva la app. Así puedes leer el patrón sin depender de lo que se editó antes.

Los tres efectos se procesan con reglas de orden y alcance: muchos parámetros repetidos se resuelven por el último comando de izquierda a derecha, mientras CHA, RNL, ARC y AUS dependen de su posición. Mover un efecto a otra columna puede cambiar el resultado.

### 9.4 Silenciar y escuchar una pista sola

En SONG y MIXER:

- **R+B:** mute de la pista seleccionada.
- **R+A:** solo de la pista seleccionada.
- Suelta **R primero** para dejar el estado fijado.
- Suelta **A o B primero**, mientras mantienes R, para un uso momentáneo.

Puedes acumular solos. Un solo no garantiza que desaparezca un mute previo de esa misma pista. Si de pronto no oyes algo, revisa ambos estados; L+R ayuda a limpiarlos.

<a id="instrumentos"></a>
## 10. Instrumentos: sampler, SoundFont y externo

Un instrumento es una receta de reproducción, no solo un archivo. Dos slots pueden cargar el mismo WAV y tener afinaciones, loops, filtros o envolventes diferentes.

### 10.1 Los tres tipos

| Tipo | Qué produce | Para qué usarlo |
|---|---|---|
| Sampler / WAV | Reproduce audio cargado. | Baterías, chops, voces, loops, bajos y texturas. |
| SoundFont / SF2 | Toca un patch de un banco SF2/SF3. | Instrumentos afinados sin preparar cada sample a mano. |
| EXTERNAL | Envía mensajes MIDI. | Controlar un sintetizador u otro dispositivo externo. No genera audio local por sí mismo. |

TYPE cambia el tipo. LOAD carga la fuente correspondiente. Los campos visibles cambian: no todos los controles del sampler aparecen en un SoundFont o instrumento MIDI.

### 10.2 Parámetros del sampler

| Campo | Explicación práctica |
|---|---|
| NAME | Nombre del instrumento para reconocerlo. |
| LOAD | Elegir el archivo de audio. |
| EDIT | Abrir el editor de la forma de onda. |
| ROOT | Nota a la que el sample suena a su velocidad original. |
| DETUNE | Afinación fina. `80` es neutral; los extremos rondan un semitono por debajo o por encima. |
| TIC | Velocidad base de avance de su TABLE. Empieza con el valor por defecto `06`. |
| VOL | Volumen base, `00`–`FF`. |
| TSP | Permitir transposición de cadena/proyecto y ajuste de escala. OFF suele ser útil para percusión afinada a mano. |
| PAN | Posición: `00` izquierda, `80` centro, `FF` derecha. |
| INST PRESET SAVE / LOAD | Guardar o cargar ajustes de instrumento en `.pti`. |
| DRIVE | Saturación: más valor, más distorsión. |
| FILTER | OFF, paso bajo, paso alto o paso banda. |
| FREQ | Frecuencia de corte del filtro. En comandos de frase se llama CUT. |
| RES | Resonancia alrededor del corte. Valores altos pueden generar picos fuertes. |
| CRUSH | Reducción de resolución, `0`–`F`. `0` es limpio. |
| DWNSMPL | Downsample, `0`–`F`: reduce la resolución temporal y añade textura. |
| REV / DEL | Cantidad enviada a la reverb o al delay globales. |
| EQ | Slot de ecualizador; A abre su editor. |
| SLICE | OFF, CUT o TRU para la reproducción de fragmentos. |
| START / END | Límites del material que se reproduce. |
| LOOP | Modo de bucle. |
| LOOP ST / LOOP END | Región interna que se repite cuando el loop está activo. |
| REVERSE | Reproducción invertida. |

Los límites START/END del instrumento son ajustes de reproducción; no equivalen a recortar y sobrescribir físicamente el WAV.

### 10.3 Filtros sin tecnicismos innecesarios

- **LP / LPF, paso bajo:** deja pasar graves y atenúa agudos. Bajar FREQ oscurece el sonido.
- **HP / HPF, paso alto:** deja pasar agudos y atenúa graves. Subir FREQ adelgaza el sonido.
- **BP / BPF, paso banda:** deja una zona alrededor de la frecuencia elegida. Útil para sonidos de teléfono o barridos.
- **RES:** enfatiza el borde del filtro. Empieza baja y súbela escuchando el nivel.

Mover FREQ/CUT con FILTER apagado no activa un filtro. Puedes encenderlo en INSTRUMENT o usar LPF/HPF/BPF desde la frase.

### 10.4 Loops y cola de salida

| Modo | Comportamiento |
|---|---|
| OFF | Reproduce una vez hasta el final. |
| FWD | Repite hacia adelante la región LOOP ST–LOOP END. |
| PNG | Va y vuelve dentro de esa región, tipo ping-pong. |

Una región mal cortada puede producir clics. Busca cruces por cero y un tramo estable del audio. Que el loop sea corto no lo vuelve automáticamente afinado: su duración también influye en el tono que percibes.

Con una envolvente ADSR de volumen, la nota puede recorrer **START → LOOP ST**, mantenerse repitiendo **LOOP ST → LOOP END** y, al recibir OFF, salir por **LOOP END → END** bajo el release. Sin esa envolvente, no cuentes con la misma cola de salida. KIL realiza un corte, no una liberación suave.

Algunas compilaciones de desarrollo pueden mostrar un modo OSC experimental. No es necesario para seguir este manual ni debe asumirse que esté disponible en la APK de release.

### 10.5 SoundFonts

Un SoundFont es un banco que relaciona notas y sonidos. Cárgalo en un instrumento de ese tipo y selecciona **PATCH** para elegir el sonido. La app prepara el patch seleccionado; un archivo con muchos presets no significa que todos estén sonando a la vez.

Puedes usar volumen, panorama, efectos y MODS según los controles disponibles. Para dar forma a ataque y release utiliza MODS; no busques en esta versión todas las filas de envolvente que aparecen en documentación antigua del instrumento SF2.

Para empezar, elige un piano o bajo reconocible, escribe notas sin FX y confirma que funcione antes de añadir procesamiento.

### 10.6 Instrumentos externos

Los campos principales son CHAN, BANK, PROG, LEN, VOL, PAN, TSP, TIC, los cuatro slots CC y BEND. CHAN selecciona el canal MIDI del receptor. BANK/PROG eligen su banco y programa; su significado sonoro lo decide el dispositivo externo.

LEN controla la duración de nota en ticks; `00` deja que el siguiente evento de nota gestione el relevo. BEND debe coincidir con el rango de pitch bend configurado en el receptor.

Si la frase se mueve pero no oyes audio, puede ser correcto: el sonido se produce en el equipo externo. Conecta también su salida de audio a donde vayas a escucharlo. Más detalles en [MIDI](#midi).

### 10.7 INST.POOL: ver todos los instrumentos

INST.POOL lista los 128 slots y permite revisar nombre, volumen, envíos REV/DEL y EQ de forma compacta. A sobre un nombre vacío abre la carga; A+START permite probar el seleccionado.

**A+B sobre el nombre puede limpiar el instrumento.** No lo uses pensando que solo oculta su fila. Si una frase todavía referencia ese slot, dejará de disponer del sonido anterior.

Los presets `.pti` permiten reutilizar la configuración del instrumento y su tabla asociada. Incluyen ajustes de modulación como parte del instrumento, pero no sustituyen una copia de sus archivos de audio. Al mover presets entre proyectos, revisa los recursos compartidos, como slots de EQ, y escucha el resultado.

<a id="samples"></a>
## 11. Editar audio, ajustar loops y cortar slices

Abre **INSTRUMENT → EDIT** en un instrumento de sample. Es un editor de audio, distinto de PHRASE: aquí cambias la forma de onda, no las notas del patrón.

### 11.1 Escuchar, seleccionar y salir

- START preescucha el material del editor.
- R+arriba/abajo cambia el zoom.
- En START o END, A+izquierda/derecha mueve el límite con precisión; A+arriba/abajo da pasos mayores.
- La precisión depende del zoom. Los marcadores buscan cruces por cero para reducir clics.
- **B cierra.** Si hay ediciones sin guardar, aparece una confirmación: A descarta y sale; B permanece en el editor.

R+flechas no sirve para abandonar esta pantalla: así evitas salir por accidente con trabajo pendiente.

### 11.2 SOURCE, RATE, BIT y PITCH

| Campo | Qué cambia | Cuándo se vuelve permanente |
|---|---|---|
| SOURCE=STEREO | Mantiene ambos canales de un archivo estéreo. | Al guardar escribe dos canales. |
| SOURCE=LEFT / RIGHT | Usa solo ese canal. | Al guardar el resultado es mono. |
| SOURCE=MONO | Combina los dos canales en mono. | Al guardar se fija esa conversión. |
| RATE=HIGH | Frecuencia de muestreo original. | Se aplica a la salida guardada. |
| RATE=NORM / LOFI | Mitad / cuarta parte de la frecuencia original. | Reduce información al guardar. |
| BIT | Profundidad de bits disponible: 32/24/16/8 según la fuente. | Se aplica a SAVE, OVERWRITE y CHOP. |
| PITCH | Cambio de altura por reproducción/remuestreo. | Se incorpora cuando aplicas o guardas la transformación correspondiente. |

Un archivo mono no se convierte en estéreo real por elegir un nombre de modo. Si el original es estéreo y quieres conservarlo, comprueba **SOURCE=STEREO** antes de guardar. El editor abre así los archivos que tienen canal derecho.

RATE y BIT son conversiones del archivo, diferentes de los efectos CRUSH/DWNSMPL del instrumento. Mientras editas puedes cambiar de opción; después de sobrescribir no puedes recuperar la información descartada simplemente subiendo el ajuste.

### 11.3 Operaciones sobre la forma de onda

| Acción | Resultado |
|---|---|
| CROP | Conserva la selección y elimina lo exterior. |
| COPY | Copia la selección al portapapeles de audio. |
| CUT | Copia y elimina la selección; acorta el sample. |
| DUPL | Añade una copia de la selección al final. |
| PASTE | Inserta el audio copiado en el inicio de la selección. |
| DEL | Elimina la selección sin copiarla. |
| NORMALIZE | Escala el pico hasta 0 dBFS. No arregla una mezcla ni iguala la sonoridad percibida. |
| FADE IN / FADE OUT | Entrada o salida gradual de volumen. |
| SILENCE | Sustituye la selección por silencio, conservando ese tiempo. |
| REVERSE | Invierte la selección. |
| UNDO | Vuelve al estado anterior a la última operación destructiva. |

Las operaciones destructivas cambian el audio de trabajo en memoria. **SAVE** crea un archivo nuevo numerado; **OVERWRITE** reemplaza el original en disco. UNDO no recupera el archivo anterior después de sobrescribirlo. Usa SAVE mientras experimentas.

### 11.4 Ajustar un loop al tempo: RPITCH frente a TSTRETCH

En la fila EFFECT puedes seleccionar **SYNC**. Aquí indicas una duración musical para que el material encaje con el tempo.

- **RPITCH** cambia la velocidad para ajustar la duración: más rápido también suena más agudo; más lento, más grave.
- **TSTRETCH** modifica la duración intentando conservar la altura, y permite un ajuste de PITCH adicional. Puede dejar textura o artefactos, especialmente con cambios grandes.

La lectura de BPM del sample se calcula a partir de su duración y del número de compases que indicas. **No es una detección infalible del tempo musical.** Si dices que un loop de dos compases contiene uno, la interpretación será incorrecta.

Al abrir TSTRETCH encontrarás:

| Campo | Para qué sirve |
|---|---|
| ORIG BPM | Lectura del tempo original según la duración declarada. |
| DURATION | Duración musical objetivo, según la opción de la lista. |
| NEW BPM | Tempo al que quieres ajustarlo; parte del tempo del proyecto. |
| PITCH | Cambio adicional de afinación. |
| GRAIN | Tamaño de los fragmentos de procesamiento, en ms; el inicio es 40 ms. |
| PREVIEW | Escuchar sin aplicar; START también preescucha. |
| APPLY | Procesar el audio con esos ajustes. |

Ejemplo: recorta un loop completo de un compás, elige la duración de un compás y NEW BPM=100. Escucha PREVIEW. Si los transitorios se deforman, prueba otro GRAIN o un cambio de tempo menor. Luego APPLY y SAVE.

### 11.5 Efectos offline

EQ, DUST, DRIVE y OTT pueden aplicarse al audio desde el editor. **APPLY los imprime en la forma de onda**; SAVE conserva ese resultado en otro archivo. Es diferente de activar un efecto del instrumento, que se calcula durante la reproducción.

Si aplicas distorsión al sample y además DRIVE en el instrumento, se suman dos etapas de procesamiento. Si quieres comparar, conserva el archivo limpio y el procesado con nombres distintos.

### 11.6 Qué es un slice

Un **slice** es un fragmento delimitado por marcadores dentro de un mismo sample. Un break de batería puede contener bombo, caja y hats; al cortarlo en slices puedes disparar cada golpe por separado sin cargar diez archivos.

Hay dos ajustes llamados SLICE que cumplen funciones distintas:

| Dónde | Opciones | Para qué sirve |
|---|---|---|
| SAMPLE EDITOR | OFF / TRANSIENT / DIVIDE / MANUAL | Crear y editar los marcadores. |
| INSTRUMENT | OFF / CUT / TRU | Decidir cómo se reproducen esos fragmentos. |

En el editor:

- **OFF:** muestra marcadores existentes sin editarlos.
- **TRANSIENT:** detecta ataques; SENS ajusta la sensibilidad.
- **DIVIDE:** reparte divisiones iguales; útil para un loop bien recortado.
- **MANUAL:** coloca los límites tú mismo.

En MANUAL, con el cursor en la fila de posición de slice, inicia la preescucha y pulsa A en los golpes para poner límites. También puedes avanzar al slot después del último y mover su posición para crear otro. A+flechas ajusta el límite; A+B lo elimina. En TRANSIENT/DIVIDE, A+B devuelve ese límite a su posición calculada.

**Las operaciones que cambian la longitud del audio pueden borrar todos los marcadores.** Recorta y estira primero; corta en slices después.

### 11.7 Tocar slices y usar CHOP

Guarda el WAV con sus marcadores, vuelve al instrumento y elige:

- **CUT:** cada nota dispara su slice y se detiene en el siguiente límite.
- **TRU:** arranca en el slice, pero permite continuar a través del audio posterior.

Las notas, a partir de ROOT, seleccionan slices consecutivos. Por eso en este modo no se tratan simplemente como alturas melódicas ni se ajustan a escala. El comando **SLI** permite elegir un índice de slice explícitamente y separar esa elección de otros cambios de tono.

**CHOP** exporta los fragmentos como WAV independientes en `Samples/Chops/nombre/`. No los carga automáticamente en una colección de instrumentos: deberás asignarlos a slots si quieres tocarlos por separado de esa manera.

### 11.8 Extraer audio de un video

En Android, el navegador puede convertir audio de videos compatibles, como MP4, MKV, WebM, 3GP o MOV, utilizando los decodificadores del sistema. La extensión por sí sola no garantiza que el códec interior sea compatible.

La ruta está pensada para clips cortos, hasta aproximadamente 60 segundos; la preescucha está limitada. Para una grabación larga, recórtala antes. La conversión toma una pista de audio del video y genera un WAV que luego puedes editar. Un video sin audio, con un códec no disponible o demasiado largo puede fallar.

<a id="modulacion"></a>
## 12. MODS: envolventes y movimiento

**Modular** significa cambiar un parámetro con otra señal. Si el volumen sube lentamente cada vez que tocas una nota, eso puede ser una envolvente. Si el panorama va y vuelve continuamente, eso puede ser un LFO.

Cada instrumento tiene **cuatro slots de modulación**. Entra con R+arriba desde INSTRUMENT. Cada slot tiene TYPE, DEST y AMT, además de los controles propios de su tipo.

- **TYPE:** qué genera el movimiento.
- **DEST:** qué parámetro recibe ese movimiento.
- **AMT:** cuánta influencia tiene. Empieza moderado, excepto cuando necesites que una envolvente de volumen controle toda la amplitud.

### 12.1 Envolventes: una forma que sigue cada nota

| Tipo | Etapas | Uso típico |
|---|---|---|
| `---` | Desactivado. | Slot libre. |
| AHD | Ataque → mantenimiento → caída. | Un sonido que entra y se apaga por sí solo. |
| ADSR | Ataque → caída → nivel sostenido → liberación. | Bajos, pads y notas cuya duración controlas con OFF. |
| DRUM | Forma percusiva con ataque, cuerpo y caída. | Golpes y caídas de tono. En esta base comparte la máquina de etapas de AHD. |
| TRIG | Controles ATK, DEC, SUS y REL. | En esta base se comporta como ADSR. |
| LFO | Onda repetitiva o un ciclo, según TRIG. | Vibrato, tremolo, autopan y movimiento de filtro. |

| Parámetro | Significado |
|---|---|
| ATK | Tiempo de ataque: cuánto tarda en llegar al pico. |
| HOLD | Cuánto mantiene el pico antes de caer. |
| DEC | Tiempo de caída: hacia cero en AHD o hacia SUS en ADSR. |
| SUS | **Nivel**, no tiempo, que conserva ADSR mientras la nota continúa. |
| REL | Tiempo de salida después de OFF. |

Los tiempos de envolvente se expresan en ticks y siguen el tempo. Si cambias los BPM, una misma cantidad de ticks dura menos o más segundos.

Un release largo no puede inventar audio cuando un sample de una sola reproducción ya terminó. Para un pad sostenido necesitas material suficientemente largo, un loop o un patch adecuado.

### 12.2 Destinos

| DEST | Qué mueve |
|---|---|
| VOL | Amplitud. |
| PAN | Posición estéreo. |
| PITCH / FINE | Afinación; utiliza poca cantidad para un vibrato sutil. |
| CUT | Frecuencia del filtro; activa antes el filtro. |
| RES | Resonancia. |
| STA | Punto de inicio del sample. |
| MOD A | Cantidad del siguiente modulador. |
| MOD R | Velocidad del siguiente modulador. |
| MOD B | Cantidad y velocidad del siguiente modulador. |

La interfaz puede mostrar el destino de otro modulador como `→M2 AMT`, `→M2 RTE` o similar. El recorrido es circular: MOD1 afecta a MOD2, MOD2 a MOD3, MOD3 a MOD4 y MOD4 a MOD1. No necesitas usar esta modulación entre moduladores para hacer un buen sonido.

### 12.3 LFO: una perilla que se mueve sola

Un LFO no es necesariamente un sonido audible. Es una señal lenta que mueve un parámetro.

| Control | Opciones / efecto |
|---|---|
| OSC | TRI triangular, SIN senoidal, RMP+/− rampas, EXP+/− curvas exponenciales, SQU+/− cuadradas, RND aleatorio por saltos, DRK movimiento errático. |
| FREQ | Velocidad del movimiento. Es diferente de la FREQ del filtro. |
| TRIG=FREE | Sigue su propio reloj sin reiniciarse con cada nota. |
| TRIG=RETG | Reinicia la forma al tocar una nota; resultado repetible. |
| TRIG=HOLD | Toma un valor del movimiento al comenzar la nota y lo conserva durante esa nota. |
| TRIG=ONCE | Ejecuta un ciclo al disparar la nota y se detiene al terminarlo. |

Para aprender, configura LFO → PAN, SIN, RETG y una cantidad moderada. Mantén una nota larga: deberías escuchar cómo se desplaza. Si el sample dura una décima de segundo y el LFO es lento, apenas habrá tiempo para percibirlo.

### 12.4 Ejercicio: un pad con entrada suave

1. Usa un sample largo/en loop o un SoundFont de pad.
2. MOD1: TYPE=ADSR, DEST=VOL, AMT=`FF`.
3. Prueba ATK=`18`, DEC=`0C`, SUS=`C0`, REL=`18`.
4. En una frase, escribe una nota en `00` y OFF en `0C`.
5. Escucha y ajusta ATK/REL al tempo y al material.

Esos valores son un punto de partida, no un preset universal. Si no oyes nada, verifica primero la fuente, el loop y los niveles; después la modulación.

<a id="tablas"></a>
## 13. TABLE: un secuenciador dentro del instrumento

Una tabla es una secuencia pequeña de **16 filas** que transforma el instrumento mientras tocas una nota. PHRASE dice cuándo empieza la nota; TABLE puede cambiar su volumen, afinación o efectos dentro de ella.

La tabla por defecto tiene el mismo ID que el instrumento: instrumento `03` usa tabla `03`. El comando TBL puede elegir otra para una nota.

### 13.1 Qué contiene

| Columna | Función |
|---|---|
| N | Transposición en semitonos con signo. `00` neutro, `0C` +12, `F4` −12. |
| VOL | Multiplicador de volumen; `--` conserva el estado. |
| FX1–FX3 | Comandos de efectos, con las restricciones de contexto de cada uno. |

### 13.2 TIC decide cómo avanza

| TIC | Comportamiento |
|---|---|
| `06` | Valor normal: una fila de tabla cada seis ticks; dos por paso normal de frase. |
| `03` | Doble de rápido que `06`. |
| `0C` | Una fila de tabla por paso normal de frase. |
| `00` | **Avanza una fila por nota nueva**, no por tiempo. |
| `FC` | Mapeo según la octava de la nota. |
| `FE` | Mapeo según la clase de nota. |
| `FF` | Avance a 200 Hz, para efectos muy rápidos. |

No confundas `TIC00` con la selección de una fila por altura: en este modo importa el orden de las notas que disparas. Los modos de mapa son FC y FE.

### 13.3 Tres lectores independientes

Las tres columnas FX tienen sus propios puntos de lectura. **FX1 lleva además N y VOL.** Por eso una tabla puede hacer un ciclo de tono de una longitud y un ciclo de efecto de otra.

- HOP dentro de TABLE dirige únicamente la columna donde está escrito.
- TIC dentro de TABLE cambia la velocidad de esa columna.
- HOP FF detiene esa columna; no obliga a detener las otras dos.
- THO desde PHRASE mueve los tres lectores; dentro de TABLE mueve el de su columna.

Para tu primera tabla, utiliza solo N, VOL y FX1. Añade las otras columnas cuando ese recorrido resulte claro.

### 13.4 Ejercicio: un movimiento de cuatro filas

En una tabla vacía del instrumento, con TIC=`06`:

| Fila TABLE | N | VOL | FX1 |
|---|---|---|---|
| `00` | `00` | `FF` | Vacío |
| `01` | `03` | `C0` | Vacío |
| `02` | `07` | `A0` | Vacío |
| `03` | `0C` | `80` | Vacío |
| `04` | `00` | `--` | `HOP00` |

Toca una nota larga del instrumento. Las cuatro filas musicales forman un pequeño arpegio menor con cambio de volumen.

**El HOP se coloca en la fila `04`, después de las cuatro filas musicales.** El salto no consume una fila de tiempo como una nota normal. Ponerlo en `03` no equivale a reproducir cuatro filas completas y luego volver.

### 13.5 TBL, acordes y automatizaciones de tabla

**TBL es una selección para la nota actual.** Una fila vacía posterior puede conservar esa selección, pero una **nota nueva sin TBL vuelve a la tabla por defecto del instrumento**. Si necesitas la tabla alternativa en cada nota, vuelve a escribir el comando.

CHD puede funcionar en tablas cuando su columna usa TIC `00`, `FC` o `FE`: la nota consulta el acorde de esa fila. No esperes que un CHD en una columna de avance temporal normal cree automáticamente una progresión de acordes.

AUS/AUF también permite rampas dentro de una tabla. El final debe estar en una fila posterior, sin envolver más allá de `0F`. Aquí la lista de destinos admitidos es más pequeña: VOL, PAN, CUT, RES, LPF, HPF, BPF, DRV, FIN, TIM, EQN y EQM. El tiempo sigue al lector de la columna del parámetro automatizado.

Una tabla compartida por varias notas/instrumentos afecta a todos los que la consultan. Guarda antes de transformar una tabla que ya funciona en otras partes de la canción.

<a id="groove-escalas"></a>
## 14. GROOVE y SCALE: ritmo y tonalidad

### 14.1 GROOVE cambia cuánto dura cada paso

El groove es una lista de duraciones. En vez de tocar todos los pasos con la misma distancia, puedes alternar uno largo y otro corto: eso produce swing.

Con PPQ=48, el paso recto dura **12 ticks**, escrito `0C`. Un groove de dos entradas `0C`, `0C` es recto. Uno `0E`, `0A` alterna **14 y 10**: el total sigue siendo 24, pero el segundo paso llega más tarde.

Ejercicio:

1. Desde PHRASE entra en GROOVE con R+arriba.
2. Elige slot `01`, dejando el `00` para el ritmo recto.
3. Mantén PPQ=48 y escribe `0E` en la primera entrada, `0A` en la segunda; termina la lista con una entrada vacía.
4. En la frase del hi-hat, escribe `GRV01` en la primera fila.
5. Escucha cómo cambia la colocación de los golpes.

**GRV elige un groove para la pista.** Afecta también a las notas siguientes que recorra esa pista, hasta que lo cambies. `GRV00` solo significa «recto» si el slot `00` sigue conteniendo un groove recto.

### 14.2 No perder la duración del compás

Para conservar el promedio del paso recto con PPQ=48, la lista debe promediar 12 ticks por entrada. Dos entradas suman 24; cuatro, 48. Si cambias el promedio, cambias la duración efectiva y puedes desajustar esa pista frente a las otras.

No cuentes con una corrección automática al terminar la frase. Si quieres un ritmo regular, revisa las sumas o usa los presets.

| Control | Significado |
|---|---|
| PPQ | Resolución de groove: 24, 48 o 96. El paso recto equivale respectivamente a 6, 12 o 24 ticks de esa lista. |
| QNT | Ayuda de edición que equilibra duraciones por pares/grupos según el ajuste; no graba ni cuantiza audio. |
| SWG | Lectura del swing resultante; no otra perilla independiente. |
| SAVE / LOAD | Guardar y cargar grooves. |
| Nombre del preset | Cargar una forma predefinida en el slot actual. |

Cambiar PPQ reescala los datos del groove para conservar el ritmo. Esto no convierte todos los valores de LAT, KIL o TIC en una unidad nueva: esos comandos conservan su referencia de ticks de efecto.

Los presets incluyen STRAIGHT, distintas cantidades de swing de corchea/semicorchea, TRIPLET, HALFTIME y DOUBLETIME. Cargar uno reemplaza los valores del slot que estás editando y afecta a todas sus referencias.

Un valor **`00`** en la lista puede saltar un paso; **`--`** termina la lista. No son equivalentes. Por ejemplo, `10`, `10`, `10`, `00` distribuye tres pasos de 16 ticks y uno saltado dentro de 48 ticks.

### 14.3 SCALE ayuda a elegir notas compatibles

Una escala es una selección de notas permitidas. **Cromática** contiene las doce; una escala mayor o menor suele contener siete; una pentatónica, cinco.

Entra en SCALE con R+arriba desde GROOVE. Hay 16 slots. El slot `00` comienza cromático, por lo que no restringe las notas.

- La fila de nombre permite elegir un preset, como mayor, menor o pentatónica.
- **KEY** es la raíz global del proyecto. Cambiarla relabela y desplaza la referencia tonal de todas las escalas.
- Las filas `0`–`B` activan o desactivan los doce intervalos. La raíz no se puede apagar.
- **LEN** indica cuántas notas contiene la escala.
- SAVE/LOAD conserva o recupera un archivo de escala.

Con una escala activa, al editar notas avanzas por las permitidas; al reproducir, las notas se ajustan a la escala. Eso puede modificar también el resultado de transposiciones y arpegios. **No es una corrección de afinación del audio grabado**, como afinar automáticamente una voz: mueve los eventos de nota.

### 14.4 Elegir escala desde la frase

**SCA XY** cambia la escala de una pista; **SCG XY** cambia la de las ocho. X es la tonalidad: `0=C`, `1=C#`, `2=D`, `3=D#`, `4=E`, `5=F`, `6=F#`, `7=G`, `8=G#`, `9=A`, `A=A#`, `B=B`. Y es el slot de escala `0`–`F`.

Si guardaste la escala menor natural en slot `01`, **SCA91** selecciona La + ese slot en la pista. El efecto permanece durante la reproducción hasta otro comando o hasta detenerla. El comando escrito sí se guarda con tu proyecto; lo que se restablece al parar es el estado temporal del reproductor.

Dos excepciones útiles:

- Los instrumentos con TSP=OFF no siguen esa adaptación tonal.
- Las notas que seleccionan slices tampoco: moverlas de escala cambiaría el fragmento elegido.

Mientras editas, el cálculo de contexto busca SCA/SCG en la **misma frase, en o por encima del cursor**. Puede no reflejar un comando que llegó de una frase anterior o de otra pista durante SONG. Si la preescucha y la canción completa difieren, revisa ese contexto.

<a id="mezcla"></a>
## 15. MIXER, reverb, delay y EQ

### 15.1 Las distintas capas de volumen

Puedes pensar en la ruta seca como una combinación de **volumen del instrumento × intensidad de nota × modulación/tabla × fader de pista × master**. Cada etapa puede bajar el resultado; subir otra no siempre compensa el problema de la primera.

| Control | Alcance |
|---|---|
| V de PHRASE, `00`–`7F` | Intensidad de ese evento. |
| VOL de INSTRUMENT, `00`–`FF` | Nivel base de ese instrumento en todas sus referencias. |
| VOL de TABLE / envolvente | Forma dinámica mientras suena. |
| Fader de pista en MIXER | Nivel de la ruta seca de esa pista. |
| REV/DEL de instrumento o nota | Cuánto entra a los buses compartidos. |
| REV/DEL del master en MIXER | Nivel de retorno de cada bus. |
| Master MIX | Nivel del conjunto. |

En faders de amplitud, `FF` es nivel completo, `80` ronda −6 dB y `00` es silencio. No son porcentajes de sonoridad percibida.

### 15.2 El detalle de los envíos pre-fader

Los envíos a reverb y delay se toman **después del nivel de instrumento/nota, pero antes del fader de pista**. Por eso bajar una pista en MIXER puede dejar su reverb o delay audibles.

Esto sirve para retirar el sonido directo y conservar una cola. Si querías apagarlo todo, revisa también sus envíos y los retornos; VTR00 por sí solo no equivale a borrar todas las colas. El master sí controla la salida conjunta.

### 15.3 Medidores y margen

Los medidores muestran nivel digital en dBFS. El rojo indica que estás alcanzando la zona donde el limitador debe trabajar con fuerza. Aunque la protección evite recorte directo en la salida, puedes perder dinámica o distorsionar el carácter.

Empieza bajando las pistas más fuertes. Deja espacio para que al sumar bombo, bajo y acordes no todo empuje permanentemente al límite. **NORMALIZE no hace esta mezcla por ti.**

El control **LIM** añade empuje antes de la limitación. `00` significa sin ese empuje adicional; no significa necesariamente «desactivar toda protección».

### 15.4 Reverb: sensación de espacio

Abre EFFECTS desde MIXER con R+abajo, o entra desde la celda de retorno correspondiente.

| Control | Qué escuchar al moverlo |
|---|---|
| Preset NORMAL / ROOM / HALL / CAVE | Puntos de partida para distintos espacios. |
| ALGO: OLD / HALL / ROOM / PLATE / FOIL | Cambia el carácter del algoritmo. |
| PRE | Tiempo antes de que aparezca la cola; hasta unos 150 ms. Separa el golpe de la reverberación. |
| SIZE | Tamaño del espacio percibido. No es el volumen ni simplemente la duración de la cola. |
| DCAY | Duración de la cola. `FF` puede sostenerla indefinidamente. |
| WIDE | Anchura: `00` mono, `80` imagen original del efecto, `FF` expansión. |
| DAMP | Brillo de la cola: bajo más oscuro, alto más brillante. |
| MOD | Movimiento tonal de la cola. |
| INP EQ | Ecualizador **antes de entrar al bus**, no un ecualizador posterior del retorno. |

PLATE y FOIL no utilizan SIZE/MOD de la misma forma que una sala; los controles no aplicables aparecen atenuados. Elegir un preset de reverb carga sus parámetros, excepto el slot EQ. SAVE/LOAD permite conservar ajustes propios.

Para una caja, empieza con envío REV bajo y una cola corta. Para un pad puedes probar más envío y mayor decay. Evita que el grave del bajo llene de cola toda la mezcla: usa poco envío o filtra la entrada al bus.

### 15.5 Delay: repeticiones

| Control | Función |
|---|---|
| Preset NORMAL / PING / TAPE | Cambia PONG, TONE y WOBL como punto de partida. |
| PONG | Alterna los ecos entre izquierda y derecha. |
| TIME | Separación entre ecos. **Un toque de A** cambia entre libre y sincronizado. |
| TONE | Brillo de las repeticiones; más bajo, ecos más oscuros. |
| FDBK | Cuánto vuelve a entrar: determina cuántas repeticiones permanecen. |
| WOBL | Variación de velocidad que produce movimiento de afinación. |
| REV | Cantidad de la salida del delay enviada a reverb. |
| INP EQ | Ecualización previa a la entrada del delay. |

TIME libre cubre aproximadamente 0–2 segundos con `00`–`FF`. En modo sincronizado eliges una subdivisión musical: `1/1`, `1/2`, `1/4`, `1/8`, `1/16`, `1/32`, tresillos `1/4T`, `1/8T`, `1/16T` y valores con puntillo `1/4.`, `1/8.`, `1/16.`.

**T** significa tresillo; el punto significa una duración de una vez y media la figura normal. Un delay a `1/8.` puede cruzarse de forma rítmica con las corcheas rectas.

Mover TIME con ecos activos puede doblar su afinación, como al cambiar la velocidad de una cinta. El comando TIM siempre usa la escala libre, aunque la pantalla estuviera sincronizada.

FDBK cerca de `E0` y por encima puede sostener o autooscilar el delay. Si «sigue sonando solo», baja FDBK y el retorno. Para aprender, prueba primero alrededor de `60`–`80` y envíos moderados.

Los presets integrados de delay no reemplazan todos los campos. Un archivo cargado con LOAD sí conserva un conjunto más amplio, incluyendo tiempo/sync y feedback, excepto EQ. Revisa TIME tras cambiar de preset.

### 15.6 OTT y DUST en el master

En EFFECTS seleccionas **TYPE**; la cantidad del efecto se controla en MIXER.

- **OTT:** compresión multibanda que puede acercar detalles suaves y controlar partes fuertes. Mucha cantidad puede exagerar el ataque, el ruido y el bombeo.
- **DUST:** procesamiento de carácter lo-fi que combina color, saturación, movimiento y compresión.

Empieza con poca cantidad y compara al mismo nivel percibido. Un sonido más fuerte parece mejor con facilidad, aunque haya perdido claridad. Puedes exportar stems y dejar este tipo de procesamiento para el DAW.

### 15.7 EQ: tres bandas para corregir o dar carácter

Un ecualizador modifica unas frecuencias más que otras. Cada slot tiene tres bandas, y cada banda cuatro controles:

| Control | Función |
|---|---|
| TYPE | Forma del filtro. |
| FREQ | Zona de trabajo, aproximadamente 20 Hz–20 kHz. |
| GAIN | Subida o bajada, −12 a +12 dB; pasos pequeños de 0,1 dB y grandes de 1 dB. |
| Q | Anchura: valor alto, zona más estrecha. |

| TYPE | Para qué sirve |
|---|---|
| OFF | Desactivar la banda. |
| LOSHELF | Subir o bajar el conjunto de graves bajo FREQ. |
| LOWCUT | Recortar graves; es un paso alto. |
| BELL | Subir o bajar una zona alrededor de FREQ. |
| HISHELF | Subir o bajar el conjunto de agudos. |
| HICUT | Recortar agudos; es un paso bajo. |

A sobre una celda EQ abre el editor; B cierra conservando los ajustes. B+izquierda/derecha recorre slots. El analizador muestra el contexto desde el que abriste la EQ.

**Los slots de EQ se comparten.** Si bombo y bajo usan EQ `02`, modificar ese preset afecta a los dos. Usa slots diferentes cuando quieras curvas independientes. No confundas el número del preset con su intensidad.

Un punto de partida razonable es recortar suavemente donde sobra energía, antes de subir todo. En un sonido que no necesita subgrave, LOWCUT puede liberar espacio; en el bombo o bajo, escuchar es más útil que aplicar el mismo corte por costumbre.

<a id="efectos"></a>
## 16. Referencia completa de comandos FX

Esta sección cubre los códigos de efectos de la base revisada. No necesitas memorizarlos. Busca el que resuelva lo que quieres hacer y prueba uno a la vez.

En las tablas, `VOL80` significa **tipo VOL y valor 80**; la interfaz los presenta como celdas separadas. `---` es un espacio sin efecto. **XX** representa un valor completo; **XY**, dos dígitos con funciones separadas.

### 16.1 Elegir el alcance correcto

| Quiero cambiar… | Comandos habituales |
|---|---|
| La nota o voz actual | VOL, PAN, PIT, FIN, CUT, RES, LPF, HPF, BPF, DRV, CRU, REV, DEL, EQN. |
| Cómo continúa o se repite una voz | ARP, ARC, RPT, PBN, PVB, PVX, PSL. |
| El ritmo o recorrido de una pista | LAT, GRV, HOP, NTH. |
| El instrumento o su tabla | INS, TBL, THO, TIC. |
| La mezcla de una pista | VTR. |
| Toda la mezcla o un bus compartido | VMV, EQM, TIM; SCG para la tonalidad global. |
| Una transición suave | AUS y AUF, con un parámetro compatible. |
| Un equipo MIDI | MPG, MPB, CCA, CCB, CCC, CCD. |

Los efectos por nota suelen volver a la base del instrumento con la siguiente nota que no los especifique. Los de pista o globales pueden persistir hasta otro comando o hasta STOP. Los comandos continuos como ARP/RPT tienen sus propias reglas de cancelación. **No existe una sola regla de duración válida para todos.**

Si una automatización está escrita más arriba y arrancas desde mitad de la canción, puede no haberse ejecutado. Para secciones reproducibles, escribe al inicio los ajustes que necesitan.

### 16.2 Notas, acordes y afinación

| Código | Valor y función | Ejemplo |
|---|---|---|
| **ARP** | XY: alterna nota base, base+X y base+Y semitonos. `00` cancela. | `ARP37`: base, tercera menor y quinta. |
| **ARC** | XY: orden X y separación Y en ticks. X=0 ascendente, 1 descendente, 2 alternado, 3 aleatorio. Configura ARP o el rasgueo de CHD según su posición. | `ARP47` seguido de `ARC04`: arpegio mayor ascendente cada cuatro ticks. |
| **CHD** | XY: tipo de acorde X y distribución/inversión Y. Requiere una nota en la fila de PHRASE. | `C4` + `CHD00`: Do mayor. |
| **PIT** | XX con signo: desplazamiento inmediato en semitonos. No selecciona otro slice. | `PIT0C`: +12; `PITF4`: −12. |
| **PSL** | XX: tiempo de deslizamiento desde la nota anterior a la nueva, en ticks. `00` inmediato. | `PSL0C`: transición durante un paso normal. |
| **PBN** | XX con signo: velocidad de cambio continuo de tono. `00` cancela; positivos suben, negativos bajan. | `PBNFF`: bajada lenta; `PBN00`: detener la bajada. |
| **PVB** | XY: velocidad X y profundidad Y de vibrato. `00` cancela. | `PVB42`: vibrato moderado para probar. |
| **PVX** | XY: vibrato más extremo que PVB, con mayor velocidad y profundidad. | `PVX42`: compara con PVB42 al mismo nivel. |
| **FIN** | XX: afinación fina; `80` neutral, `00` aproximadamente −100 cents, `FF` aproximadamente +100. | `FIN84`: ligeramente agudo. |
| **TSX** | XX con signo: multiplicador de la transposición de cadena/proyecto para esa nota. Solo PHRASE. | `TSX00` no sigue esa transposición; `01` normal; `02` doble; `FF` inversa. |

**ARP no es lo mismo que CHD.** ARP recorre alturas en el tiempo; CHD dispara un acorde de voces simultáneas o escalonadas. `ARP4B` da la base, tercera mayor y séptima mayor: aporta ese color, pero no incluye por sí solo las cuatro notas de un acorde maj7 completo.

ARP continúa sobre filas sin nota. Una nota nueva, otro efecto en su misma columna, ARP00 o KIL pueden cancelarlo. RPT tiene una lógica de continuidad parecida. PBN puede seguir doblando la afinación si no lo cancelas: no lo dejes activo por accidente.

PSL necesita una nota anterior que dé sentido al deslizamiento; probarlo como primer evento aislado puede no demostrar lo que esperas.

#### Tipos de acorde de CHD

| X | Acorde | X | Acorde |
|---|---|---|---|
| `0` | Mayor | `8` | Séptima dominante |
| `1` | Menor | `9` | Sexta mayor |
| `2` | Suspendido 2 | `A` | Sexta menor |
| `3` | Suspendido 4 | `B` | Add9 |
| `4` | Disminuido | `C` | Menor add9 |
| `5` | Aumentado | `D` | Novena mayor sin quinta |
| `6` | Séptima mayor | `E` | Novena menor sin quinta |
| `7` | Séptima menor | `F` | Menor 11 |

| Y | Distribución de las voces |
|---|---|
| `0`–`3` | Posición base y tres inversiones sucesivas. |
| `4`–`7` | Esas posiciones con drop-2: baja una octava la segunda voz más alta. |
| `8`–`B` | Con drop-3: baja una octava la tercera voz más alta. |
| `C`–`F` | Abiertas: expande las voces extremas. |

Las tríadas duplican una voz a la octava para completar cuatro voces. La escala activa puede mover las notas del acorde, de modo que el resultado no siempre conserva exactamente la calidad que elegiste. La siguiente nota sin CHD vuelve a ser individual.

**Rasgueo:** escribe `CHD00` en FX1 y `ARC02` en FX2. Las notas entran de grave a agudo cada dos ticks, permaneciendo superpuestas. ARC con velocidad 0 las toca a la vez.

ARC se asocia al CHD o ARP más cercano a su izquierda. Si entre CHD y ARC hay un ARP, ARC configura ese arpegio. En TABLE, CHD necesita uno de los modos por nota TIC00/FC/FE; no es un comando general de acordes por tiempo.

### 16.3 Tiempo, repetición y decisiones

| Código | Valor y función | Ejemplo |
|---|---|---|
| **LAT** | XX ticks de retraso para los eventos de esa fila. | `LAT03`: un cuarto de paso normal más tarde. |
| **GRV** | XX selecciona groove `00`–`7F` para la pista. | `GRV01`: usa el groove 01. |
| **HOP** | XY: salto con significado distinto en PHRASE y TABLE. | En PHRASE, `HOP00` termina aquí y entra en la siguiente frase desde su fila 00. |
| **KIL** | XX ticks hasta cortar la voz y sus efectos persistentes. | `KIL00` inmediato; `KIL06` medio paso después. |
| **RPT** | XY: con Y=0, repite cada X ticks. Con Y distinto de 0, Y es el intervalo y X controla la variación de volumen. | `RPT60`: cada seis ticks; `RPT30`: cada tres. |
| **CHA** | XY: probabilidad del vecino izquierdo y derecho de la celda. 0 nunca, F siempre. | `CHA8F` en FX1 puede hacer que la nota suene aproximadamente la mitad de las veces. |
| **NTH** | XY: permite toda la fila en la pasada X de cada Y. Y=0 significa solo la pasada X. Solo PHRASE. | `NTH44`: cada cuarta pasada; `NTH30`: solo en la tercera. |
| **RND** | XX: repite el efecto más cercano por encima en la misma columna, sumándole un valor aleatorio entre 00 y XX. | Después de VOL80, RND10 da valores entre 80 y 90. |
| **RNL** | XX en FX2/FX3: añade aleatoriedad al efecto inmediatamente a la izquierda. En FX1 de PHRASE usa XY para variar nota e instrumento. | `INS04` + `RNL03`: elige instrumento 04–07. |

#### HOP: dónde salta y qué no reproduce

En **PHRASE**, la fila de HOP no toca su nota ni consume su tiempo normal: termina la frase y la siguiente comienza en la fila Y. X se ignora, excepto el caso especial FF. En preescucha de una sola frase no existe otra a la que ir, así que vuelve a la misma. En una cadena o canción sí escucharás el recorrido real.

En **TABLE**, HOP dirige su propia columna a la fila Y, repitiendo X veces antes de continuar; X=0 repite indefinidamente. La fila de salto tampoco añade un paso musical.

**HOP FF** detiene la pista en PHRASE; en SONG puede esperar al avance correspondiente de canción, mientras en preescucha permanece detenida hasta parar. En TABLE detiene solo la columna donde está. No uses FF como si significara «ir a la última fila».

#### RPT: el dígito importa

`RPT60` y `RPT06` **no son dos maneras de escribir lo mismo**. El modo sencillo coloca el intervalo en el primer dígito y un cero en el segundo. Si Y es distinto de cero, el intervalo pasa a ser Y y X controla el volumen: 1–7 decae; 8–F crece.

Una nueva nota vuelve a disparar su propio sonido y termina la repetición previa. Para un roll, usa un sample corto, una sola nota inicial y luego filas vacías hasta el punto de corte. RPT00 o KIL permiten detenerlo.

#### CHA: no siempre aleatoriza la nota

CHA mira **el efecto ocupado más cercano a la izquierda**; solo cuando no existe toma la nota. A la derecha mira el siguiente efecto ocupado. Solo controla esos vecinos, no automáticamente toda la fila.

| FX1 | FX2 | Resultado |
|---|---|---|
| `CHA8F` | Vacío | La nota puede omitirse; `8/F` es aproximadamente 53 %, no un 50 % exacto. |
| `LPF40` | `CHA8F` | La nota sigue ocurriendo; el filtro de FX1 es el que tiene probabilidad. |

Para decidir si ocurre **la fila completa** en vueltas concretas, usa NTH. Una fila omitida por NTH conserva su tiempo: no acorta el patrón. `NTH12` toca en vueltas impares, `NTH22` en pares y `NTH00` no restringe. El conteo comienza de nuevo al iniciar reproducción; no equivale a un número absoluto de compás del arreglo.

En TABLE, CHA puede controlar N/V cuando no tiene efecto a la izquierda. RND funciona buscando hacia arriba en su columna; RNL en FX1 de TABLE no aleatoriza una nota como lo hace en PHRASE.

### 16.4 Sample, dirección, instrumento y tabla

| Código | Valor y función | Ejemplo |
|---|---|---|
| **OFF** | XX: offset o posición dentro del sample. **Es un efecto, no la nota OFF.** | `OFF80`: empieza aproximadamente a mitad del audio. |
| **SLI** | XX: índice de slice, independiente de la nota. Puede actuar incluso con SLICE=OFF si existen marcadores. | `SLI03`: elige slice 03. |
| **BCK** | `00` hacia atrás; `01` hacia adelante. Sampler. | En una fila sin nota, BCK00 invierte desde la posición actual. |
| **LPO** | XX con signo: desplaza la ventana de loop en dieciseisavos de su propia longitud. Acumulativo. | `LPO10` avanza una longitud de loop; `LPOF0` retrocede una. |
| **INS** | XX: instrumento `00`–`7F` que sustituye a I para esa nota. | `INS05`: toca el instrumento 05. |
| **TBL** | XX: tabla `00`–`7F` para esa nota. | `TBL08`: usa tabla 08; la siguiente nota sin TBL vuelve a su tabla base. |
| **THO** | XX: salta a una fila de tabla, dentro de `00`–`0F`. | `THO04`: comienza/continúa desde la fila 04. |
| **TIC** | XX: ritmo de avance de la tabla/columna, con los modos especiales de la sección TABLE. | `TIC0C`: un avance por paso normal. |

LPO requiere un loop configurado para que su desplazamiento tenga sentido musical. Mueve los límites juntos, se detiene en los extremos del audio y se restablece en una nota nueva. Es una función avanzada; no la necesitas para hacer un loop convencional.

En PHRASE, INS necesita una nota y un slot válido con sonido. En TABLE tiene un uso especial: entrega la nota a otro instrumento, que trae su propia tabla. Lo que está a la derecha de INS en esa fila no se procesa. Con TIC00 puede rotar sonidos por nota; en otros ritmos se consulta la fila de entrada correspondiente a esa ruta, no un cambio libre de instrumento en mitad de cada sonido. Usa esta función después de entender las tablas básicas.

### 16.5 Volumen, estéreo, efectos y tonalidad

| Código | Valor y función | Alcance que debes recordar |
|---|---|---|
| **VOL** | XX `00`–`FF`: nivel de la voz. | Reemplaza la base de volumen de instrumento para ese evento; sigue combinándose con intensidad y otras etapas. |
| **PAN** | XX: 00 izquierda, 80 centro, FF derecha. | Nota actual; una nota nueva sin PAN vuelve al panorama del instrumento. |
| **REV** | XX: envío a reverb. | Sobrescribe el envío de esa nota, no el ajuste de todas las notas futuras. |
| **DEL** | XX: envío a delay. | Como REV, para el bus de delay. |
| **EQN** | XX: slot EQ `00`–`7F`. | EQ de esa nota. Un slot con las bandas apagadas sirve para no ecualizarla. |
| **EQM** | XX: slot EQ `00`–`7F`. | Master; se conserva hasta otro EQM y vuelve al ajuste guardado al parar. |
| **VTR** | XX: fader `00`–`FF` de la pista que ejecuta el comando. | Reemplaza el fader durante la reproducción; no reduce por sí solo los envíos pre-fader. |
| **VMV** | XX: fader master `00`–`FF`. | Todo el conjunto, incluidos retornos. Cualquier pista puede escribirlo. |
| **TIM** | XX: tiempo libre de delay, aproximadamente 0–2 segundos. | Global; no interpreta los números como índices de subdivisión sincronizada. |
| **CUT** | XX: frecuencia del filtro, de grave/cerrado a agudo/abierto. | Necesita que el filtro esté activo. |
| **RES** | XX: resonancia. | No activa un filtro apagado. |
| **LPF** | XX: activa paso bajo y fija su corte. | Nota actual. |
| **HPF** | XX: activa paso alto y fija su corte. | Nota actual. |
| **BPF** | XX: activa paso banda y fija su corte. | Nota actual. |
| **DRV** | XX: drive, 00 limpio a FF intenso. | Nota actual. |
| **CRU** | XY: X bitcrush, Y downsample; cada uno de 0 a F. | Nota actual; no admite rampa AUS/AUF. |
| **SCA** | XY: tonalidad X y slot de escala Y. | Pista actual, hasta cambiarlo o parar. |
| **SCG** | XY: igual que SCA. | Las ocho pistas; si la misma fila lleva SCA, esa pista conserva su elección particular. |

Los valores que ves en MIXER son los ajustes de base escritos por ti. VTR/VMV pueden haber cambiado el valor usado al sonar sin que la pantalla reescriba ese número. STOP devuelve los faders a su base. TIM sigue un patrón similar con el tiempo de delay.

Para filtros, el corte no avanza linealmente en Hz: recorre aproximadamente 20 Hz–20 kHz en una escala exponencial. CUT80 no significa «128 Hz» ni exactamente la mitad de la percepción del brillo.

CRU80 reduce bits sin downsample; CRU08 hace lo contrario; CRU88 combina ambos. Un byte con dos controles separados no se debe interpretar como una única cantidad de suciedad.

### 16.6 AUS / AUF: automatizar de forma suave

**AUS** inicia una rampa; **AUF** marca su final y su valor objetivo. El parámetro inicial debe estar a la izquierda de AUS en la misma fila. AUF debe estar en una fila posterior.

| Fila | Nota | FX1 | FX2 |
|---|---|---|---|
| `00` | Una nota sostenida | `LPF30` | `AUS80` |
| `08` | Vacía | Vacío | `AUFE0` |

Esto abre un filtro paso bajo de 30 a E0 durante ocho pasos. No necesitas escribir CUT en cada fila.

El valor de AUS es **la curva**, no el destino:

| AUS | Forma |
|---|---|
| `00` | Sale despacio y acelera hacia el final. |
| `80` | Lineal. |
| `FF` | Sale rápido y se acerca lentamente al final. |

En PHRASE admite VOL, PAN, REV, DEL, VTR, VMV, TIM, CUT, RES, LPF, HPF, BPF, DRV, FIN, PVB, PVX, EQN y EQM. AUS busca el parámetro automatizable más cercano a su izquierda, saltando comandos que no sirven como destino.

- Solo hay **una rampa activa por pista** a la vez. Para dos movimientos simultáneos de alcance global o de pistas distintas, organízalos en pistas diferentes.
- Puede cruzar a frases posteriores **dentro de la misma cadena**.
- No empareja un inicio de una cadena con un final situado en la siguiente.
- Un segundo AUS antes del AUF reemplaza el inicio anterior.
- Una pareja inválida aparece atenuada: falta un destino compatible, no hay un AUF posterior, el AUF está en la misma fila o su valor no sirve para ese tipo.

**EQN/EQM son especiales:** el inicio y el final son IDs de presets. Se interpolan sus FREQ, GAIN y Q; no «pasan por todos los números de EQ» entre uno y otro. Para un resultado predecible, usa los mismos TYPE en las tres bandas de ambos presets. Una banda que parte en OFF permanece apagada durante la rampa. El ID final debe estar entre 00 y 7F.

Para un barrido largo sobre toda la música, EQM puede ser más evidente que EQN, que depende de las notas de la pista. En tablas se admite una lista más reducida de destinos y el recorrido no cruza más allá de la fila 0F; consulta TABLE.

### 16.7 Comandos MIDI

| Código | Valor y función | Detalle importante |
|---|---|---|
| **MPG** | XX `00`–`7F`: cambio de programa. | Colócalo en una fila vacía antes de la nota que necesita el nuevo sonido. |
| **MPB** | XX: posición absoluta del pitch bend. 00 abajo, 80 centro, FF arriba. | El rango lo decide BEND; mantiene la posición hasta otro MPB o STOP. |
| **CCA** | XX: valor para el controlador asignado a CC A. | La app convierte 00–FF al rango MIDI 0–127. |
| **CCB** | Igual para CC B. | Un slot sin CC asignado no envía nada. |
| **CCC** | Igual para CC C. | El número de controlador se configura en el instrumento. |
| **CCD** | Igual para CC D. | El comando de frase prevalece sobre el VAL base de ese slot en esa nota. |

Si usas MPG para elegir sonidos desde la secuencia, deja **PROG del instrumento en `--`** cuando corresponda. De lo contrario, la siguiente nota puede volver a enviar el programa fijo del instrumento.

MPB es una posición; PBN es una velocidad de doblado de tono. No son equivalentes. Para controlar un parámetro específico de un sintetizador, consulta qué CC recibe y asígnalo a CC A–D. No asumas que un efecto de audio interno tendrá la misma respuesta en cualquier equipo MIDI.

<a id="recetas"></a>
## 17. Recetas musicales para practicar

Estas recetas son ejercicios editables, no reglas de un género. Usa instrumentos distintos para sonidos simultáneos y comienza con el groove recto. Los números de fila son hexadecimales.

### 17.1 Un boceto de dembow

Prueba alrededor de 95–105 BPM:

| Pista | Sonido | Filas con golpes |
|---|---|---|
| 1 | Bombo | `00`, `08` |
| 2 | Rimshot o caja corta | `03`, `06`, `0B`, `0E` |
| 3 | Hi-hat cerrado | `00`, `02`, `04`, `06`, `08`, `0A`, `0C`, `0E` |
| 4 | Percusión adicional | Prueba un golpe suave en `07` o `0F`. |

Baja la intensidad de algunos hats y de la percusión de enlace. Después prueba un bombo adicional en `06` en una copia de la frase. Escucha cómo cambia el empuje; no necesitas rellenar todos los huecos.

### 17.2 Una textura de cumbia con güiro o shaker

Empieza con golpes cortos en las ocho corcheas y alterna `V=60` / `V=35`. Si tu güiro es una grabación larga, córtalo en dos o tres gestos y alterna sus slices. Una grabación ya contiene articulación: disparar siempre el mismo trocito puede hacerla más mecánica.

Añade un bajo sencillo con raíz y quinta, y prueba una percusión de respuesta al final del compás. Ajusta la posición de algunos golpes con LAT pequeño antes de añadir swing a toda la pista.

### 17.3 Ghost notes de caja

En la frase de caja con golpes principales en `04` y `0C`, añade golpes suaves en `03` y `0B` con `V=20`–`30`. Conserva los principales más fuertes. Una ghost note se siente como movimiento alrededor del golpe, no como otro acento igual de grande.

### 17.4 Roll de hi-hat al final

En una copia de la frase:

| Fila | Nota | FX1 |
|---|---|---|
| `0C` | Nota del hat | `RPT30` |
| `0D` | Vacía | Vacío |
| `0E` | Vacía | Vacío |
| `0F` | Vacía | `KIL00` |

Escucha la densidad. Si es demasiado, cambia a RPT60. Si quieres el fill cada cuatro vueltas de una misma frase, explora NTH44, teniendo en cuenta que las filas de inicio y fin deben diseñarse juntas; condicionar solo el inicio no convierte automáticamente todo el fragmento en una única unidad condicional.

### 17.5 Bajo con deslizamiento

Toca `A2` en `00` y `E3` en `08` con `PSL0C` en esa segunda fila. Ajusta el loop/envolvente para que haya material sostenido. Reduce el tiempo a `06` para un slide más corto.

Si el sample cambia demasiado de timbre al subir de nota, prueba otro sample raíz o un SoundFont; el comando no elimina las limitaciones de una muestra única.

### 17.6 Un barrido de filtro

Usa una nota larga, sin retriggers intermedios:

| Fila | FX1 | FX2 |
|---|---|---|
| `00` | `LPF30` | `AUS80` |
| `0C` | Vacío | `AUFE0` |

Esto enseña primero un movimiento por nota. Para barrer toda la mezcla, prepara dos presets de EQ con las mismas formas de banda y usa EQM + AUS/AUF. No pongas un filtro global sobre el master si solo querías cambiar el pad.

### 17.7 Delay en la última palabra o golpe

Deja DEL del instrumento bajo o en cero y escribe `DEL80` únicamente en la nota que quieres repetir. Configura TIME sincronizado y feedback moderado. Las otras notas conservan su envío base.

Para retirarlo con más suavidad, conserva la cola del bus aunque desaparezca el sonido directo. Si vas a exportar stems, revisa la limitación de detección de envíos explicada en la siguiente sección.

### 17.8 Un acorde convertido en sample

1. Programa un acorde con CHD y los efectos deseados.
2. Déjalo sonar dentro de una sección corta de SONG.
3. Selecciona el rango y las pistas que quieras resamplear.
4. Haz el resampling descrito en PROJECT.
5. Usa el instrumento nuevo para disparar ese acorde como un único sample.

Ahora puedes cortarlo, invertirlo o tocarlo a otra altura. El audio conserva el procesamiento impreso; modificar el acorde original ya no cambia ese WAV.

### 17.9 Variación controlada sin perder el beat

Mantén fijo el bombo principal. Pon CHA en una percusión secundaria, no en cada golpe que define el ritmo. Usa un rango pequeño de RND para variar intensidad o un efecto, y verifica que todos los instrumentos que pueda elegir RNL tengan audio válido.

La aleatoriedad no conserva necesariamente el mismo resultado entre dos renders. Si obtuviste una toma que te gusta especialmente, guarda también su audio.

<a id="proyecto"></a>
## 18. PROJECT: guardar, exportar y resamplear

### 18.1 Qué hace cada acción

| Campo / fila | Acción |
|---|---|
| TEMPO | BPM del proyecto. |
| TAP | Pulsa A varias veces al ritmo deseado para calcular el tempo. |
| TRANSPOSE | Desplazamiento global de notas en semitonos para instrumentos que lo admiten. |
| NAME | Nombre del proyecto. Cambiarlo no sustituye pulsar SAVE. |
| PROJECT SAVE | Guardar un `.ptp`. |
| PROJECT LOAD | Cargar otro proyecto. |
| PROJECT NEW | Crear uno nuevo, usando la plantilla si existe. |
| EXPORT MIX | Renderizar una mezcla estéreo. |
| EXPORT STEMS | Renderizar pistas separadas y retornos detectados. |
| COMPACT SEQ | Limpiar/compactar recursos de secuencia no utilizados. |
| COMPACT INST | Limpiar/compactar instrumentos no utilizados. |
| SYSTEM SETTINGS | Abrir los ajustes de la app. |
| MIDI | Abrir conexiones y ajustes MIDI. |
| EXIT | Salir, cuando la plataforma ofrece esta acción. |

Los controles de compactación pueden eliminar material que habías preparado en el pool pero todavía no usabas. **Guarda una versión antes**: no son una herramienta para aumentar el volumen ni tienen un deshacer general. En documentación anterior pueden aparecer como CLEAN; la interfaz revisada dice COMPACT.

### 18.2 Guardar proyecto frente a exportar

| Archivo | Conserva | No conserva por sí solo |
|---|---|---|
| `.ptp` | Notas, estructura, ajustes y referencias a sonidos. | Una copia autosuficiente de todos los WAV/SoundFonts externos. |
| WAV de mezcla | El audio de la interpretación renderizada. | Notas editables, instrumentos separados o historial del proyecto. |
| WAV de stems | Partes de audio separadas para mezclar después. | La estructura editable de tracker o el audio de equipos MIDI externos. |
| `.pti` | Preset de instrumento y tabla asociada. | Una canción completa ni necesariamente todos sus recursos externos. |

Lo normal al terminar una sesión es **guardar el proyecto**. Al compartir o llevar la música a otro programa, además exportas audio.

### 18.3 El cuadro RENDER

EXPORT MIX y EXPORT STEMS abren un panel con:

| Campo | Qué elegir |
|---|---|
| SONG START | Primera fila de SONG que quieres exportar. |
| SONG END | Última fila, o AUTO para el final de la parte del inicio elegido. |
| REPEAT | OFF para una vuelta, o x2–x16 para repetir el rango dentro del mismo render. |
| RENDER | A para comenzar. |

A+izquierda/derecha cambia valores; A+arriba/abajo mueve filas en pasos grandes. R+arriba/abajo permite cambiar de parte; B cierra el cuadro.

Una **parte** es un bloque separado por filas de SONG completamente vacías. Revisa SONG START/END si el archivo solo contiene uno de tus bocetos. REPEAT no genera archivos separados: repite durante una misma interpretación, dejando que las colas continúen entre vueltas.

La mezcla incluye las colas de los efectos mediante un tramo final de renderizado. Un feedback o reverb infinito no puede convertirse en un archivo infinito: el motor aplica límites de terminación. Reduce esos ajustes o controla el final si necesitas una salida precisa.

### 18.4 Mezcla estéreo

La salida de mezcla es WAV estéreo de 16 bits. Su frecuencia de muestreo sigue la del motor de audio: 48 kHz es la referencia preferida, pero puede variar con la salida/dispositivo. Si necesitas una frecuencia concreta para otro programa, comprueba las propiedades del WAV exportado. Se guarda bajo `Renders`, con nombres numerados para las mezclas, por ejemplo `MiPrimerBeat_0001.wav`.

**MIX incluye el procesamiento del master.** El metrónomo de trabajo no se escribe en el WAV. Escucha el archivo exportado para comprobar su inicio, final, balance y colas.

Si exportas un loop para repetir exactamente en un DAW, el archivo puede incluir cola posterior al compás. Mantén clara la duración musical de la sección y decide en el DAW si recortas al límite, solapas la cola o prefieres un render repetido más largo.

### 18.5 Stems y traslado a FL Studio u otro DAW

STEMS genera un WAV por pista activa/audible dentro del rango y, cuando se detectan envíos, archivos separados de reverb y delay. Los coloca en una subcarpeta como `Renders/MiPrimerBeat/`.

**En esta implementación los stems se renderizan sin aplicar el bus master**, para poder mezclarlos de nuevo. La mezcla MIX sí lo aplica. Por tanto, no esperes que sumar los stems sin procesamiento reproduzca exactamente una mezcla con OTT, DUST o EQ de master.

Procedimiento:

1. Guarda el proyecto y anota los BPM y el rango exportado.
2. Exporta STEMS con el mismo inicio y final para todas las pistas.
3. Copia los WAV al equipo donde mezclarás.
4. En el DAW, colócalos todos en el **mismo punto de inicio** y configura el tempo del proyecto para que la rejilla corresponda.
5. Evita una adaptación automática de duración si quieres conservar el tiempo original.
6. Escucha las pistas secas y añade los retornos exportados; después haz el procesamiento de master que desees.

La numeración `_1`, `_2`… sigue el listado de pistas activas exportadas; si omitiste pistas, no asumas que cada sufijo corresponde siempre al número original de columna. Identifica y renombra los archivos en tu DAW si te ayuda.

**Detalle de esta versión:** la decisión de crear un stem de reverb/delay consulta los envíos base de los instrumentos utilizados. Si únicamente activas el envío mediante comandos REV/DEL de frase y todos los envíos base están a cero, verifica si se generó el archivo de retorno; no des por hecho que está incluido. Conserva también un MIX como referencia del resultado completo.

Las funciones aleatorias pueden tomar decisiones diferentes en distintas pasadas de render. Cuando necesites congelar una interpretación exacta, resamplea o conserva el audio de esa toma antes de seguir variando el proyecto.

El audio de un sintetizador externo no entra en el render offline por el mero hecho de enviarle MIDI. Para incorporarlo necesitas grabar su salida de audio mediante una ruta de grabación apropiada y usar ese archivo.

### 18.6 Resampling desde SONG

Resamplear convierte una parte de tu secuencia en un nuevo sample dentro del proyecto:

1. En SONG, pulsa L+B y selecciona el rango de filas y pistas.
2. Haz un doble toque de A dentro de esa selección.
3. Confirma el resampling y el nombre que solicite el flujo.
4. La app renderiza un WAV y lo carga en el primer slot de instrumento libre.
5. Busca el resultado en `Samples/Resampled` y prueba el instrumento nuevo.

El render usa el procesamiento que corresponde a esa ruta, incluido el master. Si vuelves a reproducir ese sample a través del mismo master, puedes **procesarlo dos veces**. Escucha y ajusta el contexto antes de decidir que el resultado cambió por error.

Resampling es muy útil para liberar trabajo, construir un sonido por capas o congelar un acorde. No es una grabación del micrófono ni una captura automática de audio MIDI externo.

<a id="archivos"></a>
## 19. Archivos, copias de seguridad y plantillas

### 19.1 Qué hay en cada carpeta

| Carpeta | Contenido |
|---|---|
| `Projects` | Proyectos `.ptp`. |
| `Samples` | Audio y subcarpetas que organices tú. |
| `Samples/Resampled` | Audio creado desde la secuencia. |
| `Samples/Chops` | Fragmentos exportados con CHOP. |
| `Soundfonts` | Bancos SF2/SF3. |
| `Instruments` | Presets `.pti`. |
| `Renders` | Mezclas WAV y carpetas de stems. |
| `Themes` | Temas `.ptt`. |
| `Scales` | Escalas `.pts`. |
| `Grooves` | Grooves guardados. |
| `Reverbs` | Presets `.ptr`. |
| `Delays` | Presets `.ptd`. |

Algunas carpetas se crean cuando se necesitan. Que todavía no exista una no implica que la instalación esté dañada.

### 19.2 Administrar archivos desde el navegador

| Control | Acción en archivos/carpetas normales |
|---|---|
| A | Abrir carpeta o cargar el archivo seleccionado. |
| START | Preescuchar audio compatible. |
| A sobre `..` / R+izquierda | Subir de carpeta. |
| SELECT+A | Renombrar. |
| SELECT+B | Eliminar, con confirmación. |
| SELECT+R | Crear carpeta. |
| L+B | Iniciar selección; repetir rápido permite seleccionar todo. |
| B durante selección | Copiar. |
| L+A durante selección | Cortar para mover. |
| L+A fuera de selección | Pegar. |
| L+R | Cancelar selección. |

Al pegar no sobrescribe silenciosamente un nombre existente: crea otro nombre numerado. Una operación de cortar/mover consume ese portapapeles después de pegar; una copia puede pegarse de nuevo.

En Android, **la lista superior de carpetas autorizadas es una excepción**:

- SELECT+A establece esa carpeta como HOME, tras confirmar. Cambia dónde busca y guarda la app; **no mueve físicamente tus archivos**.
- SELECT+B olvida el permiso sobre esa carpeta. No elimina su contenido.
- `(HOME)` identifica la base actual; `(MISSING)` indica una carpeta que ya no está disponible.

No confundas olvidar una autorización con eliminar un archivo dentro de una carpeta.

### 19.3 Un respaldo que sí sirve

1. Pulsa SAVE en el proyecto actual.
2. Copia el `.ptp` y todos los samples/SoundFonts que usa; si están repartidos por varias carpetas autorizadas, inclúyelos también.
3. Conserva tus presets y ajustes si quieres reproducir el entorno.
4. Guarda al menos un WAV de referencia del resultado.
5. Antes de borrar el origen o cambiar de teléfono, abre la copia y comprueba que carga y suena.

Mover archivos puede romper referencias. La app puede necesitar que vuelvas a cargar la fuente correcta en un instrumento. El WAV exportado te permite escuchar lo terminado, pero no recupera por sí solo una sesión editable perdida.

Una convención sencilla: `Tema_01`, `Tema_02`, `Tema_03`. Evita renombrar o mover el kit a mitad de un proyecto salvo que también compruebes sus referencias.

### 19.4 Plantillas

Si siempre empiezas con el mismo kit y ajustes:

1. Prepara un proyecto limpio con los instrumentos, tempo y niveles que quieras.
2. Entra en SETTINGS → TEMPLATE → SAVE.
3. Los proyectos nuevos podrán partir de esa plantilla.
4. TEMPLATE → CLEAR elimina la plantilla guardada para volver a un inicio vacío.

Una plantilla es un punto de partida, no un sistema de versiones. Si contiene frases o cadenas, un tutorial que asume IDs vacíos debe adaptarse a los que tengas libres. Sus samples también deben seguir disponibles.

### 19.5 Recuperación y registro de errores

RESUME ofrece ASK o AUTO para recuperar trabajo tras una interrupción de la app. Eso no reemplaza guardar versiones deliberadamente ni una copia fuera del dispositivo.

El archivo `pockettracker-log.txt` ayuda a diagnosticar problemas de inicio, archivos, audio o mandos. Corresponde a la sesión reciente y puede reescribirse al abrir de nuevo la app. Si vas a reportar un fallo, conserva el log y describe los pasos que lo producen, el tipo de archivo y la pantalla donde ocurre.

<a id="ajustes"></a>
## 20. SETTINGS, temas y controles físicos

Abre SETTINGS desde PROJECT. Cambia valores con A+flechas; A abre acciones como THEME y TEMPLATE. B vuelve a PROJECT. Algunas filas solo aparecen en plataformas donde tienen utilidad.

### 20.1 Ajustes habituales

| Ajuste | Qué hace / cuándo cambiarlo |
|---|---|
| LAYOUT / SKIN | Distribución de pantalla y apariencia de botones táctiles. En teléfono sin mando, la versión de release utiliza el diseño vertical; con mando pueden aparecer otras opciones. |
| SKIN NORM / DARK / TRNS | Carcasa clara, oscura o teclas transparentes. |
| SCALING INT / BILINEAR | Píxeles más nítidos frente a escalado suavizado. |
| BTN SOUND / VOL | Sonido al pulsar botones y su volumen. |
| BTN VIBRO / POW | Vibración e intensidad, si el dispositivo la admite. |
| METRONOME / VOL | Clic de referencia mientras reproduces. No se exporta al WAV. |
| KB INSERT BEFORE / AFTER | Dónde insertar caracteres en el teclado de nombres. |
| CURSOR REMEMBER / REFRESH | Recordar o refrescar posiciones al cambiar de pantalla. |
| NAV SONG / POOL | Navegar por referencias del arreglo o por IDs globales. |
| FOLDER REMEMBER / REFRESH | Reabrir el navegador en la carpeta usada durante la sesión o en la predeterminada. |
| NOTE PREV | Preescuchar una nota al mantener A en PHRASE, con transporte detenido y sin selección. |
| VISUALIZER | Aspecto de la visualización superior. |
| HELP OFF / SHORT / FULL | Ayuda de SELECT: apagada, resumida o completa. |
| THEME | Abrir el editor de colores. |
| TEMPLATE SAVE / CLEAR | Guardar o retirar una plantilla de proyecto. |
| ABXY AUTO / XBOX / NINTENDO | Interpretar las etiquetas del mando. Aparece cuando hay uno conectado. |
| RESUME ASK / AUTO | Preguntar o recuperar automáticamente trabajo interrumpido. |
| AUDIO OUT | Selección de salida/ASIO en Windows; no busques esta fila de ASIO en Android. |

Para aprender, resulta útil **NAV=SONG, NOTE PREV=ON y HELP=FULL**. En ayuda FULL, el botón de cierre se consume al cerrar; en SHORT, el botón que la aparta puede ejecutar también su acción normal. Evita usar un gesto destructivo únicamente para cerrar la ayuda corta.

Los modos de visualizador son SCOPE, FLAT, OCTA, OCTA.F, SPECT y SPCT.P: representaciones de forma de onda, niveles o espectro. Cambiar la visualización no cambia el sonido.

Si A y B parecen intercambiados con un mando, prueba el ajuste ABXY adecuado a sus etiquetas. Algunos mandos reportan una distribución distinta de la impresa. Evita aplicar la misma inversión tanto en el sistema como en la app, porque dos intercambios pueden cancelarse.

### 20.2 Teclado físico

En las rutas de teclado compatibles, estos son los controles predeterminados:

| Función | Teclas |
|---|---|
| Cruceta | Flechas o W/A/S/D. |
| Botón A | K o Enter. |
| Botón B | J o Escape. |
| L | U. |
| R | I. |
| SELECT | Shift izquierdo. |
| START | Espacio. |

Ojo: **la tecla de letra A es dirección izquierda**, no el botón A de aceptar. Para `R+derecha`, por ejemplo, mantienes I y pulsas la flecha derecha.

### 20.3 Colores y temas

THEME ofrece CLASSIC, AMBER, BLUE y MONO como bases. Edita los canales R/G/B de cada color y usa SAVE para conservar un `.ptt` propio. RGB significa rojo, verde y azul, no los botones R y B.

| Grupo de filas | Qué colorea |
|---|---|
| BACKGROUND | Fondo y algunas letras sobre bloques de selección. |
| ROW 4TH | Filas de acento cada cuatro pasos. |
| ROW CURSOR / ROW SELECT | Celda activa y bloque seleccionado. |
| TXT TITLE / PARAM / VALUE / EMPTY / PLAY | Títulos, etiquetas, valores, huecos y marcador de reproducción. |
| VIZ BG / LINE / WAVE | Fondo, línea y dibujo de la visualización. |
| MTR BG / LOW / MID / HIGH | Fondo y zonas de medidores. |
| EQ FILL / BORDER / TXT | Espectro, contorno y etiquetas del ecualizador. |

Conserva suficiente diferencia entre fondo, cursor y texto para poder leer una celda seleccionada. Un tema bonito pero sin contraste vuelve mucho más difícil editar un patrón.

### 20.4 config.json: opcional y avanzado

La app crea `config.json` en su carpeta base. Se lee al iniciar y permite personalizar controles, repetición de teclas y carpetas iniciales. **Los ajustes de pantalla se guardan por otra ruta, en `settings.json`.**

`config.example.json` muestra opciones y valores de referencia actualizados por la app. No uses ese archivo como tu configuración personal: sus cambios pueden reemplazarse.

Ejemplo mínimo de un `config.json` completo y válido para un mando con etiquetas Nintendo:

```json
{
  "controller": {
    "abxy": "nintendo"
  }
}
```

No necesitas este archivo para completar los tutoriales. Si ya tienes uno, integra la opción conservando las demás, en vez de reemplazarlo sin mirar. Respeta comillas, comas y llaves, y reinicia para aplicar.

Las asignaciones de teclado usan nombres exactos como `Left Shift`, `Return` o `Space`; `Ctrl` a secas no sustituye a `Left Ctrl`. Una asignación nueva puede retirar esa tecla de otra función. Usa como referencia el archivo que generó tu propia versión y el [documento de entradas](docs/input-system.md).

### 20.5 Segundo plano en Android

Si la canción está reproduciéndose, la app puede continuar en segundo plano y ofrecer una notificación con Stop. La visibilidad de esa notificación depende del permiso correspondiente. El sistema del teléfono también puede cerrar aplicaciones; por eso conviene guardar antes de cambiar de tarea durante mucho tiempo.

<a id="midi"></a>
## 21. MIDI explicado desde cero

**MIDI transmite instrucciones musicales, no una grabación de audio.** Puede decir «toca La», «suelta la nota», «mueve este control» o «vamos a 100 BPM». El equipo que recibe esas instrucciones produce el sonido.

Hay dos usos principales:

- **Entrada:** un teclado o controlador toca y controla PocketFlow.
- **Salida:** PocketFlow secuencia un sintetizador u otra aplicación/dispositivo compatible.

La disponibilidad real de puertos depende del sistema, el dispositivo conectado y la compilación. Abre PROJECT → MIDI para comprobar los que aparecen. En Android, un cable USB solo sirve si el teléfono, adaptador y equipo establecen la conexión adecuada; no todos los cables llevan datos.

### 21.1 Conectar una salida

1. Conecta el equipo y elige OUTPUT=AUTO o su puerto concreto.
2. Usa TEST: envía una nota corta C4 por el canal 1.
3. En el receptor, selecciona el mismo canal y un sonido audible.
4. Configura un instrumento TYPE=EXTERNAL, con CHAN igual al canal del receptor.
5. Escribe notas en una frase con ese instrumento.

Si TEST informa envío pero no oyes nada, revisa la selección de entrada del receptor, su canal, volumen y salida de audio. MIDI enviado correctamente no garantiza que estés escuchando el dispositivo que lo recibe.

### 21.2 Controles de la pantalla

| Campo | Función |
|---|---|
| OUTPUT / INPUT | OFF, AUTO o un puerto concreto. AUTO selecciona un puerto disponible. |
| OFFSET | Compensación temporal automática o manual, aproximadamente −99 a +99 ms. |
| SYNC OUT | Enviar reloj MIDI con mensajes de transporte. |
| SYNC IN | OFF, TEMPO o FULL para seguir otro reloj. |
| CTL CH | Canal de escucha para controles mapeados: todos o uno de 1 a 16. |
| PROG CHG | Permitir los mensajes de banco/programa de instrumentos externos. |
| KEYS | MONO o polifonía de 2–8 teclas repartida entre pistas. |
| VELOCITY | Responder a la fuerza con la que tocas o usar intensidad fija. |
| MAPPING | Lista de asignaciones de perillas/faders. |
| PANIC | Enviar apagado de notas, útil si una queda colgada. |
| TEST | Enviar una nota de comprobación. |

Un **canal MIDI** es una dirección lógica del 1 al 16, no un cable ni una pista de audio. Dos instrumentos que comparten canal también pueden compartir cambios de programa o pitch bend. Separa canales cuando necesites que sus articulaciones sean independientes.

### 21.3 Tocar con un teclado

Selecciona INPUT y el instrumento que quieres tocar. En PHRASE, se usa el instrumento de la nota/contexto del cursor; en otras pantallas, el seleccionado o recordado. Las voces parten de la pista marcada en SONG.

Con KEYS=MONO tocas una nota a la vez en esa ruta. POLY reparte teclas entre varias pistas desde el contexto seleccionado, y puede reemplazar la más antigua cuando no queden voces disponibles.

**En esta versión tocar el teclado MIDI no escribe las notas en PHRASE.** Sirve para interpretar y probar sonidos; la edición de la secuencia sigue siendo explícita. No busques un botón de grabación MIDI de piano roll que este flujo no ofrece.

La entrada puede recibir controles habituales como volumen, panorama, corte, resonancia y envíos. Los controles de filtro necesitan un filtro activo. No confundas recibir un CC para tocar la voz con mapear un mando a un ajuste permanente del proyecto.

### 21.4 Sincronizar relojes

MIDI Clock transmite 24 pulsos por negra. Esa unidad pertenece al protocolo; no significa que debas cambiar el PPQ de tu groove a 24.

| SYNC IN | Qué sigue |
|---|---|
| OFF | Tempo y transporte propios. |
| TEMPO | Sigue el tempo/rejilla del emisor; conservas el control local de iniciar/parar. |
| FULL | Sigue también los mensajes de inicio y parada del emisor. |

El tempo recibido no reescribe necesariamente TEMPO del proyecto: al volver a reproducción sin reloj, regresa el valor propio. Si aparece NO CLOCK, comprueba que el otro programa realmente envíe reloj por el puerto elegido.

OFFSET adelanta o retrasa los mensajes para compensar latencia. Empieza en AUTO y ajusta por oído si los golpes externos quedan por delante o detrás de los internos. No elimina todas las diferencias de drivers, dispositivos o arranque.

Comenzar a mitad de una sección no reconstruye todos los eventos que habrían ocurrido antes, como una nota todavía sostenida o un comando de groove. Para sincronización reproducible, utiliza puntos de entrada que configuren lo necesario.

### 21.5 Mapear una perilla

1. Elige una celda compatible en MIXER, EFFECTS o INSTRUMENT.
2. Mantén **R solo** alrededor de medio segundo.
3. Mueve la perilla del controlador.
4. Comprueba el mensaje de asignación.

R combinado con otra tecla para navegar no activa este aprendizaje. En MAPPING puedes revisar CC, valor actual, MIN, MAX y DEST. MIN mayor que MAX invierte el recorrido. Un mismo CC puede mover varios destinos si tiene varias filas. El proyecto admite hasta 128 asignaciones.

Las asignaciones y PROG CHG se guardan con el proyecto; las conexiones de tu equipo se conservan como parte del entorno. Algunos controles de instrumento, como VOL/PAN/DETUNE, se aprecian en la siguiente nota; otros siguen el movimiento durante una voz sostenida.

### 21.6 Lo que MIDI no resuelve automáticamente

- No lleva el audio de un sintetizador al teléfono solo por mandar notas.
- No convierte un archivo de audio en notas editables.
- No hace compatibles todos los nombres de efectos entre equipos.
- No incorpora el sonido del receptor a EXPORT MIX/STEMS.
- No garantiza que dos equipos numeren sus programas de la misma manera visible; algunos muestran 1 donde el mensaje empieza en 0.

Para un instrumento externo, los slots CC A–D son la forma explícita de controlar parámetros de su manual. Algunos mensajes de efectos propios de esta familia de app solo tienen sentido para otro receptor compatible; otro sintetizador puede ignorarlos.

<a id="live"></a>
## 22. LIVE: tocar con las cadenas

LIVE convierte SONG en un lanzador de cadenas. En vez de recorrer automáticamente toda su columna, cada pista repite su cadena hasta que le pides otra.

En SONG, pulsa **B+izquierda o B+derecha**. El encabezado cambia a LIVE. El modo dura la sesión y no se guarda como una propiedad permanente del proyecto.

| Control en LIVE | Acción |
|---|---|
| START | Poner en cola la cadena de la celda seleccionada para esa pista. |
| START otra vez sobre la misma celda | Adelantar el lanzamiento al siguiente compás, en lugar de esperar al final de la cadena. |
| L+START | Lanzar/poner en cola toda la fila como una escena. |
| R+START | Poner en cola el silencio de esa pista. |
| B+izquierda/derecha | Regresar al modo SONG. |

Si el transporte está parado, START o L+START inicia. Los marcadores parpadeantes muestran lo que está en cola; los fijos, lo que suena. El parpadeo distingue espera al final de cadena de lanzamiento más próximo.

**Aquí START no es el botón habitual de parar.** Para una salida sencilla, vuelve a SONG y pulsa START. Cambiar entre SONG y LIVE no corta todo inmediatamente: las pistas conservan su posición y cambian su forma de avanzar en los límites correspondientes.

Una celda vacía en una fila lanzada como escena silencia esa pista. Este comportamiento de lanzamiento **no convierte los huecos del arreglo normal en compases de silencio**.

Ejercicio: prepara dos filas con cadenas de la misma duración, una sencilla y otra con fills. Alterna L+START entre ambas. Después lanza solo la variación de batería, manteniendo el bajo de la escena anterior.

<a id="problemas"></a>
## 23. Problemas frecuentes y cómo resolverlos

| Síntoma | Qué revisar primero |
|---|---|
| No suena nada aunque el cursor avanza. | Que el instrumento tenga audio, I apunte al slot correcto, V no sea 00 y los volúmenes de instrumento/pista/master estén abiertos. Revisa mute/solo y la salida del teléfono. |
| El proyecto nuevo está mudo. | Los instrumentos empiezan vacíos salvo que una plantilla los haya preparado. Carga un sample. |
| Desde PHRASE solo escucho un sonido. | START preescucha esa frase. Vuelve a SONG o usa el inicio conjunto con R+START donde corresponde. |
| No puedo entrar a CHAIN o PHRASE. | La celda de origen debe tener una referencia válida. Inserta primero la cadena/frase. |
| Edité una parte y cambiaron otras. | Comparten el mismo ID. Clona la referencia para crear una variación independiente. |
| Cloné una frase, pero cambiar el sonido afecta a ambas. | La clonación de secuencia conserva instrumentos compartidos. Usa otro slot o efectos por nota. |
| Dos golpes no caben en la misma fila. | Una frase tiene una columna de nota. Para batería simultánea reparte sonidos entre pistas. CHD resuelve acordes de un instrumento, no dos samples distintos arbitrarios. |
| Hay filas vacías, pero el sonido sigue. | Vacío significa sin evento nuevo. Usa OFF para soltar o KIL para cortar, y revisa loops/release. |
| Puse OFF y el sample empieza en otro lugar. | Escribiste el FX OFF. Para soltar, usa OFF en la columna de nota. |
| START reproduce desde mitad. | En SONG empieza en la fila del cursor. Sitúate en 00 para el principio. |
| START ya no para. | Mira si el título dice LIVE o si estás preescuchando un navegador/editor. Vuelve al transporte normal. |
| El patrón se repite antes de la sección siguiente. | Revisa huecos en SONG, referencias omitidas en CHAIN, HOP y duración de las cadenas. Las referencias vacías no son silencios temporizados. |
| Las pistas se desacomodan. | Compara duración de cadenas, grooves, HOP y promedio de ticks. Una pista puede recorrer una longitud distinta. |
| La nota está muy aguda/grave o el loop cambia de duración. | ROOT y afinación del archivo. El sampler normal cambia velocidad con la altura. Usa TSTRETCH para ajustar duración con otra estrategia. |
| Cambiar la nota reproduce otro golpe. | El instrumento tiene slices: la nota selecciona un fragmento. Revisa SLICE y SLI. |
| El filtro no hace nada. | FILTER puede estar OFF. Actívalo o usa LPF/HPF/BPF. Comprueba también que la nota dure lo suficiente. |
| La tabla no hace lo esperado. | Verifica ID de instrumento/tabla, TIC, TBL en cada nota necesaria y el lector de cada columna. TIC00 avanza por nota. |
| Una automatización está gris. | Falta parámetro compatible a la izquierda de AUS o AUF posterior válido en la misma cadena. |
| El volumen en pantalla no coincide con lo que oigo. | VTR/VMV pueden cambiar el valor de reproducción mientras MIXER muestra la base. Comprueba V, VOL, MODS, TABLE y envíos. |
| Bajé la pista a cero y sigue su reverb. | Los envíos son anteriores al fader de pista. Revisa envíos y retornos. |
| El delay no se acaba. | FDBK alto o autooscilación. Bájalo y reduce el retorno; revisa también reverb con DCAY=FF. |
| El sonido satura o bombea demasiado. | Niveles sumados, DRIVE, OTT/DUST y empuje LIM. Baja las fuentes más fuertes y compara con menos procesamiento. |
| El audio guardado quedó mono. | SOURCE en el editor pudo estar en LEFT, RIGHT o MONO. Abre el original y guarda con STEREO si la fuente era estéreo. |
| Los slices desaparecieron después de editar. | Una operación cambió la longitud. Vuelve a crearlos después de recortar/estirar. |
| CHOP no creó instrumentos. | Exporta WAV separados; después hay que cargarlos en slots. |
| El render solo contiene un fragmento. | SONG START/END, AUTO y separadores de partes completamente vacíos. |
| Los stems no suenan igual que MIX. | MIX incluye master; stems no. Revisa retornos, mute/solo y decisiones aleatorias entre pasadas. |
| Falta un stem de reverb o delay. | Revisa si el envío solo existe en FX de frase; la detección actual consulta envíos base de instrumentos. |
| Toco el teclado MIDI y no aparecen notas escritas. | La entrada interpreta pero no graba notas en PHRASE en esta versión. |
| MIDI se envía, pero no oigo nada. | El receptor produce el audio. Comprueba canal, patch, volumen y conexiones de audio. |
| Una nota MIDI se quedó sonando. | Usa PANIC, comprueba OFF/LEN y separa canales si varias pistas se interfieren. |
| Un proyecto antiguo dejó de encontrar sonidos. | Se movieron/renombraron archivos, cambió HOME o se perdió acceso a una carpeta. Restaura la ruta o vuelve a cargar la fuente en el instrumento. |
| El explorador no ve mi carpeta. | Autoriza esa carpeta con ADD FOLDER. Un permiso a otra ubicación no cubre todo el teléfono. |
| A y B hacen lo contrario con el mando. | Ajuste ABXY y posible inversión adicional en el sistema/mando. |
| No puedo salir del teclado de nombre con B. | B borra caracteres. START aplica y SELECT cancela. |
| L+R no recupera lo que borré. | No es Undo general. Carga una versión guardada si necesitas recuperar esa edición. |

Para aislar un problema de sonido, prueba en este orden: **instrumento solo → frase → cadena → canción → archivo exportado**. El primer nivel donde falle reduce mucho lo que tienes que revisar.

<a id="aprendizaje"></a>
## 24. Ruta de aprendizaje y chuleta

### 24.1 Aprender por capas

| Sesión | Objetivo concreto | Puedes darla por conseguida cuando… |
|---|---|---|
| 1 | Cargar tres sonidos y hacer el primer beat. | Suena desde SONG y sabes guardar. |
| 2 | Añadir bajo y variar intensidades. | Distingues ROOT, nota, V y volumen de instrumento. |
| 3 | Organizar cuatro compases con un fill. | Sabes cuándo compartes una frase y cuándo la clonaste. |
| 4 | Crear intro y parte principal. | Sabes programar silencio sin romper el recorrido. |
| 5 | Mezclar y exportar. | Encuentras el WAV, lo escuchas y llevas stems a un DAW. |
| 6 | Usar slices y ajustar un loop. | Puedes disparar fragmentos y conservar un original. |
| 7 | MODS y un barrido de filtro. | Sabes distinguir envolvente, LFO y automatización de frase. |
| 8 | TABLE, groove y escala. | Puedes explicar qué modifica cada uno sin confundirlos. |
| 9 | LIVE o MIDI, si te sirven. | Puedes integrar una interpretación o equipo externo de forma predecible. |

No hace falta dominar la sesión 9 para terminar una canción. Es mejor completar un tema corto con cinco pantallas que pasar días cambiando efectos sin guardar un arreglo.

### 24.2 Chuleta de bolsillo

| Quiero… | Hago… |
|---|---|
| Entrar en el siguiente nivel | R+derecha sobre una referencia ocupada. |
| Editar | A+flechas. |
| Borrar | A+B. |
| Soltar una nota | A+B en una celda de nota vacía para insertar OFF. |
| Una frase/cadena nueva | Doble A en su referencia; compruebo el ID libre. |
| Copiar / pegar | L+B, seleccionar, B; ir al destino, L+A. |
| Variación independiente | L+B+A sobre la referencia en CHAIN o SONG. |
| Oír todo | SONG y START desde la fila deseada. |
| Probar solo un instrumento | A+START en su pantalla. |
| Guardar | PROJECT → PROJECT SAVE. |
| Audio final | PROJECT → EXPORT MIX → rango → RENDER. |
| Pistas para otro DAW | PROJECT → EXPORT STEMS. |
| Ayuda | SELECT con HELP activo. |

### 24.3 Antes de dar un tema por terminado

- ¿Lo escuchaste desde el principio y también desde sus entradas principales?
- ¿Las referencias compartidas son intencionales?
- ¿El final corta o conserva las colas como querías?
- ¿Guardaste el proyecto y los samples que necesita?
- ¿Escuchaste el WAV exportado fuera de la app?

### 24.4 Una nota sobre APK, commits y releases de este fork

La APK instala el programa. Un commit guarda cambios del repositorio. El workflow de release firmado de este fork se ejecuta **manualmente (`workflow_dispatch`) o al subir una etiqueta que empieza por `v`**. Un push normal de código o documentación no activa por sí solo ese workflow, aunque otros workflows de CI puedan ejecutarse.

Ese workflow entrega un artefacto llamado **PocketFlow-release-signed**. Crear ese artefacto no equivale a publicar automáticamente una entrada en GitHub Releases. El comportamiento exacto está en [android-release.yml](.github/workflows/android-release.yml).

No necesitas recompilar la app para leer este manual: el archivo Markdown se puede abrir directamente desde GitHub o guardar como texto.

<a id="glosario"></a>
## 25. Glosario

| Término | Explicación |
|---|---|
| ADSR | Ataque, caída, nivel sostenido y liberación de una envolvente. |
| Arpegio | Notas de un acorde recorridas sucesivamente. |
| Automatización | Cambio programado de un parámetro a lo largo del tiempo. |
| Beat / tiempo | Pulso básico con el que cuentas la música. |
| Bit depth | Resolución de amplitud digital del archivo; 16 bits, 24 bits, etc. |
| BPM | Beats por minuto; tempo. |
| Bus | Ruta de audio compartida por varias fuentes. |
| Cadena / chain | Lista de frases que una pista recorre. |
| CC | Mensaje MIDI de control continuo; su número identifica el control. |
| Cent | Una centésima de semitono. |
| Clonar | Crear una copia independiente del objeto; distinto de reutilizar su ID. |
| Compás | Grupo de tiempos; aquí los primeros ejemplos usan cuatro. |
| Cutoff | Frecuencia donde actúa el borde de un filtro. |
| DAW | Programa de producción musical, como un secuenciador con edición y mezcla. |
| dB / dBFS | Medida de nivel relativo / nivel digital respecto al máximo de escala. |
| Decay | Caída tras el ataque; en reverb, duración de la cola. |
| Delay | Repeticiones del sonido. |
| Destructivo | Procesamiento que modifica los datos de audio, aunque exista una opción limitada de deshacer. |
| Detune | Desafinación fina respecto a la nota base. |
| Dry / wet | Sonido directo / sonido procesado. |
| Envío / send | Cantidad de señal que se dirige a un bus auxiliar. |
| Envolvente | Forma de cambio que sigue etapas, normalmente a partir de una nota. |
| Feedback | Parte de una salida que vuelve a entrar; en delay sostiene las repeticiones. |
| Fader | Control de nivel de pista o master. |
| Fill | Variación de cierre o enlace; también el nombre de una herramienta de relleno. |
| Frase / phrase | Patrón de 16 pasos de PocketFlow. |
| Ghost note | Golpe suave que añade movimiento sin ser el acento principal. |
| Groove | Distribución de duraciones de los pasos. |
| Headroom / margen | Espacio que dejas antes de alcanzar el máximo de nivel. |
| Hexadecimal | Sistema de numeración en base 16. |
| Hz / kHz | Ciclos por segundo / miles de ciclos por segundo. |
| ID / slot | Número que identifica una posición en un banco. |
| Instrumento | Fuente sonora más sus ajustes; puede reutilizarse en distintas pistas. |
| Latencia | Retraso entre una acción y su resultado audible. |
| LFO | Oscilador lento que mueve un parámetro. |
| Loop | Región o secuencia que se repite. |
| Master | Ruta final donde se combina la mezcla. |
| MIDI | Mensajes de interpretación y control musical; no audio grabado. |
| Monofónico / polifónico | Una nota a la vez / varias notas simultáneas. |
| Mute / solo | Silenciar una parte / escuchar las partes elegidas aisladas. |
| Nibble | Un dígito hexadecimal: cuatro bits, valores 0–F. |
| Note-on / note-off | Mensaje de comenzar / soltar una nota. |
| Offset | Desplazamiento de posición o tiempo, según el contexto. |
| One-shot | Sample que se dispara y termina sin repetir un loop interno. |
| Panorama / pan | Distribución entre izquierda y derecha. |
| Patch / preset | Configuración guardada de un sonido o efecto. |
| Pitch | Altura musical: grave o agudo. |
| Pool | Banco de frases, cadenas, instrumentos u otros recursos. |
| Portamento | Deslizamiento entre alturas de dos notas. |
| PPQ | Pulsos por negra; especifica una resolución temporal en un contexto concreto. |
| Quantize / cuantizar | Ajustar a una rejilla o conjunto discreto; QNT de GROOVE es una ayuda de edición de duraciones. |
| Release | Salida de una envolvente después de soltar la nota. |
| Render | Calcular la secuencia y escribir su audio en un archivo. |
| Resampling | Convertir una parte ya producida en un nuevo sample. |
| Resonancia | Énfasis alrededor del corte de un filtro. |
| Retorno / return | Salida de un bus de efectos que vuelve a la mezcla. |
| Retrigger | Disparar de nuevo una voz a intervalos cortos. |
| Reverb | Reflexiones y cola que sugieren un espacio. |
| Root | Nota raíz de un sample o tonalidad de referencia; mira el contexto. |
| Sample | Fragmento de audio digital grabado. |
| Sample rate | Muestras de audio por segundo, por ejemplo 48.000 Hz. |
| Semitono | Distancia de una tecla a la siguiente en la escala cromática. |
| Slice | Fragmento definido por marcadores dentro de un sample. |
| SoundFont | Banco de sonidos e instrucciones para reproducirlos según la nota. |
| Stem | Archivo de audio de una parte o grupo de la producción, para mezclar aparte. |
| Step / paso | Una fila de la frase. |
| Strum | Entrada escalonada de las notas de un acorde, como un rasgueo. |
| Sustain | Nivel mantenido mientras una envolvente ADSR espera la liberación. |
| Swing | Alternancia de pasos largos/cortos que modifica el balance rítmico. |
| Table | Microsecuencia de cambios asociada al instrumento. |
| Tick / tic | Subdivisión interna de tiempo. |
| Time-stretch | Cambio de duración con control separado de la altura, dentro de los límites del procesamiento. |
| Tracker | Secuenciador que representa eventos musicales en filas y columnas. |
| Transient / transitorio | Ataque breve de energía, como el inicio de un golpe. |
| Transpose / transponer | Mover notas por una cantidad de semitonos. |
| Velocity | Intensidad de una nota; en PHRASE es V. |
| Voice / voz | Una instancia sonora activa; no significa necesariamente voz humana. |
| Zero crossing | Cruce por cero de la forma de onda; un punto útil para cortar con menos clics. |

<a id="fuentes"></a>
## 26. Fuentes y alcance de esta revisión

Este documento es una guía de uso en español, redactada para esta base de PocketFlow. Los ejemplos musicales son ejercicios propios; los comportamientos de controles, archivos y comandos se contrastaron con fuentes del repositorio.

| Tema | Fuentes del proyecto |
|---|---|
| Descripción general y referencia previa | [Manual en inglés](docs/manual-en.md), [guía rápida](docs/quick-start-guide.md), [sistema de entradas](docs/input-system.md). |
| Estructura y comandos | [Modelo del proyecto](native/songcore/model.h), [definición de efectos](native/songcore/effects.h), [secuenciador](native/songcore/scheduler.cpp). |
| Reproducción y controles | [Botones](native/ui/dispatch/buttons.cpp), [navegación](native/ui/modules/navigation_map.cpp). |
| Instrumentos y modulación | [Editor de instrumento](native/ui/modules/instrument_editor.cpp), [estructura de filas](native/ui/instrument_row_layout.h), [modulación](native/ui/modules/modulation.h). |
| Audio y edición de samples | [Editor de samples](native/ui/modules/sample_editor.cpp), [acciones del editor](native/ui/dispatch/screens/sample_editor.cpp). |
| Proyecto y archivos | [Serialización](native/songcore/project_io.h), [acciones de proyecto](native/ui/project_actions.cpp). |
| Exportación | [Render y planificación de stems](native/songcore/render.h), [host de audio](native/songcore/host.cpp). |
| Ajustes e interfaz | [SETTINGS](native/ui/modules/settings_editor.cpp), [PROJECT](native/ui/modules/project_editor.cpp), [EFFECTS](native/ui/modules/effects_editor.cpp). |
| MIDI | [Pantalla MIDI](native/ui/modules/midi_settings.cpp), [asignaciones](native/ui/modules/midi_map_editor.cpp). |

Se dio prioridad al código cuando la documentación anterior tenía indicaciones incompatibles. Algunos puntos corregidos explícitamente aquí son:

- START en SONG utiliza la fila del cursor.
- B solo no es el borrado general de celdas; se utiliza A+B.
- TIC00 avanza una fila de tabla por nota.
- Una nota nueva sin TBL vuelve a la tabla base.
- HOP no ocupa el tiempo de una fila musical normal.
- CHAIN omite referencias vacías; para entrar desde SONG necesita ocupada su primera entrada.
- La etiqueta actual de limpieza es COMPACT.
- INP EQ está antes de los buses de efectos.
- Los stems omiten el procesamiento del master y tienen una limitación de detección de retornos basada en envíos de instrumento.
- El RPITCH del editor ajusta duración cambiando velocidad y altura.
- El WAV exportado utiliza la frecuencia de muestreo del motor de audio; no fuerza siempre 48 kHz.
- Los comandos SCA/SCG escritos se guardan; su estado durante reproducción se reinicia al detenerse.

No se promete compatibilidad de proyectos con LGPT/M8, funcionamiento de cada equipo MIDI ni igualdad exacta entre renders que contienen procesos aleatorios. Si una versión posterior cambia una pantalla, usa el código y la ayuda contextual de esa versión para actualizar la sección correspondiente.

**Tu primer objetivo ya está al alcance con lo básico: cargar un sonido, escribir una frase, ordenarla en una cadena, ponerla en SONG y guardar. Todo lo demás amplía esas mismas ideas.**
