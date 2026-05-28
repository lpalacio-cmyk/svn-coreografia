# CLAUDE.md — App de Armado Coreográfico SVN (INTERNA)

## Qué es este proyecto
App web de una sola página (`index.html`, HTML/CSS/JS vanilla, sin frameworks ni build) para **generar y explorar formaciones coreográficas de Street Dance**. Es la versión **PRIVADA / de uso interno** del coreógrafo de SVN. Su rasgo distintivo es el **generador algorítmico de formaciones** (el "cerebro"): produce ubicaciones de bailarines según parámetros. Esta lógica es know-how del autor y **no debe simplificarse, exponerse ni removerse**.

> Existe una segunda app, PÚBLICA, en OTRO repositorio: es un editor manual sin generador. NO confundir. Los cambios de un repo no aplican al otro.

## Stack y restricciones técnicas (IMPORTANTE)
- **Un único archivo `index.html`** en la raíz. Todo el CSS y JS va inline en ese archivo. No introducir build steps, npm, bundlers, ni dividir en múltiples archivos sin que el usuario lo pida explícitamente.
- **Vanilla JS puro.** No React, no Vue, no jQuery, no librerías externas salvo Google Fonts.
- **Deploy en Vercel como sitio estático.** Cualquier cambio debe seguir funcionando con solo servir el HTML.
- **Sin backend.** Toda persistencia es `localStorage` del navegador.
- **No usar `localStorage`/`sessionStorage` está PERMITIDO acá** (a diferencia de los artifacts de Claude.ai): este proyecto corre en Vercel, donde sí funcionan. De hecho ya se usan para favoritos y settings.
- Los logos SVN están embebidos como **PNG en base64** e inyectados como CSS variables (`--logo-icon-url`, `--logo-combo-url`) que se usan vía `-webkit-mask`/`mask`. Esto permite que el logo tome el color del tema. No reemplazar por `<img>`.

## Identidad visual (NO cambiar sin pedido explícito)
- **Tipografía**: `Poppins` para todo el texto/UI; `JetBrains Mono` solo para etiquetas pequeñas tipo "data" (mayúsculas, espaciadas). NO usar serfiles ni fuentes editoriales (se probó y se descartó por verse "antiguo").
- **Color de acento por defecto**: verde `#1C913D`. Hay un picker de 6 acentos: acid (verde), cobalt, magma, hibiscus, pollen, mono.
- **Temas**: dark (default) y light, vía `data-theme` en `<html>`. Todo el color sale de CSS variables en cascada — respetar ese sistema, no hardcodear colores.
- **Tono general**: moderno, limpio, joven, street. Evitar lo "corporativo editorial premium".
- El escenario muestra **público arriba, fondo abajo** por convención del autor.

## Preferencias de trabajo del usuario (Christian / SVN)
- Habla **español rioplatense (vos)**. Respondé en ese registro.
- **No es programador.** Explicá los cambios en lenguaje simple, sin jerga innecesaria.
- Quiere **archivos completos y funcionando**, no parches a medias. Antes de dar por cerrado un cambio, verificá que el JS no rompa (sintaxis) y que las etiquetas HTML estén balanceadas.
- **Hacer SOLO lo que se pide.** No agregar features fuera del alcance del pedido. Si tenés una idea extra, sugerila al final, no la implementes sin permiso.
- Prefiere **previsualizar / revisar antes** de dar por final.

## Cómo está organizado el código (referencia rápida)
Dentro del `<script>` principal:
- `state` — objeto global con todo el estado (theme, accent, count, params del generador, modo, favoritos, etc.).
- **Generador**: `generateFormation` → `generateCandidate` → `placeClusters` / `distributeDancers`, con `passesConstraints` y `scoreFormation` para elegir la mejor de varias tiradas. ESTE ES EL NÚCLEO. Tratar con cuidado.
- Parámetros del generador: densidad, asimetría, aislamiento, sesgo diagonal, distancia mínima, vacíos intencionales.
- **Divisiones HHI**: mini (3-4), crew (5-9), mega (10-40). Hay referencia al reglamento HHI 2026 (opcional, no obligatorio para el usuario).
- **Modos**: estática, transición (anima A→B con estela y trayectorias), secuencia (varias formaciones encadenadas).
- `matchTransition` / `getPathPoint` — asignación de bailarín a destino y curvas de trayectoria.
- **Drag & drop**: `onDragStart`/`onDragMove`/`onDragEnd`. Mover posiciones in-place SIN re-render durante el drag (re-renderizar rompe el pointer capture). Click corto (sin mover) abre editor de nombre.
- **Nombres por bailarín**: opcionales, se asignan tocando un bailarín.
- Favoritos y settings en `localStorage` con claves versionadas.

## Reglas de seguridad / alcance
- **No remover ni simplificar el generador algorítmico.** Es el valor de esta app.
- No cambiar identidad visual (fuentes, colores, logos) salvo pedido explícito.
- No agregar tracking, analítica ni dependencias externas sin pedido.
- Al terminar una tarea, dejá el `index.html` funcionando de punta a punta.
