# Universidad Privada Domingo Savio
## Carrera de Ingeniería de Sistemas / Redes y Telecomunicaciones
### Programación Web II — Gestión 2026

---

# 🚀 TaskFlow — Full Stack Development
> **Proyecto Final:** Sistema de Gestión de Tareas Colaborativo y Seguro con Arquitectura Desacoplada (Flask + React + Supabase + Despliegue en la Nube).

---

## 📋 Ficha Técnica del Proyecto

* **Estudiante:** [Tu Nombre Completo]
* **Docente:** [Nombre del Docente]
* **Materia:** Programación Web II
* **Semestre / Módulo:** 2026
* **Repositorio GitHub:** `https://github.com/[TU_USUARIO]/taskflow`
* **URL Backend (Render):** `https://[tu-servicio].onrender.com`
* **URL Frontend (Cloudflare Pages):** `https://[tu-proyecto].pages.dev`
* **Proyecto Supabase ID:** `dhkekikrxljvawghcunc`

---

## 🏗️ 1. Arquitectura del Sistema

```
┌─────────────────────────────────────────────────────────┐
│              CLIENTE / FRONTEND                         │
│   React 18 + Vite + Tailwind CSS + React Router 6       │
│   Desplegado en: Cloudflare Pages (Global Edge CDN)     │
└───────────────────────────┬─────────────────────────────┘
                            │ HTTPS / JSON (REST API)
                            │ Headers: Authorization Bearer JWT
┌───────────────────────────▼─────────────────────────────┐
│              SERVIDOR / BACKEND                         │
│   Python 3.11+ / Flask / Gunicorn / Flask-CORS          │
│   Arquitectura MVC + Blueprints                         │
│   Desplegado en: Render (Web Service)                   │
└───────────────────────────┬─────────────────────────────┘
                            │ Supabase SDK / HTTPS
                            │ Service Role & Public Anon
┌───────────────────────────▼─────────────────────────────┐
│             BASE DE DATOS & AUTENTICACIÓN               │
│   Supabase (PostgreSQL en la Nube + Supabase Auth)      │
│   Row Level Security (RLS) habilitado                   │
└─────────────────────────────────────────────────────────┘
```

---

## 📂 2. Estructura de Directorios

```text
taskflow/
├── backend/
│   ├── app/
│   │   ├── __init__.py           # Inicialización y fábrica create_app()
│   │   ├── routes/
│   │   │   ├── __init__.py
│   │   │   ├── users.py          # Blueprints para /api/users
│   │   │   └── tasks.py          # Blueprints para /api/tasks
│   │   ├── services/
│   │   │   ├── user_service.py   # Lógica de negocio de usuarios
│   │   │   └── task_service.py   # Lógica de negocio de tareas
│   │   └── models/
│   │       └── schemas.py        # Esquemas de validación de datos
│   ├── .env                      # Variables de entorno locales
│   ├── .gitignore
│   ├── requirements.txt          # Dependencias (Flask, gunicorn, etc.)
│   └── run.py                    # Punto de entrada para ejecución
├── frontend/
│   ├── src/
│   │   ├── components/
│   │   │   ├── Button.jsx        # Componente reutilizable con variantes
│   │   │   ├── ProtectedRoute.jsx# Guardia de rutas autenticadas
│   │   │   ├── Modal.jsx         # Ventana modal para agregar/editar
│   │   │   └── Navbar.jsx        # Barra de navegación principal
│   │   ├── context/
│   │   │   └── AuthContext.jsx   # Estado global de usuario y token
│   │   ├── pages/
│   │   │   ├── Home.jsx          # Landing page de bienvenida
│   │   │   ├── Login.jsx         # Inicio de sesión
│   │   │   ├── Register.jsx      # Registro de nuevos usuarios
│   │   │   ├── Dashboard.jsx     # Panel con métricas y estadísticas
│   │   │   └── TaskList.jsx      # Tabla y CRUD de tareas
│   │   ├── App.jsx               # Enrutador principal
│   │   ├── main.jsx              # Entrada React DOM
│   │   └── index.css             # Estilos globales y directivas Tailwind
│   ├── .env                      # Variables de frontend (VITE_API_URL)
│   ├── tailwind.config.js
│   ├── vite.config.js
│   └── package.json
└── docs/                         # Documentación y capturas
    └── DOCUMENTACION.md
```

---

## 💻 3. Requisitos Previos e Instalaciones Locales

| Herramienta | Versión Mínima | Comando de Verificación |
| :--- | :--- | :--- |
| **Git** | 2.x.x | `git --version` |
| **Node.js** | 20.x.x (LTS) | `node --version` |
| **Python** | 3.11+ | `python --version` |
| **VS Code / IDE** | Última versión | `code --version` |

---

## 🗄️ 4. Base de Datos & Seguridad (Supabase)

### 4.1. Esquema SQL de Tablas

