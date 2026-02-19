# Diagrama C4 - Contenedores

Este diagrama describe la estructura principal de despliegue del sistema E-Market Multiplataforma, mostrando los contenedores (aplicaciones y servicios) y sus tecnologías clave.

```mermaid
C4Container
    title E-Market Multiplataforma - Contenedores
    
    Person(cliente, "Cliente", "Usuario final del sistema")

    System_Boundary(system, "E-Market Multiplataforma") {
      Container(web, "Web App", "React/Next.js", "Interfaz web para clientes")
      Container(mobile, "Mobile App", "React Native", "App móvil para clientes")
      Container(api, "API Gateway", "Kong/Ocelot", "Punto de entrada y autenticación")
      Container(cat, "Microservicio Catálogo", "Node.js/.NET", "Gestión de productos y búsquedas")
      Container(ord, "Microservicio Órdenes", "Node.js/.NET", "Gestión de pedidos")
      Container(pay, "Microservicio Pagos", "Node.js/.NET", "Procesamiento de pagos")
      Container(inv, "Microservicio Inventario", "Node.js/.NET", "Control de stock")
      Container(kafka, "Bus de Eventos", "Kafka", "Comunicación asíncrona")
    }

    Rel(cliente, web, "Usa", "HTTPS")
    Rel(cliente, mobile, "Usa", "HTTPS")
    Rel(web, api, "Llamadas API", "JSON/HTTPS")
    Rel(mobile, api, "Llamadas API", "JSON/HTTPS")
    Rel(api, cat, "Enruta a", "gRPC/REST")
    Rel(api, ord, "Enruta a", "gRPC/REST")
    Rel(api, pay, "Enruta a", "gRPC/REST")
    Rel(api, inv, "Enruta a", "gRPC/REST")
    
    Rel(cat, kafka, "Publica eventos", "Async")
    Rel(ord, kafka, "Publica/consume", "Async")
    Rel(pay, kafka, "Publica/consume", "Async")
    Rel(inv, kafka, "Publica/consume", "Async")
```

**Explicación:**
- **Web App y Mobile App:** Proveen la interfaz de usuario para clientes, permitiendo navegación, compras y seguimiento de pedidos.
- **API Gateway:** Centraliza la autenticación, el enrutamiento y la seguridad de las peticiones hacia los microservicios.
- **Microservicios:** Cada uno gestiona un dominio específico (catálogo, órdenes, pagos, inventario), permitiendo escalabilidad y despliegue independiente.
- **Bus de Eventos (Kafka):** Facilita la comunicación asíncrona y desacoplada entre microservicios, soportando eventos de negocio en tiempo real.

Esta estructura permite escalar y mantener cada parte del sistema de forma autónoma, facilitando la evolución y la resiliencia ante fallos.
