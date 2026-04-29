[![License: MIT](https://img.shields.io/badge/License-MIT-black.svg)](https://opensource.org/licenses/MIT)

# UID — Universal Intelligent Decoder

> Una herramienta de decodificación inteligente en un solo archivo, sin dependencias, con auto-detección de 6 formatos.

```
 _   _ ___ ____
| | | |_ _|  _ \
| | | || || | | |
| |_| || || |_| |
 \___/|___|____/

Universal Intelligent Decoder v1.0
```

---

## ¿Qué es esto?

UID es una single-page application brutalista-minimalista (`index.html`) que detecta y decodifica texto codificado en tiempo real — sin botones, sin servidor, sin dependencias. Pegás el input y el motor identifica el formato y te tira el resultado al toque.

---

## Features

- **Motor de auto-detección** — analiza el input y elige el formato correcto solo
- **6 formatos soportados** — Brainfuck, Binario, Hexadecimal, Base64, Morse, ROT-13
- **Decodificación en tiempo real** — escucha el evento `input`, se actualiza con cada tecla
- **Indicador de formato** — etiqueta `[DETECTADO: FORMATO]` siempre visible en el panel de resultado
- **Puntaje de confianza** — ALTA / MEDIA / BAJA según heurísticas por formato
- **Cero dependencias** — HTML + CSS + JS vanilla puro, un solo archivo, funciona offline
- **Intérprete de Brainfuck** — motor BF completo con mapa de brackets pre-computado y límite de 200k pasos
- **Responsive** — layout de dos columnas en desktop, una columna en mobile
- **Copiar al portapapeles** — un clic y listo

---

## Formatos soportados

| Formato | Método de detección | Notas |
|---|---|---|
| **Brainfuck** | Presencia de `[ ] > < + - . ,` | Intérprete completo, límite de 200k pasos |
| **Binario** | Solo `0`, `1` y espacios | Agrupa en bloques de 8 bits, valida salida ASCII |
| **Hexadecimal** | Pares `0–9`, `A–F` | Acepta espacios, `:` o sin separador |
| **Base64** | Regex + longitud múltiplo de 4 | Usa `atob()` nativo, valida salida imprimible |
| **Morse** | Solo `.`, `-`, `/` y espacios | Tabla de 46 símbolos, `/` o doble espacio como separador de palabras |
| **ROT-13** | Heurística sobre texto ASCII plano | Se activa solo si la rotación produce palabras reconocibles en inglés |

### Prioridad de detección

El motor prueba los formatos en este orden exacto, para en el primer decode exitoso:

```
1. Brainfuck  →  2. Binario  →  3. Hex  →  4. Base64  →  5. Morse  →  6. ROT-13
```

---

## Uso

Sin instalación, sin npm, sin nada.

```bash
# Cloná el repo
git clone https://github.com/juakolp25/decode_klk.git

# Abrí el archivo en cualquier navegador
open index.html
```

O simplemente descargás `index.html` y lo abrís localmente — corre 100% en el navegador.

---

## Ejemplos de input

```
Binario        →   01001000 01100101 01101100 01101100 01101111
Hexadecimal    →   48 65 6C 6C 6F
Base64         →   SGVsbG8gV29ybGQ=
Morse          →   .... . .-.. .-.. --- / .-- --- .-. .-.. -..
ROT-13         →   Uryyb Jbeyq
Brainfuck      →   ++++++++[>++++[>++>+++>+++>+<<<<-]>+>+>->>+[<]<-]>>.>---.+++++++..+++.>>.<-.<.+++.------.--------.>>+.>++.
```

---

## Diseño técnico

### Arquitectura

Toda la aplicación es un único archivo HTML estructurado en tres capas:

```
index.html
├── <style>   — Variables CSS, layout grid brutalista, animaciones
├── <body>    — Layout dos paneles (input / output) + barra de formatos + strip de metadata
└── <script>  — Motor de auto-detección + 6 decoders + lógica reactiva de UI
```

### Motor de Brainfuck

El intérprete BF pre-computa un mapa de brackets en el momento del parseo, reduciendo los saltos `[` / `]` de búsquedas lineales O(n) a lookups O(1). Un contador de pasos corta la ejecución al llegar a 200.000 ciclos para evitar loops infinitos.

```js
// Pre-cómputo del mapa de brackets
for (let i = 0; i < bf.length; i++) {
  if (bf[i] === '[') stack.push(i);
  else if (bf[i] === ']') {
    const open = stack.pop();
    bracketMap[open] = i;
    bracketMap[i] = open;
  }
}
```

### Heurística ROT-13

Como ROT-13 siempre es texto ASCII sintácticamente válido, un detector naive dispararía con cualquier string. UID puntúa la salida post-rotación contra una lista de ~35 palabras frecuentes en inglés. Solo muestra el decode si hay al menos un match, y gradúa la confianza según la cantidad de coincidencias.

### Niveles de confianza

| Nivel | Significado |
|---|---|
| `ALTA` | Firma del formato inequívoca, salida 100% imprimible |
| `MEDIA` | Formato matcheado pero la salida tiene alguna anomalía menor |
| `BAJA` | Señal débil — posible falso positivo |

---

## Sistema de diseño

La UI sigue una estética **Brutalista Minimalista**:

- **Paleta:** `#000000` / `#FFFFFF` — sin grises, sin gradientes
- **Bordes:** `2px solid #000` — cero border-radius en ningún lado
- **Tipografía:** `'Courier New', Courier, Consolas, monospace` — stack monospace del sistema
- **Movimiento:** CSS `@keyframes flash-in` en cada nuevo decode; cursor parpadeante en estado idle
- **Layout:** CSS Grid de dos columnas en desktop, una columna por debajo de 700px

---

## Soporte de navegadores

Funciona en cualquier navegador moderno con soporte ES6+. Sin build, sin bundler, sin npm.

| Chrome | Firefox | Safari | Edge |
|---|---|---|---|
| ✓ 80+ | ✓ 75+ | ✓ 14+ | ✓ 80+ |

---

## Licencia

MIT — hacé lo que quieras.

---

*Hecho con HTML, CSS y JavaScript vanilla. Ningún framework fue lastimado en el proceso.*
