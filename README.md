# Paintball Layout 3D — resumen del proyecto

Prototipo independiente (fuera del app de Aggressive) para convertir el diagrama oficial de un layout NXL en un field 3D recorrible en primera persona. Referencia a superar: gunzup.com (player en Unity, requiere cuenta).

## Archivos
- `index.html` — TODO el app en un solo archivo (HTML + CSS + JS). Sin build, sin dependencias locales.
- Única librería: Three.js r128 desde cdnjs (`three.min.js`). No usa addons (OrbitControls, GLTFLoader, etc.).
- Versión publicada para el celular: https://claude.ai/code/artifact/709d3e64-04c5-4c1d-826b-d56fbb7ef055

## Qué hace
1. **Editor 2D** (canvas): coloca bunkers arrastrando; rotar (Q/E o rueda), renombrar (doble clic), borrar, duplicar.
   Paleta: dorito, can, mini can, cubo, temple, brick, brick S, mini, X, snake, beam. Snake/beam tienen largo editable.
   Foto del layout como fondo semitransparente para calcar (escala/offset/opacidad). "Espejar ↑" copia la mitad de abajo a la de arriba.
2. **Modo 3D** (Three.js): primera persona con WASD + mouse (pointer lock), Shift correr, C agacharse, T vista aérea.
   Celular: joystick táctil (lado izq.) y mirar arrastrando (lado der.). "Ir a bunker…" teletransporta detrás del bunker.
3. **Persistencia**: layouts en localStorage; exportar/importar JSON.

## Datos
- Campo NXL: 150 × 120 ft. Coordenadas en pies, origen en el centro; x a la derecha, z hacia el home de abajo.
- Bunker: `{id, type, x, z, rot (grados, horario), color: 'red'|'blue', name, len?}`.
- Layout precargado: **Lonestar Open 2026** (transcrito a ojo de la foto oficial, función `lonestar()`).

## Cómo están hechos los bunkers (versión actual)
- Cajas, temple, X y doritos: **superelipsoides** — se toma una esfera subdividida y cada vértice se proyecta hacia la forma (planos del bunker) con una norma L‑k (k≈3.2–4.5). Da bordes tipo almohada como los inflables Sup'Air. Función `inflatedGeo(planes,k,center)`.
- La "piel" es una textura equirectangular por tipo/color (`skinMat`): color base, puntas del color opuesto, costuras y el logo del evento (nombre del layout) horneado.
- Cans: LatheGeometry con panza + cúpula del otro color + decal con logo. Snake/beam: tubo (cilindro + semiesferas) con anillos de costura.
- Material: MeshPhysicalMaterial (vinilo: roughness 0.36, clearcoat 0.5, envMap del cielo).

## Entorno
Cielo con shader de gradiente + sol + nubes procedurales; PMREM del cielo para reflejos; sombras PCFSoft; turf con rayas de corte, líneas, números de yardas y nombre del layout; red negra con banners abajo, postes de madera, cable; carpas de pits; marcador en primera persona con balanceo; etiquetas flotantes que se desvanecen al acercarte.

## Historial de intentos (por si otra AI quiere mejorarlo)
1. Cajas con bordes redondeados (ExtrudeGeometry + bevel): se veían "de caja".
2. Paneles planos hinchados con costuras (pillow faces): seguían pareciendo cuadrados.
3. Superelipsoides (actual): mucho más parecido a un inflable. Siguiente nivel real = modelos GLB hechos en Blender/Higgsfield (generate_3d) de cada bunker Sup'Air y cargarlos con GLTFLoader.

## Pendientes / ideas
- Modelos 3D "de verdad" (GLB) por tipo de bunker, con texturas fotográficas de vinilo.
- Líneas de tiro (lanes) entre bunkers; jugadores fantasma en los bunkers rivales.
- Detección automática de bunkers desde la foto del layout (hoy es manual calcando).
- Compartir layouts vía Firebase e integrarlo al app de Aggressive (D:\Aggressive).
- El botón "Exportar JSON" no descarga dentro del artifact de Claude (sí en local); copia al portapapeles.

## Revisión de estabilidad — 11 septiembre 2026
- Nombres existentes protegidos al crear, renombrar, importar y cargar el ejemplo.
- Importación validada: tipos, coordenadas, colores, largos e identificadores.
- Estado de guardado visible, guardado al abandonar la página y aviso si falta espacio.
- Se evita duplicar el bucle de animación al pulsar varias veces Recorrer 3D.
- Controles reiniciados al perder foco, salir del modo 3D o cancelar el botón táctil.
- Aviso si no carga Three.js o falla el inicio de WebGL.
- Cancelación del arrastre y límites en las propiedades de posición y largo.

### Uso local
Abre index.html con Edge o Chrome. Se necesita Internet para cargar Three.js desde cdnjs. Los layouts se guardan en el navegador; exporta JSON para conservar copias independientes. La foto de referencia no se incluye en el JSON exportado.

### Validación
Comprobada la sintaxis de JavaScript y ejecutadas pruebas con DOM simulado de arranque, nombres, importaciones, guardado y controles. No se ha verificado visualmente WebGL ni realizado pruebas en un dispositivo móvil real. Los modelos GLB, detección automática, Firebase e integración con Aggressive siguen pendientes.

