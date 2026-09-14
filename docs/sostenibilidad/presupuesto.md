# Presupuesto de Sostenibilidad y Límites de Ingeniería

**Documento:** `docs/sostenibilidad/presupuesto.md`  
**Laboratorio:** 01 — Arquitectura Primero  
**Materia:** Programación Web II (UPDS)  
**Estudiante:** Charles  

---

## 1. Presupuesto de Recursos del Sistema

La sostenibilidad del software moderno no solo comprende el impacto ambiental directo por consumo de cómputo en la nube, sino también la eficiencia de transferencia y la ligereza del cliente:

| Métrica | Límite Objetivo (Budget) | Justificación Técnica |
|---|---|---|
| **Peso del Bundle Frontend (Gzip)** | `< 150 KB` | Carga ultrarrápida incluso en redes móviles 3G/4G inestables. |
| **Volumen de Requests por Acción** | `1 request HTTP` | Cada operación CRUD del usuario debe resolverse en un solo viaje de ida y vuelta (round-trip). |
| **Tiempo de Respuesta de la API (p95)** | `< 100 ms` | Respuestas inmediatas aprovechando la baja latencia de la base de datos gestionada. |
| **Consumo de Memoria Backend** | `< 128 MB RAM` | Permite ejecución eficiente en contenedores de capa gratuita (Render / Fly.io). |

---

## 2. Política de Control de Dependencias Externas

Para evitar el sobrepeso de `node_modules` y riesgos en la cadena de suministro (vulnerabilidades npm), se establece la siguiente regla obligatoria:

> **Regla de Oro:** *Antes de ejecutar `npm install <paquete>` para añadir cualquier dependencia externa, se debe justificar por escrito respondiendo a 3 preguntas:*

1. **¿Se puede resolver con la API estándar de JavaScript / Node.js o CSS nativo en menos de 30 líneas de código?**  
   *Si la respuesta es Sí, queda prohibido instalar la dependencia.*
2. **¿Cuál es el impacto en peso (`bundlephobia`) y cuántas subdependencias transitivas introduce al árbol del proyecto?**  
   *Si introduce más de 5 subdependencias o pesa más de 50 KB sin ser un componente crítico, se debe buscar una alternativa micro o nativa.*
3. **¿La librería cuenta con mantenimiento activo, soporte TypeScript/ESM y licencia permisiva (MIT/Apache)?**  
   *Si está en estado de abandono o tiene licencias restrictivas, se descarta de inmediato.*

---

## 3. Decisión de Sostenibilidad: Mermaid & Markdown vs Figma / Miro

Para la fase de diseño arquitectónico y modelado del sistema se tomó la decisión consciente de utilizar **Mermaid integrado en Markdown**, en lugar de herramientas pesadas en la nube como Figma o Miro:

| Dimensión Evaluada | Mermaid + Markdown | Herramientas Cloud (Figma / Miro) |
|---|---|---|
| **Peso y Eficiencia** | **~2 KB** de texto plano puro | Varios **Megabytes (MB)** de assets vectoriales y bundles JS pesados |
| **Control de Versiones** | **100% Versionable en Git** junto al código fuente | Requiere exportaciones manuales o plataformas propietarias |
| **Disponibilidad Offline** | **Trabajo sin conexión** permanente en el editor local | Dependencia obligatoria de conexión a Internet |
| **Trazabilidad (Diff)** | **Diff línea por línea** legible en cada pull request | Imposible auditar cambios granulares en binarios |
| **Costo Operativo** | **$0 / Sin cuentas** ni dependencias de licencias | Requiere registro de cuentas y cuotas de uso |

### ¿Qué recurso estamos ahorrando con esta decisión?
Estamos ahorrando **ancho de banda, consumo energético de servidores remotos, tiempo de desarrollo y fricción cognitiva**. Al mantener la arquitectura como código plano en el mismo repositorio, se garantiza la preservación del conocimiento en el tiempo sin depender de plataformas externas.

---

## 4. Matriz de 7 Preguntas Clave: Decisión Tecnológica Justificada

| # | Pregunta Clave de Análisis | Decisión y Justificación del Proyecto |
|---|---|---|
| **1** | ¿Qué problema real resuelve la tecnología seleccionada? | **Node.js/Express** resuelve el transporte HTTP sin imponer abstracciones innecesarias; **PostgreSQL** resuelve la persistencia confiable y atómica de datos relacionales. |
| **2** | ¿Por qué no una solución monolítica acoplada? | Porque mezclar SQL y HTML en un solo archivo imposibilita el testing automatizado, bloquea el trabajo colaborativo e introduce vulnerabilidades críticas de seguridad. |
| **3** | ¿Cuál es la carga computacional añadida? | Mínima: la arquitectura de 5 capas introduce llamadas a funciones en memoria con sobrecarga de microsegundos, ahorrando costos por errores no controlados. |
| **4** | ¿Cómo afecta la latencia al usuario final? | La separación permite que el Frontend cargue estáticos desde un CDN perimetral y que la API procese únicamente payloads JSON pequeños y optimizados. |
| **5** | ¿Es fácil de reemplazar en el futuro? | Sí: al respetar la interfaz del repositorio, la base de datos o el framework web pueden ser sustituidos sin reescribir la lógica de negocio. |
| **6** | ¿Qué impacto tiene en la mantenibilidad del equipo? | Reduce la fatiga cognitiva: un nuevo desarrollador puede entender una capa aislada sin tener que descifrar toda la pila de una sola vez. |
| **7** | ¿Qué es aquello que permanece inalterable? | **Las 5 responsabilidades conceptuales y sus contratos.** Las herramientas (`Express`, `React`) pueden cambiar mañana; las fronteras y reglas de dominio permanecen. |
