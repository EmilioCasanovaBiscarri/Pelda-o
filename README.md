# Peldaño

Una escalera que conecta dos palabras. Juego de palabras diario en español: se baja de una palabra a otra de la misma longitud (4 o 5 letras) cambiando **una sola letra en cada peldaño**, y cada peldaño tiene que ser una palabra válida.

*De la risa a la pena:* risa → pisa → pesa → pena (3 peldaños).

## Reglas

- Solo se cambia una letra por paso, en la misma posición: no se añaden, quitan ni reordenan letras.
- Las tildes no cuentan (pésa = pesa). La **ñ** es una letra propia.
- No se puede repetir una palabra dentro de la misma escalera.
- Valen las palabras de la lista `palabras` del diccionario (`data/diccionario.json`).
- El **par** es el número mínimo de peldaños usando solo palabras corrientes (viene calculado en `data/retos.json`).
- Puntuación tipo golf: peldaños + pistas, frente al par. Menos: «¡Bajo par!» (posible con atajos de palabras raras); igual: «En par»; más: «+1», «+2»…
- **Tres escaleras al día**, una por nivel: Principiante (par 3–4), Intermedio (par 5) y Experto (par 6 o más). Se elige con el selector de arriba; al terminar una, se ofrece la siguiente. El día está **completado** cuando se terminan las tres (llegando o rindiéndose).
- El **nº 1 es el 15 de octubre de 2026** (constante `LANZAMIENTO` en `js/juego.js`). El reto *n* de cada nivel es el del día *n*; si hay menos retos que días, se vuelve a empezar por el primero.

## Cómo se juega en pantalla

- La escalera: la palabra de salida arriba (rellena), los peldaños debajo y la de llegada abajo, con contorno discontinuo hasta alcanzarla. Las letras que ya coinciden con la de llegada en su posición van en verde.
- En la **fila activa** (la última palabra) se toca la letra que se quiere cambiar y se escribe la nueva: aparece una fila de **borrador** con la letra cambiada resaltada. **Subir** fija el peldaño.
- Teclado en pantalla con ñ (QWERTYUIOP / ASDFGHJKLÑ / ZXCVBNM), **Borrar** y **Subir**. Con teclado físico: letras (las vocales con tilde valen como sin ella), Intro para subir, Retroceso para borrar y flechas para mover la letra elegida.
- Errores en la propia página, sin alertas: «PESU» no está en el diccionario; «PESA» ya está en tu escalera.
- **Deshacer** quita el último peldaño sin penalizar.
- **Pista** suma 1 al marcador: busca el camino más corto (búsqueda en anchura) desde la palabra actual hasta la de llegada por palabras `corrientes` sin repetir las de la escalera; si no lo hay, por cualquier palabra válida; si tampoco, sugiere deshacer. El siguiente peldaño queda como borrador. Si ya está en el borrador, no se vuelve a cobrar.
- **Rendirse** pide confirmación en el propio botón («Confirmar», 5 segundos) y enseña la ruta del par.
- Arriba: nombre, calendario, racha y ayuda; el selector de nivel (con el par o el resultado de cada uno); el número del día y el título del reto; y el marcador (par, peldaños y pistas, en rojo si ya se pasa del par).
- **Resultado** (al terminar, y luego con «Ver resultado»): veredicto («¡Bajo par!», «En par», «+n sobre el par» o «Te has rendido»), tu escalera, la ruta del par (`sol`), el mínimo absoluto si es menor que el par (`min`), la **Nota curiosa** (`note`) y el texto para compartir con **Copiar resultado**:

  ```
  Peldaño #3 · Intermedio
  De la reja al muro
  🪜 6 peldaños (par 5) · +1
  joseleking.github.io/Peldano
  ```

  Con pistas, «🪜 5 peldaños + 1 pista (par 5) · +1»; al rendirse, «🏳️ Me he rendido (par 5)».
- **Volver a jugar** repite la misma escalera desde cero. El resultado que cuenta (estadísticas, selector, compartir) es siempre el primero; el de la repetición se ve aparte en la ventana de resultado.
- **Siguiente nivel**: el siguiente sin terminar de ese día.

## Días pasados, racha y estadísticas

