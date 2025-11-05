Perfecto — te doy la primera parte completa y lista para ejecutar del proyecto Agencia de Viajes (aunque el nombre del procedimiento de carpeta queda como indicaste UIII_Agencia_Viajes_0472). Incluyo: pasos (1–31) paso a paso, comandos a ejecutar, models.py (solo Cliente por ahora), views.py con las funciones solicitadas (sin forms.py), urls.py de la app, cambios en settings.py, registro en admin.py, y los archivos HTML básicos (base.html, navbar.html, footer.html, inicio.html) y las vistas de clientes (agregar, ver, actualizar, borrar). Usa VS Code y Python/Django.
# 1 — Estructura general y nombres

Proyecto raíz: UIII_Agencia_Viajes_0472

Proyecto Django (backend): backend_agencia

App: app_clientes

# 2 — Crear carpeta del proyecto (paso 1)

En tu sistema (Terminal):

# desde la carpeta donde quieras crear el proyecto:
    mkdir UIII_Agencia_Viajes_0472
    cd UIII_Agencia_Viajes_0472

# 3 — Abrir VS Code en la carpeta (paso 2)

En la misma carpeta:

code .


(Si code no funciona, abre VS Code y selecciona File > Open Folder... y elige UIII_Agencia_Viajes_0472.)

# 4 — Abrir terminal en VS Code (paso 3)

En VS Code: Terminal > New Terminal o usa el atajo (normalmente Ctrl+ñ o Ctrl+ dependiendo de tu configuración).

# 5 — Crear carpeta entorno virtual .venv desde terminal (paso 4)

Dentro de la carpeta del proyecto:

    python -m venv .venv


(Esto crea la carpeta oculta .venv en UIII_Agencia_Viajes_0472.)

# 6 — Activar el entorno virtual (paso 5)

En Windows PowerShell:

    .venv\Scripts\Activate.ps1


En Windows (cmd):

    .venv\Scripts\activate
    
    
    En macOS / Linux:
    
    source .venv/bin/activate

# 7 — Activar intérprete de Python en VS Code (paso 6)

En VS Code, abre la paleta (Ctrl+Shift+P).

Escribe Python: Select Interpreter.

Selecciona el que apunta a .../UIII_Agencia_Viajes_0472/.venv/bin/python (o equivalente en Windows .venv\Scripts\python.exe).

# 8 — Instalar Django (paso 7)

Con el entorno activado:

    pip install --upgrade pip
    pip install django

# 9 — Crear proyecto backend_agencia sin duplicar carpeta (paso 8)

Para evitar que Django cree una carpeta anidada con el mismo nombre, ejecuta:

    django-admin startproject backend_agencia .


Nota: El punto . hace que los archivos de proyecto estén en la carpeta actual y evita crear UIII_Agencia_Viajes_0472/backend_agencia/backend_agencia.

# 10 — Ejecutar servidor en puerto 8472 (paso 9)

Asegúrate de estar en la raíz del proyecto (UIII_Agencia_Viajes_0472) y con .venv activado:

    python manage.py runserver 8472


Esto iniciará en http://127.0.0.1:8472/.

# 11 — Copiar y pegar el link en el navegador (paso 10)

Abre tu navegador y pega: http://127.0.0.1:8472/ (o el link que muestre la consola).

# 12 — Crear aplicación app_clientes (paso 11)

Con el entorno activado y en la raíz del proyecto:

    python manage.py startapp app_clientes

# 13 — models.py (paso 12 y 13)

Trabajaremos primero solo con el modelo Cliente. Crea/edita app_clientes/models.py así:
# MODELO: clientes
    from django.db import models
    class Cliente(models.Model):
        nombre = models.CharField(max_length=100)
        apellido = models.CharField(max_length=100)
        correo = models.EmailField(max_length=254, blank=True, null=True)
        telefono = models.CharField(max_length=30, blank=True, null=True)
        direccion = models.TextField(blank=True, null=True)
        fecha_nacimiento = models.DateField(blank=True, null=True)
    
        def __str__(self):
            return f"{self.nombre} {self.apellido}"


He dejado fuera por ahora Viaje y Transporte para seguir tu indicación de trabajar primero con clientes. Cuando quieras los agregamos.

# 14 — Migraciones (paso 12.5)

