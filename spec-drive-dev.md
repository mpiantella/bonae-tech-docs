
### Guía de Aprendizaje: Desarrollo Impulsado por Especificaciones (Spec-Driven Development) con IA

[Video](https://www.youtube.com/watch?v=RhaF4LVAVng)

Este video explica cómo mejorar la calidad del código generado por agentes de IA mediante el uso de **especificaciones técnicas** detalladas antes de comenzar a programar. En lugar de dar instrucciones vagas a la IA, el autor propone un flujo de trabajo estructurado para evitar que la IA tome decisiones incorrectas o adivine tus intenciones.

#### Conceptos clave:

*   **¿Qué es el Desarrollo Impulsado por Especificaciones? (0:31 - 1:24):** Es la práctica de definir un documento de especificación (spec) antes de pedirle a la IA que escriba código. Este documento actúa como un contrato que define claramente qué se va a construir, las restricciones (lo que NO se debe hacer) y las decisiones arquitectónicas clave.
*   **El flujo de trabajo recomendado (4:52 - 5:40):**
    1.  **Generar el spec:** Usar un *metaprompt* para describir la intención.
    2.  **Revisar e iterar:** Asegurarse de que los detalles técnicos sean correctos.
    3.  **Desglose de tareas:** Dividir el trabajo en tareas pequeñas y discretas.
    4.  **Ejecución incremental:** Implementar y verificar cada tarea de forma independiente.
*   **Componentes de una buena especificación (3:39 - 4:51):**
    *   **El Porqué (Why):** Contexto del problema y valor de negocio.
    *   **El Qué (What):** Definición clara de las características funcionales.
    *   **Restricciones:** Librerías permitidas o prohibidas, patrones de diseño y qué está fuera del alcance.
    *   **Tareas:** Lista de pasos concretos y criterios de verificación para cada uno.

#### Ventajas del método:

*   **Mayor control:** Evita que el agente de IA escriba código innecesario o utilice enfoques no deseados (17:31 - 18:02).
*   **Calidad de código:** Trabajar de forma incremental permite revisar el trabajo en etapas, lo que facilita detectar errores temprano (13:41 - 14:49).
*   **Persistencia:** Al almacenar la especificación en el disco local como un archivo, puedes retomar proyectos complejos en diferentes sesiones sin perder el hilo (14:38 - 14:57).

**Conclusión:** La idea principal es tratar a la IA como a un ingeniero de software en un equipo real. No se le da todo un proyecto complejo de golpe; se le proporciona un plan (la especificación) y tareas pequeñas que pueda ejecutar, revisar y validar paso a paso.
