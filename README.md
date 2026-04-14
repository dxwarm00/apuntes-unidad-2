# 🖥️ Graficación por Computadora

> Este repositorio integra teoría matemática, programación y aplicación práctica en Blender, permitiendo comprender la graficación 2D desde un enfoque visual y computacional.

---

## 📑 Índice General

- [Unidad 1 — Introducción a la Graficación](#) *(ver entrega anterior)*
- [**Unidad 2 — Graficación 2D**](#unidad-2-graficación-2d)
  - [2.1 Transformaciones Bidimensionales](#21-transformaciones-bidimensionales)
    - [2.1.1 Traslación](#211-traslación)
    - [2.1.2 Escalamiento](#212-escalamiento)
    - [2.1.3 Rotación](#213-rotación)
    - [2.1.4 Sesgado](#214-sesgado-shearing)
  - [2.2 Representación Matricial de las Transformaciones 2D](#22-representación-matricial-de-las-transformaciones-2d)
    - [Ejercicio: Control con Teclas de Dirección en Blender](#-ejercicio-control-con-teclas-de-dirección-en-blender)
  - [2.3 Trazo de Líneas Curvas](#23-trazo-de-líneas-curvas)
    - [2.3.1 Curvas de Bézier](#231-curvas-de-bézier)
    - [2.3.2 B-Spline (NURBS en Blender)](#232-b-spline-nurbs-en-blender)
    - [Ejercicio: Animación con Curvas en Blender](#-ejercicio-animación-con-curvas-en-blender)
  - [2.4 Fractales](#24-fractales)
  - [2.5 Uso y Creación de Fuentes de Texto](#25-uso-y-creación-de-fuentes-de-texto)
- [Referencias](#referencias)

---

# Unidad 2: Graficación 2D

La graficación 2D comprende el conjunto de técnicas matemáticas que permiten representar, transformar y manipular figuras en un plano bidimensional. En Blender, aunque es un software primariamente 3D, existe un entorno dedicado al trabajo 2D llamado **Grease Pencil**, y todas las transformaciones 2D son la base de cualquier operación sobre objetos. Esta unidad estudia dichas transformaciones, las curvas, los fractales y la tipografía, siempre desde la perspectiva de Blender.

---

## 2.1 Transformaciones Bidimensionales

En Blender, las transformaciones se aplican sobre objetos en el **espacio 3D**, pero cuando se trabaja en modo **Grease Pencil** o se restringe el movimiento a los ejes X e Y (`G → X` o `G → Y`), se obtiene un comportamiento puramente 2D.

Toda transformación parte de las propiedades del objeto visibles en el panel lateral `N`:

```
Transform
  Location:  X  Y  Z
  Rotation:  X  Y  Z
  Scale:     X  Y  Z
```

Las transformaciones elementales en 2D son:

| Transformación | Atajo en Blender | Panel N          |
|----------------|-----------------|------------------|
| Traslación     | `G`             | Location X, Y    |
| Escalamiento   | `S`             | Scale X, Y       |
| Rotación       | `R`             | Rotation Z       |
| Sesgado        | `Ctrl + Alt + S`| (solo en Edit Mode)|

---

### 2.1.1 Traslación

La **traslación** desplaza un objeto sumando un vector de desplazamiento a su posición actual.

**Definición matemática:**

Dado un punto $P = (x, y)$ y un vector de traslación $(t_x, t_y)$:

$$x' = x + t_x \qquad y' = y + t_y$$

**En Blender — Interfaz gráfica:**

1. Seleccionar el objeto.
2. Presionar `G` para activar grab (traslación).
3. Presionar `X` o `Y` para restringir al eje deseado.
4. Escribir el valor numérico (ej. `G → Y → 3 → Enter` mueve 3 unidades en Y).
5. Confirmar con `Enter` o cancelar con `Esc`.

**En Blender — Python scripting:**

```python
import bpy

# Seleccionar el objeto activo
obj = bpy.context.active_object

# Trasladar: mover 3 unidades en X y -2 en Y
obj.location.x += 3
obj.location.y -= 2

# También se puede asignar directamente
obj.location = (5.0, 2.0, 0.0)  # X, Y, Z
```

> 💡 En Blender la coordenada Z siempre existe. Para trabajo 2D puro se fija en `Z = 0`.

---

### 2.1.2 Escalamiento

El **escalamiento** modifica el tamaño de un objeto multiplicando sus dimensiones por un factor.

**Definición matemática:**

Dado un punto $P = (x, y)$ y factores $s_x$, $s_y$:

$$x' = x \cdot s_x \qquad y' = y \cdot s_y$$

**Casos importantes:**

| Condición            | Efecto                          |
|----------------------|---------------------------------|
| $s_x = s_y > 1$      | Ampliación uniforme             |
| $0 < s_x = s_y < 1$  | Reducción uniforme              |
| $s_x \neq s_y$       | Deformación (estiramiento)      |
| $s_x$ o $s_y < 0$    | Reflexión (espejo)              |

**En Blender — Interfaz gráfica:**

- `S` → Escalamiento uniforme
- `S → X → 2 → Enter` → Duplicar tamaño solo en X
- `S → Y → 0.5 → Enter` → Reducir a la mitad en Y
- Para espejo: `S → X → -1 → Enter`

**En Blender — Python scripting:**

```python
import bpy

obj = bpy.context.active_object

# Escalar uniformemente al doble
obj.scale = (2.0, 2.0, 1.0)

# Escalar solo en X (deformación)
obj.scale.x = 3.0

# Aplicar la escala (bake transform)
bpy.ops.object.transform_apply(scale=True)
```

> ⚠️ En Blender es importante usar **Apply Scale** (`Ctrl + A → Scale`) después de escalar, para que los valores internos de la malla reflejen la transformación real.

---

### 2.1.3 Rotación

La **rotación** gira un objeto un ángulo $\theta$ alrededor de un punto de pivote.

**Definición matemática (respecto al origen):**

$$x' = x\cos\theta - y\sin\theta$$
$$y' = x\sin\theta + y\cos\theta$$

**En Blender — Interfaz gráfica:**

- `R` → Rotación libre
- `R → Z → 45 → Enter` → Rotar 45° alrededor del eje Z (equivale a rotación 2D)
- El **punto de pivote** se configura en la barra superior:
  - `Individual Origins` — cada objeto rota alrededor de su propio centro
  - `3D Cursor` — rota alrededor del cursor 3D
  - `Median Point` — rota alrededor del centro del grupo seleccionado

**En Blender — Python scripting:**

```python
import bpy
import math

obj = bpy.context.active_object

# Rotar 45 grados en Z (rotación 2D equivalente)
# Blender usa radianes internamente
obj.rotation_euler.z = math.radians(45)

# Rotar de forma incremental
obj.rotation_euler.z += math.radians(30)
```

> 💡 Blender almacena rotaciones en **radianes** internamente, aunque la interfaz muestra grados. Al usar Python siempre se utiliza `math.radians()` para convertir.

---

### 2.1.4 Sesgado (Shearing)

El **sesgado** inclina la figura a lo largo de un eje, deformando sus ángulos internos sin cambiar el área.

**Definición matemática:**

Sesgado en X:
$$x' = x + sh_x \cdot y \qquad y' = y$$

Sesgado en Y:
$$x' = x \qquad y' = y + sh_y \cdot x$$

**En Blender — Interfaz gráfica:**

El sesgado en Blender se aplica en **Edit Mode**:

1. Entrar a Edit Mode (`Tab`).
2. Seleccionar todos los vértices (`A`).
3. Presionar `Ctrl + Alt + S` para activar Shear.
4. Mover el mouse horizontalmente para sesgar en X, o verticalmente en Y.
5. Se puede restringir al eje: `Ctrl + Alt + S → X` o `Ctrl + Alt + S → Y`.

**En Blender — Python scripting:**

```python
import bpy
import bmesh

obj = bpy.context.active_object
bpy.ops.object.mode_set(mode='EDIT')

bm = bmesh.from_edit_mesh(obj.data)

shear_factor = 0.5  # Factor de sesgado en X

for vert in bm.verts:
    # Sesgar: x' = x + sh * y
    vert.co.x += shear_factor * vert.co.y

bmesh.update_edit_mesh(obj.data)
bpy.ops.object.mode_set(mode='OBJECT')
```

> 💡 **Aplicación práctica en Blender:** El sesgado se usa para simular perspectivas, crear efectos de inclinación en textos, y deformar mallas para animaciones.

---

## 2.2 Representación Matricial de las Transformaciones 2D

En Blender, **todas las transformaciones** se representan internamente como matrices. La librería `mathutils` de Blender provee clases nativas para trabajar con matrices, vectores y cuaterniones.

### Coordenadas Homogéneas

Para expresar traslación como multiplicación matricial se añade una coordenada ficticia $w = 1$:

$$P = (x, y) \longrightarrow \tilde{P} = \begin{pmatrix} x \\ y \\ 1 \end{pmatrix}$$

### Matrices de Transformación 2D (3×3)

**Traslación:**
$$T(t_x, t_y) = \begin{pmatrix} 1 & 0 & t_x \\ 0 & 1 & t_y \\ 0 & 0 & 1 \end{pmatrix}$$

**Escalamiento:**
$$S(s_x, s_y) = \begin{pmatrix} s_x & 0 & 0 \\ 0 & s_y & 0 \\ 0 & 0 & 1 \end{pmatrix}$$

**Rotación:**
$$R(\theta) = \begin{pmatrix} \cos\theta & -\sin\theta & 0 \\ \sin\theta & \cos\theta & 0 \\ 0 & 0 & 1 \end{pmatrix}$$

**Sesgado:**
$$SH(sh_x, sh_y) = \begin{pmatrix} 1 & sh_x & 0 \\ sh_y & 1 & 0 \\ 0 & 0 & 1 \end{pmatrix}$$

### Matrices en Blender con `mathutils`

En Blender, los objetos tienen una **matriz de transformación** `matrix_world` que acumula todas sus transformaciones:

```python
import bpy
import mathutils
import math

obj = bpy.context.active_object

# Ver la matriz world del objeto (4x4 en 3D)
print(obj.matrix_world)

# Crear matrices 2D con mathutils (usando 4x4 para compatibilidad con Blender)
# Traslación
T = mathutils.Matrix.Translation((3.0, 2.0, 0.0))

# Rotación en Z (equivale a rotación 2D)
R = mathutils.Matrix.Rotation(math.radians(45), 4, 'Z')

# Escala
S = mathutils.Matrix.Scale(2.0, 4)   # Escala uniforme
# O por ejes:
Sx = mathutils.Matrix.Scale(2.0, 4, (1, 0, 0))  # Solo eje X
Sy = mathutils.Matrix.Scale(0.5, 4, (0, 1, 0))  # Solo eje Y

# Composición de transformaciones (orden importa)
# Primero escalar, luego rotar, luego trasladar
M = T @ R @ S

# Aplicar la matriz compuesta al objeto
obj.matrix_world = M
```

### Composición de Transformaciones

Combinar transformaciones se logra **multiplicando sus matrices** con el operador `@` en Python:

```python
import bpy, mathutils, math

obj = bpy.context.active_object

# Ejemplo: rotar el objeto alrededor de un punto arbitrario (px, py)
px, py = 2.0, 1.0

# 1. Trasladar el punto pivot al origen
T1 = mathutils.Matrix.Translation((-px, -py, 0))

# 2. Rotar
R = mathutils.Matrix.Rotation(math.radians(60), 4, 'Z')

# 3. Trasladar de regreso
T2 = mathutils.Matrix.Translation((px, py, 0))

# Composición: aplicar de derecha a izquierda
M = T2 @ R @ T1

obj.matrix_world = M @ obj.matrix_world
```

> ⚠️ En Python/Blender el operador `@` es la multiplicación de matrices. El **orden importa**: `T @ R ≠ R @ T`.

---

### 🎮 Ejercicio: Control con Teclas de Dirección en Blender

Este script de Blender mueve, rota y escala un objeto usando el **Modal Operator** con teclas de teclado, demostrando el uso de transformaciones matriciales en tiempo real.

**Cómo usarlo:**
1. Abrir Blender → Workspace **Scripting**.
2. Crear un nuevo script, pegar el código.
3. Ejecutar con el botón ▶ Run Script.
4. Regresar al viewport 3D y presionar `F3`, buscar **"Transform Modal Demo"** y ejecutarlo.
5. Usar las teclas indicadas mientras el objeto está seleccionado.

```python
import bpy
import mathutils
import math

# ─────────────────────────────────────────────
#  Operador Modal: Transformaciones con Teclado
# ─────────────────────────────────────────────
class OBJECT_OT_transform_modal(bpy.types.Operator):
    bl_idname = "object.transform_modal_demo"
    bl_label  = "Transform Modal Demo"
    bl_description = "Mover, rotar y escalar con teclas de dirección"

    _timer = None
    STEP_MOVE  = 0.1
    STEP_ROT   = math.radians(5)
    STEP_SCALE = 0.05

    def modal(self, context, event):
        obj = context.active_object
        if not obj:
            return {'CANCELLED'}

        # ── Traslación ──────────────────────────
        if event.type == 'LEFT_ARROW'  and event.value == 'PRESS':
            obj.location.x -= self.STEP_MOVE
        if event.type == 'RIGHT_ARROW' and event.value == 'PRESS':
            obj.location.x += self.STEP_MOVE
        if event.type == 'UP_ARROW'    and event.value == 'PRESS':
            obj.location.y += self.STEP_MOVE
        if event.type == 'DOWN_ARROW'  and event.value == 'PRESS':
            obj.location.y -= self.STEP_MOVE

        # ── Rotación (Z = rotación 2D) ──────────
        if event.type == 'Q' and event.value == 'PRESS':
            obj.rotation_euler.z += self.STEP_ROT   # Antihorario
        if event.type == 'E' and event.value == 'PRESS':
            obj.rotation_euler.z -= self.STEP_ROT   # Horario

        # ── Escalamiento ────────────────────────
        if event.type == 'PLUS' and event.value == 'PRESS':
            obj.scale *= 1 + self.STEP_SCALE
        if event.type == 'MINUS' and event.value == 'PRESS':
            factor = 1 - self.STEP_SCALE
            obj.scale.x *= factor
            obj.scale.y *= factor

        # ── Salir con ESC o Enter ───────────────
        if event.type in {'ESC', 'RET'}:
            self.report({'INFO'}, "Transformación finalizada")
            return {'FINISHED'}

        return {'RUNNING_MODAL'}

    def invoke(self, context, event):
        if context.active_object:
            context.window_manager.modal_handler_add(self)
            self.report({'INFO'}, "←→↑↓: Mover | Q/E: Rotar | +/-: Escalar | ESC: Salir")
            return {'RUNNING_MODAL'}
        return {'CANCELLED'}


def menu_func(self, context):
    self.layout.operator(OBJECT_OT_transform_modal.bl_idname)


def register():
    bpy.utils.register_class(OBJECT_OT_transform_modal)
    bpy.types.VIEW3D_MT_object.append(menu_func)

def unregister():
    bpy.utils.unregister_class(OBJECT_OT_transform_modal)
    bpy.types.VIEW3D_MT_object.remove(menu_func)

register()
```

**Controles del ejercicio:**

| Tecla     | Acción                      |
|-----------|-----------------------------|
| ← → ↑ ↓  | Trasladar el objeto         |
| Q         | Rotar en sentido antihorario|
| E         | Rotar en sentido horario    |
| + (Numpad)| Aumentar escala             |
| - (Numpad)| Reducir escala              |
| ESC / Enter| Finalizar                  |

---

## 2.3 Trazo de Líneas Curvas

Las curvas son fundamentales para representar formas suaves y orgánicas. Blender tiene soporte nativo muy completo para curvas, con dos grandes tipos: **Bézier** y **NURBS** (que son la generalización de las B-Spline).

Para agregar una curva en Blender:
`Add → Curve → Bézier` o `Add → Curve → NURBS Curve`

---

### 2.3.1 Curvas de Bézier

Las curvas de Bézier, desarrolladas por Pierre Bézier (1962) para diseño de carrocerías, son el tipo de curva más usado en diseño vectorial, tipografía y animación. Blender las implementa nativamente.

#### Fundamento Matemático

Una curva de Bézier cúbica (la más utilizada) está definida por 4 puntos de control: el punto inicial $P_0$, dos **puntos de control** $P_1$ y $P_2$, y el punto final $P_3$:

$$C(t) = (1-t)^3 P_0 + 3t(1-t)^2 P_1 + 3t^2(1-t) P_2 + t^3 P_3, \quad t \in [0,1]$$

Al variar $t$ de 0 a 1 se recorre la curva completa.

#### Anatomía de una Curva Bézier en Blender

En Blender, cada **punto de control** de una curva Bézier tiene:

- **Handle Left** y **Handle Right**: los puntos $P_1$ y $P_2$ de la fórmula, que determinan la dirección y suavidad de la curva.
- **Tipos de handle:**

| Tipo      | Atajo | Comportamiento                                     |
|-----------|-------|----------------------------------------------------|
| Auto      | `V → A` | Blender calcula los handles automáticamente     |
| Vector    | `V → V` | Handles rectos → esquinas afiladas              |
| Aligned   | `V → L` | Handles alineados → curva suave (C1 continua)   |
| Free      | `V → F` | Handles independientes → control total          |

#### Trabajar con Curvas Bézier en Blender

**Crear y editar (GUI):**
1. `Add → Curve → Bézier` en el viewport.
2. Entrar a **Edit Mode** (`Tab`).
3. Seleccionar puntos de control y moverlos con `G`.
4. Extrudir nuevos puntos con `E`.
5. Cambiar tipo de handle con `V`.
6. Cerrar la curva: `Alt + C`.

**Propiedades importantes (panel de propiedades, pestaña 🟢 Curve):**
- **Resolution Preview U**: controla cuántos segmentos se usan al renderizar (más = más suave).
- **Extrude**: da profundidad a la curva (convierte en forma 3D).
- **Bevel Depth**: redondea los bordes.

#### Curvas Bézier con Python en Blender

```python
import bpy

# ── Crear una curva Bézier con Python ──────────────────────────
# Limpiar escena
bpy.ops.object.select_all(action='SELECT')
bpy.ops.object.delete()

# Crear objeto de curva
curva_data = bpy.data.curves.new(name="MiCurvaBezier", type='CURVE')
curva_data.dimensions = '2D'           # Modo 2D
curva_data.resolution_u = 32           # Suavidad de la curva

# Crear un spline tipo Bézier
spline = curva_data.splines.new(type='BEZIER')
spline.bezier_points.add(3)            # Total 4 puntos (1 inicial + 3 nuevos)

# Definir los puntos de control (co) y sus handles
puntos = [
    {"co": (-3, -1, 0), "h_left": (-4, -2, 0), "h_right": (-2, 0, 0)},
    {"co": (-1,  2, 0), "h_left": (-2,  3, 0), "h_right": (0,  1, 0)},
    {"co": ( 1, -2, 0), "h_left": (0,  -3, 0), "h_right": (2, -1, 0)},
    {"co": ( 3,  1, 0), "h_left": (2,   0, 0), "h_right": (4,  2, 0)},
]

for i, p in enumerate(puntos):
    bp = spline.bezier_points[i]
    bp.co            = p["co"]
    bp.handle_left   = p["h_left"]
    bp.handle_right  = p["h_right"]
    bp.handle_left_type  = 'FREE'
    bp.handle_right_type = 'FREE'

# Crear el objeto y añadirlo a la escena
obj = bpy.data.objects.new("CurvaBezier", curva_data)
bpy.context.collection.objects.link(obj)
bpy.context.view_layer.objects.active = obj
```

#### Propiedades Matemáticas Clave

| Propiedad              | Descripción                                                       |
|------------------------|-------------------------------------------------------------------|
| Interpolación extremos | La curva **pasa** por el primer y último punto de control         |
| Aproximación interior  | La curva **no pasa** por los puntos intermedios (los "atrae")     |
| Envoltura convexa      | La curva está contenida dentro del polígono de control            |
| Invarianza afín        | Las transformaciones (traslación, rotación, escala) se aplican directamente a los puntos de control |

---

### 2.3.2 B-Spline (NURBS en Blender)

Las **B-Splines** (*Basis Splines*) son una generalización de las curvas de Bézier. Su variante más potente son las **NURBS** (*Non-Uniform Rational B-Splines*), que Blender implementa directamente como tipo de curva nativo.

#### ¿Por qué NURBS?

| Limitación Bézier           | Solución en NURBS/B-Spline                          |
|-----------------------------|-----------------------------------------------------|
| Al agregar puntos, el grado sube y la curva se vuelve difícil de controlar | El grado se mantiene constante independientemente del número de puntos |
| Un punto de control afecta **toda** la curva | Cada punto afecta solo una **región local** |
| No pueden representar cónicas exactas (círculos, elipses) | NURBS representa cónicas **exactamente** con pesos racionales |

#### Fundamento Matemático

Una B-Spline de grado $k$ con $n+1$ puntos de control $P_0, \ldots, P_n$ se define como:

$$C(t) = \sum_{i=0}^{n} P_i \cdot N_{i,k}(t)$$

Las funciones base $N_{i,k}(t)$ se definen recursivamente (fórmula de Cox–de Boor):

**Caso base:**
$$N_{i,0}(t) = \begin{cases} 1 & \text{si } t_i \leq t < t_{i+1} \\ 0 & \text{en otro caso} \end{cases}$$

**Recursión:**
$$N_{i,k}(t) = \frac{t - t_i}{t_{i+k} - t_i} N_{i,k-1}(t) + \frac{t_{i+k+1} - t}{t_{i+k+1} - t_{i+1}} N_{i+1,k-1}(t)$$

En **NURBS** cada punto tiene además un **peso** $w_i$ que controla la atracción de la curva hacia ese punto:

$$C(t) = \frac{\sum_{i=0}^{n} w_i P_i N_{i,k}(t)}{\sum_{i=0}^{n} w_i N_{i,k}(t)}$$

#### NURBS en Blender (GUI)

1. `Add → Curve → NURBS Curve` o `Add → Surface → NURBS Surface`.
2. En **Edit Mode**, seleccionar puntos y moverlos con `G`.
3. Cambiar el **Order** (grado + 1) en las propiedades de la curva: a mayor orden, mayor suavidad global.
4. Activar **Endpoint** en las propiedades para que la curva interpole el primer y último punto.
5. El **peso** de cada punto se puede editar en el panel N → Item → W.

#### NURBS con Python en Blender

```python
import bpy

# Limpiar escena
bpy.ops.object.select_all(action='SELECT')
bpy.ops.object.delete()

# Crear curva tipo NURBS
curva_data = bpy.data.curves.new(name="MiNURBS", type='CURVE')
curva_data.dimensions = '2D'

spline = curva_data.splines.new(type='NURBS')
spline.points.add(4)         # Agregar 4 puntos más (total 5 incluyendo el inicial)
spline.order_u = 4           # Grado cúbico (order = grado + 1)
spline.use_endpoint_u = True # La curva pasa por el primer y último punto

# Puntos de control: (x, y, z, weight)
coordenadas = [
    (-3.0, -1.0, 0.0, 1.0),
    (-1.5,  2.0, 0.0, 1.0),
    ( 0.0,  0.0, 0.0, 2.0),  # Mayor peso → curva se acerca más
    ( 1.5, -2.0, 0.0, 1.0),
    ( 3.0,  1.0, 0.0, 1.0),
]

for i, (x, y, z, w) in enumerate(coordenadas):
    spline.points[i].co = (x, y, z, w)

obj = bpy.data.objects.new("CurvaNURBS", curva_data)
bpy.context.collection.objects.link(obj)
bpy.context.view_layer.objects.active = obj
```

#### Comparativa: Bézier vs NURBS en Blender

| Característica          | Bézier (Blender)             | NURBS (Blender)                     |
|-------------------------|------------------------------|-------------------------------------|
| Interfaz               | Handles visuales intuitivos  | Puntos de control con pesos         |
| Control local          | Limitado                     | Excelente (afecta región local)     |
| Representar círculos   | Aproximación                 | Exacto (con pesos racionales)       |
| Uso típico             | Ilustración, animación, texto| Modelado de superficies, ingeniería |
| Facilidad de uso       | Alta                         | Media                               |

---

### 🎬 Ejercicio: Animación con Curvas en Blender

Este ejercicio anima un objeto siguiendo una curva Bézier, una de las técnicas más usadas en animación 3D/2D con Blender.

#### Método 1 — GUI (Follow Path Constraint)

1. Crear una **curva Bézier**: `Add → Curve → Bézier`. Editarla a gusto.
2. Crear un objeto a animar: `Add → Mesh → UV Sphere` (o cualquier objeto).
3. Seleccionar el objeto (esfera).
4. En el panel de **Propiedades → Object Constraint Properties** (🔗 ícono de cadena):
   - Agregar constraint: **Follow Path**.
   - En el campo **Target**, seleccionar la curva.
   - Activar **Fixed Position** y **Follow Curve**.
5. Con la curva seleccionada: `Object Data Properties → Path Animation`:
   - Activar **Frames**: define cuántos frames dura el recorrido.
   - Presionar `Alt + A` para previsualizar la animación.

#### Método 2 — Python scripting

```python
import bpy
import math

# ── Limpiar escena ────────────────────────────────────────────
bpy.ops.object.select_all(action='SELECT')
bpy.ops.object.delete()

# ── Crear la curva de trayectoria ─────────────────────────────
curva_data = bpy.data.curves.new("Trayectoria", type='CURVE')
curva_data.dimensions = '3D'
curva_data.path_duration = 100   # La animación dura 100 frames

spline = curva_data.splines.new('BEZIER')
spline.bezier_points.add(3)

# Definir la forma de la curva (S)
pts = [
    {"co":(-3,-2,0), "hl":(-4,-3,0), "hr":(-2,-1,0)},
    {"co":(-1, 2,0), "hl":(-2, 3,0), "hr":( 0, 1,0)},
    {"co":( 1,-2,0), "hl":( 0,-3,0), "hr":( 2,-1,0)},
    {"co":( 3, 2,0), "hl":( 2, 1,0), "hr":( 4, 3,0)},
]
for i, p in enumerate(pts):
    bp = spline.bezier_points[i]
    bp.co = p["co"]
    bp.handle_left  = p["hl"]
    bp.handle_right = p["hr"]
    bp.handle_left_type  = 'FREE'
    bp.handle_right_type = 'FREE'

curva_obj = bpy.data.objects.new("Trayectoria", curva_data)
bpy.context.collection.objects.link(curva_obj)

# ── Crear el objeto animado ───────────────────────────────────
bpy.ops.mesh.primitive_cone_add(radius1=0.2, depth=0.5)
cone = bpy.context.active_object
cone.name = "Cohete"

# ── Agregar constraint Follow Path ───────────────────────────
constraint = cone.constraints.new(type='FOLLOW_PATH')
constraint.target = curva_obj
constraint.use_curve_follow = True    # Orienta el objeto según la curva
constraint.forward_axis = 'FORWARD_Y' # Eje frontal del objeto

# Activar animación de la curva
curva_data.use_path = True

print("✅ Animación creada. Presiona Space para reproducirla.")
```

**¿Qué demuestra este ejercicio?**
- La curva actúa como una **trayectoria paramétrica** $C(t)$ con $t \in [0, 1]$ mapeado a los frames de la animación.
- El constraint **Follow Path** calcula automáticamente la tangente de la curva en cada frame para orientar el objeto.
- Internamente Blender evalúa la curva Bézier con la misma fórmula matemática vista en la sección 2.3.1.

---

## 2.4 Fractales

Los **fractales** son estructuras geométricas con **autosimilitud**: se ven iguales a cualquier escala de observación. El término fue acuñado por Benoît Mandelbrot (1975), del latín *fractus* (quebrado, irregular).

### Conceptos Fundamentales

**Dimensión fractal (Hausdorff):**

A diferencia de la geometría euclidiana, los fractales tienen dimensiones **no enteras**:

$$D = \frac{\log N}{\log (1/r)}$$

donde $N$ es el número de copias auto-similares y $r$ es el factor de reducción de cada copia.

**Ejemplo — Triángulo de Sierpiński:**
- $N = 3$ copias a escala $r = 1/2$
- $D = \log(3)/\log(2) \approx 1.585$

### Fractales Clásicos

#### 1. Conjunto de Mandelbrot

El conjunto de Mandelbrot es el conjunto de números complejos $c$ para los cuales la iteración $z_{n+1} = z_n^2 + c$ (con $z_0 = 0$) **no diverge**.

```python
# Conjunto de Mandelbrot renderizado en Blender con el nodo Script
# (usar en Geometry Nodes o como imagen generada)

import bpy
import numpy as np

def mandelbrot(width=512, height=512, max_iter=100):
    """Genera imagen del conjunto de Mandelbrot como array."""
    img = np.zeros((height, width, 4), dtype=np.float32)
    
    for py in range(height):
        for px in range(width):
            # Mapear píxel a número complejo
            cr = (px / width)  * 3.5 - 2.5   # Rango real: [-2.5, 1.0]
            ci = (py / height) * 2.0 - 1.0   # Rango imag: [-1.0, 1.0]
            
            zr, zi = 0.0, 0.0
            it = 0
            while zr*zr + zi*zi < 4.0 and it < max_iter:
                zr, zi = zr*zr - zi*zi + cr, 2*zr*zi + ci
                it += 1
            
            # Color según velocidad de escape
            t = it / max_iter
            img[py, px] = [t**0.5, t**2, t, 1.0]  # RGBA
    
    return img

# Crear imagen en Blender
w, h = 256, 256
pixel_data = mandelbrot(w, h).flatten().tolist()

if "Mandelbrot" in bpy.data.images:
    bpy.data.images.remove(bpy.data.images["Mandelbrot"])

img = bpy.data.images.new("Mandelbrot", width=w, height=h)
img.pixels = pixel_data
img.update()
print("✅ Imagen 'Mandelbrot' generada en el Image Editor de Blender.")
```

#### 2. Curva de Koch con Geometry Nodes / Python

```python
import bpy
import math

def koch_points(p1, p2, depth):
    """Genera los puntos de la curva de Koch recursivamente."""
    if depth == 0:
        return [p1, p2]
    
    dx, dy = p2[0]-p1[0], p2[1]-p1[1]
    
    # Dividir en tercios
    a = (p1[0] + dx/3,    p1[1] + dy/3)
    b = (p1[0] + 2*dx/3,  p1[1] + 2*dy/3)
    
    # Pico del triángulo (rotar 60°)
    angle = math.atan2(dy, dx) - math.pi/3
    length = math.hypot(dx, dy) / 3
    c = (a[0] + math.cos(angle)*length, a[1] + math.sin(angle)*length)
    
    # Recursión
    return (koch_points(p1, a, depth-1)[:-1] +
            koch_points(a,  c, depth-1)[:-1] +
            koch_points(c,  b, depth-1)[:-1] +
            koch_points(b, p2, depth-1))

# Generar puntos con profundidad 4
puntos_2d = koch_points((-2, 0), (2, 0), depth=4)

# Crear curva en Blender
curva = bpy.data.curves.new("Koch", type='CURVE')
curva.dimensions = '2D'
spline = curva.splines.new('POLY')
spline.points.add(len(puntos_2d) - 1)

for i, (x, y) in enumerate(puntos_2d):
    spline.points[i].co = (x, y, 0, 1)

obj = bpy.data.objects.new("CurvaKoch", curva)
bpy.context.collection.objects.link(obj)
print(f"✅ Curva de Koch creada con {len(puntos_2d)} puntos.")
```

#### 3. Triángulo de Sierpiński con Geometry Nodes

```python
import bpy
import bmesh

def sierpinski_vertices(v1, v2, v3, depth):
    """Retorna lista de triángulos (como ternas de vértices)."""
    if depth == 0:
        return [(v1, v2, v3)]
    
    m12 = ((v1[0]+v2[0])/2, (v1[1]+v2[1])/2)
    m23 = ((v2[0]+v3[0])/2, (v2[1]+v3[1])/2)
    m31 = ((v3[0]+v1[0])/2, (v3[1]+v1[1])/2)
    
    return (sierpinski_vertices(v1, m12, m31, depth-1) +
            sierpinski_vertices(m12, v2, m23, depth-1) +
            sierpinski_vertices(m31, m23, v3, depth-1))

# Generar triángulos
triangulos = sierpinski_vertices((-2,0), (2,0), (0, 3.46), depth=5)

# Crear malla en Blender
mesh = bpy.data.meshes.new("Sierpinski")
bm = bmesh.new()

for t in triangulos:
    verts = [bm.verts.new((x, y, 0)) for x, y in t]
    bm.faces.new(verts)

bm.to_mesh(mesh)
bm.free()

obj = bpy.data.objects.new("Sierpinski", mesh)
bpy.context.collection.objects.link(obj)
print(f"✅ Triángulo de Sierpiński: {len(triangulos)} triángulos, profundidad 5.")
```

### Aplicaciones de los Fractales

- **Generación de terrenos** en videojuegos y cine (Blender usa fractales en texturas procedurales como *Musgrave* y *Noise*).
- **Texturas procedurales**: el nodo **Noise Texture** de Blender implementa ruido fractal.
- **Antenas fractales**: tamaño reducido con alta eficiencia multifrecuencia.
- **Arte generativo** y efectos visuales.

> 💡 En Blender, explorar `Shader Editor → Add → Texture → Musgrave Texture` para ver un fractal aplicado como textura procedural.

---

## 2.5 Uso y Creación de Fuentes de Texto

Blender tiene soporte nativo para texto tipográfico en 3D y 2D, permitiendo cargar fuentes TrueType (`.ttf`) y OpenType (`.otf`) directamente.

### Tipos de Fuentes en Informática

#### 1. Fuentes de Mapa de Bits (Bitmap/Raster)

Los caracteres se almacenan como **matrices de píxeles**.

```
Letra 'A' 5×7 px:
. █ █ .
█ . . █
█ █ █ █
█ . . █
█ . . █
```

**Ventajas:** Renderizado instantáneo para tamaños fijos.
**Desventajas:** No escalan → se pixelan. Requieren una imagen por tamaño.

#### 2. Fuentes Vectoriales (Outline Fonts)

Los caracteres se definen con **curvas Bézier**. Blender utiliza esta tecnología internamente para renderizar texto.

| Formato  | Desarrollador | Curva usada        | Extensión |
|----------|---------------|--------------------|-----------|
| Type 1   | Adobe (1984)  | Bézier cúbica      | `.pfb`    |
| TrueType | Apple/MS (1991)| Bézier cuadrática | `.ttf`    |
| OpenType | Adobe/MS (1997)| Cúbica o cuadrática| `.otf`    |
| WOFF2    | W3C (2010)    | Comprimido para web| `.woff2`  |

#### 3. Fuentes Variables (Variable Fonts)

Un solo archivo contiene toda la familia, interpolando entre ejes:

| Eje    | Etiqueta | Efecto                |
|--------|----------|-----------------------|
| Weight | `wght`   | Grosor del trazo      |
| Width  | `wdth`   | Expansión horizontal  |
| Slant  | `slnt`   | Inclinación           |

### Texto en Blender

#### GUI — Crear y editar texto

1. `Add → Text` en el viewport 3D.
2. Entrar a **Edit Mode** (`Tab`) para escribir.
3. En **Object Data Properties** (pestaña con ícono de `A`):
   - **Font**: cargar fuente `.ttf` o `.otf` desde disco.
   - **Font Bold / Italic / Bold-Italic**: fuentes para cada estilo.
   - **Size**: tamaño del texto.
   - **Character Spacing / Word Spacing / Line Distance**: espaciado.
4. En **Paragraph**: alineación (izquierda, centro, derecha, justificado).
5. Convertir a malla: `Object → Convert → Mesh` para poder editar vértices.

#### Métricas Tipográficas en Blender

```
                ┌── Ascender
    ▲           │
    │  ████████ ← Cap-height
    │  ██  ████
    │  ████████ ← x-height
    │  ██
    │  ████████
────┼─────────── Baseline ─────
    │  ██        ▲
    ▼  ██        └── Descender
```

| Métrica    | Descripción                                    | En Blender                |
|------------|------------------------------------------------|---------------------------|
| Baseline   | Línea base de los caracteres                   | Posición Y del objeto texto|
| Ascender   | Altura sobre la baseline                       | Afecta la caja de texto   |
| Descender  | Profundidad bajo la baseline                   | Idem                      |
| Kerning    | Ajuste de espacio entre pares de letras        | Character Spacing          |

#### Python — Crear y manipular texto en Blender

```python
import bpy
import math

# ── Crear objeto de texto ──────────────────────────────────────
bpy.ops.object.select_all(action='SELECT')
bpy.ops.object.delete()

# Crear texto
bpy.ops.object.text_add(location=(0, 0, 0))
obj = bpy.context.active_object
obj.name = "TextoDemo"

# Acceder a los datos del texto
texto = obj.data
texto.body           = "Graficación 2D"
texto.size           = 0.8
texto.align_x        = 'CENTER'
texto.align_y        = 'CENTER'
texto.space_character = 1.1   # Kerning global

# ── Cargar fuente personalizada ───────────────────────────────
# (Descomenta y ajusta la ruta a tu fuente .ttf)
# fuente = bpy.data.fonts.load("/ruta/a/fuente.ttf")
# texto.font = fuente

# ── Extrusión para efecto 3D ──────────────────────────────────
texto.extrude = 0.05        # Dar profundidad
texto.bevel_depth = 0.01    # Redondear bordes

# ── Animación del texto: escalar con keyframes ────────────────
obj.scale = (0.01, 0.01, 0.01)
obj.keyframe_insert(data_path="scale", frame=1)    # Frame 1: pequeño

obj.scale = (1.0, 1.0, 1.0)
obj.keyframe_insert(data_path="scale", frame=30)   # Frame 30: tamaño normal

print("✅ Texto creado y animado entre frame 1 y 30.")
```

#### Convertir Texto a Curvas y Malla

```python
import bpy

# Texto → Curva (preserva las curvas Bézier de los glifos)
bpy.ops.object.select_all(action='SELECT')
texto_obj = bpy.context.active_object
bpy.ops.object.convert(target='CURVE')

# O Texto → Malla (triangula los glifos)
bpy.ops.object.convert(target='MESH')

# Esto permite editar los vértices individuales de cada letra
# Útil para efectos de "texto destruido" o animaciones de letras
```

> 💡 Convertir texto a **Curva** permite ver exactamente las curvas Bézier con las que está construida cada letra, conectando la teoría de la sección 2.3.1 con la tipografía.

---

## Referencias

Bézier, P. (1972). *Numerical control: Mathematics and applications* (A. R. Forrest & A. F. Pankhurst, Trads.). John Wiley & Sons. (Obra original publicada en 1970)

Blender Foundation. (2024). *Blender 4.x reference manual: Curves*. https://docs.blender.org/manual/en/latest/modeling/curves/index.html

Blender Foundation. (2024). *Blender 4.x reference manual: Text objects*. https://docs.blender.org/manual/en/latest/modeling/texts/index.html

Blender Foundation. (2024). *Blender Python API documentation*. https://docs.blender.org/api/current/index.html

Foley, J. D., van Dam, A., Feiner, S. K., & Hughes, J. F. (1995). *Computer graphics: Principles and practice* (2.ª ed.). Addison-Wesley.

Hess, R. (2010). *The essential Blender: Guide to 3D creation with the open source suite Blender*. No Starch Press.

Mandelbrot, B. B. (1982). *The fractal geometry of nature*. W. H. Freeman and Company.

Piegl, L., & Tiller, W. (1997). *The NURBS book* (2.ª ed.). Springer-Verlag. https://doi.org/10.1007/978-3-642-59223-2

Shirley, P., & Marschner, S. (2009). *Fundamentals of computer graphics* (3.ª ed.). A K Peters/CRC Press.

Van Gumster, J. (2020). *Blender for dummies* (4.ª ed.). John Wiley & Sons.

---

<div align="center">

**Unidad 2 — Graficación 2D** | Materia: Graficación por Computadora | Herramienta: Blender

*Elaborado con fines académicos*

</div>
