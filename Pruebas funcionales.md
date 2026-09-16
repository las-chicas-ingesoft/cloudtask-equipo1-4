# Documentación de pruebas funcionales — CloudTasks

**Equipo:** cloudtask-equipo1-4
**Integrantes:** Verónica Flor Escobar, Isabella Vergara Valencia, Valentina Tangarife Rincón, Dayanna Fernández
**Fecha de ejecución:** 15/09/2026
**URL de la aplicación:** https://cloudtask-equipo1-4-ieudbxbmq-iisaaaa.vercel.app/index.html (provicional mientras nos dan el dominio)
**Repositorio:** las-chicas-ingesoft/ cloudtask-equipo-1-4

---

## 1. Entorno de pruebas

| Elemento | Detalle |
|---|---|
| Navegador y versión | Chrome 152 |
| Sistema operativo | Windows |
| Frontend desplegado en | Vercel |
| Backend / base de datos | Supabase (PostgreSQL administrado) |
| DNS y TLS | Cloudflare |
| Usuario de prueba | isabellavergaravalencia@gmail.com |

---

## 2. Pruebas de integración definidas por el docente

Estas son las pruebas de la hoja "Pruebas integración" de la rúbrica. Llenar las columnas **Resultado obtenido**, **Cumple** y **Evidencia**.

### 2.1 Cloudflare

