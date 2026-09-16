# CloudTasks — cloudtask-equipo1-4

Aplicación web para la gestión de tareas personales o de un equipo de trabajo, desarrollada como parte del laboratorio desafío del **Seminario de Ingeniería de Software** de la **Universidad Icesi**.

**Repositorio:** https://github.com/las-chicas-ingesoft/cloudtask-equipo1-4
**Aplicación desplegada:** https://cloudtask-equipo1-4-ieudbxbmq-iisaaaa.vercel.app/index.html (URL provisional de Vercel; se reemplazará por el dominio propio en cuanto Cloudflare termine de propagarlo)

https://cloudtask-equipo1-4.vercel.app/index.html

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

`id`, `title`, `description`, `completed`, `created_at`, `deadline`, `priority`, `user_id`.

---

## Estado del proyecto

| Etapa | Estado |
|---|---|
| Etapa 1 — Desarrollo local (HTML/CSS/JS, Git) | ✅ Completa |
| Etapa 2 — GitHub | ✅ Completa |
| Etapa 2 — Supabase (persistencia, Auth, RLS) | ✅ Completa |
| Etapa 2 — Despliegue en Vercel | ✅ Completa |
| Etapa 3 — Cloudflare (DNS/HTTPS) | 🟡 En proceso — dominio solicitado, pendiente de propagación de nameservers |

---

## Etapa 1 — Desarrollo local

La primera versión de CloudTasks se ejecutaba completamente en el navegador. Las tareas se almacenaban temporalmente en `localStorage`, de modo que persistían entre recargas de página mientras se desarrollaba localmente, sin depender todavía de un backend. Esta etapa permitió construir y probar la interfaz y la lógica de la aplicación (crear, visualizar, completar y eliminar tareas) antes de introducir persistencia real.

## Etapa 2 — Persistencia y backend en la nube

La aplicación dejó de usar `localStorage` y ahora persiste la información en **Supabase (PostgreSQL administrado)**, con autenticación de usuarios y **Row Level Security (RLS)**. Cada usuario solo puede ver, crear, editar y eliminar sus propias tareas.

#### Base de datos

* Tabla `tasks` en Supabase/PostgreSQL.
* Cada tarea tiene un `user_id` asociado a `auth.users`.
* Índices sobre `created_at` y `user_id` para mejorar el rendimiento de las consultas.

#### Seguridad

* RLS habilitado.
* Políticas independientes de `select`, `insert`, `update` y `delete`.
* Las operaciones están restringidas al usuario autenticado mediante:

```sql
auth.uid() = user_id
```

#### Autenticación

* Registro e inicio de sesión mediante **Supabase Auth**.
* Implementado en `js/auth.js`.
* Se almacena el nombre y apellido del usuario en los metadatos (`user_metadata`), sin necesidad de una tabla `profiles` adicional.

#### CRUD

`js/app.js` implementa:

* `loadTasks()` — consulta las tareas del usuario autenticado.
* `createTask()` — crea una nueva tarea.
* `toggleTaskCompleted()` — actualiza el estado de una tarea, con actualización optimista de la interfaz.
* `deleteTask()` — elimina una tarea, revirtiendo el cambio en la interfaz si la operación falla en el servidor.
* Manejo de errores en cada operación.

#### Interfaz

* Sidebar con navegación y datos del usuario.
* Mini calendario para filtrar tareas por fecha.
* Pantalla de login y registro.
* Sistema de diseño propio, tema **"Despegue"** (paleta de cielo/azules, avión animado y pantalla de carga), implementado en `css/styles.css` y `css/cloudtasks-avion.css`.

#### Despliegue

* Frontend desplegado en **Vercel**, vinculado al repositorio de GitHub.
* Cada `push` a la rama `main` genera automáticamente un nuevo despliegue en producción.

## Etapa 3 — Dominio, DNS y seguridad (en proceso)

Se está incorporando **Cloudflare** al flujo de acceso de la aplicación. El dominio fue solicitado el 10/09/2026 y, a la fecha, sigue en proceso de propagación de nameservers. En cuanto quede activo, se configurará el registro DNS hacia Vercel y se activará HTTPS/TLS, para que el acceso siga el flujo:

```text
https://cloudtasks.dominio.com
            ↓
       Cloudflare  (DNS y HTTPS/TLS)
            ↓
          Vercel   (frontend)
            ↓
       Supabase    (backend y datos)
```

---

## Tecnologías

* **HTML5**
* **CSS3** — sistema de diseño propio ("Despegue").
* **Bootstrap 5** — utilizado únicamente para estilos y componentes visuales.
* **Bootstrap Icons**
* **JavaScript Vanilla** — sin frameworks.
* **Supabase** — autenticación y base de datos PostgreSQL administrada, con RLS.
* **Vercel** — despliegue y hosting del frontend.
* **Cloudflare** — DNS y HTTPS (en proceso).
* **Git** y **GitHub** — control de versiones.

---
## Estructura del proyecto

```text
cloudtasks/
│
├── index.html                  # Vista principal (lista de tareas, requiere sesión)
│
├── login.html                  # Vista de autenticación (login/registro)
│
├── css/
│   ├── styles.css              # Sistema de diseño base "Despegue"
│   └── cloudtasks-avion.css    # Capa visual adicional (avión animado, splash screen)
│
├── js/
│   ├── config.js                # Cliente de Supabase (URL + anon key)
│   ├── auth.js                  # Lógica de login y registro
│   └── app.js                   # Lógica de la aplicación (CRUD, filtros, calendario)
│
├── img/
│   ├── plane-mark.png           # Ícono de marca
│   └── plane-climb.png          # Ilustración del avión animado
│
├── README.md
│
└── .gitignore

 
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
 
Si se desea que el usuario quede con la sesión activa inmediatamente después de registrarse, se puede desactivar **Confirm email**. Esta configuración es recomendada para el laboratorio.
 
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
 
## Despliegue en Vercel
 
1. Vincular el repositorio `las-chicas-ingesoft/cloudtask-equipo1-4` de GitHub con una cuenta de Vercel.
2. Configurar el proyecto como sitio estático (sin framework).
3. Cada `push` a la rama `main` genera automáticamente un nuevo despliegue en producción.
---
 
## Pruebas
 
El equipo documentó pruebas funcionales y de integración (Supabase, Vercel, Cloudflare) y pruebas de validación/manejo de errores, con capturas de evidencia. Entre los casos cubiertos:
 
* Creación, actualización y eliminación de tareas, verificadas directamente en la base de datos.
* Persistencia de datos entre sesiones.
* Aislamiento de datos entre usuarios (RLS).
* Validación de formularios (título vacío, fecha límite en el pasado).
* Manejo de credenciales incorrectas y registro con correo duplicado.
* Comportamiento ante pérdida de conexión a internet (recuperación con `try/catch`).
Ver el documento de pruebas del equipo para el detalle completo, capturas y resultados.
 
---
 
## Próximos pasos
 
* Completar la configuración de Cloudflare en cuanto el dominio termine de propagarse: registro DNS hacia Vercel, activación de HTTPS/TLS, y validación del flujo completo `usuario → Cloudflare → Vercel → CloudTasks`.
* Reemplazar la URL provisional de Vercel por el dominio propio una vez esté activo.
  
