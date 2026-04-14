# 🖥️ Graficación por Computadora
 
> Apuntes de la materia de Graficación — Ingeniería en Sistemas Computacionales
 
---
 
## 📑 Índice General
 
- [Unidad 1 — Introducción a la Graficación](#) *(ver entrega anterior)*
- [**Unidad 2 — Graficación 2D**](#unidad-2-graficación-2d)
  - [2.1 Transformaciones Bidimensionales](#21-transformaciones-bidimensionales)
    - [2.1.1 Traslación](#211-traslación)
    - [2.1.2 Escalamiento](#212-escalamiento)
    - [2.1.3 Rotación](#213-rotación)
    - [2.1.4 Sesgado (Shearing)](#214-sesgado-shearing)
  - [2.2 Representación Matricial de las Transformaciones 2D](#22-representación-matricial-de-las-transformaciones-2d)
    - [Ejercicio: Control con Teclas de Dirección](#-ejercicio-control-con-teclas-de-dirección)
  - [2.3 Trazo de Líneas Curvas](#23-trazo-de-líneas-curvas)
    - [2.3.1 Curvas de Bézier](#231-curvas-de-bézier)
    - [2.3.2 B-Spline](#232-b-spline)
    - [Ejercicio: Animación con Curvas](#-ejercicio-animación-con-curvas)
  - [2.4 Fractales](#24-fractales)
  - [2.5 Uso y Creación de Fuentes de Texto](#25-uso-y-creación-de-fuentes-de-texto)
- [Referencias](#referencias)
 
---
 
# Unidad 2: Graficación 2D
 
La graficación 2D es la base de cualquier sistema gráfico por computadora. Comprende el conjunto de técnicas matemáticas y algorítmicas que permiten representar, transformar y manipular figuras en un plano bidimensional. En esta unidad se estudian las transformaciones geométricas, la representación matricial, el trazo de curvas, los fractales y el manejo de tipografía.
 
---
 
## 2.1 Transformaciones Bidimensionales
 
Las **transformaciones geométricas** son operaciones matemáticas que modifican la posición, el tamaño o la orientación de un objeto en el plano 2D sin alterar su forma intrínseca (en el caso de transformaciones afines). Son fundamentales en animación, diseño gráfico, videojuegos y simulación.
 
Las transformaciones elementales en 2D son:
 
| Transformación | Modifica         | Preserva          |
|----------------|------------------|-------------------|
| Traslación     | Posición         | Forma y tamaño    |
| Escalamiento   | Tamaño           | Ángulos (uniforme)|
| Rotación       | Orientación      | Forma y tamaño    |
| Sesgado        | Ángulos internos | Área (parcial)    |
 
---
 
### 2.1.1 Traslación
 
La **traslación** desplaza un punto (o un conjunto de puntos) desde una posición a otra sumando valores constantes a sus coordenadas.
 
**Definición matemática:**
 
Dado un punto $P = (x, y)$ y un vector de traslación $(t_x, t_y)$, el punto trasladado $P' = (x', y')$ se obtiene mediante:
 
$$x' = x + t_x$$
$$y' = y + t_y$$
 
O de forma vectorial:
 
$$P' = P + T \quad \text{donde } T = (t_x, t_y)$$
 
**Ejemplo:**
 
Si un punto está en $(3, 4)$ y se aplica una traslación de $(+5, -2)$:
 
$$x' = 3 + 5 = 8 \qquad y' = 4 + (-2) = 2$$
 
El punto se mueve a $(8, 2)$.
 
**Propiedades:**
- Es una transformación **rígida** (no deforma el objeto).
- Es **conmutativa**: trasladar por $T_1$ y luego por $T_2$ es igual a trasladar por $T_1 + T_2$.
- Se puede combinar con otras transformaciones usando **coordenadas homogéneas** (ver sección 2.2).
 
**Representación gráfica:**
 
```
  y
  │       P'(8,2)
  │    ↗
  │  P(3,4)
  └──────── x
```
 
> 💡 **Aplicación:** Mover sprites en videojuegos, animar objetos a lo largo de una trayectoria.
 
---
 
### 2.1.2 Escalamiento
 
El **escalamiento** modifica el tamaño de un objeto multiplicando sus coordenadas por factores de escala. Puede ser **uniforme** (mismo factor en $x$ y $y$) o **no uniforme** (factores distintos).
 
**Definición matemática:**
 
Dado un punto $P = (x, y)$ y factores de escala $s_x$ y $s_y$:
 
$$x' = x \cdot s_x$$
$$y' = y \cdot s_y$$
 
**Casos particulares:**
 
| Condición        | Efecto                                  |
|------------------|-----------------------------------------|
| $s_x = s_y > 1$  | Ampliación uniforme                     |
| $0 < s_x = s_y < 1$ | Reducción uniforme                   |
| $s_x \neq s_y$   | Deformación (escalamiento no uniforme)  |
| $s_x$ o $s_y < 0$| Reflexión (espejo)                      |
| $s_x = s_y = 1$  | Sin cambio                              |
 
**Escalamiento respecto a un punto fijo $(p_x, p_y)$:**
 
Cuando se escala respecto al origen, el objeto se desplaza también. Para escalar respecto a un punto arbitrario:
 
1. Trasladar el punto fijo al origen: $x'' = x - p_x,\ y'' = y - p_y$
2. Aplicar el escalamiento: $x''' = x'' \cdot s_x,\ y''' = y'' \cdot s_y$
3. Trasladar de regreso: $x' = x''' + p_x,\ y' = y''' + p_y$
 
> 💡 **Aplicación:** Zoom en interfaces gráficas, ajuste de imágenes, efectos de "grow/shrink" en animaciones.
 
---
 
### 2.1.3 Rotación
 
La **rotación** gira un objeto un ángulo $\theta$ alrededor de un punto (usualmente el origen o el centro del objeto).
 
**Definición matemática (respecto al origen):**
 
$$x' = x \cos\theta - y \sin\theta$$
$$y' = x \sin\theta + y \cos\theta$$
 
Donde $\theta$ es positivo en sentido **antihorario** (convención matemática estándar).
 
**Demostración intuitiva:**
 
Si un punto $P$ está a distancia $r$ del origen, en ángulo $\phi$:
- $x = r\cos\phi$, $y = r\sin\phi$
 
Después de rotar $\theta$:
- $x' = r\cos(\phi + \theta) = r\cos\phi\cos\theta - r\sin\phi\sin\theta = x\cos\theta - y\sin\theta$ ✓
 
**Rotación respecto a un punto arbitrario $(c_x, c_y)$:**
 
1. Trasladar el centro al origen.
2. Aplicar la rotación.
3. Trasladar de regreso.
 
$$x' = (x - c_x)\cos\theta - (y - c_y)\sin\theta + c_x$$
$$y' = (x - c_x)\sin\theta + (y - c_y)\cos\theta + c_y$$
 
**Tabla de valores comunes:**
 
| $\theta$ | $\cos\theta$ | $\sin\theta$ | Efecto               |
|----------|-------------|-------------|----------------------|
| $0°$     | 1           | 0           | Sin cambio           |
| $90°$    | 0           | 1           | Giro 90° antihorario |
| $180°$   | -1          | 0           | Inversión            |
| $270°$   | 0           | -1          | Giro 90° horario     |
 
> 💡 **Aplicación:** Animación de personajes, rotación de la cámara en un mapa, efectos de giro en interfaces.
 
---
 
### 2.1.4 Sesgado (Shearing)
 
El **sesgado** (o *shearing*) inclina la figura a lo largo de un eje, deformando los ángulos internos del objeto mientras mantiene su área. Es una transformación que "empuja" las coordenadas de un eje en función del valor del otro eje.
 
**Sesgado en el eje X (horizontal):**
 
$$x' = x + sh_x \cdot y$$
$$y' = y$$
 
**Sesgado en el eje Y (vertical):**
 
$$x' = x$$
$$y' = y + sh_y \cdot x$$
 
**Sesgado combinado:**
 
$$x' = x + sh_x \cdot y$$
$$y' = sh_y \cdot x + y$$
 
**Ejemplo visual — Sesgado horizontal con $sh_x = 0.5$:**
 
```
Antes:           Después:
┌──┐             ╱──╱
│  │    →       ╱  ╱
└──┘           ╱──╱
```
 
**Propiedades:**
- Conserva las **líneas paralelas** (son paralelas antes y después).
- **No conserva** los ángulos internos.
- El factor $sh_x$ representa la tangente del ángulo de inclinación horizontal.
 
> 💡 **Aplicación:** Efectos de perspectiva falsa (*fake perspective*), fuentes tipográficas en cursiva, sombras proyectadas.
 
---
 
## 2.2 Representación Matricial de las Transformaciones 2D
 
El uso de **matrices** permite unificar todas las transformaciones geométricas en una sola operación algebraica. Esto es especialmente útil para **componer** (encadenar) varias transformaciones de manera eficiente.
 
### Coordenadas Homogéneas
 
Para poder expresar la traslación (que es una suma) en forma matricial (multiplicación), se añade una tercera coordenada ficticia $w = 1$ a cada punto:
 
$$P = (x, y) \longrightarrow \tilde{P} = \begin{pmatrix} x \\ y \\ 1 \end{pmatrix}$$
 
Con esto, **todas las transformaciones** se representan como matrices $3 \times 3$.
 
---
 
### Matrices de Transformación
 
**Traslación:**
 
$$T(t_x, t_y) = \begin{pmatrix} 1 & 0 & t_x \\ 0 & 1 & t_y \\ 0 & 0 & 1 \end{pmatrix}$$
 
**Escalamiento:**
 
$$S(s_x, s_y) = \begin{pmatrix} s_x & 0 & 0 \\ 0 & s_y & 0 \\ 0 & 0 & 1 \end{pmatrix}$$
 
**Rotación:**
 
$$R(\theta) = \begin{pmatrix} \cos\theta & -\sin\theta & 0 \\ \sin\theta & \cos\theta & 0 \\ 0 & 0 & 1 \end{pmatrix}$$
 
**Sesgado:**
 
$$SH(sh_x, sh_y) = \begin{pmatrix} 1 & sh_x & 0 \\ sh_y & 1 & 0 \\ 0 & 0 & 1 \end{pmatrix}$$
 
---
 
### Composición de Transformaciones
 
La gran ventaja de la representación matricial es que aplicar varias transformaciones consecutivas equivale a **multiplicar sus matrices** y luego multiplicar una sola vez el punto:
 
$$P' = M_n \cdot M_{n-1} \cdots M_2 \cdot M_1 \cdot P$$
 
> ⚠️ **Atención:** La multiplicación de matrices **NO es conmutativa**. El orden importa: rotar y luego trasladar ≠ trasladar y luego rotar.
 
**Ejemplo — Rotación alrededor de un punto $(c_x, c_y)$:**
 
$$M = T(c_x, c_y) \cdot R(\theta) \cdot T(-c_x, -c_y)$$
 
$$= \begin{pmatrix} 1 & 0 & c_x \\ 0 & 1 & c_y \\ 0 & 0 & 1 \end{pmatrix} \begin{pmatrix} \cos\theta & -\sin\theta & 0 \\ \sin\theta & \cos\theta & 0 \\ 0 & 0 & 1 \end{pmatrix} \begin{pmatrix} 1 & 0 & -c_x \\ 0 & 1 & -c_y \\ 0 & 0 & 1 \end{pmatrix}$$
 
---
 
### 🎮 Ejercicio: Control con Teclas de Dirección
 
El siguiente ejercicio en **p5.js** muestra cómo aplicar transformaciones matriciales para mover una figura usando las teclas de dirección del teclado.
 
```javascript
// Ejercicio: Transformaciones 2D con teclas de dirección
// Plataforma: p5.js (https://editor.p5js.org/)
 
let x = 200, y = 200;   // Posición del objeto
let angle = 0;           // Ángulo de rotación (en radianes)
let escala = 1.0;        // Factor de escala
 
function setup() {
  createCanvas(400, 400);
  textAlign(CENTER);
}
 
function draw() {
  background(30);
 
  // Leer teclas presionadas
  if (keyIsDown(LEFT_ARROW))  x -= 3;          // Traslación izquierda
  if (keyIsDown(RIGHT_ARROW)) x += 3;          // Traslación derecha
  if (keyIsDown(UP_ARROW))    y -= 3;          // Traslación arriba
  if (keyIsDown(DOWN_ARROW))  y += 3;          // Traslación abajo
  if (keyIsDown(90))          angle -= 0.05;   // Tecla Z: rotar antihorario
  if (keyIsDown(88))          angle += 0.05;   // Tecla X: rotar horario
  if (keyIsDown(65))          escala += 0.02;  // Tecla A: agrandar
  if (keyIsDown(83))          escala -= 0.02;  // Tecla S: reducir
  escala = constrain(escala, 0.1, 5);
 
  // Aplicar transformaciones (orden: trasladar → rotar → escalar)
  push();
    translate(x, y);     // Matriz de traslación
    rotate(angle);       // Matriz de rotación
    scale(escala);       // Matriz de escalamiento
 
    // Dibujar figura
    fill(100, 200, 255);
    noStroke();
    rectMode(CENTER);
    rect(0, 0, 60, 60);
 
    // Indicador de dirección
    stroke(255, 80, 80);
    strokeWeight(3);
    line(0, 0, 40, 0);
    fill(255, 80, 80);
    noStroke();
    triangle(40, 0, 30, -7, 30, 7);
  pop();
 
  // HUD
  fill(255);
  noStroke();
  textSize(12);
  text(`Pos: (${int(x)}, ${int(y)})`, width/2, 20);
  text(`Ángulo: ${nf(degrees(angle), 1, 1)}°`, width/2, 38);
  text(`Escala: ${nf(escala, 1, 2)}x`, width/2, 56);
  textSize(10);
  fill(180);
  text("← → ↑ ↓: Mover | Z/X: Rotar | A/S: Escalar", width/2, height - 10);
}
```
 
**Controles:**
 
| Tecla | Acción                    |
|-------|---------------------------|
| ←→↑↓  | Trasladar el objeto       |
| Z     | Rotar en sentido antihorario |
| X     | Rotar en sentido horario  |
| A     | Aumentar escala           |
| S     | Reducir escala            |
 
> 🔗 Puedes probarlo en [editor.p5js.org](https://editor.p5js.org/) pegando el código anterior.
 
**¿Qué demuestra este ejercicio?**
 
Internamente, p5.js construye una **pila de matrices de transformación**. Cada llamada a `translate()`, `rotate()` o `scale()` multiplica la matriz actual por la correspondiente. Al llamar `push()` y `pop()`, se guarda y restaura el estado de la matriz, lo que permite aplicar transformaciones locales sin afectar el resto de la escena.
 
---
 
## 2.3 Trazo de Líneas Curvas
 
Las curvas son esenciales en graficación para representar contornos suaves, trayectorias y formas orgánicas. En lugar de definir miles de puntos individuales, se utilizan **funciones paramétricas** que generan curvas a partir de unos pocos **puntos de control**.
 
### Curvas Paramétricas
 
Una curva paramétrica en 2D se define como:
 
$$C(t) = \bigl(x(t),\ y(t)\bigr), \quad t \in [0, 1]$$
 
Al variar $t$ de 0 a 1 se recorre la curva completa.
 
---
 
### 2.3.1 Curvas de Bézier
 
Las **curvas de Bézier** fueron desarrolladas independientemente por Paul de Casteljau (Citroën, 1959) y Pierre Bézier (Renault, 1962) para el diseño asistido por computadora de carrocerías de automóviles. Hoy son omnipresentes en tipografía, diseño vectorial (SVG, PostScript, PDF) y animación.
 
#### Fundamento: Interpolación de Bernstein
 
Una curva de Bézier de grado $n$ se define mediante $n+1$ **puntos de control** $P_0, P_1, \ldots, P_n$:
 
$$C(t) = \sum_{i=0}^{n} \binom{n}{i} t^i (1-t)^{n-i} P_i, \quad t \in [0, 1]$$
 
donde los **polinomios de Bernstein** $B_{i,n}(t) = \binom{n}{i} t^i (1-t)^{n-i}$ actúan como pesos que siempre suman 1.
 
#### Curva Lineal (grado 1, 2 puntos de control)
 
$$C(t) = (1-t)P_0 + tP_1$$
 
Es simplemente una interpolación lineal entre $P_0$ y $P_1$.
 
#### Curva Cuadrática (grado 2, 3 puntos de control)
 
$$C(t) = (1-t)^2 P_0 + 2t(1-t)P_1 + t^2 P_2$$
 
```
P1 (punto de control)
 ●
╱ ╲
╱   ╲
P0●───────●P2
   curva
```
 
#### Curva Cúbica (grado 3, 4 puntos de control) — La más utilizada
 
$$C(t) = (1-t)^3 P_0 + 3t(1-t)^2 P_1 + 3t^2(1-t) P_2 + t^3 P_3$$
 
```
    P1●         ●P2
      ╲         ╱
P0●───╲───────╱───●P3
        curva suave
```
 
**Propiedades importantes:**
 
| Propiedad            | Descripción                                                          |
|----------------------|----------------------------------------------------------------------|
| Interpolación extremos | La curva **pasa** por $P_0$ y $P_n$ (primero y último punto)    |
| Aproximación interior | La curva **no pasa** por los puntos intermedios (solo los "atrae")|
| Envoltura convexa    | La curva está contenida en el casco convexo de los puntos de control |
| Variación disminuida | Una línea recta corta la curva a lo sumo tantas veces como corta la poligonal de control |
| Invarianza afín      | Las transformaciones afines se aplican a los puntos de control directamente |
 
#### Algoritmo de De Casteljau
 
El algoritmo de De Casteljau es una forma recursiva y numéricamente estable de evaluar puntos sobre la curva:
 
Para evaluar $C(t)$:
1. Interpolar linealmente entre cada par de puntos consecutivos con parámetro $t$.
2. Repetir con los nuevos puntos hasta obtener un único punto.
 
```
Nivel 0: P0, P1, P2, P3
Nivel 1: Q0=(1-t)P0+tP1, Q1=(1-t)P1+tP2, Q2=(1-t)P2+tP3
Nivel 2: R0=(1-t)Q0+tQ1, R1=(1-t)Q1+tQ2
Nivel 3: S0=(1-t)R0+tR1  ← punto en la curva
```
 
#### Implementación en p5.js
 
```javascript
// Curva de Bézier cúbica en p5.js
function setup() {
  createCanvas(400, 300);
}
 
function draw() {
  background(240);
 
  // Puntos de control
  let P0 = { x: 50,  y: 250 };
  let P1 = { x: 100, y: 50  };
  let P2 = { x: 300, y: 50  };
  let P3 = { x: 350, y: 250 };
 
  // Dibujar poligonal de control
  stroke(180);
  strokeWeight(1);
  noFill();
  beginShape();
    vertex(P0.x, P0.y);
    vertex(P1.x, P1.y);
    vertex(P2.x, P2.y);
    vertex(P3.x, P3.y);
  endShape();
 
  // Dibujar curva de Bézier
  stroke(0, 100, 255);
  strokeWeight(2);
  noFill();
  beginShape();
    // Muestrear 100 puntos con la fórmula cúbica
    for (let t = 0; t <= 1; t += 0.01) {
      let mt = 1 - t;
      let bx = mt*mt*mt*P0.x + 3*t*mt*mt*P1.x + 3*t*t*mt*P2.x + t*t*t*P3.x;
      let by = mt*mt*mt*P0.y + 3*t*mt*mt*P1.y + 3*t*t*mt*P2.y + t*t*t*P3.y;
      vertex(bx, by);
    }
  endShape();
 
  // Dibujar puntos de control
  fill(255, 60, 60);
  noStroke();
  for (let p of [P0, P1, P2, P3]) {
    circle(p.x, p.y, 10);
  }
}
```
 
---
 
### 2.3.2 B-Spline
 
Las **B-Splines** (*Basis Splines*) son curvas que superan una limitación de las curvas de Bézier: cuando se necesitan curvas muy complejas, una sola Bézier de alto grado se vuelve difícil de controlar. Las B-Splines permiten usar **muchos puntos de control** manteniendo el grado de la curva **bajo y constante**.
 
#### Concepto fundamental
 
Una B-Spline de grado $k$ con $n+1$ puntos de control $P_0, \ldots, P_n$ se define como:
 
$$C(t) = \sum_{i=0}^{n} P_i \cdot N_{i,k}(t)$$
 
donde $N_{i,k}(t)$ son las **funciones base de B-Spline**, definidas recursivamente (fórmula de Cox-de Boor):
 
**Base caso (grado 0):**
$$N_{i,0}(t) = \begin{cases} 1 & \text{si } t_i \leq t < t_{i+1} \\ 0 & \text{en otro caso} \end{cases}$$
 
**Caso recursivo:**
$$N_{i,k}(t) = \frac{t - t_i}{t_{i+k} - t_i} N_{i,k-1}(t) + \frac{t_{i+k+1} - t}{t_{i+k+1} - t_{i+1}} N_{i+1,k-1}(t)$$
 
donde $\{t_0, t_1, \ldots, t_{n+k+1}\}$ es el **vector de nodos** (*knot vector*).
 
#### Tipos de vectores de nodos
 
| Tipo         | Descripción                                              | Ejemplo (3 puntos, grado 2) |
|--------------|----------------------------------------------------------|-----------------------------|
| Uniforme     | Nodos igualmente espaciados                              | `[0, 1, 2, 3, 4, 5]`       |
| Abierto      | Repite primer y último nodo $k+1$ veces                  | `[0, 0, 0, 1, 2, 2, 2]`    |
| No uniforme  | Espaciado arbitrario para control local                  | `[0, 0, 1, 3, 4, 4]`       |
 
> El vector de nodos **abierto** hace que la curva **interpole** (pase por) el primer y último punto de control, comportamiento similar a Bézier.
 
#### Diferencias clave: Bézier vs B-Spline
 
| Característica         | Bézier                            | B-Spline                              |
|------------------------|-----------------------------------|---------------------------------------|
| Interpolación extremos | Sí (pasa por P0 y Pn)            | Solo con vector abierto               |
| Control local          | ❌ Un punto afecta toda la curva  | ✅ Un punto afecta solo una sección   |
| Grado                  | Crece con puntos de control       | Se elige independientemente           |
| Continuidad            | $C^{n-1}$ globalmente             | Controlable por repetición de nodos   |
| Facilidad de uso       | Alta                              | Media (requiere manejar knot vector)  |
 
#### Implementación simplificada en p5.js
 
```javascript
// B-Spline cúbica con vector de nodos abierto (usando curveVertex de p5.js)
// p5.js implementa Catmull-Rom, que es una B-Spline uniforme
 
let pts = [];
 
function setup() {
  createCanvas(400, 300);
}
 
function mousePressed() {
  pts.push({ x: mouseX, y: mouseY });
}
 
function draw() {
  background(240);
 
  // Necesitamos al menos 4 puntos para una B-Spline cúbica
  if (pts.length >= 4) {
    stroke(0, 150, 80);
    strokeWeight(2);
    noFill();
    beginShape();
      curveVertex(pts[0].x, pts[0].y);          // Repetir primer punto
      for (let p of pts) curveVertex(p.x, p.y);
      curveVertex(pts[pts.length-1].x, pts[pts.length-1].y); // Repetir último
    endShape();
  }
 
  // Dibujar puntos de control
  fill(255, 100, 0);
  noStroke();
  for (let p of pts) circle(p.x, p.y, 10);
 
  // Instrucciones
  fill(80); noStroke(); textSize(12);
  text("Haz clic para agregar puntos de control", 10, 20);
  text(`Puntos: ${pts.length} (mínimo 4)`, 10, 38);
}
```
 
---
 
### 🎬 Ejercicio: Animación con Curvas
 
El siguiente ejercicio anima un objeto a lo largo de una curva de Bézier cúbica, combinando curvas con transformaciones 2D.
 
```javascript
// Animación: objeto siguiendo una curva de Bézier
let t = 0;
let speed = 0.005;
 
// Puntos de control de la curva
let P0, P1, P2, P3;
 
function setup() {
  createCanvas(500, 400);
  P0 = createVector(50,  350);
  P1 = createVector(150, 50);
  P2 = createVector(350, 50);
  P3 = createVector(450, 350);
}
 
function draw() {
  background(20, 20, 40);
 
  // Dibujar curva de referencia
  stroke(80, 80, 120);
  strokeWeight(1);
  noFill();
  beginShape();
  for (let s = 0; s <= 1; s += 0.01) {
    let pos = bezierPoint3D(s);
    vertex(pos.x, pos.y);
  }
  endShape();
 
  // Obtener posición y tangente en t
  let pos = bezierPoint3D(t);
  let pos2 = bezierPoint3D(t + 0.01);
  let angle = atan2(pos2.y - pos.y, pos2.x - pos.x);
 
  // Dibujar objeto animado (cohete/flecha orientada)
  push();
    translate(pos.x, pos.y);
    rotate(angle);
 
    // Cuerpo
    fill(100, 200, 255);
    noStroke();
    ellipse(0, 0, 30, 15);
 
    // Punta
    fill(255, 100, 100);
    triangle(15, 0, 5, -8, 5, 8);
 
    // Flama
    fill(255, 200, 0, 180);
    triangle(-15, 0, -30, -8, -30, 8);
  pop();
 
  // Punto brillante en la posición actual
  fill(255, 255, 100);
  noStroke();
  circle(pos.x, pos.y, 8);
 
  // Avanzar t
  t += speed;
  if (t > 1) t = 0;
 
  // Puntos de control
  fill(255, 80, 80); noStroke();
  for (let p of [P0, P1, P2, P3]) circle(p.x, p.y, 8);
 
  // HUD
  fill(200); noStroke(); textSize(11);
  text(`t = ${nf(t, 1, 3)}`, 10, 20);
}
 
// Función auxiliar: punto en la curva de Bézier cúbica
function bezierPoint3D(t) {
  let mt = 1 - t;
  return createVector(
    mt*mt*mt*P0.x + 3*t*mt*mt*P1.x + 3*t*t*mt*P2.x + t*t*t*P3.x,
    mt*mt*mt*P0.y + 3*t*mt*mt*P1.y + 3*t*t*mt*P2.y + t*t*t*P3.y
  );
}
```
 
**¿Qué demuestra este ejercicio?**
- Evaluación paramétrica de la curva de Bézier con $t \in [0,1]$.
- Cálculo de la **tangente** (derivada aproximada) para orientar el objeto.
- Combinación de `translate()` y `rotate()` para posicionar y orientar el sprite.
 
---
 
## 2.4 Fractales
 
Los **fractales** son estructuras geométricas que exhiben **autosimilitud**: se ven iguales (o similares) a cualquier escala de observación. El término fue acuñado por Benoît Mandelbrot (1975), derivado del latín *fractus* (quebrado, irregular).
 
### Conceptos Fundamentales
 
**Dimensión fractal:**
A diferencia de los objetos euclidianos (una línea tiene dimensión 1, un plano tiene dimensión 2), los fractales pueden tener **dimensiones no enteras**. La dimensión de Hausdorff-Besicovitch mide la "rugosidad":
 
$$D = \frac{\log N}{\log (1/r)}$$
 
donde $N$ es el número de copias auto-similares y $r$ es el factor de reducción.
 
**Ejemplo — Triángulo de Sierpiński:**
- Se divide el triángulo en 3 copias a escala $1/2$: $N = 3$, $r = 1/2$
- $D = \log(3)/\log(2) \approx 1.585$
 
### Tipos de Fractales
 
#### 1. Conjunto de Mandelbrot
 
El conjunto de Mandelbrot es el conjunto de números complejos $c$ para los cuales la iteración:
 
$$z_{n+1} = z_n^2 + c, \quad z_0 = 0$$
 
**no diverge** (permanece acotada). Su frontera es infinitamente compleja y constituye uno de los objetos matemáticos más famosos.
 
```javascript
// Conjunto de Mandelbrot en p5.js
function setup() {
  createCanvas(400, 400);
  pixelDensity(1);
  noLoop();
}
 
function draw() {
  loadPixels();
  let maxIter = 100;
  let xMin = -2.5, xMax = 1.0;
  let yMin = -1.25, yMax = 1.25;
 
  for (let px = 0; px < width; px++) {
    for (let py = 0; py < height; py++) {
      // Mapear píxel a coordenada compleja
      let cr = map(px, 0, width, xMin, xMax);
      let ci = map(py, 0, height, yMin, yMax);
 
      let zr = 0, zi = 0;
      let iter = 0;
 
      // Iterar z = z² + c
      while (zr*zr + zi*zi < 4 && iter < maxIter) {
        let temp = zr*zr - zi*zi + cr;
        zi = 2*zr*zi + ci;
        zr = temp;
        iter++;
      }
 
      // Colorear según velocidad de escape
      let idx = 4 * (py * width + px);
      if (iter === maxIter) {
        // Dentro del conjunto → negro
        pixels[idx] = pixels[idx+1] = pixels[idx+2] = 0;
      } else {
        // Fuera → gradiente suave
        let ratio = iter / maxIter;
        pixels[idx]   = int(9   * (1-ratio) * ratio*ratio*ratio * 255); // R
        pixels[idx+1] = int(15  * (1-ratio)*(1-ratio) * ratio*ratio * 255); // G
        pixels[idx+2] = int(8.5 * (1-ratio)*(1-ratio)*(1-ratio) * ratio * 255); // B
      }
      pixels[idx+3] = 255; // Alpha
    }
  }
  updatePixels();
}
```
 
#### 2. Conjunto de Julia
 
Relacionado con Mandelbrot: se fija $c$ como constante y se varía $z_0$. Cada valor de $c$ produce un conjunto de Julia diferente.
 
$$z_{n+1} = z_n^2 + c, \quad c = \text{constante fija}, \quad z_0 = \text{variable}$$
 
#### 3. Curva de Koch (Copo de Nieve)
 
Se construye iterativamente dividiendo cada segmento en tercios y levantando un triángulo equilátero en el segmento central:
 
- Dimensión fractal: $D = \log(4)/\log(3) \approx 1.261$
- Perímetro: **infinito** (crece por factor $4/3$ en cada iteración)
- Área: **finita** (converge)
 
```javascript
// Curva de Koch recursiva
function kochCurve(x1, y1, x2, y2, depth) {
  if (depth === 0) {
    line(x1, y1, x2, y2);
    return;
  }
  let dx = x2 - x1, dy = y2 - y1;
 
  // Dividir en tercios
  let ax = x1 + dx/3,        ay = y1 + dy/3;
  let bx = x1 + 2*dx/3,      by = y1 + 2*dy/3;
 
  // Punto del pico del triángulo (rotación 60°)
  let angle = atan2(dy, dx) - PI/3;
  let len = dist(x1, y1, x2, y2) / 3;
  let cx = ax + cos(angle)*len;
  let cy = ay + sin(angle)*len;
 
  kochCurve(x1, y1, ax, ay, depth - 1);
  kochCurve(ax, ay, cx, cy, depth - 1);
  kochCurve(cx, cy, bx, by, depth - 1);
  kochCurve(bx, by, x2, y2, depth - 1);
}
 
function setup() { createCanvas(400, 400); }
function draw() {
  background(255);
  stroke(0, 80, 200); strokeWeight(1);
  kochCurve(50, 300, 350, 300, 5);
}
```
 
#### 4. Triángulo de Sierpiński
 
Se construye eliminando recursivamente el triángulo central de cada triángulo:
 
```javascript
function sierpinski(x1, y1, x2, y2, x3, y3, depth) {
  if (depth === 0) {
    fill(100, 180, 255); noStroke();
    triangle(x1, y1, x2, y2, x3, y3);
    return;
  }
  let mx12 = (x1+x2)/2, my12 = (y1+y2)/2;
  let mx23 = (x2+x3)/2, my23 = (y2+y3)/2;
  let mx31 = (x3+x1)/2, my31 = (y3+y1)/2;
 
  sierpinski(x1, y1, mx12, my12, mx31, my31, depth-1);
  sierpinski(mx12, my12, x2, y2, mx23, my23, depth-1);
  sierpinski(mx31, my31, mx23, my23, x3, y3, depth-1);
}
```
 
#### 5. Sistema de Funciones Iteradas (IFS) — Helecho de Barnsley
 
El helecho de Barnsley es un fractal generado por 4 transformaciones afines aplicadas aleatoriamente con distintas probabilidades:
 
| Transformación | $a$   | $b$   | $c$   | $d$   | $e$ | $f$   | Prob. |
|----------------|-------|-------|-------|-------|-----|-------|-------|
| $f_1$ (tallo)  | 0     | 0     | 0     | 0.16  | 0   | 0     | 1%    |
| $f_2$ (hoja)   | 0.85  | 0.04  | -0.04 | 0.85  | 0   | 1.60  | 85%   |
| $f_3$ (izq.)   | 0.20  | -0.26 | 0.23  | 0.22  | 0   | 1.60  | 7%    |
| $f_4$ (der.)   | -0.15 | 0.28  | 0.26  | 0.24  | 0   | 0.44  | 7%    |
 
### Aplicaciones de los Fractales
 
- **Generación de terrenos** y paisajes naturales (nubes, montañas, costas).
- **Compresión de imágenes** (compresión fractal).
- **Antenas fractales**: tamaño reducido con alta eficiencia multifrequencia.
- **Medicina**: análisis de patrones en electrocardiogramas, estructuras pulmonares.
- **Arte generativo** y efectos visuales en cine.
 
---
 
## 2.5 Uso y Creación de Fuentes de Texto
 
La **tipografía computacional** es la disciplina que estudia la representación, almacenamiento y renderizado de caracteres en sistemas digitales. Los gráficos de texto son en sí mismos una forma de graficación 2D.
 
### Tipos de Representación de Fuentes
 
#### 1. Fuentes de Mapa de Bits (Raster/Bitmap Fonts)
 
Los caracteres se almacenan como **matrices de píxeles** (imágenes).
 
```
Letra 'A' en 5×7 píxeles:
. █ █ .
█ . . █
█ █ █ █
█ . . █
█ . . █
```
 
**Ventajas:**
- Renderizado rápido (los píxeles están precalculados).
- Perfectas para tamaños fijos y pantallas de baja resolución.
 
**Desventajas:**
- **No escalables**: al aumentar el tamaño, se pixelan.
- Requieren una imagen por cada tamaño y estilo.
 
**Ejemplos:** Fuentes de terminales clásicos (como las fuentes `.FON` de Windows), fuentes de sistemas embebidos.
 
#### 2. Fuentes Vectoriales (Outline Fonts)
 
Los caracteres se definen mediante **curvas matemáticas** (principalmente Bézier cúbicas o cuadráticas) almacenadas como vectores. Al renderizar, se rasteriza al tamaño deseado.
 
**Tecnologías principales:**
 
| Formato | Desarrollador | Curvas usadas  | Extensión  |
|---------|---------------|----------------|------------|
| Type 1  | Adobe (1984)  | Bézier cúbica  | `.pfb`     |
| TrueType| Apple/MS (1991)| Bézier cuadrática | `.ttf` |
| OpenType| Adobe/MS (1997)| Cúbica o cuadrática | `.otf` |
| WOFF/WOFF2 | W3C (2010) | Empaquetado web | `.woff` |
 
**Ventajas:**
- **Escalables** a cualquier tamaño sin pérdida de calidad.
- Un solo archivo cubre todos los tamaños.
- Soportan **hinting** para mejorar la legibilidad en pantallas.
 
**Anatomía de un glifo vectorial (letra 'O'):**
 
```
     ●──────────●          ← puntos de control Bézier
    ╱              ╲
   ●                ●
   │                │
   ●                ●
    ╲              ╱
     ●──────────●
```
 
Cada carácter es un contorno cerrado de curvas Bézier. El interior se rellena mediante reglas como *even-odd* o *non-zero winding*.
 
#### 3. Fuentes de Color (Color Fonts)
 
Formatos modernos que permiten glifos multicolor, gradientes y transparencia:
 
- **COLR/CPAL** (OpenType 1.8, 2016): capas vectoriales con paletas de color.
- **CBDT/CBLC**: mapas de bits a color integrados en OpenType.
- **SVG** en OpenType: glifos descritos directamente con SVG.
- **sbix** (Apple): mapas de bits de alta resolución.
 
#### 4. Fuentes Variables (Variable Fonts)
 
OpenType 1.8 introduce las **fuentes variables**: un único archivo que contiene toda la familia, interpolando entre **ejes de variación**:
 
| Eje   | Etiqueta | Rango típico | Efecto        |
|-------|----------|--------------|---------------|
| Weight | `wght` | 100–900      | Grosor del trazo |
| Width  | `wdth` | 75%–125%     | Expansión horizontal |
| Slant  | `slnt` | -15°–15°     | Inclinación   |
| Optical Size | `opsz` | 6–144pt | Ajuste óptico |
 
### Creación de Fuentes con p5.js / opentype.js
 
```javascript
// Cargar y usar una fuente TrueType en p5.js
let myFont;
 
function preload() {
  // Cargar fuente desde archivo local o URL
  myFont = loadFont('assets/OpenSans-Regular.ttf');
}
 
function setup() {
  createCanvas(400, 200);
}
 
function draw() {
  background(240);
 
  // Usar la fuente cargada
  textFont(myFont);
  textSize(48);
  textAlign(CENTER, CENTER);
  fill(30, 80, 200);
  text("Graficación 2D", width/2, height/2 - 30);
 
  // Obtener contornos del texto como puntos (para manipulación)
  let bounds = myFont.textBounds("Hola", 50, 100, 40);
  let pts = myFont.textBounds("Hola", 50, 100, 40);
 
  // Texto con transformaciones
  push();
    translate(width/2, height/2 + 30);
    rotate(sin(frameCount * 0.05) * 0.1);
    scale(1 + sin(frameCount * 0.07) * 0.05);
    textSize(24);
    fill(200, 60, 60);
    text("Texto animado", 0, 0);
  pop();
}
```
 
### Algoritmos de Renderizado de Texto
 
#### Rasterización (Scanline Fill)
 
Para renderizar un glifo vectorial:
 
1. **Descomposición**: separar el contorno en segmentos y curvas.
2. **Scanline**: para cada fila de píxeles, calcular intersecciones con el contorno.
3. **Rellenado**: colorear los píxeles según la regla de llenado (even-odd o non-zero winding).
4. **Anti-aliasing** (suavizado): usar sub-muestreo o cobertura de área para píxeles en el borde.
 
#### Hinting
 
El *hinting* son instrucciones especiales dentro de la fuente que ajustan los contornos del glifo a la cuadrícula de píxeles en tamaños pequeños, mejorando la legibilidad:
 
- **Instrucciones TrueType**: lenguaje de bajo nivel, muy preciso.
- **PostScript hints**: más simples, dependen más del renderizador.
- **Auto-hinting**: FreeType calcula hints automáticamente.
 
#### Sub-pixel Rendering (ClearType)
 
Aprovecha la disposición física RGB de los sub-píxeles en monitores LCD para triplicar efectivamente la resolución horizontal:
 
```
Píxel normal:    ████
Sub-píxel RGB:   R G B | R G B | R G B
                 ←──────────────────→
                  Resolución triplicada horizontal
```
 
### Fuentes en la Web
 
```css
/* Definir fuente personalizada */
@font-face {
  font-family: 'MiFuente';
  src: url('fonts/mifuente.woff2') format('woff2'),
       url('fonts/mifuente.woff')  format('woff');
  font-weight: 100 900;   /* Variable font */
  font-style: normal;
}
 
/* Fuente variable con eje de grosor */
h1 {
  font-family: 'MiFuente', sans-serif;
  font-variation-settings: 'wght' 750, 'wdth' 110;
}
```
 
### Métricas Tipográficas
 
Entender las métricas es esencial para el posicionamiento preciso del texto:
 
```
                         ┌── Ascender
    ▲                    │
    │    ████████        │ ← Capline (altura de mayúsculas)
    │    ██  ████        │
    │    ████████        ← x-height (altura de minúscula x)
    │    ██              │
    │    ████████        ▼
────┤────────────────── Baseline ──
    │    ██              ▲
    ▼    ██              └── Descender
```
 
| Métrica    | Descripción                                              |
|------------|----------------------------------------------------------|
| Baseline   | Línea base sobre la que "descansan" los caracteres       |
| Ascender   | Cuánto suben los caracteres sobre la baseline            |
| Descender  | Cuánto bajan caracteres como 'p', 'q', 'y'              |
| x-height   | Altura de las minúsculas (medida con la 'x')            |
| Cap-height | Altura de las mayúsculas                                 |
| Advance    | Espacio horizontal que ocupa un glifo (incluyendo kern)  |
| Kerning    | Ajuste de espacio entre pares específicos de letras      |
 
---
 
## Referencias
 
Aceved, D. (2023). *Fundamentos de computación gráfica: Curvas y superficies*. Universidad Nacional Autónoma de México.
 
Barnsley, M. F. (1988). *Fractals everywhere*. Academic Press.
 
Bézier, P. (1972). *Numerical control: Mathematics and applications* (A. R. Forrest & A. F. Pankhurst, Trads.). John Wiley & Sons. (Obra original publicada en 1970)
 
Foley, J. D., van Dam, A., Feiner, S. K., & Hughes, J. F. (1995). *Computer graphics: Principles and practice* (2.ª ed.). Addison-Wesley.
 
Hill, F. S., & Kelley, S. M. (2006). *Computer graphics using OpenGL* (3.ª ed.). Pearson Prentice Hall.
 
Mandelbrot, B. B. (1982). *The fractal geometry of nature*. W. H. Freeman and Company.
 
OpenType specification. (2023). *OpenType 1.9 specification*. Microsoft Corporation. https://docs.microsoft.com/en-us/typography/opentype/spec/
 
Piegl, L., & Tiller, W. (1997). *The NURBS book* (2.ª ed.). Springer-Verlag. https://doi.org/10.1007/978-3-642-59223-2
 
Shirley, P., & Marschner, S. (2009). *Fundamentals of computer graphics* (3.ª ed.). A K Peters/CRC Press.
 
Shoemake, K. (1985). Animating rotation with quaternion curves. *ACM SIGGRAPH Computer Graphics, 19*(3), 245–254. https://doi.org/10.1145/325165.325242
 
Shreiner, D., Sellers, G., Kessenich, J., & Licea-Kane, B. (2013). *OpenGL programming guide: The official guide to learning OpenGL* (8.ª ed.). Addison-Wesley.
 
The Processing Foundation. (2023). *p5.js reference*. https://p5js.org/reference/
 
---
 
<div align="center">
 
**Unidad 2 — Graficación 2D** | Materia: Graficación por Computadora
 
*Elaborado con fines académicos*
 
</div>