Crear migraciones:

    python manage.py makemigrations


Aplicar migraciones:

    python manage.py migrate

# 15 — Views para app_clientes (paso 14)

Crea/edita app_clientes/views.py con las funciones solicitadas sin usar forms.py, manejando POST manualmente:

    from django.shortcuts import render, redirect, get_object_or_404
    from .models import Cliente
    from django.urls import reverse
    from django.utils import timezone
    
    def inicio_agencia(request):
        # página principal de la app
        return render(request, 'inicio.html', {})
    
    def agregar_clientes(request):
        if request.method == 'POST':
            nombre = request.POST.get('nombre', '').strip()
            apellido = request.POST.get('apellido', '').strip()
            correo = request.POST.get('correo', '').strip() or None
            telefono = request.POST.get('telefono', '').strip() or None
            direccion = request.POST.get('direccion', '').strip() or None
            fecha_nacimiento = request.POST.get('fecha_nacimiento') or None

        cliente = Cliente(
            nombre=nombre,
            apellido=apellido,
            correo=correo,
            telefono=telefono,
            direccion=direccion,
            fecha_nacimiento=fecha_nacimiento
        )
        cliente.save()
        return redirect('ver_clientes')
    return render(request, 'clientes/agregar_clientes.html', {})

    def ver_clientes(request):
        clientes = Cliente.objects.all().order_by('apellido', 'nombre')
        return render(request, 'clientes/ver_clientes.html', {'clientes': clientes})
    
    def actualizar_clientes(request, cliente_id):
        cliente = get_object_or_404(Cliente, id=cliente_id)
        return render(request, 'clientes/actualizar_clientes.html', {'cliente': cliente})
    
    def realizar_actualizacion_clientes(request, cliente_id):
        cliente = get_object_or_404(Cliente, id=cliente_id)
        if request.method == 'POST':
            cliente.nombre = request.POST.get('nombre', cliente.nombre).strip()
            cliente.apellido = request.POST.get('apellido', cliente.apellido).strip()
            correo = request.POST.get('correo', '').strip()
            cliente.correo = correo or None
            telefono = request.POST.get('telefono', '').strip()
            cliente.telefono = telefono or None
            direccion = request.POST.get('direccion', '').strip()
            cliente.direccion = direccion or None
            fecha_nacimiento = request.POST.get('fecha_nacimiento')
            cliente.fecha_nacimiento = fecha_nacimiento or None
            cliente.save()
            return redirect('ver_clientes')
        # si llama GET, redirigir a formulario de edición
        return redirect('actualizar_clientes', cliente_id=cliente.id)
    
    def borrar_clientes(request, cliente_id):
        cliente = get_object_or_404(Cliente, id=cliente_id)
        if request.method == 'POST':
            cliente.delete()
            return redirect('ver_clientes')
        return render(request, 'clientes/borrar_clientes.html', {'cliente': cliente})

# 16 — Templates: estructura (pasos 15–22)

Crea la estructura de carpetas:

    app_clientes/
        templates/
            base.html
            header.html
            navbar.html
            footer.html
            inicio.html
            clientes/
                agregar_clientes.html
                ver_clientes.html
                actualizar_clientes.html
                borrar_clientes.html

# base.html (paso 17: agregar Bootstrap)

