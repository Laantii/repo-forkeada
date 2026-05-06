# Feed de Publicaciones

Este proyecto implementa un feed social sencillo, sin usuarios ni autenticación, centrado en la interacción entre publicaciones, likes y comentarios. La aplicación está pensada para simular una plataforma de contenido visual con lógica de negocio realista pero acotada.

## Requerimientos

- Docker

## Resumen funcional

El sistema permite crear publicaciones con imagen, texto y descripción, y mostrarlas en un feed central. Cada publicación puede recibir likes y comentarios, y esas interacciones modifican cómo se percibe su importancia dentro del feed.

El comportamiento general del producto gira alrededor de tres ideas:

- **contenido**: las publicaciones son la unidad principal del sistema,
- **interacción**: likes y comentarios enriquecen cada publicación,
- **priorización**: el feed puede cambiar de orden según distintos criterios de relevancia.

## Lógica de negocio principal

La lógica del sistema no solo guarda datos, también construye una vista enriquecida del feed. Para cada publicación se calcula información derivada, como la cantidad de interacciones y una puntuación de relevancia que combina actividad reciente con volumen de participación.

Además, antes de persistir comentarios se aplica una validación/moderación para filtrar contenido problemático. El sistema también ejecuta efectos operativos cuando se crean interacciones (por ejemplo trazas y procesos internos de recálculo), reflejando un flujo típico de aplicaciones de contenido.

## Contexto técnico

La solución está construida con NestJS en backend, Prisma ORM y SQLite como almacenamiento local.

La base de datos es fija en `sqlite.db`

## Ejecución:

Para levantar todo el sistema con Docker:

1. `make setup`
2. `make run`

Este comando construye la imagen, instala dependencias dentro del contenedor, aplica migraciones Prisma, genera el cliente y arranca NestJS en modo watch.

En este flujo, los artefactos de compilación y cache de paquetes se mantienen dentro de volúmenes Docker para no ensuciar el directorio del proyecto.

La aplicación queda disponible en:

- `http://localhost:3000`
- `http://localhost:3000/docs`
- `http://localhost:5555` (Prisma Studio - Database Manager)

Comandos útiles:

- `make stop` para detener el contenedor
- `make logs` para ver logs en tiempo real



🧪 Implementación y validación del sistema (trabajo realizado)

Durante esta etapa del proyecto se trabajó en la implementación, corrección y validación del backend del sistema de feed social. El desarrollo se enfocó en asegurar el correcto funcionamiento de las principales funcionalidades del sistema y en verificar la lógica de negocio mediante pruebas reales.

🔧 Funcionalidades implementadas y verificadas

Se completó y validó el funcionamiento de los siguientes módulos:

Creación de publicaciones mediante POST /api/posts
Sistema de likes con peso asociado mediante POST /api/posts/:id/likes
Obtención del feed de publicaciones mediante GET /api/posts/feed
Cálculo dinámico de métricas del sistema:
Cantidad total de likes (likesCount)
Cantidad de comentarios (commentsCount)
Score de relevancia (relevanceScore)
Generación de metadata asociada a cada publicación
⚙️ Ordenamiento del feed

Se validó el comportamiento del feed según distintos modos de ordenamiento:

latest: ordenado por fecha de creación
mostLiked: ordenado por cantidad de likes
mostCommented: ordenado por cantidad de comentarios
relevance: ordenado por score de relevancia calculado

Este comportamiento implementa de forma implícita un patrón de diseño tipo Strategy, donde el criterio de ordenamiento cambia dinámicamente según el parámetro mode.

🧪 Pruebas realizadas

Las pruebas se realizaron utilizando herramientas como cURL y Swagger, validando:

Creación correcta de publicaciones
Persistencia de datos en la base de datos SQLite
Funcionamiento del sistema de likes y su impacto en el feed
Cambios dinámicos en el orden del feed según el modo seleccionado
📊 Resultado obtenido

El sistema responde correctamente a todas las operaciones implementadas, reflejando los cambios en tiempo real dentro del feed y validando la coherencia de la lógica de negocio.

🧠 Conclusión técnica

El sistema implementa una lógica de comportamiento dinámico equivalente al patrón Strategy, aplicado en el ordenamiento del feed. Esto permite modificar el comportamiento del sistema sin alterar su estructura principal, facilitando su escalabilidad.
