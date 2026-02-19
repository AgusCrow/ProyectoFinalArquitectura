# ADR-004: Persistencia Políglota por Servicio

## Estado
Aceptado

## Contexto
Los microservicios de OpenMarket Corp gestionan dominios con necesidades de almacenamiento diferentes. El catálogo de productos requiere flexibilidad y escalabilidad para atributos variables (NoSQL), mientras que las órdenes y pagos exigen integridad transaccional y consultas complejas (SQL relacional). Un enfoque monolítico de base de datos limitaría la evolución y el rendimiento de cada dominio.

## Decisión
Cada microservicio gestionará su propia base de datos, eligiendo la tecnología más adecuada: MongoDB para el catálogo (NoSQL, flexible) y PostgreSQL para órdenes y pagos (SQL, transaccional). Se evita el acoplamiento entre servicios a nivel de base de datos.

## Consecuencias
- Permite optimizar el almacenamiento y rendimiento según el dominio.
- Facilita la evolución independiente de cada microservicio.
- Requiere patrones de gestión de transacciones distribuidas (como Saga) y mayor esfuerzo de administración de datos.