app_clientes/templates/base.html:

    <!doctype html>
    <html lang="es">
    <head>
      <meta charset="utf-8">
      <meta name="viewport" content="width=device-width,initial-scale=1">
      <title>{% block title %}Sistema Agencia{% endblock %}</title>
      <!-- Bootstrap CSS (CDN) -->
      <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/css/bootstrap.min.css" rel="stylesheet">
      <style>
        /* Colores suaves y modernos */
        body { background: #f7f9fb; color: #213547; }
        .navbar-brand { font-weight: 700; }
        footer { position: fixed; bottom: 0; left: 0; right: 0; background:#fff; padding:8px 16px; border-top:1px solid #e6e9ec;}
        .main-container { padding-bottom: 70px; } /* espacio para footer fijo */
      </style>
      {% block extra_head %}{% endblock %}
    </head>
    <body>
      {% include 'navbar.html' %}
      <div class="container main-container mt-4">
        {% block content %}{% endblock %}
      </div>
    
      {% include 'footer.html' %}
    
      <!-- Bootstrap JS (CDN) -->
      <script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.2/dist/js/bootstrap.bundle.min.js"></script>
      {% block extra_js %}{% endblock %}
    </body>
    </html>

# navbar.html (paso 18)

app_clientes/templates/navbar.html:

    <nav class="navbar navbar-expand-lg navbar-light bg-white shadow-sm">
      <div class="container">
        <a class="navbar-brand" href="{% url 'inicio_agencia' %}">✈️ Sistema de Administración Agencia</a>
        <button class="navbar-toggler" type="button" data-bs-toggle="collapse" data-bs-target="#navMenu">
          <span class="navbar-toggler-icon"></span>
        </button>
        <div class="collapse navbar-collapse" id="navMenu">
          <ul class="navbar-nav ms-auto">
            <li class="nav-item"><a class="nav-link" href="{% url 'inicio_agencia' %}">Inicio</a></li>
    
            <li class="nav-item dropdown">
              <a class="nav-link dropdown-toggle" href="#" id="clientesDrop" role="button" data-bs-toggle="dropdown">
                <i class="bi bi-people-fill"></i> Clientes
              </a>
              <ul class="dropdown-menu" aria-labelledby="clientesDrop">
                <li><a class="dropdown-item" href="{% url 'agregar_clientes' %}">Agregar clientes</a></li>
                <li><a class="dropdown-item" href="{% url 'ver_clientes' %}">Ver clientes</a></li>
              </ul>
            </li>
    
            <li class="nav-item dropdown">
              <a class="nav-link dropdown-toggle" href="#" id="viajesDrop" role="button" data-bs-toggle="dropdown">
                <i class="bi bi-globe2"></i> Viajes
              </a>
              <ul class="dropdown-menu" aria-labelledby="viajesDrop">
                <li><a class="dropdown-item" href="#">Agregar viajes</a></li>
                <li><a class="dropdown-item" href="#">Ver viajes</a></li>
              </ul>
            </li>
    
            <li class="nav-item dropdown">
              <a class="nav-link dropdown-toggle" href="#" id="transpDrop" role="button" data-bs-toggle="dropdown">
                <i class="bi bi-truck"></i> Transporte
              </a>
              <ul class="dropdown-menu" aria-labelledby="transpDrop">
                <li><a class="dropdown-item" href="#">Agregar transporte</a></li>
                <li><a class="dropdown-item" href="#">Ver transporte</a></li>
              </ul>
            </li>
          </ul>
        </div>
      </div>
    </nav>

    <!-- Bootstrap Icons CDN (opcional para iconos) -->
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap-icons@1.10.5/font/bootstrap-icons.css">


Los iconos se muestran solo en las opciones principales (no en los submenus), como pediste.

# footer.html (paso 19)

app_clientes/templates/footer.html:
    
    <footer class="text-center">
      <div class="container">
        <small>
          &copy; {{ now|date:"Y" }} Derechos reservados. Creado por Yared Coss, Cbtis 128.
        </small>
      </div>
    </footer>


Nota: Para que now funcione, añade django.template.context_processors.request (ya incluido) o pasa {'now': timezone.now()} desde la vista; o mejor: en settings.py activar django.template.context_processors.request (viene por defecto). Alternativamente puedes sustituir {{ now|date:"Y" }} por 2025 si prefieres fijo.

# inicio.html (paso 20)

app_clientes/templates/inicio.html:

    {% extends 'base.html' %}
    {% block title %}Inicio - Agencia{% endblock %}
    {% block content %}
    <div class="row align-items-center">
      <div class="col-md-7">
        <h1>Bienvenido al sistema de administración - Agencia de Viajes</h1>
        <p>Gestión de clientes, viajes y transportes. Usa el menú para navegar.</p>
      </div>
      <div class="col-md-5">
        <img src="https://images.unsplash.com/photo-1504384308090-c894fdcc538d?w=800&q=80" class="img-fluid rounded" alt="Agencia de viajes">
      </div>
    </div>
    {% endblock %}


(Imagen tomada desde la red — URL pública de Unsplash.)

# Templates de clientes (paso 22, sin validación)

Crea los archivos dentro de app_clientes/templates/clientes/.

agregar_clientes.html

    {% extends 'base.html' %}
    {% block title %}Agregar Cliente{% endblock %}
    {% block content %}
    <h2>Agregar Cliente</h2>
    <form method="post">
      {% csrf_token %}
      <div class="mb-3">
        <label class="form-label">Nombre</label>
        <input type="text" name="nombre" class="form-control" required>
      </div>
      <div class="mb-3">
        <label class="form-label">Apellido</label>
        <input type="text" name="apellido" class="form-control" required>
      </div>
      <div class="mb-3">
        <label class="form-label">Correo</label>
        <input type="email" name="correo" class="form-control">
      </div>
      <div class="mb-3">
        <label class="form-label">Teléfono</label>
        <input type="text" name="telefono" class="form-control">
      </div>
      <div class="mb-3">
        <label class="form-label">Dirección</label>
        <textarea name="direccion" class="form-control"></textarea>
      </div>
      <div class="mb-3">
        <label class="form-label">Fecha de nacimiento</label>
        <input type="date" name="fecha_nacimiento" class="form-control">
      </div>
      <button class="btn btn-primary">Guardar</button>
      <a href="{% url 'ver_clientes' %}" class="btn btn-secondary">Cancelar</a>
    </form>
    {% endblock %}

ver_clientes.html

    {% extends 'base.html' %}
    {% block title %}Ver Clientes{% endblock %}
    {% block content %}
    <h2>Lista de Clientes</h2>
    <a href="{% url 'agregar_clientes' %}" class="btn btn-success mb-3">Agregar cliente</a>
    <table class="table table-striped">
      <thead>
        <tr>
          <th>Nombre</th>
          <th>Apellido</th>
          <th>Correo</th>
          <th>Teléfono</th>
          <th>Acciones</th>
        </tr>
      </thead>
      <tbody>
        {% for c in clientes %}
        <tr>
          <td>{{ c.nombre }}</td>
          <td>{{ c.apellido }}</td>
          <td>{{ c.correo }}</td>
          <td>{{ c.telefono }}</td>
          <td>
            <a class="btn btn-sm btn-info" href="{% url 'actualizar_clientes' c.id %}">Editar</a>
            <a class="btn btn-sm btn-danger" href="{% url 'borrar_clientes' c.id %}">Borrar</a>
          </td>
        </tr>
        {% empty %}
        <tr><td colspan="5">No hay clientes registrados.</td></tr>
        {% endfor %}
      </tbody>
    </table>
    {% endblock %}

actualizar_clientes.html

    {% extends 'base.html' %}
    {% block title %}Actualizar Cliente{% endblock %}
    {% block content %}
    <h2>Editar Cliente</h2>
    <form method="post" action="{% url 'realizar_actualizacion_clientes' cliente.id %}">
      {% csrf_token %}
      <div class="mb-3">
        <label class="form-label">Nombre</label>
        <input type="text" name="nombre" value="{{ cliente.nombre }}" class="form-control" required>
      </div>
      <div class="mb-3">
        <label class="form-label">Apellido</label>
        <input type="text" name="apellido" value="{{ cliente.apellido }}" class="form-control" required>
      </div>
      <div class="mb-3">
        <label class="form-label">Correo</label>
        <input type="email" name="correo" value="{{ cliente.correo }}" class="form-control">
      </div>
      <div class="mb-3">
        <label class="form-label">Teléfono</label>
        <input type="text" name="telefono" value="{{ cliente.telefono }}" class="form-control">
      </div>
      <div class="mb-3">
        <label class="form-label">Dirección</label>
        <textarea name="direccion" class="form-control">{{ cliente.direccion }}</textarea>
      </div>
      <div class="mb-3">
        <label class="form-label">Fecha de nacimiento</label>
        <input type="date" name="fecha_nacimiento" value="{{ cliente.fecha_nacimiento }}" class="form-control">
      </div>
      <button class="btn btn-primary">Actualizar</button>
      <a href="{% url 'ver_clientes' %}" class="btn btn-secondary">Cancelar</a>
    </form>
    {% endblock %}

borrar_clientes.html

    {% extends 'base.html' %}
    {% block title %}Borrar Cliente{% endblock %}
    {% block content %}
    <h2>Confirmar borrado</h2>
    <p>¿Deseas eliminar al cliente <strong>{{ cliente.nombre }} {{ cliente.apellido }}</strong>?</p>
    <form method="post">
      {% csrf_token %}
      <button class="btn btn-danger">Sí, borrar</button>
      <a href="{% url 'ver_clientes' %}" class="btn btn-secondary">Cancelar</a>
    </form>
    {% endblock %}


No hay validación cliente-side/server-side extra (tal como pediste "No validar entrada de datos").

# 17 — Crear urls.py en app_clientes (paso 24)

Crea app_clientes/urls.py:

    from django.urls import path
    from . import views
    
    urlpatterns = [
        path('', views.inicio_agencia, name='inicio_agencia'),
        path('clientes/agregar/', views.agregar_clientes, name='agregar_clientes'),
        path('clientes/', views.ver_clientes, name='ver_clientes'),
        path('clientes/editar/<int:cliente_id>/', views.actualizar_clientes, name='actualizar_clientes'),
        path('clientes/editar/guardar/<int:cliente_id>/', views.realizar_actualizacion_clientes, name='realizar_actualizacion_clientes'),
        path('clientes/borrar/<int:cliente_id>/', views.borrar_clientes, name='borrar_clientes'),
    ]

# 18 — Agregar app a settings.py (paso 25)

En backend_agencia/settings.py, en INSTALLED_APPS añade:

    INSTALLED_APPS = [
        # apps por defecto...
        'app_clientes',
    ]

# 19 — Configurar backend_agencia/urls.py (paso 26)

Edita backend_agencia/urls.py:

    from django.contrib import admin
    from django.urls import path, include
    
    urlpatterns = [
        path('admin/', admin.site.urls),
        path('', include('app_clientes.urls')),  # ruta raíz para la app
    ]

# 20 — Registrar modelos en admin.py (paso 27)

app_clientes/admin.py:

    from django.contrib import admin
    from .models import Cliente
    
    @admin.register(Cliente)
    class ClienteAdmin(admin.ModelAdmin):
        list_display = ('nombre','apellido','correo','telefono')
        search_fields = ('nombre','apellido','correo')


Luego crear migraciones si hubo cambios:

    python manage.py makemigrations app_clientes
    python manage.py migrate

# 21 — Crear superusuario (opcional para probar admin)

    python manage.py createsuperuser


Sigue instrucciones (email/username/password).

# 22 — Estructura de carpetas completa (paso 29)

Al inicio crea toda la estructura (carpetas y archivos mostrados arriba). Puedes crear con VS Code o en terminal:

    mkdir -p app_clientes/templates/clientes
    touch app_clientes/templates/base.html app_clientes/templates/navbar.html app_clientes/templates/footer.html app_clientes/templates/inicio.html
    touch app_clientes/templates/clientes/{agregar_clientes.html,ver_clientes.html,actualizar_clientes.html,borrar_clientes.html}

# 23 — Estilo y colores (paso 28)

Usé colores suaves y moderno en base.html: fondo #f7f9fb, texto #213547, botones de Bootstrap. Si quieres paleta distinta dime preferencia.

# 24 — No usar forms.py (paso 23)

He respetado esto: los formularios son HTML simples y la vista procesa request.POST manualmente.

# 25 — Proyecto totalmente funcional (paso 30)

Con las instrucciones anteriores el proyecto debería:

crear la app,

tener CRUD básico para clientes,

templates listos,

admin con modelo Cliente,

servidor listo en puerto 8472.

# 26 — Ejecutar servidor en el puerto 8472 (paso 31) — recordatorio final

Asegúrate que el entorno .venv esté activado y ejecuta:

    python manage.py runserver 8472


Accede a http://127.0.0.1:8472/.

Archivos clave (resumen rápido)

app_clientes/models.py → contiene Cliente (código arriba).

app_clientes/views.py → contiene funciones: inicio_agencia, agregar_clientes, ver_clientes, actualizar_clientes, realizar_actualizacion_clientes, borrar_clientes.

app_clientes/urls.py → rutas para views.

backend_agencia/urls.py → incluye app_clientes.urls.

backend_agencia/settings.py → INSTALLED_APPS incluye 'app_clientes'.

Templates en app_clientes/templates/... como indiqué.