```sql
-- Tabla de usuarios
CREATE TABLE users (
    id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
    nombre VARCHAR(100) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    rol VARCHAR(20) DEFAULT 'usuario',
    created_at TIMESTAMPTZ DEFAULT NOW()
);

-- Tabla de tareas
CREATE TABLE tasks (
    id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
    user_id UUID REFERENCES users(id) ON DELETE CASCADE,
    titulo VARCHAR(200) NOT NULL,
    descripcion TEXT,
    estado VARCHAR(20) DEFAULT 'pendiente',     -- 'pendiente', 'en progreso', 'completada'
    prioridad VARCHAR(10) DEFAULT 'media',      -- 'baja', 'media', 'alta'
    fecha_limite DATE,
    created_at TIMESTAMPTZ DEFAULT NOW()
);
```

### 4.2. Políticas de Seguridad por Fila (Row Level Security - RLS)

```sql
-- 1. Habilitar RLS en la tabla
ALTER TABLE tasks ENABLE ROW LEVEL SECURITY;

-- 2. Política de Lectura (SELECT): Un usuario solo puede ver sus propias tareas
CREATE POLICY "Users can view own tasks"
ON tasks FOR SELECT
USING (auth.uid() = user_id);

-- 3. Política de Inserción (INSERT): Un usuario solo puede registrar tareas a su nombre
CREATE POLICY "Users can create own tasks"
ON tasks FOR INSERT
WITH CHECK (auth.uid() = user_id);

-- 4. Política de Actualización (UPDATE): Un usuario solo modifica sus tareas
CREATE POLICY "Users can update own tasks"
ON tasks FOR UPDATE
USING (auth.uid() = user_id);

-- 5. Política de Eliminación (DELETE): Un usuario solo elimina sus tareas
CREATE POLICY "Users can delete own tasks"
ON tasks FOR DELETE
USING (auth.uid() = user_id);
```

---

## 📝 5. Bitácora de Desarrollo por Sesiones

### Sesión 1: Entorno de Desarrollo + Git + GitHub
* **Objetivo:** Configuración de herramientas base, inicialización del repositorio Git y estructura inicial.
* **Comandos ejecutados:**
  ```bash
  mkdir taskflow && cd taskflow
  mkdir backend frontend docs
  git init
  git config --global user.name "Tu Nombre"
  git config --global user.email "tu@email.com"
  git add .
  git commit -m "Initial project structure"
  git remote add origin https://github.com/TU_USER/taskflow.git
  git push -u origin main
  ```
* **Estado / Notas:** [Completado / Pendiente]

---

### Sesión 2: Python + Flask (Primera API REST)
* **Objetivo:** Creación del entorno virtual de Python, instalación de Flask, Flask-CORS y primeros endpoints `/api/health` y `/api/users`.
* **Comandos ejecutados:**
  ```bash
  cd backend
  python -m venv venv
  # En Windows:
  venv\Scripts\activate
  # Instalar dependencias:
  pip install flask flask-cors python-dotenv
  ```
* **Prueba con Thunder Client / Postman:**
  * `GET http://localhost:5000/api/health` ➔ `{"status": "ok"}`
  * `GET http://localhost:5000/api/users` ➔ Lista de usuarios inicial en formato JSON.
* **Estado / Notas:** [Completado / Pendiente]

---

### Sesión 3: CRUD Completo + Estructura MVC con Blueprints
* **Objetivo:** Modularizar el backend utilizando el patrón MVC (Model-View-Controller) y Flask Blueprints para usuarios y tareas.
* **Rutas implementadas:**
  * `GET /api/tasks` — Listar tareas
  * `POST /api/tasks` — Crear nueva tarea con validación
  * `GET /api/tasks/<id>` — Detalle de una tarea
  * `PUT /api/tasks/<id>` — Modificar tarea existente
  * `DELETE /api/tasks/<id>` — Eliminar tarea
* **Estado / Notas:** [Completado / Pendiente]

---

### Sesión 4: Supabase como Base de Datos Cloud
* **Objetivo:** Conexión de Flask a Supabase mediante `supabase-py` y variables de entorno seguras.
* **Variables requeridas en `backend/.env`:**
  ```ini
  SUPABASE_URL=https://dhkekikrxljvawghcunc.supabase.co
  SUPABASE_KEY=tu_supabase_anon_o_service_key
  FLASK_ENV=development
  PORT=5000
  ```
* **Estado / Notas:** [Completado / Pendiente]

---

### Sesión 5: Autenticación y Autorización (JWT & Supabase Auth)
* **Objetivo:** Implementación de registro (`sign_up`), login (`sign_in_with_password`), obtención de token de acceso JWT y decorador `@token_required` para asegurar rutas privadas en la API.
* **Estado / Notas:** [Completado / Pendiente]

---

### Sesión 6: React + Vite + Setup de Tailwind CSS
* **Objetivo:** Inicialización del frontend con Vite, configuración de Tailwind CSS y creación de los primeros componentes funcionales y reutilizables.
* **Comandos ejecutados:**
  ```bash
  cd taskflow
  npm create vite@latest frontend -- --template react
  cd frontend
  npm install
  npm install -D tailwindcss postcss autoprefixer
  npx tailwindcss init -p
  ```
