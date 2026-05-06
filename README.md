Refactorización: Integración de Moderación Legada usando el Patrón Adapter

Problemas Identificados

Durante la revisión del código del repositorio, se identificaron los siguientes problemas de diseño y arquitectura relacionados con el servicio de moderación de publicaciones (`legacy-moderation.client.ts`):

1. Interfaz Inconsistente (Código Legado):
   El cliente de moderación existente retornaba múltiples tipos de datos de forma inconsistente (`string`, `number`, `object`), dependiendo de la regla de negocio que se activara. Por ejemplo:
   - Retornaba `"BLOCK"` si encontraba palabras prohibidas.
   - Retornaba `{ pass: true, reason: "legacy-rule-3" }` para ciertas validaciones.
   - Retornaba `1` u `"OK"` en otros casos.

2. Alto Acoplamiento en la Lógica de Negocio:
   Si se usaba el cliente legado directamente, el servicio principal (`PostsService`) iba a estar fuertemente acoplado a las particularidades de esta API antigua, obligando a manejar toda la lógica condicional de tipos mixtos en el flujo principal de creación de publicaciones.

3. Falta de Abstracción:
   No existía un contrato claro sobre lo que el sistema esperaba de un "Moderador de Contenido", dificultando la posibilidad de cambiar de proveedor en el futuro o realizar pruebas unitarias (Testing) a través de mocks.

4. Inconsistencia en los DTOs:
   Se estaba intentando enviar a moderación un campo genérico `content` que no existía en `CreatePostDto`, el cual realmente estructuraba la información en `title` y `description`.

Solución Aplicada

Para aislar el problema y estandarizar la comunicación en nuestro sistema, implementamos el **Patrón Estructural Adapter (Adaptador)**. 

1. Definición de la Interfaz Objetivo (Target)
Creamos la interfaz `ContentModerator` (`src/posts/moderation.interface.ts`) que define un contrato limpio y estandarizado. Ahora, cualquier sistema de moderación debe simplemente recibir un texto y devolver un `Promise<boolean>`.

2. Creación del Adaptador (Adapter)
Desarrollamos la clase `LegacyModerationAdapter` que implementa la interfaz `ContentModerator`. Su única responsabilidad es:
- Llamar a la API legada subyacente (`legacyModerationApi.review`).
- "Traducir" o normalizar todas las respuestas inconsistentes (`"BLOCK"`, `"OK"`, `1`, `{ pass: true }`) a un simple valor booleano (`true` para contenido seguro, `false` para contenido bloqueado).

3. Inyección de Dependencias (Dependency Inversion)
Refactorizamos `PostsService` para que dependa únicamente de la abstracción (`CONTENT_MODERATOR_TOKEN`), ignorando por completo la implementación del cliente legado. Configuramos el módulo de NestJS (`PostsModule`) para inyectar nuestro Adapter cuando se requiera dicho token.

4. Corrección del Flujo de Datos
Actualizamos el servicio para que lea correctamente los campos validados por class-validator (`dto.title` y `dto.description`) y aplique la moderación a los textos correctos.
