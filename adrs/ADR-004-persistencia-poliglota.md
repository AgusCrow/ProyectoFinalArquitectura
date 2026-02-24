# ADR-004: Persistencia Políglota por Servicio

## Estado
Aceptado

## Contexto
Los microservicios de OpenMarket Corp gestionan dominios con necesidades de almacenamiento diferentes. El catálogo de productos requiere flexibilidad y escalabilidad para atributos variables (NoSQL), mientras que las órdenes y pagos exigen integridad transaccional y consultas complejas (SQL relacional). Un enfoque monolítico de base de datos limitaría la evolución y el rendimiento de cada dominio.

## Alternativas Consideradas

- **Base de datos única compartida (PostgreSQL para todo):** Simplifica la administración, pero genera acoplamiento entre servicios y limita la escalabilidad independiente. Los cambios de schema en un servicio pueden impactar a otros.
- **Base de datos única compartida (MongoDB para todo):** Flexible pero inadecuada para dominios que requieren integridad transaccional estricta (pagos, órdenes).
- **Base de datos por servicio, misma tecnología (solo PostgreSQL):** Reduce la complejidad operativa, pero no aprovecha las fortalezas de bases de datos especializadas para cada dominio (ej. la flexibilidad de schema de MongoDB para catálogos con atributos variables).

## Decisión
Cada microservicio gestionará su propia base de datos, eligiendo la tecnología más adecuada: MongoDB para el catálogo (NoSQL, flexible) y PostgreSQL para órdenes, pagos y usuarios (SQL, transaccional). Se evita el acoplamiento entre servicios a nivel de base de datos.

## Consecuencias
- Permite optimizar el almacenamiento y rendimiento según el dominio.
- Facilita la evolución independiente de cada microservicio.
- Requiere patrones de gestión de transacciones distribuidas (como Saga) y mayor esfuerzo de administración de datos.