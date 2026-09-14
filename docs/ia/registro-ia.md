# Bitácora de Inteligencia Artificial (AI-DLC) — Registro de Decisiones

**Documento:** `docs/ia/registro-ia.md`  
**Laboratorio:** 01 — Arquitectura Primero  
**Materia:** Programación Web II (UPDS)  
**Estudiante:** Charles (asumiendo roles AI-DLC en modalidad individual)  

---

## 1. Metodología de Colaboración Humano-IA (Mob Session)

En este laboratorio se aplica el flujo metodológico **AI-DLC (AI-Driven Development Lifecycle)** para evitar la adopción pasiva o ciega de código generado por IA. Los roles operativos ejecutados durante la sesión son:

* **El Facilitador:** Define las preguntas y objetivos arquitectónicos prioritarios.
* **El Piloto:** Formula los prompts técnicos con contexto delimitado.
* **La IA (Copiloto):** Propone opciones técnicas iniciales o esqueletos de diseño.
* **El Retador:** Evalúa críticamente: *¿esto respeta las 5 capas? ¿viola YAGNI? ¿introduce complejidad innecesaria?*
* **El Equipo / Decisor:** Toma la decisión arquitectónica final fundamentada.
* **El Escriba:** Documenta la decisión, sus alternativas y lo que fue explícitamente rechazado.

---

## 2. Decisión Arquitectónica 01: Organización de Capas en el Backend

### Paso 01 (Facilitador)
> *¿Cómo debemos estructurar las carpetas del backend para que las 5 capas sean físicamente visibles y no caigamos en un archivo monolítico ni en una estructura hiper-compleja?*

### Paso 02 (Piloto)
> *Prompt a la IA:* "Propón una estructura de carpetas en Node.js/Express para un Mini Task Manager con PostgreSQL que implemente la separación estricta de responsabilidades (API, Lógica, Acceso a Datos) para el Laboratorio 1."

### Paso 03 (Propuesta de la IA)
La IA sugirió una arquitectura basada en Clean Architecture / Domain-Driven Design (DDD) con carpetas: `adapters/`, `inbound/`, `outbound/`, `ports/`, `use-cases/`, `entities/`, `value-objects/` e inyección de dependencias con interfaces complejas.

### Paso 04 (El Retador - Cuestionamiento Crítico)
* **¿Es simple para un MVP inicial?** No, añade más de 8 niveles de indirección para una entidad `Task` de 5 campos.
* **¿Lo necesitamos hoy (YAGNI)?** No, oscurece la comprensión didáctica de las 5 capas que exige la rúbrica del laboratorio.
* **¿Qué problema real resuelve en esta etapa?** Ninguno, introduce sobre-ingeniería prematura.

### Paso 05 (Decisión Final del Equipo)
Se adopta una estructura de 3 niveles en backend que mapea exactamente las responsabilidades del sistema:
```text
api/
├── routes/       # Capa 2: API / Transporte (recibe HTTP y valida formato)
├── services/     # Capa 3: Lógica de Negocio (reglas del dominio)
└── repositories/ # Capa 4: Acceso a Datos (SQL parametrizado)
```

### Paso 06 (Sección Obligatoria: Qué Rechazamos y Por Qué)
* **Rechazamos:** El boilerplate dogmático de DDD / Clean Architecture con puertos y adaptadores abstractos.
* **Argumento Técnico:** Se prioriza la transparencia pedagógica y la agilidad del andamio arquitectónico. Tres carpetas claras (`routes/`, `services/`, `repositories/`) garantizan que cualquier miembro del equipo identifique la frontera de cada capa en menos de 5 segundos.

---

## 3. Decisión Arquitectónica 02: Estrategia de Acceso a Datos (ORM vs Repository Parametrizado)

### Paso 01 (Facilitador)
> *¿Debemos usar un ORM completo como Prisma o TypeORM, o implementar el patrón Repository con consultas SQL nativas parametrizadas?*

### Paso 02 (Piloto)
> *Prompt a la IA:* "¿Conviene utilizar Prisma ORM o el driver `pg` / Supabase SDK con el patrón Repository para el acceso a datos de este proyecto?"

### Paso 03 (Propuesta de la IA)
La IA recomendó de forma estándar instalar Prisma ORM para generar automáticamente migraciones, tipos de TypeScript y un cliente fuertemente tipado.

### Paso 04 (El Retador - Cuestionamiento Crítico)
* **¿Esto ayuda a entender la seguridad y la inyección SQL?** No, los ORMs ocultan la generación de SQL detrás de métodos mágicos (`prisma.task.create()`), impidiendo que el estudiante comprenda los marcadores de posición (`$1, $2`) y la anatomía de un ataque OWASP A03.
* **¿Cumple el presupuesto de sostenibilidad?** Prisma introduce motores binarios de generación (`query-engine`) que pesan más de 40 MB, violando el presupuesto ligero establecido en `docs/sostenibilidad/presupuesto.md`.

### Paso 05 (Decisión Final del Equipo)
Se adopta el **patrón Repository utilizando sentencias SQL explícitas parametrizadas** (`pg` / Supabase REST SDK):
```javascript
const query = 'INSERT INTO tasks (title, status) VALUES ($1, $2) RETURNING *';
```

### Paso 06 (Sección Obligatoria: Qué Rechazamos y Por Qué)
* **Rechazamos:** La instalación de Prisma ORM o TypeORM en las primeras fases del proyecto.
* **Argumento Técnico:** Se privilegia el aprendizaje profundo de la seguridad SQL parametrizada, la ligereza del contenedor en ejecución y el respeto riguroso a las restricciones de peso y memoria.

---

## 4. Síntesis de la Postura Ética y Crítica Frente a la IA

1. **La IA es un generador de alternativas, no el arquitecto:** Toda propuesta debe someterse a la prueba de las 3 preguntas fundamentales de arquitectura.
2. **Rechazo activo del "código basura" (Bloatware):** Se descartan dependencias de moda sugeridas por prompts genéricos que no justifiquen su valor frente al presupuesto de sostenibilidad.
3. **Trazabilidad obligatoria:** Cada decisión estructural queda registrada con su razonamiento de aprobación y su lista de descartes.
