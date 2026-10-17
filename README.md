<div align="center">

<img src="./docs/screenshot.png" alt="Pasa Pasa: la pasa en la playa, frente a la casita de la isla" width="360" />

# 🍇 Pasa Pasa

*Un mini-juego de arrastrar en un solo archivo, sin dependencias y en español*

[![Jugar ahora](https://img.shields.io/badge/Jugar-ahora-6c4bd9?style=flat-square&logo=vercel&logoColor=white)](https://olaolapasapasa.vercel.app)
[![Dependencias: 0](https://img.shields.io/badge/dependencias-0-5cb05c?style=flat-square)](#ejecutar-en-local)
[![GitHub stars](https://img.shields.io/github/stars/Natanmiuz/olaolapasapasa?style=flat-square&logo=github)](https://github.com/Natanmiuz/olaolapasapasa)

[Características](#características) • [Cómo jugar](#cómo-jugar) • [Ejecutar en local](#ejecutar-en-local) • [Cómo funciona](#cómo-funciona) • [Accesibilidad](#accesibilidad) • [Colaborar](#colaborar)

</div>

Una pasa llega a la playa buscando refugio y se encuentra con una ola. Arrástrala hasta la puerta de la casita, charla con la ola y déjala pasar: «¡Ola, ola!» — «¡Pasa, pasa!». Todo el chiste está en el doble sentido: *ola* suena a «hola» y *pasa* es a la vez la uva pasa y «adelante».

Cada partida completada suma a un contador que se guarda en tu navegador.

## Características

- **Física con gracia**: la pasa se estira al cogerla, se aplasta y rebota al soltarla, y conserva la inercia si la lanzas. Si llega deslizando a la puerta, también cuenta.
- **Final con recompensa**: la puerta se abre, la pasa entra en casa, se enciende la luz y cae confeti.
- **Contador de partidas** guardado en `localStorage`: «¡Has dejado pasar a la pasa 7 veces!».
- **Ratón, táctil y teclado**: eventos *pointer* unificados y control con las flechas.
- **Sonido sintetizado** con la Web Audio API, sin archivos de audio.
- **Un solo `index.html`**: sin build, sin dependencias, sin imágenes; todo se dibuja con Canvas 2D.

> [!TIP]
> Está pensado sobre todo para el móvil: ábrelo en el teléfono y lanza la pasa con el dedo.

## Cómo jugar

1. Abre la [demo](https://olaolapasapasa.vercel.app) o el archivo `index.html`.
2. Arrastra (o lanza) la pasa hasta la puerta. En escritorio también puedes moverla con las flechas: un toque la empuja un poco y, manteniéndolas pulsadas, camina.
3. Pulsa **Siguiente** para avanzar el diálogo con la ola y **¡Entrar!** para terminar.
4. **Jugar otra vez** reinicia la partida sin recargar; el contador se conserva.

El botón 🔊 (arriba a la izquierda) silencia o reactiva el sonido.

## Ejecutar en local

No hay nada que instalar. Basta con abrir el archivo en el navegador:

```bash
start index.html   # Windows
open index.html    # macOS
```

Si prefieres servirlo por HTTP (por ejemplo, para probarlo desde el móvil en tu red local), cualquier servidor estático vale:

```bash
python -m http.server 8765
```

## Cómo funciona

El juego es un motor pequeño hecho a mano dentro de `index.html`:

- **Híbrido canvas + HTML.** El mundo (playa, casa, pasa, ola, confeti) se dibuja en un `<canvas>`; el diálogo, los botones y el contador siguen siendo HTML para conservar el foco, `aria-live` y el texto nítido.
- **Bucle de juego.** `requestAnimationFrame` llama a `update(dt)` y `render()` en cada fotograma; todo el movimiento usa *delta time*.
- **Física con muelles.** La elevación, el *squash & stretch*, la inclinación y la aparición de la ola son muelles amortiguados (`spring`); la inercia es velocidad con fricción y rebote en los bordes.
- **Secuencia final con *tweens*.** La puerta, el paseo de la pasa y la cuenta del marcador son *tweens* basados en promesas, encadenados con `async`/`await`.
- **Perspectiva.** La pasa encoge a medida que sube por el camino, así cabe por la puerta de la casa del horizonte.

```
index.html           # Todo el juego: CSS, markup y JavaScript en línea
docs/screenshot.png  # Captura del README
AGENTS.md            # Guía de arquitectura y convenciones para tocar el código
```

> [!NOTE]
> Si vas a modificar el juego, empieza por [AGENTS.md](AGENTS.md): explica la estructura del bucle, el estado, el reinicio sin recarga y los detalles que conviene no romper.

## Accesibilidad

- Se puede jugar solo con teclado: el escenario recibe el foco (con un anillo visible alrededor de la pasa) y las flechas la mueven.
- Con `prefers-reduced-motion` se desactivan las animaciones CSS y, en el canvas, se congela el escenario, la física deja de rebotar y no hay confeti.
- El diálogo y el resultado final se anuncian con `aria-live`, y los controles tienen etiquetas accesibles.

## Colaborar

¿Ideas para mejorar el juego? Las pull requests son bienvenidas.

> [!IMPORTANT]
> Se aceptan mejoras con tal de trollear al pelucas del ADRITY. 😎
