# Diario de Sueños

App para registrar sueños, ver patrones a lo largo del tiempo y apoyarte en una IA para interpretarlos cada cierto tiempo. No necesita instalación, servidor ni internet: es HTML + JavaScript autocontenido que corre en el navegador.

## Cómo abrirla

**Doble click en `diario-suenos-landing.html`** — ese es el archivo de entrada (la pantalla de bienvenida animada, con cielo estrellado en Three.js y sonido ambiente). Al hacer click en "Comenzar" pasa, con una transición, a la app real, que vive en `diario-de-suenos.html` y se carga adentro en un iframe (por eso los dos archivos tienen que estar siempre en la misma carpeta, junto con `head-top.png`, `head-bottom.png` y el mp3 del sonido ambiente).

Si preferís entrar directo a la app sin la landing (por ejemplo, para probar algo puntual más rápido), también podés abrir `diario-de-suenos.html` directamente — funciona igual de bien solo, la landing es un "portal" opcional antes de entrar.

## Dónde se guardan los datos

Los sueños se guardan en el `localStorage` del navegador donde abriste el archivo — es decir, quedan asociados a **ese navegador en esa PC**. Esto implica:

- Si abrís el archivo con otro navegador (o en modo incógnito, o en otra PC), no vas a ver los sueños ya cargados.
- Si el navegador borra sus datos (reinstalación, "borrar historial y datos de sitios", etc.), se pierde lo que no hayas exportado.

**Por eso la pestaña "Backup" es importante.** Recomendación: exportar un JSON cada vez que cargues varios sueños, o al menos una vez por semana o dos. La app te avisa con un aviso si pasaron más de 14 días desde el último backup exportado.

- **Exportar JSON**: backup completo (todos los sueños + interpretaciones guardadas). Sirve para restaurar todo tal cual, o para pasar los datos a otra PC/navegador (usando "Importar").
- **Exportar Markdown**: un archivo de texto legible con todos los sueños, pensado para leer, archivar o pegar en otro lado (no para restaurar en la app).
- **Importar JSON**: carga un backup exportado antes. Te pregunta si querés reemplazar todo o fusionar con lo que ya tenés cargado.

## Las pestañas

1. **Nuevo sueño** — formulario para cargar un sueño: fecha (con la fase lunar de ese día calculada automáticamente), título, relato, tipo (normal/lúcido/pesadilla/recurrente/**viaje astral**), claridad del recuerdo, intensidad emocional, y etiquetas de emociones, personas, lugares y símbolos (se escriben y se agregan con Enter, o con los emojis de un click para emociones). Arriba aparece un aviso si hace 3 días o más que no cargás un sueño. El relato y las notas al despertar tienen un botón **🎤 Dictar por voz** (ver sección aparte).
   - **Modo carga rápida**: un checkbox arriba del formulario oculta claridad/intensidad, personas, lugares y notas, para anotar solo lo esencial medio dormido y completar el resto después editando el sueño.
   - **Diccionario de símbolos**: al escribir en el campo de símbolos, si coincide con un símbolo conocido (agua, volar, dientes, caída, etc.) aparece una lectura tradicional de referencia debajo del campo. Esa misma lectura se muestra también en el detalle del sueño y se suma como contexto extra al generar el prompt de interpretación — siempre aclarando que es una lectura simbólica de referencia, no un diagnóstico.
   - **Sugerencia de etiquetas desde el relato**: al terminar de escribir el relato (al salir del campo), la app sugiere palabras repetidas del texto como posibles símbolos, para agregarlas con un click.
2. **Mis sueños** — listado de todo lo cargado, con buscador y filtros por tipo y rango de fechas. Click en un sueño para ver el detalle completo, editarlo o eliminarlo.
3. **Mapa personal** — el panel de patrones: total registrado, racha de días consecutivos, % de sueños lúcidos; un **calendario de constancia** estilo "mapa de contribuciones" (un cuadrito por día del último año, más oscuro cuantos más sueños anotaste ese día); sueños por semana (últimas 12 semanas); tipos de sueño; sueños por fase lunar; y las emociones y símbolos que más se repiten (hacé click en una barra de emociones o símbolos para saltar directo al explorador de conexiones con esa etiqueta).
4. **Conexiones** — el explorador: elegís una etiqueta (emoción, persona, lugar o símbolo) escribiéndola o haciendo click en una de las más frecuentes, y ves todos los sueños donde aparece, más las otras etiquetas con las que más suele aparecer junto (esas también son clickeables, para seguir explorando de una a otra).
5. **Interpretar** — elegís qué sueños incluir (los que todavía no interpretaste, los últimos N días, un rango de fechas, o todos) y la app arma un **prompt en texto** listo para copiar y pegar en Claude (u otra IA). El prompt pide una lectura reflexiva y no clínica, buscando patrones y símbolos recurrentes. Después de pegarlo en la IA y tener la respuesta, la podés pegar de vuelta acá para guardarla asociada a esos sueños — queda en el historial y también se ve en el detalle de cada sueño incluido.
6. **Backup** — exportar/importar, más **exportar a PDF** (abre una ventana de impresión con todos los sueños; desde ahí elegís "Guardar como PDF" como destino de impresión).

## Dictado por voz

El botón 🎤 usa el reconocimiento de voz del navegador (Web Speech API). Es **gratis, sin API key ni registro**. Ojo con la privacidad: en Chrome/Edge, el audio se manda a los servidores de Google para transcribirlo (no se procesa localmente) — no tiene costo, pero no es 100% privado. Firefox no lo soporta bien, así que ahí el botón no aparece (la app detecta automáticamente si el navegador lo soporta). Al hacer click empieza a grabar y transcribir en español; un segundo click detiene la grabación. Si entraste por la landing, dictar por voz corta automáticamente la música de fondo para no interferir con el micrófono (y se reactiva sola al terminar).

## La pantalla de inicio (`diario-suenos-landing.html`)

Landing con scroll narrativo: una cabeza (ilustración 2D) que se va abriendo a medida que scrolleás, revelando un orbe que se ilumina cada vez más, con un campo de estrellas real en 3D (Three.js) de fondo. Tiene sonido ambiente (mp3 en loop, botón de silenciar siempre visible arriba a la derecha).

Sobre el sonido — reglas que sigue, por si hay que tocarlas en el futuro:
- Arranca en cuanto hay un primer click/tecla/touch en la página (el scroll solo no cuenta para la política de autoplay de los navegadores — es una restricción real, no configurable).
- Se silencia solo al cambiar de pestaña/ventana, y se reactiva solo al volver.
- Se silencia mientras hay un dictado por voz activo dentro de la app (comunicación entre el iframe y la landing vía `postMessage`, con el mensaje `{ tipo: "diario-suenos-dictado", activo: true/false }`).
- El botón de silenciar manual tiene prioridad sobre todo lo anterior mientras esté activado.

## Por qué "prompt para copiar" y no una API conectada directamente

Se eligió así a propósito: sin necesidad de una API key propia ni costo por uso, con control total sobre qué IA usar y cuándo. Si en algún momento se quiere automatizar (por ejemplo, un botón que llame directamente a la API de Claude), es una ampliación posible sobre esta misma base — avisale a Claude Code para que lo agregue.

## Privacidad

Todo el contenido queda en tu navegador y en los archivos que vos exportes — no se manda a ningún servidor. Los relatos de sueños suelen ser información personal/sensible: si alguna vez compartís un backup o una exportación, tené en cuenta qué hay adentro.
