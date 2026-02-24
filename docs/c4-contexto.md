# Diagrama C4 - Contexto

Este diagrama muestra la visión global del sistema E-Market Multiplataforma, sus principales actores y sistemas externos involucrados.


![Descripción de la imagen del diagrama](./../public/c4-contexto-diagram.png)

<details>
<summary>Codigo del diagrama visualizado en la imagen</summary>

```mermaid
C4Context
    title E-Market Multiplataforma - Contexto
    
    Person(cliente, "Cliente", "Navega, compra y sigue pedidos")
    Person(proveedor, "Proveedor Externo", "Gestiona catálogo y ventas")
    Person(admin, "Administrador OpenMarket", "Supervisa y gestiona la plataforma")
    Person(marketing, "Gestor de Marketing", "Gestiona campañas, cupones y reportes")
    
    System(system, "E-Market Multiplataforma", "Plataforma central de comercio electrónico")
    
    System_Ext(pagos, "Pasarela de Pagos", "Procesa transacciones financieras")
    System_Ext(logistica, "Servicio de Logística", "Gestiona envíos y seguimiento")
    System_Ext(notif, "Servicio de Notificaciones", "Envía emails y SMS")
    System_Ext(cupones, "Plataforma de Cupones/Descuentos", "Provee cupones y promociones de terceros")

    Rel(cliente, system, "Navega, compra, consulta pedidos")
    Rel(proveedor, system, "Sincroniza catálogo, gestiona ventas")
    Rel(admin, system, "Administra usuarios y disputas")
    Rel(marketing, system, "Configura campañas, consulta reportes")
    
    Rel(system, pagos, "Solicita procesamiento de pagos", "HTTPS/API")
    Rel(system, logistica, "Solicita y consulta envíos", "HTTPS/API")
    Rel(system, notif, "Envía notificaciones a usuarios", "SMTP/SMS Gateway")
    Rel(system, cupones, "Valida y aplica cupones externos", "HTTPS/API")
```

</details>


</br>

**Explicación:**
- **Cliente:** Usuario final que navega, compra y realiza seguimiento de pedidos.
- **Proveedor Externo:** Empresas que cargan y actualizan su inventario, gestionan ventas y reciben notificaciones.
- **Administrador:** Personal de OpenMarket que gestiona usuarios, resuelve disputas y supervisa la operación.
- **Gestor de Marketing:** Responsable de campañas promocionales, gestión de cupones y consulta de reportes de ventas segmentados.
- **Pasarela de Pagos:** Sistema externo (ej. Stripe, PayPal) encargado de procesar pagos de manera segura.
- **Servicio de Logística:** Integración con transportistas para gestionar envíos y seguimiento de pedidos.
- **Servicio de Notificaciones:** Plataforma para el envío de emails y mensajes a clientes y proveedores.
- **Plataforma de Cupones/Descuentos:** Sistema de terceros que provee cupones y promociones externas que se validan durante el checkout.

Este contexto define los límites del sistema y las principales interacciones con el entorno, sirviendo de base para el diseño detallado de la arquitectura.



