# E-Market Multiplataforma - Organización del Proyecto

## Estructura de carpetas

- `/src`: Organización del código fuente (clases/archivos vacíos según arquitectura)
- `/adrs`: Decisiones de arquitectura (Architecture Decision Records)
- `/docs`: Diagramas y documentación complementaria (C4, clases, métricas)

## ¿Cómo está organizado el código?

El código está organizado en microservicios con arquitectura hexagonal (puertos y adaptadores), siguiendo las decisiones documentadas en los ADRs. Cada carpeta dentro de `/src` representa un microservicio correspondiente a un dominio de negocio específico.

### Microservicios

| Microservicio    | Dominio                                      |
| ---              | ---                                          |
| `Catalogo/`      | Productos, búsquedas y categorías            |
| `Usuarios/`      | Registro, autenticación, roles y permisos    |
| `Compras/`       | Carrito, pedidos y ciclo de vida de la orden |
| `Pagos/`         | Procesamiento de pagos y reembolsos          |
| `Logistica/`     | Envíos, seguimiento e integración con transportistas |
| `Integraciones/` | Sincronización con proveedores y notificaciones |
| `Reportes/`      | Reportes de ventas y estadísticas            |

### Capas internas (por microservicio)

Cada microservicio sigue la misma estructura hexagonal:

```
NombreServicio/
├── Domain/          → Entidades, reglas de negocio e interfaces (puertos de salida)
├── Application/     → Casos de uso / servicios de aplicación
├── Infrastructure/  → Adaptadores de salida (repositorios, publishers, clientes API)
└── WebApi/          → Controladores REST (puertos de entrada)
```

## ¿Qué contiene cada carpeta raíz?

- **src/**: Estructura de microservicios con capas hexagonales. Los archivos están vacíos o con firmas mínimas (sin implementación funcional).
- **adrs/**: Documentos ADR numerados con índice en ADR-000.md.
- **docs/**: Diagramas C4, diagrama de clases y cálculo de métricas de distancia desde la secuencia principal.
