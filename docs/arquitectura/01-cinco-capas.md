# Fundamentos de Arquitectura: Las 5 Responsabilidades

**Documento:** `docs/arquitectura/01-cinco-capas.md`  
**Laboratorio:** 01 — Arquitectura Primero  
**Materia:** Programación Web II (UPDS)  
**Estudiante:** Charles  

---

## 1. La Analogía de la Casa sin Plano

Construir software sin arquitectura equivale a construir una vivienda sin planos: comprar ladrillos, cemento y ventanas y empezar a colocarlos de forma impulsiva. Al cabo de pocos días nos encontramos con que:
* El baño quedó sin tubería de desagüe.
* La cocina no tiene ventilación ni ventanas.
* La escalera choca directamente con una viga estructural.

En el desarrollo web ocurre lo mismo cuando se aprende o implementa tecnología de manera fragmentada (`React`, `Node.js`, `PostgreSQL`) sin comprender la estructura sistémica general: se produce código desordenado, frágil, imposible de probar y costoso de mantener.

---

## 2. El Problema Concreto: Una Aplicación SIN Capas

Obsérvese el siguiente patrón monolítico anti-arquitectura comúnmente encontrado en principiantes:

```javascript
// CÓDIGO MONOLÍTICO Y ACOPLADO (Anti-patrón)
app.post('/tasks', (req, res) => {
  const title = req.body.title;
  if (title.length > 0) {                             // (a) Validación de formato
    if (title.includes('grosería')) {                 // (b) Regla de negocio en el handler HTTP
      return res.status(400).send('Contenido inapropiado');
    }
    const sql = `INSERT INTO tasks (title) VALUES ('${title}')`; // (c, d) SQL directo + Concatenación (SQL Injection)
    db.query(sql, (err, rows) => {
      res.send(`<li>${rows[0].title}</li>`);          // (e) HTML acoplado directamente en la API
    });
  }
});
```