| ID | Prueba | Procedimiento | Resultado esperado | Resultado obtenido | Cumple | Evidencia |
|---|---|---|---|---|---|---|
| CF-01 | Acceso por dominio | Abrir el dominio/subdominio en el navegador. | La aplicación carga correctamente. | | Sí / No / **Bloqueado** | Captura #1 capturas/dominio.jpeg |
| CF-02 | DNS | Revisar el panel de DNS en Cloudflare e identificar el registro creado. | El dominio resuelve al destino configurado (Vercel). | | | Captura #2 capturas/cloudflaredom.jpg |
| CF-03 | HTTPS/TLS | Abrir la URL con `https://` y revisar el candado del navegador.jpg | HTTPS funcional, certificado válido. | | | Captura #3 capturas/pagweb. |
| CF-04 | Cloudflare → Vercel | Revisar el dominio registrado en ambos paneles. | La petición llega al deployment correcto. | | | ![Captura #4](capturas/vercel.jpg) |

> **Nota del equipo:** el dominio fue solicitado el 10/09/2026 y a la fecha de entrega no ha sido asignado, por lo que las pruebas CF-01 a CF-04 no pudieron ejecutarse por una dependencia externa al equipo. Se adjunta la evidencia de la solicitud. La configuración quedó preparada para aplicarse apenas se reciba el dominio.

### 2.2 Vercel

| ID | Prueba | Procedimiento | Resultado esperado | Resultado obtenido | Cumple | Evidencia |
|---|---|---|---|---|---|---|
| VE-01 | Deployment | Revisar el proyecto y el último deployment en Vercel. | Deployment en estado "Ready" / exitoso. | | | Captura #1 capturas/vercel.jpg |
| VE-02 | GitHub → Vercel | Hacer un cambio pequeño, `git push` a `main` y revisar Vercel. | Se genera automáticamente un nuevo deployment. | | | Captura #2 capturas/deployv.jpg |

> Para VE-02 conviene registrar el hash del commit y la hora del deployment, así se ve que uno disparó al otro.

### 2.3 Supabase

| ID | Prueba | Procedimiento | Resultado esperado | Resultado obtenido | Cumple | Evidencia |
|---|---|---|---|---|---|---|
| SU-01 | Crear y verificar en BD | Crear una tarea desde la app y buscarla en Table Editor → `tasks`. | El registro aparece en PostgreSQL con su `user_id`. | | | Captura #__ |
| SU-02 | Actualizar | Marcar la tarea como completada, recargar la página y revisar la BD. | El campo `completed` queda en `true` y coincide app ↔ BD. | | | Captura #__ |
| SU-03 | Eliminar | Eliminar la tarea desde la app y revisar la tabla. | El registro desaparece de `tasks`. | | | Captura #__ |
| SU-04 | Persistencia | Crear/modificar tareas, cerrar el navegador por completo y volver a entrar. | Los datos permanecen tal como se dejaron. | | | Captura #__ |

### 2.4 Arquitectura (sustentación oral)

| ID | Prueba | Procedimiento | Resultado esperado | Respuesta preparada por el equipo |
|---|---|---|---|---|
| AR-01 | Explicar arquitectura | Explicar el flujo Usuario → Cloudflare → Vercel → Supabase → PostgreSQL. | Se explican responsabilidades de cada capa y el flujo de datos. | _(resumir aquí, apoyarse en el diagrama de arquitectura)_ |
| AR-02 | Falla de PostgreSQL | Responder qué ocurre si PostgreSQL no responde. | Se identifica el impacto y cómo lo maneja la aplicación. | _(ver sección 4)_ |

---

## 3. Pruebas de validación y manejo de errores

La rúbrica exige cubrir **casos normales y de error**. Estas pruebas demuestran que la aplicación valida entradas y responde bien ante fallos.

| ID | Prueba | Procedimiento | Resultado esperado | Resultado obtenido | Cumple | Evidencia |
|---|---|---|---|---|---|---|
| ER-01 | Título vacío | Enviar el formulario de nueva tarea dejando el título en blanco (o solo espacios). | La tarea no se crea; el campo se marca como inválido. | | | Captura #__ |
| ER-02 | Fecha límite en el pasado | Crear una tarea con fecha límite anterior a hoy. | La tarea no se crea; el campo de fecha se marca como inválido. | | | Captura #__ |
| ER-03 | Credenciales incorrectas | Intentar iniciar sesión con una contraseña errónea. | Se muestra un mensaje de error traducido y no se inicia sesión. | | | Captura #__ |
| ER-04 | Registro con correo duplicado | Registrarse con un correo que ya existe. | Se muestra un mensaje de error claro y no se crea el usuario. | | | Captura #__ |
| ER-05 | Aislamiento entre usuarios (RLS) | Iniciar sesión con el usuario A, crear tareas; cerrar sesión, entrar con el usuario B. | El usuario B no ve ninguna tarea del usuario A. | | | Captura #__ |
| ER-06 | Acceso sin sesión | Abrir `index.html` directamente sin haber iniciado sesión. | Se redirige a la pantalla de login; no se muestran tareas. | | | Captura #__ |
| ER-07 | Pérdida de conexión | Activar el modo offline del navegador (DevTools → Network → Offline) e intentar crear una tarea. | La app muestra un mensaje de error y no se cuelga. | | | Captura #__ |

> ER-05 es especialmente valiosa: demuestra que las políticas de Row Level Security (`auth.uid() = user_id`) funcionan de verdad y no solo están escritas en el SQL.

---

## 4. Análisis: qué pasa si PostgreSQL no responde (AR-02)

Puntos para desarrollar en el informe y en la sustentación:

- El frontend en Vercel sigue sirviéndose normalmente, porque es contenido estático: la página carga, pero sin datos.
- Las llamadas del cliente de Supabase devuelven un objeto con `error`, que la aplicación captura en las funciones de carga, creación, actualización y eliminación de tareas.
- En esos casos se muestra un mensaje al usuario mediante el componente de estado, en lugar de dejar la interfaz en blanco o congelada.
- Las actualizaciones optimistas de la interfaz deben revertirse cuando la operación falla, para que lo que ve el usuario no contradiga lo que hay en la base de datos.
- Conclusión: la base de datos es el punto único de falla de la arquitectura; el resto de capas degrada de forma controlada.

---

## 5. Resumen de resultados

| Categoría | Ejecutadas | Cumplen | No cumplen | Bloqueadas |
|---|---|---|---|---|
| Cloudflare (CF) | | | | |
| Vercel (VE) | | | | |
| Supabase (SU) | | | | |
| Errores y validaciones (ER) | | | | |
| **Total** | | | | |

---

## 6. Defectos encontrados y correcciones

La rúbrica valora explícitamente identificar defectos y **documentar cómo se corrigieron**. Registrar aquí cualquier fallo detectado durante las pruebas, aunque ya esté resuelto.

| N° | Prueba donde se detectó | Descripción del defecto | Causa | Corrección aplicada | Commit |
|---|---|---|---|---|---|
| 1 | | | | | |
| 2 | | | | | |

---

## 7. Anexo de capturas

| N° | Descripción | Prueba asociada |
|---|---|---|
| 1 | | |
| 2 | | |
| 3 | | |
