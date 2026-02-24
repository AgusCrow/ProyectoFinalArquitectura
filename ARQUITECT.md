# Informe de Arquitectura
- **Nombre del Proyecto:** E-Market Multiplataforma (OpenMarket Corp.)
- **Equipo:** Agustin De Luca, Francisco Chahla, Guido Mamani, Luciano Moise, Valentin Coria
- **Dominio:** Sistema de E-Commerce (Multi-vendedor)


## 1. Priorización de características de arquitectura

La priorización de características se realizó mediante una matriz de impacto y urgencia, considerando los objetivos de negocio, riesgos del dominio multi-vendedor y requerimientos funcionales. Participó todo el equipo, ponderando especialmente la escalabilidad y disponibilidad, dado el modelo multi-vendedor y la criticidad del negocio en línea. La seguridad fue priorizada por requerimientos legales y de confianza. El rendimiento y la extensibilidad se consideraron claves para la experiencia de usuario y la evolución futura del sistema.

### Top 3 Características Priorizadas

1. **Escalabilidad** — El sistema debe soportar picos de tráfico en campañas promocionales y la incorporación continua de nuevos proveedores sin degradación.
2. **Disponibilidad** — Al ser un negocio en línea, cualquier tiempo de inactividad impacta directamente en ventas, reputación y retención de clientes.
3. **Seguridad** — El manejo de pagos y datos personales exige cumplimiento de normativas (PCI-DSS) y altos estándares de protección.

### Otras Características Priorizadas

4. **Rendimiento** — La velocidad de carga influye en la conversión y el posicionamiento en buscadores.
5. **Extensibilidad** — Permite integrar nuevas pasarelas de pago, proveedores y funcionalidades sin reescribir el sistema.
6. **Mantenibilidad** — La estructura modular facilita el trabajo paralelo de múltiples equipos y reduce conflictos.

### Características Implícitas

- **Viabilidad:** El equipo debe poder construir y operar el sistema con las tecnologías y recursos disponibles.
- **Observabilidad:** En una arquitectura distribuida, es crítico contar con logging, tracing y métricas para diagnosticar problemas rápidamente.

### Características Consideradas

- **Elasticidad:** Se evaluó como relevante ante picos de tráfico, pero queda cubierta en gran parte por la escalabilidad de microservicios y Kafka.
- **Tolerancia a fallos:** Considerada importante, pero se mitiga con la arquitectura dirigida por eventos y la independencia de servicios.
- **Interoperabilidad:** Necesaria para integraciones con proveedores y transportistas, pero se cubre a nivel de adaptadores en la arquitectura hexagonal.
- **Concurrencia:** Relevante para operaciones simultáneas de múltiples usuarios, se aborda con el bus de eventos y la escalabilidad horizontal.

### Matriz de priorización

| Característica   | Prioridad | Impacto en el Negocio         | Justificación Técnica                                                                 |
| --- | --- | --- | --- |
| Escalabilidad    | Alta      | Crítico para el crecimiento   | Soportar picos de tráfico y expansión de proveedores sin degradación de rendimiento.  |
| Disponibilidad   | Alta      | Vital para la retención       | El tiempo de inactividad impacta ventas y reputación.                                 |
| Seguridad        | Alta      | Legal y confianza             | Manejo de pagos y datos requiere altos estándares de protección.                      |
| Rendimiento      | Media     | Influye en la conversión      | La velocidad de carga afecta experiencia y posicionamiento.                           |
| Extensibilidad   | Media     | Facilita la innovación        | Permite integrar nuevas pasarelas y proveedores fácilmente.                           |
| Mantenibilidad   | Media     | Optimiza costos operativos    | Estructura clara permite trabajo paralelo y menos conflictos.                         |

> Justificación detallada en [ADR-002-priorizacion-caracteristicas.md](adrs/ADR-002-priorizacion-caracteristicas.md)


## 2. Estilo arquitectónico seleccionado

- **Arquitecturas seleccionadas:** ✅ Microservices + ✅ Event-Driven (complementado con patrón Hexagonal)

### Matriz comparativa — Características vs Estilos

| Característica       | Layered | Monolito Modular | Microkernel | Microservices | Event-Driven |
| ---                  | ---     | ---              | ---         | ---           | ---          |
| **Partitioning**     | Technical | Domain         | Domain      | Domain        | Technical    |
| **Cost**             | $       | $                | $           | $$$$$         | $$$          |
| Maintainability      | ★       | ★★★              | ★★★         | ★★★★★         | ★★★★         |
| Testability          | ★★      | ★★★              | ★★★         | ★★★★          | ★★★          |
| Deployability        | ★       | ★★               | ★★★         | ★★★★★         | ★★★★         |
| Simplicity           | ★★★★★   | ★★★★             | ★★★★        | ★             | ★★           |
| **Scalability**      | ★       | ★★               | ★★          | ★★★★★         | ★★★★★        |
| Elasticity           | ★       | ★                | ★★          | ★★★★★         | ★★★★★        |
| **Fault-tolerance**  | ★       | ★                | ★★          | ★★★★          | ★★★★★        |
| Responsiveness       | ★★★     | ★★★              | ★★★         | ★★★           | ★★★★★        |
| Evolvability         | ★       | ★★               | ★★★         | ★★★★★         | ★★★★★        |
| Abstraction          | ★       | ★★               | ★★★         | ★★★★          | ★★★★         |
| Interoperability     | ★       | ★                | ★★          | ★★★★          | ★★★★★        |

