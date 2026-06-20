## Guía de Aprendizaje: Términos Actuales en el Desarrollo de Software con IA

El panorama de la programación ha evolucionado rápidamente, pasando de escribir el código línea por línea a orquestar herramientas inteligentes. A continuación, te presento una guía estructurada con los conceptos más importantes que todo desarrollador debe dominar hoy en día al trabajar con asistencia de inteligencia artificial.

---

### 1. Asistentes de Código y Copilotos (AI Coding Assistants / Copilots)

**¿Qué es?** Son herramientas integradas directamente en tu editor de código (IDE) que actúan como un "compañero de programación". Utilizan modelos de lenguaje para autocompletar líneas, generar bloques enteros a partir de comentarios (lenguaje natural) y detectar errores en tiempo real.
**Por qué es importante:** Han evolucionado para entender el contexto de todo tu repositorio, no solo del archivo abierto. Herramientas líderes como GitHub Copilot, Cursor o Windsurf están cambiando la velocidad a la que se entrega el software.

* **Referencia:** [Los 13 mejores asistentes de programación con IA para usar en 2026 por DataCamp](https://www.datacamp.com/es/blog/best-ai-coding-assistants)

### 2. Agentes de IA (AI Agents / Agentic Coding)

**¿Qué es?** A diferencia de un copiloto que espera tus instrucciones constantes, un **agente de IA** es un sistema que posee autonomía. Se le asigna un objetivo complejo (por ejemplo, "crear y conectar una base de datos para el login") y el agente muestra capacidad de razonamiento, planificación y puede utilizar herramientas externas de forma autónoma (como leer la terminal, crear archivos o instalar dependencias).
**Por qué es importante:** Representan el salto de la "asistencia" a la "delegación". Permiten automatizar flujos completos de desarrollo.

* **Referencia:** [¿Qué son los agentes de IA? Definición y ejemplos por Google Cloud](https://cloud.google.com/discover/what-are-ai-agents?hl=es)

### 3. Desarrollo Impulsado por Especificaciones (Spec-Driven Development / SDD)

**¿Qué es?** Es una metodología clave para trabajar eficientemente con Agentes de IA. Consiste en definir un documento técnico riguroso (la "especificación" o *spec*) **antes** de pedirle a la IA que escriba el código. Este documento actúa como un contrato que incluye el contexto de negocio, las funcionalidades, el desglose de tareas y las restricciones técnicas (lo que la IA *no* debe hacer).
**Por qué es importante:** Evita el "vibe coding" (programar de forma improvisada, dejando que la IA adivine y cometa errores estructurales). Al aplicar SDD, tratas a la IA como a un desarrollador de tu equipo: le entregas un plan estructurado para ejecutar paso a paso.

* **Referencia:** [Video Tutorial: How I Code With AI Agents (Spec-Driven Development)](https://www.youtube.com/watch?v=RhaF4LVAVng)
* **Referencia:** [Curso sobre Spec Driven Development (SDD) en EDteam](https://ed.team/cursos/sdd)

### 4. Generación Aumentada por Recuperación (RAG - Retrieval-Augmented Generation)

**¿Qué es?** Es una arquitectura que conecta un modelo de IA a bases de datos o documentación externa antes de que genere su respuesta. Al hacer una consulta técnica, el sistema primero "recupera" (Retrieval) la documentación oficial pertinente y luego "aumenta" (Augments) la respuesta generada por la IA basándose estrictamente en esos documentos.
**Por qué es importante:** Es la principal vacuna contra las **alucinaciones** (cuando la IA inventa métodos o librerías que no existen). En programación, garantiza que el código generado utilice la última versión de un framework en lugar de depender únicamente de los datos antiguos con los que la IA fue entrenada.

* **Referencia:** [¿Qué es el RAG (generación aumentada por recuperación)? por IBM](https://www.ibm.com/es-es/think/topics/retrieval-augmented-generation)

### 5. Ingeniería de Prompts (Prompt Engineering) y Context Window

**¿Qué es?** Es el arte y la ciencia de diseñar instrucciones (prompts) óptimas para la IA. En el desarrollo de software, implica conocer cómo enviar el contexto adecuado y utilizar técnicas precisas:

* **Zero-shot:** Pedir una tarea sin dar ejemplos previos.
* **Few-shot:** Incluir un par de ejemplos de cómo debe lucir el código de salida (entradas y salidas esperadas) para fijar un patrón.
* **Chain-of-Thought (Cadena de pensamiento):** Obligar a la IA a que explique su razonamiento paso a paso antes de soltar la solución de código final.
**Por qué es importante:** El código resultante será tan bueno como el prompt y el contexto proporcionado en la **Ventana de Contexto** (Context Window), que es la cantidad de tokens o información que la IA puede "recordar" durante una interacción.
* **Referencia:** [Prompt Engineering 2026: Guía Completa con Técnicas y Ejemplos por Aprender21](https://www.aprender21.com/blog/hub-prompt-engineering)

---

¿Hay algún término en particular de esta lista sobre el que te gustaría profundizar o aplicar a un proyecto en el que estés trabajando ahora?