### Fallas Críticas Identificadas:
1. **(a) Validación de Formato Mezclada:** La verificación estructural básica (`title` existe y es string no vacío) está ligada al flujo completo.
2. **(b) Regla de Negocio en el Controlador:** Las políticas operativas (como moderación de contenido o restricciones de dominio) pertenecen a la Capa de Negocio, no a un manejador de transporte HTTP.
3. **(c y d) Acceso a Datos y Concatenación SQL:** El controlador construye consultas SQL directamente y concatena valores de entrada, exponiendo el sistema a inyecciones SQL críticas ([OWASP A03:2021](https://owasp.org/Top10/A03_2021-Injection/)).
4. **(e) Presentación HTML dentro de la API:** La API devuelve etiquetas HTML (`<li>`), destruyendo la reutilización para clientes móviles, consolas o interfaces modernas desacopladas.

---

## 3. Matriz de las 5 Responsabilidades

La regla de oro de esta arquitectura es: **"Cada capa tiene un solo trabajo y únicamente habla con su vecina inmediata."**

| Nivel | Capa | QUÉ HACE | QUÉ NO HACE | HABLA CON |
|:---:|---|---|---|---|
| **01** | **Frontend** *(Cliente)* | • Representa visualmente la interfaz.<br>• Captura interacciones del usuario (clics, texto).<br>• Envía peticiones HTTP a la API y muestra estados de carga y error. | • No contiene reglas de negocio operacionales.<br>• No ejecuta sentencias SQL.<br>• No persiste el estado verdadero del sistema. | **Usuario** (hacia arriba vía pantalla) y **API** (hacia abajo vía HTTP/JSON). |
| **02** | **API** *(Controlador/Ruta)* | • Recibe peticiones HTTP (`GET`, `POST`, etc.).<br>• Valida el formato y tipo de datos entrantes.<br>• Mapea códigos de estado HTTP (`200`, `201`, `400`, `404`, `500`). | • No decide reglas de negocio complejas.<br>• No ejecuta consultas directas ni arma SQL. | **Frontend** (hacia arriba) y **Lógica de Negocio** (hacia abajo). |
| **03** | **Lógica de Negocio** *(Service)* | • Aplica las políticas del dominio (qué se permite, qué no, en qué orden).<br>• Normaliza datos y coordina los flujos operativos. | • Desconoce el protocolo de transporte (no sabe si vino por HTTP, CLI o WebSocket).<br>• Desconoce el origen físico de los datos (PostgreSQL, memoria, archivo). | **API** (hacia arriba) y **Acceso a Datos** (hacia abajo vía interfaz). |
| **04** | **Acceso a Datos** *(Repository)* | • Traduce operaciones abstractas (*"guardar tarea"*, *"listar tareas"*) en sentencias de base de datos.<br>• Aísla los detalles y dialecto del motor de base de datos. | • No toma decisiones de negocio ni valida reglas operacionales. | **Lógica de Negocio** (hacia arriba) y **Base de Datos** (hacia abajo vía driver/SQL). |
| **05** | **Base de Datos** *(Motor)* | • Almacena los datos de forma persistente y atómica.<br>• Garantiza integridad y consistencia (tipos, claves primarias, unicidad, claves foráneas). | • No ejecuta lógica de aplicación ni decide flujos de usuario.<br>• No renderiza vistas. | Exclusivamente con la capa de **Acceso a Datos**. |

---

## 4. Las 3 Preguntas que Definen una Arquitectura

Para auditar y validar cada capa del sistema, siempre debemos formularnos tres preguntas obligatorias:
1. **¿Qué problema resuelve esta capa?** (Razón de existir).
2. **¿Qué responsabilidad tiene — y cuál definitivamente NO tiene?** (Frontera estricta).
3. **¿Cómo le pasa el trabajo a la siguiente capa?** (Contrato e interfaz).

---

## 5. Beneficios del Desacoplamiento

```
┌─────────────────┐   ┌─────────────────┐   ┌─────────────────┐   ┌─────────────────┐
│ Se entiende sola│   │  Se prueba sola │   │  Se cambia sola │   │ Errores acotados│
│ Menor carga     │   │ Pruebas unita-  │   │ Cambios confina-│   │ Fallos no se    │
│ mental por capa │   │ rias aisladas   │   │ dos a una capa  │   │ propagan        │
└─────────────────┘   └─────────────────┘   └─────────────────┘   └─────────────────┘
```

* **Se entiende sola:** El desarrollador puede razonar sobre la lógica de negocio sin preocuparse por los selectores CSS o las sentencias SQL.
* **Se prueba sola:** Se pueden ejecutar pruebas unitarias sobre las reglas de negocio usando repositorios simulados (mocks) sin levantar un servidor ni una base de datos real.
* **Se cambia sola:** Si se migra de PostgreSQL a MySQL, solo se modifica el módulo de Acceso a Datos; el Frontend y la Lógica quedan intactos. Si se cambia React por Vue, el backend ni se entera.
* **Errores acotados:** Un error visual en el cliente jamás corromperá la consistencia transaccional de la base de datos.

---

## 6. Mapeo Tecnológico del Curso

| Responsabilidad | Implementación Oficial en este Curso | Alternativas Válidas de la Industria |
|---|---|---|
| **Frontend** | React + Vite | Vue.js, Svelte, HTML5 + Vanilla JS |
| **API** | Node.js + Express | Fastify, Python/FastAPI, Python/Flask |
| **Lógica de Negocio** | Módulos JS (`taskService.js`) | Servicios Python, Clases Java, Go services |
| **Acceso a Datos** | Patrón Repository con cliente `pg` / Supabase SDK | Prisma, Sequelize, Drizzle, SQLAlchemy |
| **Base de Datos** | PostgreSQL (Gestionado en Supabase Cloud) | PostgreSQL propio, MySQL, SQLite |
| **Empaquetado** | Docker + Docker Compose | Podman, ejecución directa en Host |

---

## 7. Resolución de los Desafíos de Pensamiento Arquitectónico

### Desafío 01: ¿Qué pasa si ponemos SQL en el componente React?
* **Seguridad Crítica:** Expondría las credenciales maestras de la base de datos en el navegador del cliente (inspeccionables en DevTools), permitiendo que cualquier usuario ejecute sentencias destructivas (`DROP TABLE`).
* **Imposibilidad de Pruebas:** El componente dependería de una conexión de red directa al puerto de base de datos (inseguro y bloqueado por cortafuegos).
* **Falta de Abstracción:** Cambiar un nombre de columna obligaría a reescribir la interfaz de usuario.

### Desafío 02: ¿Qué pasa si el Controller contiene TODAS las reglas?
* **Falta de Reutilización:** Si mañana se añade un comando de terminal (CLI) o un consumidor de colas, se tendría que duplicar toda la lógica.
* **Sobredimensión:** Se crean "controladores gordos" (fat controllers) de miles de líneas difíciles de auditar.
* **Dificultad de Testing:** Probar una simple regla aritmética exigiría instanciar peticiones HTTP falsas completas.

### Desafío 03: ¿Qué pasa si mañana cambiamos PostgreSQL por MySQL? ¿Qué capa se toca? ¿Cuál NO?
* **Capa Afectada:** Únicamente la capa de **Acceso a Datos (Repository)** para adaptar la sintaxis de los queries o el driver de conexión.
* **Capas Intactas:** Frontend, API, Lógica de Negocio y los contratos de transferencia permanecen 100% inalterados.

### Desafío 04: ¿Qué parte de la arquitectura PERMANECE aunque cambiemos React + Express + PostgreSQL a la vez?
* **Permanecen las 5 responsabilidades conceptuales, sus fronteras y los contratos de comunicación:** El flujo unidireccional de responsabilidades, las reglas de negocio del dominio y la separación estricta de intereses no dependen de ninguna librería o lenguaje específico.

### Desafío 05: Para el MVP del Mini Task Manager: ¿en qué capa vive cada parte?
* **Frontend:** Botón *"Crear tarea"*, formulario de entrada del título y renderizado de la lista.
* **API:** Ruta `POST /tasks`, validación de que el campo `title` exista y no sea una cadena vacía.
* **Lógica de Negocio:** Asignación del estado inicial obligatorio `status: 'pending'`, sanitización del texto y timestamps.
* **Acceso a Datos:** Función `createTaskInDb({ title, status })` ejecutando la consulta parametrizada.
* **Base de Datos:** Almacenamiento seguro en la tabla `tasks` de PostgreSQL generando el `UUID` primario.