## Acabado de bunkers — referencia visual GunzUp
- Vinilo con mapas procedurales de relieve y rugosidad, pliegues leves y reflejo moderado.
- Caras más rectas en cajas, temples y doritos; deformación suave cerca de la base.
- Texturas por panel para cajas y temples, logos por cara y tapas de color con borde continuo.
- Uniones en cans y tubos, pequeñas lengüetas de anclaje y ajuste de iluminación.
- Referencia visual consultada: https://gunzup.com/league/national-xball-league/ (imágenes públicas del Lone Star Open).
- Geometrías y texturas propias generadas por código; no son modelos escaneados ni assets extraídos de GunzUp.
- Validación: sintaxis y pruebas de lógica correctas; render comprobado visualmente en primera persona en el navegador integrado. No probado en móvil real.

Revisión adicional con el visor de GunzUp autenticado: tensión en bases de temples y doritos, logos mayores y textura fina del césped. Render final verificado en primera persona.

## Referencia adicional: Infinite Tournament Paintball
Se revisó el tráiler oficial en Steam (https://store.steampowered.com/app/1341160/Infinite_Tournament_Paintball/). Se incorporó un acabado propio con impactos amarillos irregulares, gotas, residuos secos, roce y suciedad leve. Cada bunker usa una semilla distinta. El botón Pintura permite comparar el acabado limpio y usado en tiempo real. Son marcas visuales estáticas, no impactos de un sistema de disparo. No se reutilizaron archivos del juego.
Comprobación: render y alternancia del botón revisados visualmente en el navegador; sintaxis y pruebas de lógica correctas. Rendimiento móvil pendiente de comprobar.

## Temple reconstruido por paneles
Se sustituyó la geometría del tipo temple por cuatro paneles paramétricos, tapa abombada, perfil de presión con falda, soldaduras tubulares, pliegues cerca de la base, válvula y anclajes. La textura tiene proyección horizontal corregida para evitar deformar el logo. Los demás tipos conservan su geometría anterior.
El botón Ver temple de cerca abre una escena de inspección: arrastrar gira el modelo, la rueda modifica la distancia y Esc vuelve al campo. Los temples del layout usan también la nueva geometría.
Validación: geometría finita, índices válidos, altura, continuidad entre los cuatro paneles y proyección del logo; arranque y pruebas de lógica; inspección visual y giro comprobados en navegador. No se ha comprobado en móvil real.

## Todos los bunkers por paneles — 12 septiembre 2026 (Claude)
Se generalizó la técnica del temple de ChatGPT a un constructor genérico: `panelFrom(surface, angleAt, U, VV)` muestrea una superficie paramétrica `surface(angle, v)` en paneles con normales por diferencias finitas y UV por panel; `lidFrom` genera la tapa abombada; `seamAlong` traza las soldaduras como tubos sobre la superficie.
- `makePressureBox` (brick, brick S, mini, cubo, brazos de la X): superelipse n=6.4, panza leve, falda, pliegues cerca de la base, tapa, 4 soldaduras verticales + 2 anillos, válvula. Puntas del otro color en los extremos largos; el cubo lleva la tapa del otro color; el logo va en las caras grandes.
- `makePressureDorito`: tres paneles que convergen en el vértice (triángulo redondeado con norma L‑k que se desplaza hacia el vértice T sobre el borde delantero), perfil panzón `(1-v)^0.72`, soldaduras en las tres aristas, anillo base, remate del vértice y válvula.
- `makePressureCan`: cuerpo torneado con textura de vinilo (logo con escala 0.5 para compensar la proyección cilíndrica), cúpula del otro color, dos anillos y dos soldaduras verticales, válvula.
- `makePressureTube` (snake, beam): cilindro + semiesferas del otro color con la textura de vinilo sin bordes soldados (`plainSeams`), anillos cada ~3 ft y dos soldaduras longitudinales.
- `pressureVinyl(color, logo, opts)` ahora acepta `{sx, sy, plainSeams}`.
- Backup previo: `index.before-panels-20260912.html`. Validación: sintaxis y render en primera persona comprobados en navegador; sin errores de consola. No probado en móvil real (la carga de geometría es más pesada que antes).

## Exportar foto y renombrar en 3D — 12 septiembre 2026 (Claude)
- **Exportar foto del layout** (editor, botón grande): dibuja el diagrama a 1400×1900 px con título, campo, bunkers y nombres legibles, y lo muestra en un visor con "Guardar / compartir". El guardado intenta en orden: capacidad `downloads` del visor de Claude (`claude.use('downloads')`), `navigator.share` con archivo (celular), y enlace de descarga; si nada aplica, se mantiene presionada la imagen. El artifact se publica con `capabilities: {downloads: true}`.
- **Foto 3D** (HUD): captura el canvas WebGL sin el marcador y abre el mismo visor.
- **Renombrar (R)** (HUD): raycast desde la mira al bunker apuntado (`group.userData.bunkerId`), `prompt` con el nombre y actualiza la etiqueta flotante y el selector "Ir a bunker…" sin reconstruir la escena. En el editor 2D sigue el doble clic y el campo Nombre.

## Logo y nombre del equipo en los bunkers — 12 septiembre 2026 (Claude)
- `drawLogo` ahora pinta un parche negro con borde blanco y el logo del equipo (`teamImg`); si no hay imagen, escribe el nombre. Debajo del parche, en `pressureVinyl`, va el nombre del evento (layout).
- `TEAM_DEFAULT` trae el logo de Aggressive embebido en base64 (`team-logo.jpg`, 320 px, copia reducida de `D:\Aggressive\logo.png`). El equipo se guarda en `localStorage` (`pb_team`) y se edita en el panel "Equipo": nombre, subir logo (se reduce a 320 px) y "Volver a Aggressive". Al cambiar se vacían las cachés de materiales (`clearMaterialCaches`) y el 3D se reconstruye al entrar.
- Los banners de la red usan `team.name`. `show3D` espera a que cargue el logo antes de construir.
- Backup previo: `index.before-team-20260912.html`.
