# CloudTasks — cloudtask-equipo1-4

Aplicación web para la gestión de tareas personales o de un equipo de trabajo, desarrollada como parte del laboratorio desafío del **Seminario de Ingeniería de Software** de la **Universidad Icesi**.

## Descripción

CloudTasks permite:

* Registrarse e iniciar sesión con correo y contraseña.
* Crear una tarea.
* Visualizar las tareas registradas, propias de cada usuario.
* Marcar una tarea como completada.
* Eliminar una tarea.
* Mostrar el estado de cada tarea: pendiente / completada.
* Filtrar tareas por estado: todas / pendientes / completadas.
* Ordenar tareas por fecha de creación, fecha límite o prioridad.
* Filtrar tareas por día desde un mini calendario.
* Validar los datos introducidos por el usuario:

  * El título es obligatorio.
  * La fecha límite no puede ser anterior a hoy.

Cada tarea contiene:

`id`, `title`, `description`, `completed`, `created_at`, `deadline`, `priority`.

---

## Etapa 1 — Desarrollo local

En esta etapa, la aplicación se ejecuta completamente en el navegador.

Las tareas se almacenan temporalmente en `localStorage`, de modo que persisten entre recargas de página mientras se desarrolla localmente.

En la **Etapa 2**, este almacenamiento será reemplazado por **Supabase (PostgreSQL)** sin modificar la interfaz de usuario.

---

## Etapa actual: Etapa 2 — Persistencia y backend en la nube

La aplicación dejó de usar `localStorage` y ahora persiste la información en **Supabase (PostgreSQL administrado)**, con autenticación de usuarios y **Row Level Security (RLS)**.

Cada usuario solo puede ver, crear, editar y eliminar sus propias tareas.

### Implementado hasta ahora

#### Base de datos

* Tabla `tasks` en Supabase/PostgreSQL.
* Cada tarea tiene un `user_id` asociado a `auth.users`.

#### Seguridad

* RLS habilitado.
* Políticas de `select`, `insert`, `update` y `delete`.
* Las operaciones están restringidas al usuario autenticado mediante:

```sql
auth.uid() = user_id
```

#### Autenticación

* Registro e inicio de sesión mediante **Supabase Auth**.
* Implementado en `js/auth.js`.
* Se almacena el nombre y apellido del usuario en los metadatos (`user_metadata`).

#### CRUD

`js/app.js` implementa:

* Crear tareas.
* Leer tareas.
* Actualizar tareas, incluyendo el estado de completada.
* Eliminar tareas.
* Manejo de errores.
* Actualizaciones optimistas en la interfaz.

#### Interfaz

La interfaz cuenta con:

* Sidebar.
* Mini calendario para filtrar tareas por fecha.
* Pantalla de login y registro.
* Sistema de diseño propio implementado en `css/styles.css`.

### Pendiente dentro de la Etapa 2

* Despliegue del frontend en **Vercel**.
* Vinculación del proyecto con el repositorio de GitHub.
* Configuración de despliegue continuo.
* Verificación del flujo completo desde Internet y no únicamente desde el entorno local.

---

## Tecnologías

* **HTML5**
* **CSS3** — sistema de diseño propio.
* **Bootstrap 5** — utilizado únicamente para estilos y componentes visuales.
* **Bootstrap Icons**
* **JavaScript Vanilla** — sin frameworks.
* **Supabase** — autenticación y base de datos PostgreSQL administrada.
* **Git**
* **GitHub**

---

## Estructura del proyecto

```text
cloudtasks/
│
├── index.html          # Vista principal (lista de tareas, requiere sesión)
│
├── login.html          # Vista de autenticación (login/registro)
│
├── css/
│   └── styles.css      # Sistema de diseño CloudTasks
│
├── js/
│   ├── config.js       # Cliente de Supabase (URL + anon key)
│   ├── auth.js         # Lógica de login y registro
│   └── app.js          # Lógica de la aplicación (CRUD, filtros, calendario)
│
├── README.md
│
└── .gitignore
```

---

## Configuración de Supabase

### 1. Crear el proyecto

Crear un proyecto en [Supabase](https://supabase.com/).

### 2. Configurar la base de datos

Ejecutar el script SQL del esquema, incluyendo:

* Tabla `tasks`.
* Índices.
* Políticas de seguridad RLS.

El script debe ejecutarse desde:

**Project → SQL Editor**

### 3. Configurar la autenticación

Ingresar a:

**Authentication → Providers → Email**

Si se desea que el usuario quede con la sesión activa inmediatamente después de registrarse, se puede desactivar **Confirm email**.

Esta configuración es recomendada para el laboratorio.

### 4. Configurar las credenciales

Copiar la:

* `Project URL`
* Clave pública `anon` / `publishable key`

desde:

**Project Settings → API**

y colocarlas en:

```text
js/config.js
```

> ⚠️ **Importante:** en `js/config.js` solo debe utilizarse la clave pública `anon` / `publishable`, diseñada para exponerse en el navegador.
>
> **Nunca se debe utilizar la `service_role key` en el frontend.**

---

## Próximas etapas

### Etapa 2 — Cierre

* Desplegar el frontend en **Vercel**.
* Vincular Vercel con el repositorio de GitHub.
* Configurar el despliegue automático.
* Verificar el funcionamiento completo de CloudTasks desde Internet.

### Etapa 3 — Dominio, DNS y seguridad

Configuración de:

* Dominio personalizado.
* DNS.
* HTTPS/TLS.
* Proxy inverso mediante **Cloudflare**.

El flujo esperado será:

```text
https://cloudtasks.dominio.com
            ↓
       Cloudflare
            ↓
          Vercel
            ↓
       CloudTasks
```

├── login.html           # Vista de autenticación (login/registro)

├── css/

│   └── styles.css       # Sistema de diseño CloudTasks

├── js/

│   ├── config.js        # Cliente de Supabase (URL + anon key)

│   ├── auth.js           # Lógica de login y registro

│   └── app.js            # Lógica de la aplicación (CRUD, filtros, calendario)

├── README.md

└── .gitignore
Configuración de Supabase
Crear un proyecto en Supabase.
Ejecutar el script SQL del esquema (tabla tasks, índices y políticas de RLS) en Project → SQL Editor.
En Authentication → Providers → Email, desactivar "Confirm email" si se quiere que el usuario quede con sesión activa inmediatamente después de registrarse (recomendado para el laboratorio).
Copiar la Project URL y la clave pública (anon/publishable key) desde Project Settings → API y colocarlas en js/config.js.

⚠️ En js/config.js solo debe usarse la clave anon/public, diseñada para exponerse en el navegador. Nunca se debe usar la service_role key en el frontend.
Próximas etapas
Etapa 2 (cierre): Despliegue del frontend en Vercel, vinculado a GitHub con despliegue automático.
Etapa 3: Configuración de dominio, DNS, HTTPS/TLS y proxy inverso mediante Cloudflare, para que el acceso siga el flujo https://cloudtasks.dominio.com → Cloudflare → Vercel → CloudTasks.