### Análisis de la selección

Se evaluaron cinco estilos arquitectónicos contra las características priorizadas para OpenMarket. La decisión final combina **Microservicios** y **Event-Driven Architecture (EDA)**, complementados con el **patrón Hexagonal** (puertos y adaptadores) dentro de cada servicio:

| Estilo seleccionado   | Ventajas para OpenMarket                                   | Desafíos Mitigados                                         |
| --- | --- | --- |
| Microservicios        | Escalabilidad y despliegue independiente por dominio. Autonomía de equipos. Máxima evolvability. | Gestiona la complejidad de múltiples dominios de negocio (catálogo, pagos, logística). |
| Event-Driven (EDA)    | Respuesta en tiempo real a eventos de negocio. Máxima resiliencia y desacoplamiento temporal. | Elimina cuellos de botella de comunicación síncrona entre servicios. |
| Hexagonal (por servicio) | Alta testabilidad, desacoplamiento de frameworks y bases de datos. | Protege la lógica de negocio de cambios tecnológicos y facilita el testing aislado. |

**¿Por qué se descartaron los demás?**

- **Layered:** Alta simplicidad pero pésima escalabilidad (★) y nula tolerancia a fallos (★). No viable para un e-commerce con picos de tráfico y múltiples proveedores.
- **Monolito Modular:** Mejor que Layered, pero la escalabilidad (★★) y elasticidad (★) no alcanzan para soportar el crecimiento esperado ni el trabajo de múltiples equipos independientes.
- **Microkernel:** Aceptable para extensibilidad via plugins, pero su escalabilidad (★★) y tolerancia a fallos (★★) son insuficientes para un sistema distribuido de alta demanda.

> Decisión y justificación en [ADR-001-estilo-arq.md](adrs/ADR-001-estilo-arq.md)


## 3. Diagramas C4

Los diagramas C4 permiten visualizar la arquitectura desde el contexto global hasta el detalle de componentes críticos. Cada nivel está documentado y explicado:

- [Contexto](docs/c4-contexto.md): visión global, actores y sistemas externos.
- [Contenedores](docs/c4-contenedores.md): estructura de despliegue y tecnologías clave.
- [Componentes](docs/c4-componentes.md): estructura interna de microservicios críticos.


## 4. Organización del código

El repositorio sigue una estructura modular basada en microservicios y arquitectura hexagonal. Cada carpeta en `/src` representa un dominio o servicio clave (Catálogo, Usuarios, Compras, Pagos, Logística, Integraciones, Reportes). Dentro de cada servicio, se separan las capas de dominio, aplicación, infraestructura y API (puertos y adaptadores), facilitando la mantenibilidad y la escalabilidad. Los archivos están vacíos o con firmas mínimas, cumpliendo el requerimiento de no implementar funcionalidad.

> Ver detalles en [README.md](README.md)


## 5. Diagrama de clases a elección

Se eligió el dominio de Pedidos por su centralidad en el negocio y su relevancia para el cálculo de métricas de distancia desde la secuencia principal. El diagrama de clases muestra las entidades principales, sus relaciones y los métodos esenciales para el análisis arquitectónico. Esto permite identificar dependencias, puntos de acoplamiento y oportunidades de abstracción.

- [Diagrama de clases](docs/diagrama-clases.md)


## 6. Distancia desde la secuencia principal

Se aplicaron las métricas de Robert C. Martin a los componentes críticos. El análisis incluye el cálculo de abstracción (A), inestabilidad (I) y distancia (D) para cada componente, interpretando los resultados y proponiendo mejoras cuando es necesario. Un valor de D cercano a 0 indica buen balance; valores cercanos a 1 sugieren necesidad de refactorización.

- [Distancia desde la secuencia principal](docs/distancia-secuencia-principal.md)


## Autoevaluación grupal

La autoevaluación se realizó de manera colaborativa, considerando la participación en reuniones, aportes técnicos y revisión de entregables. Todos los miembros contribuyeron activamente en la documentación, el diseño y la toma de decisiones.

- Agustin De Luca: 5/5
- Francisco Chahla: 5/5
- Guido Mamani: 5/5
- Luciano Moise: 5/5
- Valentin Coria: 5/5


## Índice de ADRs

- [ADR-000: Índice de Decisiones](adrs/ADR-000.md)
- [ADR-001: Estilo arquitectónico](adrs/ADR-001-estilo-arq.md)
- [ADR-002: Priorización de características](adrs/ADR-002-priorizacion-caracteristicas.md)
- [ADR-003: Bus de eventos Kafka](adrs/ADR-003-bus-eventos-kafka.md)
- [ADR-004: Persistencia poliglota](adrs/ADR-004-persistencia-poliglota.md)


> Todos los archivos y diagramas están referenciados y organizados según los lineamientos del proyecto final.
