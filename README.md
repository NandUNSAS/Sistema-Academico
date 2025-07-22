## 📝 Convenciones de Codificación Aplicadas

### 1. 📁 Organización modular: paquete `reporte`

**Práctica:**  
Se creó un paquete llamado `reporte` para agrupar plantillas y componentes relacionados. Esto sigue la convención de paquetes en Python, donde un paquete es un directorio con un archivo `__init__.py`.

**Estructura:**
```

/reporte/
├── __init__.py
├── layout_reporte.html
└── reporte.html
````
---

### 2. 📄 Nombres de archivos HTML en `snake_case`

**Práctica:**  
Se usa `snake_case` en los archivos de plantilla HTML (`layout_reporte.html`, `reporte.html`) para mantener consistencia con las convenciones de nombres de archivos en Python y facilitar su uso en `render_template()`.

**Ejemplo:**
```python
return render_template('reporte/reporte.html')
````

---

### 3. ⚙️ Separación de configuración (archivo `config/default.py`)

**Práctica:**
Se agregaron las variables de configuración de la base de datos en un archivo independiente, lo cual sigue el principio de separación de responsabilidades y facilita el mantenimiento.

**Convención de codificación aplicada (PEP 8):**
Las constantes (como configuraciones globales) están nombradas usando mayúsculas con guiones bajos (UPPER_CASE_WITH_UNDERSCORES), que es la convención recomendada para constantes en Python.

**Fragmento:**

```python
MYSQL_HOST = "localhost"
MYSQL_USER = "root"
MYSQL_PASSWORD = "*******"
MYSQL_DB = "edunet"
MYSQL_PORT = 3306
```
---

### 4. 🔗 Registro de rutas mediante `Blueprint` (en `run.py`)

**Práctica:**
Se utilizó el sistema de Blueprints de Flask para registrar rutas específicas del módulo `reporte`, lo cual mejora la escalabilidad y modularidad del proyecto.

**Fragmento en `run.py`:**

```python
from interfaces.controllers.reporte_controlador import reporte_bp
app.register_blueprint(reporte_bp)
```

---

### 5. 🐍 Convenciones de nombres en `reporte_controlador.py`

**Prácticas aplicadas:**

* `snake_case` para el nombre del archivo: `reporte_controlador.py`
* `snake_case` para funciones: `mostrar_reporte()`
* Uso de variables descriptivas: `reporte_bp`
* Organización modular mediante `Blueprint`

**Fragmento de código:**

```python
from flask import Blueprint, render_template

reporte_bp = Blueprint('reporte', __name__, url_prefix='/reporte')

@reporte_bp.route('/')
def mostrar_reporte():
    return render_template('reporte/reporte.html')
```



# Estilos de Programación Aplicados

Este proyecto implementa diversos **estilos de programación**, los cuales permiten una estructura clara, mantenible y coherente en todas sus capas. A continuación se detallan con ejemplos concretos del código.



## 1. Cookbook

Este estilo se refleja en la estructura modular del código, donde cada función realiza una tarea bien definida y aislada.

Ejemplo en `curso_repositorio_impl.py`:

```python
def obtener(self, session, id):
    return session.query(curso).filter_by(curso_id=id).first()

def agregar(self, session, curso):
    session.add(curso)
    session.commit()
````

En el controlador, cada ruta se encarga de una acción específica:

```python
@curso_bp.route('/cursos', methods=['GET'])
def mostrar_cursos():
    session = get_session()
    cursos = repositorio.obtener_todos(session)
    return render_template('cursos/cursos.html', cursos=cursos)
```

---

## 2. Error/Exception Handling

Se utiliza manejo de excepciones para capturar errores durante operaciones críticas como la manipulación de la base de datos:

```python
try:
    repositorio.agregar(session, nuevo_curso)
    flash("Curso creado exitosamente.", "success")
except SQLAlchemyError:
    flash("Error al crear el curso. Intente más tarde.", "danger")
```

En la vista (`cursos.html`), se notifican al usuario los mensajes con `get_flashed_messages`:

```html
{% for category, message in messages %}
  <div class="alert alert-{{ category }}">{{ message }}</div>
{% endfor %}
```

---

## 3. Persistent-Tables

Mediante el uso de SQLAlchemy como ORM, los objetos se vinculan directamente a tablas de base de datos:

```python
class curso(Base):
    __tablename__ = 'cursos'

    curso_id = Column(Integer, primary_key=True)
    nombre = Column(String(100), nullable=False)
    profesor_id = Column(Integer, ForeignKey('profesores.profesor_id'))
