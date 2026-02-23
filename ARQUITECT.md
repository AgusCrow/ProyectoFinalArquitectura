# Informe de Arquitectura

## 1. Priorización de características de arquitectura

La priorización de características se realizó mediante una matriz de impacto y urgencia, considerando los objetivos de negocio, riesgos del dominio multi-vendedor y requerimientos funcionales. Participó todo el equipo, ponderando especialmente la escalabilidad y disponibilidad, dado el modelo multi-vendedor y la criticidad del negocio en línea. La seguridad fue priorizada por requerimientos legales y de confianza. El rendimiento y la extensibilidad se consideraron claves para la experiencia de usuario y la evolución futura del sistema.

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

Se evaluaron estilos como monolito modular y microkernel, pero se descartaron por su limitada escalabilidad y flexibilidad. La combinación de microservicios, arquitectura hexagonal y Event-Driven Architecture (EDA) permite autonomía de equipos, integración sencilla de nuevos servicios y resiliencia ante fallos parciales. Por ejemplo, si la pasarela de pagos falla, el resto del sistema sigue operativo y el usuario es notificado. Esta decisión se fundamenta en la necesidad de soportar crecimiento, cambios y alta demanda, así como en la facilidad para integrar nuevos proveedores y tecnologías.

| Estilo                | Ventajas para OpenMarket                                   | Desafíos Mitigados                                         |
----|
| Monolito Modular      | Simplicidad inicial, fácil despliegue                     | No escala bien con múltiples equipos y grandes catálogos    |
| Microservicios        | Escalabilidad, autonomía, despliegue continuo             | Gestiona la complejidad de múltiples dominios de negocio    |
| Hexagonal             | Alta testabilidad, desacoplamiento de frameworks/DB       | Protege la lógica de negocio de la obsolescencia técnica    |
| Dirigida por Eventos  | Respuestas en tiempo real, resiliencia, escalabilidad     | Elimina cuellos de botella de comunicación síncrona         |

> Decisión y justificación en [ADR-001-estilo-arq.md](adrs/ADR-001-estilo-arq.md)


## 3. Diagramas C4

Los diagramas C4 permiten visualizar la arquitectura desde el contexto global hasta el detalle de componentes críticos. Cada nivel está documentado y explicado:

- [Contexto](docs/c4-contexto.md): visión global, actores y sistemas externos.
- [Contenedores](docs/c4-contenedores.md): estructura de despliegue y tecnologías clave.
- [Componentes](docs/c4-componentes.md): estructura interna de microservicios críticos.


## 4. Organización del código

El repositorio sigue una estructura modular basada en microservicios y arquitectura hexagonal. Cada carpeta en `/src` representa un dominio o servicio clave (Catálogo, Usuarios, Compras, Logística, Integraciones, Reportes). Dentro de cada servicio, se separan las capas de dominio, aplicación, infraestructura y API, facilitando la mantenibilidad y la escalabilidad. Los archivos están vacíos o con firmas mínimas, cumpliendo el requerimiento de no implementar funcionalidad.

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
