# Diagrama C4 - Contenedores

Este diagrama describe la estructura principal de despliegue del sistema E-Market Multiplataforma, mostrando los contenedores (aplicaciones y servicios) y sus tecnologías clave.

```mermaid
C4Container
    title E-Market Multiplataforma - Contenedores
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
    Person(cliente, "Cliente")
    cliente -> web : Usa
    cliente -> mobile : Usa
    web -> api : REST
    mobile -> api : REST
    api -> cat : REST
    api -> ord : REST
    api -> pay : REST
    api -> inv : REST
    cat -> kafka : Publica eventos
    ord -> kafka : Publica/consume eventos
    pay -> kafka : Publica/consume eventos
    inv -> kafka : Publica/consume eventos
```

**Explicación:**
- **Web App y Mobile App:** Proveen la interfaz de usuario para clientes, permitiendo navegación, compras y seguimiento de pedidos.
- **API Gateway:** Centraliza la autenticación, el enrutamiento y la seguridad de las peticiones hacia los microservicios.
- **Microservicios:** Cada uno gestiona un dominio específico (catálogo, órdenes, pagos, inventario), permitiendo escalabilidad y despliegue independiente.
- **Bus de Eventos (Kafka):** Facilita la comunicación asíncrona y desacoplada entre microservicios, soportando eventos de negocio en tiempo real.

Esta estructura permite escalar y mantener cada parte del sistema de forma autónoma, facilitando la evolución y la resiliencia ante fallos.
