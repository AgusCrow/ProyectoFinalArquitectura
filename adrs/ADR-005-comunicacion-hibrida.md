# ADR-005: Patrón de Comunicación Híbrida REST + Event-Driven

## Estado
Aceptado

## Contexto
El sistema E-Market Multiplataforma adoptó una arquitectura de microservicios con Event-Driven Architecture (EDA) según ADR-001 y Apache Kafka como bus de eventos según ADR-003. Sin embargo, surge la necesidad de definir claramente cómo se comunican los diferentes actores del sistema:

- Los clientes (Web App, Mobile App) requieren respuestas inmediatas para operaciones como login, ver catálogo, o crear un pedido.
- Los microservicios necesitan comunicarse entre sí de forma desacoplada para mantener la resiliencia ante fallos parciales.
- Un patrón 100% síncrono (REST everywhere) generaría acoplamiento temporal y puntos únicos de fallo.
- Un patrón 100% asíncrono (event-driven everywhere) complicaría la experiencia de usuario y el manejo de errores HTTP.

## Alternativas Consideradas

- **REST síncrono para todo:** Simple de implementar y debuggear, pero genera acoplamiento temporal. Si el servicio de Pagos está caído, el usuario no puede ni siquiera ver su carrito si la cadena de llamadas incluye validaciones de pago.
- **Event-driven puro (Kafka para todo, incluyendo queries del cliente):** Máxima resiliencia y desacoplamiento, pero introduce latencia innecesaria para operaciones que el usuario espera que sean instantáneas. El manejo de errores (400 Bad Request, 404 Not Found) se vuelve complejo vía eventos.
- **gRPC en lugar de REST:** Mejor rendimiento que REST, pero pierde la simplicidad de debugging y el ecosistema de herramientas HTTP estándar. No resuelve el problema fundamental de acoplamiento síncrono.

## Decisión
Se adopta un **patrón de comunicación híbrida**:

| Flujo | Tecnología | Razonamiento |
|-------|------------|--------------|
| **Cliente → API Gateway → Microservicios** | REST síncrono (HTTP/JSON) | El usuario espera respuesta inmediata. REST proporciona latencia baja, manejo simple de errores HTTP (4xx, 5xx) y compatibilidad universal con navegadores y apps móviles. |
| **Microservicio ↔ Microservicio** | Kafka asíncrono (eventos) | Eventos de negocio ("PedidoCreado", "PagoProcesado", "StockActualizado") se publican en el bus. Los servicios consumen lo que necesitan sin conocer al productor. Esto garantiza resiliencia: si Logística está caído, el evento queda en Kafka hasta que vuelva. |

Esta separación combina la **simplicidad y velocidad de REST** para interacciones cliente-servidor con la **resiliencia y desacoplamiento de EDA** para procesos internos del sistema.

## Consecuencias
- **Ventajas:**
  - UX fluida: el usuario recibe respuestas inmediatas donde las espera.
  - Resiliencia interna: fallos parciales de microservicios no afectan al flujo principal si el evento queda persistido en Kafka.
  - Escalabilidad independiente: cada servicio consume eventos a su propio ritmo.
- **Desventajas:**
  - Complejidad cognitiva: los desarrolladores deben entender cuándo usar REST vs. eventos.
  - Consistencia eventual: los datos entre servicios pueden desfasarse temporalmente hasta que se procesen los eventos.
  - Monitoreo más complejo: hay que trazar operaciones que cruzan ambos mundos (REST y Kafka).

## Relación con otros ADRs
- **ADR-001:** Define la arquitectura de microservicios + EDA. Este ADR-005 especifica cómo se materializa esa EDA en términos de protocolos concretos.
- **ADR-003:** Define Kafka como tecnología del bus de eventos. Este ADR-005 define qué flujos usan Kafka (solo inter-servicios, no cliente→API).
- **ADR-004:** La persistencia políglota soporta este patrón: cada servicio guarda sus datos vía REST, luego publica eventos para que otros se sincronicen.
