# Diagrama C4 - Contexto

Este diagrama muestra la visión global del sistema E-Market Multiplataforma, sus principales actores y sistemas externos involucrados.

```mermaid
C4Context
    title E-Market Multiplataforma - Contexto
    
    Person(cliente, "Cliente", "Navega, compra y sigue pedidos")
    Person(proveedor, "Proveedor Externo", "Gestiona catálogo y ventas")
    Person(admin, "Administrador OpenMarket", "Supervisa y gestiona la plataforma")
    
    System(system, "E-Market Multiplataforma", "Plataforma central de comercio electrónico")
    
    System_Ext(pagos, "Pasarela de Pagos", "Procesa transacciones financieras")
    System_Ext(logistica, "Servicio de Logística", "Gestiona envíos y seguimiento")
    System_Ext(notif, "Servicio de Notificaciones", "Envía emails y SMS")

    Rel(cliente, system, "Navega, compra, consulta pedidos")
    Rel(proveedor, system, "Sincroniza catálogo, gestiona ventas")
    Rel(admin, system, "Administra usuarios y disputas")
    
    Rel(system, pagos, "Solicita procesamiento de pagos", "HTTPS/API")
    Rel(system, logistica, "Solicita y consulta envíos", "HTTPS/API")
    Rel(system, notif, "Envía notificaciones a usuarios", "SMTP/SMS Gateway")
```

**Explicación:**
- **Cliente:** Usuario final que navega, compra y realiza seguimiento de pedidos.
- **Proveedor Externo:** Empresas que cargan y actualizan su inventario, gestionan ventas y reciben notificaciones.
- **Administrador:** Personal de OpenMarket que gestiona usuarios, resuelve disputas y supervisa la operación.
- **Pasarela de Pagos:** Sistema externo (ej. Stripe, PayPal) encargado de procesar pagos de manera segura.
- **Servicio de Logística:** Integración con transportistas para gestionar envíos y seguimiento de pedidos.
- **Servicio de Notificaciones:** Plataforma para el envío de emails y mensajes a clientes y proveedores.

Este contexto define los límites del sistema y las principales interacciones con el entorno, sirviendo de base para el diseño detallado de la arquitectura.
