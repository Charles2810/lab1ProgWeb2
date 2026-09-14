# Reflexión Metacognitiva y Decisión Tecnológica Justificada

**Documento:** `docs/reflexiones/lab-01-Charles.md`  
**Laboratorio:** 01 — Arquitectura Primero  
**Materia:** Programación Web II (UPDS)  
**Estudiante:** Charles  

---

## 1. Eje 1: Dimensión Técnica y de Equipo (5 líneas)

1. En este laboratorio comprendí que la arquitectura desacoplada en 5 capas evita que el frontend o el controlador asuman tareas ajenas como escribir sentencias SQL directas.
2. Definimos el stack técnico basado en React con Vite para el cliente, Express en Node.js para la API y PostgreSQL gestionado en Supabase como motor de datos.
3. Se evaluó la alternativa de utilizar Python con Flask en el backend, la cual es válida pero se priorizó la coherencia del ecosistema JavaScript de la diapositiva 16.
4. La decisión del equipo fue estructurar el backend en módulos explícitos de rutas, servicios de negocio y repositorios parametrizados para cumplir la regla de vecindad.
5. Este desacoplamiento garantiza que cada responsabilidad pueda probarse de forma aislada sin levantar todo el sistema, acotando el alcance de cualquier fallo.

---

## 2. Eje 2: Dimensión de IA y Sostenibilidad (5 líneas)

1. La inteligencia artificial propuso inicialmente un diseño sobre-estructurado con frameworks de Clean Architecture y Prisma ORM, agregando dependencias innecesarias.
2. Rechazamos conscientemente dicha propuesta aplicando el principio YAGNI y optamos por consultas SQL nativas parametrizadas para dominar la prevención de inyecciones.
3. Redujimos el consumo de recursos al documentar toda la arquitectura en Markdown con diagramas Mermaid (~2 KB) en lugar de depender de plataformas pesadas como Figma.
4. En accesibilidad acordamos que la API debe rechazar códigos de error crípticos y devolver mensajes descriptivos vinculables a los campos de formulario del cliente.
5. Estos principios y fronteras de responsabilidad se reutilizan de forma íntegra e inalterable en el MVP del Mini Task Manager y en las futuras fases del proyecto.

---

## 3. Decisión Tecnológica Justificada

### Pregunta Conductora:
> **"¿Qué es aquello que permanece inalterable aunque decidas cambiar de tecnología en el futuro?"**

### Justificación:
Aquello que permanece inalterable son **las 5 responsabilidades conceptuales del sistema y la definición estricta de sus fronteras**:
* La capa de **Presentación** siempre tendrá la exclusiva responsabilidad de capturar interacción y renderizar información, sin albergar lógica de negocio ni sentencias SQL.
* La capa de **API** siempre actuará como un filtro y traductor de protocolos de entrada, validando sintaxis sin asumir las políticas complejas del dominio.
* La capa de **Lógica de Negocio** siempre gobernará las reglas operacionales e invariantes del sistema, manteniéndose agnóstica de si la petición vino por HTTP, gRPC o una terminal CLI.
* La capa de **Acceso a Datos (Repository)** siempre será el único componente con permiso para dialogar con el motor físico, aislando el dialecto y los queries del resto de la aplicación.
* La capa de **Base de Datos** siempre velará por la integridad relacional, transaccionalidad y persistencia duradera de los datos.

Aunque en el futuro se decida reemplazar Express por FastAPI, React por Svelte, o PostgreSQL por MySQL, **el flujo unidireccional y las fronteras entre estas 5 capas permanecerán intactas**, preservando la salud arquitectónica del software.