- **Calendario** (botón del calendario): se juegan los días anteriores; no se puede adelantar a días futuros. Los días con algún nivel empezado llevan un punto; los que tienen los tres niveles terminados, relleno verde. Mientras se juega un día pasado, una franja lo indica, con «Volver a hoy».
- **Racha** (llama en la barra de arriba): días seguidos en que se completan los tres niveles del día, ese mismo día. Jugar el archivo no cuenta. Si hoy aún no se ha completado pero ayer sí, la racha sigue viva (borde discontinuo) hasta medianoche. Al tocarla se ve también la mejor racha.
- **Estadísticas** (segunda pestaña del calendario): días completados, racha actual y mejor racha; y por nivel, partidas, veces en par o bajo par (con porcentaje) y una barra con el reparto bajo par / en par / sobre el par / rendido.
- Se abre siempre en **modo claro**. El **modo noche** se activa con el interruptor al final de la ventana «?», se recuerda en el navegador y se aplica antes de pintar la página (script en el `<head>`).
- La partida de cada día y nivel, la racha y las estadísticas se guardan en el dispositivo (`localStorage`, clave `peldano-v1`), así que se puede cerrar y volver sin perder nada. Si el juego está abierto en varios sitios a la vez, al guardar se une lo de cada uno (de cada partida, la más reciente, conservando el primer resultado) y cada pestaña se pone al día cuando otra guarda (evento `storage`). Si se cambia `LANZAMIENTO`, el progreso guardado se corre solo para que cada partida siga en su fecha.
- Es una PWA: se puede instalar en el móvil y funciona sin conexión. Tras el tercer día jugado, el juego invita una sola vez a instalarlo (en iPhone/iPad con los dos pasos de Safari; en Android/Chrome con un botón «Instalar»). Nunca sale si ya se juega desde la app instalada.
- Respeta `prefers-reduced-motion` (sin animaciones) y todos los controles tienen foco visible.

## Estructura

```
index.html              página del juego
reiniciar/index.html    borra el progreso guardado en el dispositivo (/reiniciar)
css/estilo.css          estilos (modo claro y oscuro; paleta del logo: verde salvia #7DB48C, azul marino #1E2A3A, crema #F6F1E7)
js/juego.js             lógica del juego: reglas, búsqueda de caminos, partidas, calendario, estadísticas y compartir
data/retos.json         retos por nivel: a, b, t (título), par, sol (ruta del par), min, note
data/diccionario.json   palabras (todas las válidas) y corrientes (las del par y las pistas)
manifest.webmanifest    datos de la app instalable
sw.js                   service worker (funcionamiento sin conexión)
icons/                  logo e iconos de la app (favicon.svg es el original); og.png es la vista previa al compartir el enlace, generada desde og-fuente.html
fonts/                  Bricolage Grotesque y Atkinson Hyperlegible, alojadas aquí para ir sin conexión
```

## Probarlo en local

El juego carga `data/retos.json` y `data/diccionario.json` con `fetch`, así que hay que servirlo por HTTP (abrir `index.html` con doble clic no funciona):

```sh
python3 -m http.server 8000
```

y abrir <http://localhost:8000>.

Para empezar de cero, abre <http://localhost:8000/reiniciar> y confirma con «Sí, borrar todo». Borra las partidas, la racha y los ajustes de este navegador, además de la copia sin conexión del service worker, y vuelve al juego.

## Añadir retos

`data/retos.json` tiene tres listas (`principiante`, `intermedio`, `experto`) con **la misma cantidad de retos**; el reto *n* de cada una es el del día *n*. Para añadir días, añade un reto al final de cada lista. **Después**, sube `VERSION` en `sw.js` (por ejemplo, `peldano-v2`) para que los móviles con la app instalada reciban los cambios. Lo mismo al cambiar cualquier otro archivo del juego.

## Publicación en GitHub Pages

El juego es estático y usa rutas relativas, así que funciona tal cual en `https://joseleking.github.io/Peldano/`:

1. En GitHub: *Settings → Pages → Build and deployment → Source: Deploy from a branch*.
2. Rama `main`, carpeta `/ (root)`, *Save*.
3. A los pocos minutos estará en <https://joseleking.github.io/Peldano/>.

Para instalarlo en el móvil: abrir la dirección y, en Android (Chrome), *Instalar aplicación*; en iPhone (Safari), *Compartir → Añadir a pantalla de inicio*. La instalación y el modo sin conexión necesitan HTTPS, que GitHub Pages ya da.

Si cambia la dirección, actualiza `URL_JUEGO` en `js/juego.js` (de ella sale el enlace del texto de compartir, sin `https://`) y las etiquetas `og:` de `index.html`.

## Derechos de autor

© 2026 Emilio Casanova Biscarri. Todos los derechos reservados. El código, los textos y los diseños de este repositorio no pueden copiarse ni reutilizarse sin permiso. Consulta el archivo [LICENSE](LICENSE).
