# ADR-003: Bus de Eventos Kafka para Comunicación Asíncrona

## Estado
Aceptado

## Contexto
El sistema requiere comunicación eficiente y desacoplada entre microservicios para soportar operaciones en tiempo real (seguimiento de pedidos, sincronización de stock, notificaciones). La integración con múltiples proveedores y servicios externos demanda un mecanismo robusto para el intercambio de eventos y la resiliencia ante fallos parciales.

## Alternativas Consideradas
- **RabbitMQ:** Broker de mensajes maduro y fácil de configurar. Sin embargo, no ofrece la misma capacidad de replay de eventos ni el throughput necesario para picos de tráfico masivos. Mejor para colas de trabajo que para event streaming.
- **Amazon SQS/SNS:** Solución gestionada que reduce la carga operativa. Descartada por el acoplamiento a un proveedor cloud específico y las limitaciones en ordenamiento y replay de eventos.
- **Comunicación síncrona (REST entre servicios):** Más simple de implementar, pero genera acoplamiento temporal entre servicios. Si un servicio falla, toda la cadena se ve afectada. No cumple con los requisitos de resiliencia y disponibilidad.

## Decisión
Se adopta Apache Kafka como bus de eventos para la comunicación asíncrona entre microservicios. Kafka permite la publicación y suscripción de eventos, desacoplando productores y consumidores, y soportando grandes volúmenes de mensajes con alta disponibilidad.

## Consecuencias
- El sistema es eventual consistente y resiliente ante caídas temporales de servicios individuales.
- Se facilita la escalabilidad horizontal y la integración de nuevos servicios.
- Se introduce complejidad operativa: requiere monitoreo, gestión de particiones y manejo de errores en la entrega de eventos.