* **Estado / Notas:** [Completado / Pendiente]

---

### Sesión 7: Enrutamiento y Navegación (React Router)
* **Objetivo:** Configuración de `react-router-dom`, definición de rutas públicas (`/`, `/login`, `/register`) y privadas con `ProtectedRoute` (`/dashboard`, `/tasks`).
* **Estado / Notas:** [Completado / Pendiente]

---

### Sesión 8: Consumo de APIs & Estado Global (`AuthContext`)
* **Objetivo:** Implementar la Context API para gestionar el estado de autenticación, almacenamiento del token en `localStorage`, y consumo de endpoints mediante `fetch` con manejo de loading y errores.
* **Estado / Notas:** [Completado / Pendiente]

---

### Sesión 9: Dashboard y CRUD en React
* **Objetivo:** Construcción de la interfaz de usuario para el Dashboard con cards de estadísticas (Total Tareas, Pendientes, En Progreso, Completadas), tabla de tareas y modal interactivo para crear/editar.
* **Estado / Notas:** [Completado / Pendiente]

---

### Sesión 10: Diseño Responsive con Tailwind CSS
* **Objetivo:** Aplicación del principio Mobile-First y breakpoints adaptables (`sm:`, `md:`, `lg:`, `xl:`) para asegurar compatibilidad en dispositivos móviles, tablets y escritorios.
* **Estado / Notas:** [Completado / Pendiente]

---

### Sesión 11: Formularios Avanzados y Validación
* **Objetivo:** Formularios controlados en React con validación de campos obligatorios, formatos de correo, contraseñas y mensajes de retroalimentación visual (toasts/alertas).
* **Estado / Notas:** [Completado / Pendiente]

---

### Sesión 12: Despliegue del Backend en Render
* **Servicio:** Render Web Service
* **Root Directory:** `backend`
* **Build Command:** `pip install -r requirements.txt`
* **Start Command:** `gunicorn run:app`
* **Variables de Entorno en Render:**
  * `SUPABASE_URL`
  * `SUPABASE_KEY`
  * `PYTHON_VERSION=3.11.0`
* **Estado / URL Pública:** `https://[tu-backend].onrender.com`

---

### Sesión 13: Despliegue del Frontend en Cloudflare Pages
* **Servicio:** Cloudflare Pages
* **Framework Preset:** Vite
* **Root Directory:** `frontend`
* **Build Command:** `npm run build`
* **Build Output Directory:** `dist`
* **Variables de Entorno en Cloudflare:**
  * `VITE_API_URL=https://[tu-backend].onrender.com`
* **Estado / URL Pública:** `https://[tu-frontend].pages.dev`

---

## 🎯 6. Checklist de Evaluación Final (Sesión 14)

Marcar las casillas conforme se completen los requisitos:

- [ ] **Backend en Render funcionando:** Endpoint `/api/health` retorna `{ "status": "ok" }`.
- [ ] **Frontend en Cloudflare Pages funcionando:** Carga pública con dominio HTTPS activo.
- [ ] **Login y Registro completos:** Creación de nuevos usuarios y persistencia de sesión con JWT.
- [ ] **CRUD de tareas 100% operativo:** Crear, Leer, Actualizar y Eliminar tareas funcionando en la nube.
- [ ] **Seguridad RLS activa:** Cada usuario únicamente visualiza y manipula sus propias tareas.
- [ ] **Diseño Responsive:** Interfaz adaptable comprobada en móvil y navegador de escritorio.
- [ ] **README / Documentación actualizada:** Enlaces públicos funcionales, descripción y capturas adjuntas.
- [ ] **Evidencias & Capturas:** Imágenes adjuntas en la carpeta `docs/`.

---

## 📸 7. Capturas de Pantalla y Evidencias

*(Agrega aquí las capturas de pantalla de tu proyecto funcionando)*

### 7.1. Base de Datos en Supabase
> *Captura de la tabla `tasks` y `users` con RLS habilitado.*
<!-- ![Supabase DB](docs/supabase-db.png) -->

### 7.2. Interfaz de Inicio de Sesión y Registro
> *Formulario de acceso con validaciones.*
<!-- ![Login](docs/login.png) -->

### 7.3. Dashboard con Estadísticas
> *Panel principal con conteo de tareas por estado.*
<!-- ![Dashboard](docs/dashboard.png) -->

### 7.4. Gestión de Tareas (CRUD y Modal)
> *Tabla interactiva y modal de edición.*
<!-- ![Tareas](docs/tasks.png) -->

### 7.5. Despliegue en Render y Cloudflare Pages
> *Paneles de control con los builds completados exitosamente.*
<!-- ![Render](docs/render.png) -->
<!-- ![Cloudflare](docs/cloudflare.png) -->
