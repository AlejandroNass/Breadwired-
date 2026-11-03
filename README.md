# breadwire

**Del esquema a la protoboard, hueco por hueco.** breadwire es un banco de
electrónica que funciona en el navegador: dibujas un esquema y te dice dónde va
cada pata y cada cable en una protoboard de verdad, en 2D y en 3D, comprobado
red por red. Además trae un entorno de electrónica digital con Arduino, un
simulador analógico y un entorno de análisis de circuitos.

- **Sitio**: <https://breadwire-beta.vercel.app>
- **Versión**: `1.0.0-beta.5` (beta: funciona y está verificada, pero puede
  tener errores). Los cambios de cada versión están en [`CHANGELOG.md`](CHANGELOG.md).
- **Idiomas**: español (el idioma fuente), inglés y japonés.
- **Sin cuenta y sin servidor**: todo se guarda en el navegador. La cuenta con
  Google, los proyectos en la nube y el asistente son opcionales.

![Taller y montaje: del esquema a la protoboard](img/home/traductor.jpg)

## Los cuatro entornos

| Entorno | Página | Qué hace |
| --- | --- | --- |
| **Taller y montaje** (Traductor) | `index.html` → `protoboard.html` → `protoboard3d.html` | Esquema dibujado a mano → montaje en protoboard 2D y 3D, con guía paso a paso, revisión eléctrica (ERC), lista de materiales y exportación a SPICE, KiCad y Wokwi |
| **Sistemas digitales** | `digital.html` | Tabla de verdad o expresión → simplificación (Quine–McCluskey) → compuertas → 119 integrados 74xx en la placa. Arduino Uno, ESP32 y Pico con IDE, Blockly, depurador y siete piezas (servo, HC-SR04, potenciómetro, LDR, DHT11, LCD I²C y tira WS2812) |
| **Laboratorio de electrónica** (Simulador) | `simulador.html` | Continua y transitorio, osciloscopio de dos canales, generador de funciones y polímetro |
| **Circuitos eléctricos** (Análisis) | `circuitos.html` | CC y CA con fasores, potencia compleja, Thévenin y diagrama de Bode |

Las demás páginas: `login.html` (la puerta), `home.html` (la portada),
`projects.html` (mis proyectos), `privacidad.html` y `condiciones.html`.
`start.html` sólo redirige.

| | |
| --- | --- |
| ![Sistemas digitales](img/home/digital.jpg) | ![Laboratorio de electrónica](img/home/simulador.jpg) |
| ![Montaje en 3D](img/home/digital-3d.jpg) | ![Análisis de circuitos](img/home/circuitos.jpg) |

## Ponerlo en marcha

Hace falta **Node.js 18 o más nuevo** (la integración continua usa el 22) y nada más: no hay `package.json`, ni
dependencias, ni paso de compilación para desarrollar.

```bash
node tools/serve.js
```

Y se abre <http://localhost:3000>. **No vale abrir los `.html` con doble clic**
(`file://`): son módulos ES y no arrancan.

## Pruebas

```bash
node tools/test.js --fast        # las suites rápidas, ~1 minuto
node tools/test.js               # todas, ~11 minutos (dos son lentas de verdad)
node tools/run-probes.cjs        # las sondas de navegador (Playwright + Chrome)
```

Las suites no necesitan navegador. Las sondas sí: Playwright y un Chrome
instalado (`tools/_pw.cjs` los busca). Hay además cuatro auditorías
(`tools/audit-*.cjs`) que comprueban que no queda un `import` roto, una pieza
que no se pueda colocar, una página con errores de consola ni un `dist/`
distinto del árbol. Cómo se usan, en [`AGENTS.md`](AGENTS.md) §5.

La integración continua (`.github/workflows/test.yml`) pasa las suites rápidas
en cada push y la batería completa cada noche.

## Publicar

