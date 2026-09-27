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
