CloudTasks — cloudtask-equipo1-4
Aplicación web para la gestión de tareas personales o de un equipo de trabajo, desarrollada como parte del laboratorio desafío del Seminario de Ingeniería de Software (Universidad Icesi).
Descripción
CloudTasks permite:

Registrarse e iniciar sesión con correo y contraseña.
Crear una tarea.
Visualizar las tareas registradas (propias de cada usuario).
Marcar una tarea como completada.
Eliminar una tarea.
Mostrar el estado de cada tarea (pendiente / completada).
Filtrar tareas por estado (todas / pendientes / completadas).
Ordenar tareas por fecha de creación, fecha límite o prioridad.
Filtrar tareas por día desde un mini calendario.
Validar los datos introducidos por el usuario (título obligatorio, fecha límite no puede ser anterior a hoy).

Cada tarea contiene: id, title, description, completed, created_at, deadline, priority.

Etapa 1 — Desarrollo local

En esta etapa la aplicación se ejecuta completamente en el navegador. Las tareas se almacenan
temporalmente en `localStorage`, de modo que persisten entre recargas de página mientras se desarrolla
localmente. En la Etapa 2 este almacenamiento será reemplazado por Supabase (PostgreSQL) sin modificar
la interfaz de usuario


Etapa actual: Etapa 2 — Persistencia y backend en la nube (en curso)
La aplicación dejó de usar localStorage y ahora persiste la información en Supabase (PostgreSQL administrado), con autenticación de usuarios y Row Level Security (RLS): cada usuario solo puede ver, crear, editar y eliminar sus propias tareas.

Lo implementado hasta ahora en esta etapa:

Base de datos: tabla tasks en Supabase/PostgreSQL con user_id asociado a auth.users.
Seguridad: RLS habilitado con políticas de select, insert, update y delete restringidas al usuario autenticado (auth.uid() = user_id).
Autenticación: registro e inicio de sesión con Supabase Auth (js/auth.js), guardando nombre y apellido en los metadatos del usuario (user_metadata).
CRUD desde JavaScript: js/app.js implementa crear, leer, actualizar (completado) y eliminar tareas contra Supabase, con manejo de errores y actualizaciones optimistas en la interfaz.
Interfaz: rediseño con sidebar, mini calendario para filtrar tareas por fecha, pantalla de login/registro y sistema de diseño propio (css/styles.css).

Pendiente dentro de la Etapa 2:

Despliegue del frontend en Vercel (vinculación con el repositorio de GitHub y despliegue continuo).
Verificación del flujo completo desde Internet (no solo en local).
Tecnologías
HTML5
CSS3 (sistema de diseño propio) + [Bootstrap 5] (https://getbootstrap.com/) (solo para estilos/componentes visuales))   + Bootstrap Icons
JavaScript (vanilla, sin frameworks)
Supabase (Auth + base de datos PostgreSQL administrada)
Git y GitHub para control de versiones
Estructura del proyecto
cloudtasks/

├── index.html          # Vista principal (lista de tareas, requiere sesión)

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