```

Las operaciones CRUD se realizan sobre estas entidades persistentes.

---

## 4. Things

El diseño está centrado en entidades del mundo real, como `curso`, que se representa en todas las capas:

* Modelo:

  ```python
  class curso(Base)
  ```
* Repositorio:

  ```python
  repositorio.obtener(session, curso_id)
  ```
* Vista:

  ```html
  {{ curso.nombre }}
  ```

Este enfoque basado en objetos concretos del dominio es típico del estilo *Things*.

---

## 5. RESTful (Parcial)

Aunque no es una API REST completa (no responde con JSON), la aplicación adopta convenciones REST en sus rutas y uso de métodos HTTP:

* `GET /cursos`: Listar cursos
* `POST /cursos`: Crear curso
* `POST /cursos/eliminar/<id>`: Eliminar curso
* `GET /cursos/<id>`: Ver detalle

Ejemplo en Flask:

```python
@curso_bp.route('/cursos/eliminar/<int:curso_id>', methods=['POST'])
def eliminar_curso(curso_id):
    session = get_session()
    repositorio.eliminar(session, curso_id)
    return redirect(url_for('curso.mostrar_cursos'))
```

Formulario HTML correspondiente:

```html
<form method="POST" action="{{ url_for('curso.eliminar_curso', curso_id=curso.curso_id) }}">
    <button type="submit">Eliminar</button>
</form>
```

## 🧼 Mejora de Código Aplicando Principios de Clean Code

Este informe documenta los cambios realizados para mejorar la legibilidad, mantenibilidad y claridad del código siguiendo los principios propuestos por Robert C. Martin en *Clean Code*.

---

### 1. Uso de Nombres Explícitos en Lugar de Abreviaturas

**Práctica aplicada:**  
Se evita el uso de abreviaturas como `impl` y se prefiere el uso completo como `implementation`.

**Justificación:**  
Los nombres explícitos mejoran la legibilidad y eliminan ambigüedad en equipos colaborativos o proyectos a largo plazo.

**Antes:**
```python
self.impl.get_course_grades_report(course_id)
````

**Después:**

```python
self.reports_implementation.get_course_grades_report(course_identifier)
```

---

### 2. Mejora de Nombres en Instancias y Parámetros

**Práctica aplicada:**
Reemplazo de nombres abreviados por nombres completos y con significado claro.

**Cambios realizados:**

| Nombre original | Reemplazo            | Justificación                                  |
| --------------- | -------------------- | ---------------------------------------------- |
| `grade_repo`    | `grade_repository`   | Indica que es un repositorio completo          |
| `student_repo`  | `student_repository` | Explicita que gestiona entidades de estudiante |
| `course_repo`   | `course_repository`  | Aclara que opera sobre cursos                  |
| `course_id`     | `course_identifier`  | Precisa que es un identificador, no un objeto  |

**Código ajustado:**

```python
class ReportsImplementation:
    def __init__(self):
        self.grade_repository = GradeRepository()
        self.student_repository = StudentRepository()
        self.course_repository = CourseRepository()

    def get_course_grades_report(self, course_identifier: int):
        ...
```

---

### 3. Eliminación de Comentarios Redundantes

**Práctica aplicada:**
Eliminación de comentarios obvios que repiten lo que ya dice el código.

**Antes:**

```python
# Ruta que llama a la función del controlador
@reporte_bp.route('/curso/<int:course_id>', methods=['GET'])
def course_report(curso_id):
    return show_course_report(curso_id)
```

**Después:**

```python
@reporte_bp.route('/curso/<int:course_id>', methods=['GET'])
def course_report(course_id):
    return show_course_report(course_id)
```

---

### 4. Separación de Responsabilidades en Funciones

**Práctica aplicada:**
Refactorización de una función grande en funciones más pequeñas que cumplen una única responsabilidad.

**Antes:**

```python
def get_course_grades_report(self, course_identifier: int):
    course = self.course_repository.get(course_identifier)
    if not course:
        return None

    all_grades = self.grade_repository.list_all()
    course_grades = [g for g in all_grades if g.course_id == course_identifier]

    report_data = []
    for grade in course_grades:
        student = self.student_repository.get(grade.student_id)
        report_data.append({
            "student_id": student.user_id,
            "score": grade.score
        })

    return {
        "course_name": course.name,
        "professor_id": course.professor_id,
        "grades": report_data
    }
```

**Después:**

```python
def get_course_grades_report(self, course_identifier: int):
    course = self._get_course_or_none(course_identifier)
    if not course:
        return None

    grades = self._get_grades_for_course(course_identifier)
    report_data = self._build_report_data(grades)

    return {
        "course_name": course.name,
        "professor_id": course.professor_id,
        "grades": report_data
    }

def _get_course_or_none(self, course_identifier: int):
    return self.course_repository.get(course_identifier)

def _get_grades_for_course(self, course_identifier: int):
    all_grades = self.grade_repository.list_all()
    return [g for g in all_grades if g.course_id == course_identifier]

def _build_report_data(self, grades):
    report_data = []
    for grade in grades:
        student = self.student_repository.get(grade.student_id)
        report_data.append({
            "student_id": student.user_id,
            "score": grade.score
        })
    return report_data
```

**Beneficios obtenidos:**

* Las funciones son más cortas y claras.
* Cada método tiene una única responsabilidad.
* La legibilidad y el mantenimiento del código mejoran sustancialmente.
* Se evita el uso de comentarios innecesarios gracias a nombres descriptivos.


