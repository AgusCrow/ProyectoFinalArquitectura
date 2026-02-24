# Diagrama C4 - Componentes (Microservicio Compras/Órdenes)

Este diagrama muestra la estructura interna del microservicio de Compras (Órdenes), uno de los componentes críticos del sistema E-Market Multiplataforma. Se utiliza arquitectura hexagonal (puertos y adaptadores) para aislar la lógica de negocio de las dependencias externas, facilitando la mantenibilidad y la evolución del sistema.

![Descripción de la imagen del diagrama](./../public/c4-component-diagram.png)

<details>
<summary>Codigo del diagrama visualizado en la imagen</summary>

```mermaid
C4Component
    title Microservicio Compras - Componentes (Arquitectura Hexagonal)
    
    Container_Boundary(ord, "Microservicio Compras") {
      Component(api, "PedidoController", "REST Controller / Puerto de entrada", "Expone endpoints HTTP de pedidos.")
      Component(crearSvc, "CrearPedidoService", "Caso de uso", "Orquesta la creación de un pedido.")
      Component(cancelarSvc, "CancelarPedidoService", "Caso de uso", "Orquesta la cancelación de un pedido.")
      Component(domain, "Pedido / LineItem", "Dominio", "Entidades y reglas de negocio puras.")
      Component(repo, "IPedidoRepository", "Puerto de salida", "Interfaz abstracta para persistencia.")
      Component(pg, "PedidoRepositoryPostgres", "Adaptador de salida", "Implementación concreta con PostgreSQL.")
      Component(pub, "PedidoEventPublisherKafka", "Adaptador de salida", "Publica eventos de dominio en Kafka.")
    }

    ContainerDb(db, "PostgreSQL Compras", "SQL", "Base de datos relacional")
    Container(kafka, "Bus de Eventos", "Apache Kafka", "Comunicación asíncrona")
    Container(apiGw, "API Gateway", "Kong/Ocelot", "Punto de entrada")

    Rel(apiGw, api, "Enruta peticiones", "REST/JSON")
    Rel(api, crearSvc, "Invoca caso de uso")
    Rel(api, cancelarSvc, "Invoca caso de uso")
    Rel(crearSvc, domain, "Usa entidades y reglas")
    Rel(cancelarSvc, domain, "Usa entidades y reglas")
    Rel(crearSvc, repo, "Persiste pedidos")
    Rel(cancelarSvc, repo, "Actualiza pedidos")
    Rel(repo, pg, "Implementa persistencia")
    Rel(pg, db, "Lee/Escribe", "Driver PostgreSQL")
    Rel(crearSvc, pub, "Publica PedidoCreado")
    Rel(cancelarSvc, pub, "Publica PedidoCancelado")
    Rel(pub, kafka, "Envía eventos", "Async")
```

</details>

</br>

**Explicación:**
- **PedidoController (Puerto de entrada):** Recibe las solicitudes HTTP desde el API Gateway y las traduce a comandos de aplicación.
- **CrearPedidoService / CancelarPedidoService (Casos de uso):** Orquestan la lógica de negocio, validando reglas y coordinando la persistencia y publicación de eventos.
- **Pedido / LineItem (Dominio):** Contienen las entidades y lógica pura del dominio (CalcularTotal, AgregarItem, Cancelar). No dependen de infraestructura.
- **IPedidoRepository (Puerto de salida):** Define la interfaz abstracta para almacenar y recuperar pedidos. Permite cambiar la implementación sin afectar el dominio.
- **PedidoRepositoryPostgres (Adaptador de salida):** Implementa la persistencia usando PostgreSQL con integridad transaccional.
- **PedidoEventPublisherKafka (Adaptador de salida):** Publica eventos de dominio (PedidoCreado, PedidoCancelado) en el bus de eventos Kafka para que otros servicios reaccionen.

Este diseño permite desacoplar la lógica de negocio de la infraestructura, facilitando pruebas, cambios tecnológicos y escalabilidad. Los nombres de los componentes corresponden directamente a los archivos en `/src/Compras/`.