El sitio se publica en **Vercel** desde la rama `master` de GitHub: cada push
despliega. Vercel ejecuta `node tools/build-deploy.js`, que copia lo que la
aplicación necesita en `dist/`, minifica `js/` y `css/` con `esbuild`, ofusca
`translator.js` si encuentra el ofuscador y versiona las URLs para la caché.
La configuración (cabeceras, CSP, la función del asistente) está en
`vercel.json`.

Para probar el paquete en local:

```bash
node tools/build-deploy.js --keep
node tools/audit-dist.cjs
```

### Lo opcional

- **Asistente de circuitos** (`api/bot.js`): necesita `GEMINI_API_KEY` o
  `GROQ_API_KEY` en Vercel. Sin claves, el panel lo dice y no hace peticiones.
- **Cuenta y nube** (`js/auth/`, `supabase/`): necesita `js/auth/config.js`
  con el proyecto de Supabase. Vacío, la aplicación es local y no se descarga
  nada. La puesta en marcha paso a paso está en
  [`docs/CUENTA-Y-NUBE.md`](docs/CUENTA-Y-NUBE.md).

## Mapa del repositorio

```
*.html              las 12 páginas
translator.js       el motor del Traductor (script clásico, no módulo)
js/                 el resto de la aplicación, por entornos (editor/, protoboard/,
                    digital/, sim/, circuitos/, analog/, design/, bot/, auth/, parts/)
css/                estilos, uno por entorno más el tema y la cabecera comunes
vendor/             three.js, Blockly y el cliente de Supabase, servidos desde el sitio
ejemplos/           plantillas, retos y lecciones
formato/            el JSON Schema del proyecto (.breadwire.json)
api/                el proxy del asistente (función de Vercel)
supabase/           las migraciones de la base de datos
tests/              las suites (node, sin dependencias)
tools/              servidor, build, lanzadores, sondas y auditorías
docs/               documentación técnica; docs/historia/ guarda lo fechado
planes/             el estado medido (ESTADO.md) y el plan (PLAN.md)
```

## Documentación

| Documento | Para qué |
| --- | --- |
| [`AGENTS.md`](AGENTS.md) | Reglas para tocar el código, cómo se prueba y las trampas que ya han pasado. **Léelo antes de cambiar nada.** |
| [`planes/ESTADO.md`](planes/ESTADO.md) | Lo que hay hoy, medido, con la prueba de cada cosa |
| [`planes/PLAN.md`](planes/PLAN.md) | Lo que falta, en orden y por fases |
| [`docs/PROYECTO.md`](docs/PROYECTO.md) | Cómo funciona cada entorno por dentro |
| [`docs/DIAGRAMA-FLUJO.md`](docs/DIAGRAMA-FLUJO.md) | El flujo interno, módulo a módulo |
| [`docs/DISENO.md`](docs/DISENO.md) | El sistema visual: temas, tipografía, componentes |
| [`docs/FORMATO-PROYECTO.md`](docs/FORMATO-PROYECTO.md) | El formato `.breadwire.json` |
| [`docs/BIBLIOTECA-DIGITAL.md`](docs/BIBLIOTECA-DIGITAL.md) | Los componentes del entorno digital y sus límites |
| [`docs/ARDUINO-COMPAT.md`](docs/ARDUINO-COMPAT.md) | Qué del lenguaje y de las librerías de Arduino está soportado |
| [`docs/CUENTA-Y-NUBE.md`](docs/CUENTA-Y-NUBE.md) | Login con Google y Supabase |
| [`CHANGELOG.md`](CHANGELOG.md) | Los cambios de cada versión |

## Lo que se sabe que falta

Es una beta. Lo pendiente, con su prioridad, está en `planes/PLAN.md`; lo más
visible:

- La revisión eléctrica ya mide corrientes y potencias con el solver, pero el
  Simulador todavía no tiene un panel donde enseñarla.
- Lo que no abriste nunca no funciona sin conexión: el service worker guarda lo
  que visitas, no el sitio entero.
- La migración `0002` de Supabase está escrita y sin aplicar.
