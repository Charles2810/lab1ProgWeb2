# Definición del Alcance — Mini Task Manager

**Documento:** `docs/alcance.md`  
**Laboratorio:** 01 — Arquitectura Primero  
**Materia:** Programación Web II (UPDS)  
**Estudiante:** Charles  
**Repositorio Oficial:** [https://github.com/Charles2810/lab1ProgWeb2](https://github.com/Charles2810/lab1ProgWeb2)  

---

## 1. Identificación del Problema Central

En el entorno cotidiano de estudio y trabajo, las personas y equipos pequeños a menudo sufren de dispersión cognitiva debido a la acumulación de pendientes desorganizados y al uso de herramientas excesivamente complejas (con demasiadas configuraciones, campos innecesarios y curvas de aprendizaje pronunciadas).

El **Mini Task Manager** resuelve este problema ofreciendo una solución ligera, rápida y minimalista que permite registrar, visualizar y actualizar el estado de tareas pendientes inmediatas sin fricción operativa. Sirve además como el **andamio arquitectónico (scaffolding)** fundamental para afianzar el desacoplamiento en 5 capas antes de incorporar complejidad tecnológica.

---

## 2. Usuario Objetivo

* **Perfil Principal:** Estudiantes, desarrolladores y profesionales que necesitan un registro rápido y persistente de sus tareas diarias o compromisos del sprint.
* **Necesidad Clave:** Abrir la aplicación, ver de un vistazo qué está pendiente o terminado, añadir una tarea en menos de tres segundos, marcarla como completada al finalizar o removerla si fue descartada.

---

## 3. Acciones Clave del MVP (Máximo 4)

Cumpliendo con la restricción metodológica del Laboratorio 1 (enfoque en lo esencial), el MVP se delimita estrictamente a las siguientes **4 acciones operativas**:

| # | Acción Clave | Descripción Funcional | Parámetros / Entrada |
|---|---|---|---|
| **1** | **Ver lista de tareas** | Obtiene y lista de forma ordenada todas las tareas registradas con su estado actual. | Ninguno (lectura global o por estado) |
| **2** | **Crear una tarea** | Registra una nueva tarea en el sistema con título descriptivo y estado inicial `pending`. | `title` (texto obligatorio, no vacío) |
| **3** | **Cambiar estado de tarea** | Alterna o actualiza el estado de una tarea existente entre `pending` y `done`. | `id` (identificador único), nuevo `status` |
| **4** | **Eliminar una tarea** | Remueve de forma definitiva una tarea existente del sistema. | `id` (identificador único) |

---

## 4. Lista de Exclusiones Explícitas (Filosofía YAGNI)

> *"You Aren't Gonna Need It" (No lo vas a necesitar): Si no es estrictamente indispensable para validar la arquitectura y el flujo central del MVP, queda fuera.*

Para evitar el sobre-diseño y la sobre-ingeniería en esta fase, quedan expresamente excluidas las siguientes características:

* ❌ **Sin autenticación ni gestión de roles avanzada:** El MVP inicial no implementa OAuth, contraseñas hash complejas ni perfiles de usuario; se enfoca en el flujo puro de datos de la entidad `Task`.
* ❌ **Sin categorización jerárquica ni tags:** No se soportan etiquetas múltiples, carpetas, proyectos anidados ni prioridades complejas en la primera iteración.
* ❌ **Sin subtareas (checklist interno):** Cada tarea es una unidad atómica; no se permiten árboles de tareas secundarias.
* ❌ **Sin notificaciones ni recordatorios automáticos:** No se incluyen envíos de correo, webhooks ni notificaciones push de escritorio.
* ❌ **Sin búsqueda full-text ni ordenamientos multifactoriales:** La visualización se limita al ordenamiento cronológico por fecha de creación.
* ❌ **Sin interfaz rica ni dependencias pesadas de UI:** No se usarán librerías pesadas de componentes UI de terceros que inflen el presupuesto de transferencia.
