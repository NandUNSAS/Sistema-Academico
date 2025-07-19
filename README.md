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

