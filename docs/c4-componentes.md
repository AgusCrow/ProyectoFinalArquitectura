# Diagrama C4 - Componentes (Microservicio Órdenes)

Este diagrama muestra la estructura interna del microservicio de Órdenes, uno de los componentes críticos del sistema E-Market Multiplataforma. Se utiliza arquitectura hexagonal para aislar la lógica de negocio de las dependencias externas, facilitando la mantenibilidad y la evolución del sistema.

```mermaid
C4Component
    title Microservicio Órdenes - Componentes
    Container_Boundary(ord, "Microservicio Órdenes") {
      Component(api, "OrderController", "REST Controller", "Expone endpoints de pedidos.")
      Component(app, "OrderService", "Aplicación", "Gestiona los casos de uso.")
      Component(domain, "OrderDomain", "Dominio", "Entidades y reglas de negocio.")
      Component(repo, "OrderRepository", "Puerto de salida", "Interfaz para persistencia.")
      Component(pg, "PostgresRepository", "Adaptador", "Implementación concreta.")
      Component(pub, "EventPublisher", "Adaptador", "Publica eventos en Kafka.")
    }

    Rel(api, app, "Invoca casos de uso")
    Rel(app, domain, "Usa entidades y reglas")
    Rel(app, repo, "Persiste pedidos")
    Rel(repo, pg, "Implementa persistencia")
    Rel(app, pub, "Publica eventos")
```

**Explicación:**
- **OrderController:** Recibe las solicitudes HTTP y las traduce a comandos de aplicación.
- **OrderService:** Orquesta la lógica de negocio, validando reglas y coordinando la persistencia y publicación de eventos.
- **OrderDomain:** Contiene las entidades (Pedido, LineItem) y lógica pura del dominio.
- **OrderRepository:** Define la interfaz para almacenar y recuperar pedidos.
- **PostgresRepository:** Implementa la persistencia usando una base de datos relacional.
- **EventPublisher:** Publica eventos relevantes (por ejemplo, PedidoCreado) en el bus de eventos Kafka.

Este diseño permite desacoplar la lógica de negocio de la infraestructura, facilitando pruebas, cambios tecnológicos y escalabilidad.
