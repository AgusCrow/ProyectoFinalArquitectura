# ADR-002: Priorización de Características de Arquitectura

## Estado
Aceptado

## Contexto
La priorización se realizó considerando los requisitos funcionales, los riesgos del dominio multi-vendedor y los objetivos de negocio de OpenMarket Corp. Se utilizaron criterios de impacto en el negocio, urgencia, viabilidad técnica y alineación con la visión de crecimiento.

## Alternativas Consideradas

- **Elasticidad como Top 3:** Se evaluó priorizarla, pero queda cubierta por la escalabilidad de microservicios y Kafka. No justifica ser driving characteristic independiente.
- **Tolerancia a fallos como Top 3:** Importante, pero se mitiga con la arquitectura dirigida por eventos y la independencia de servicios. No requiere ser driving characteristic por separado.
- **Interoperabilidad como driving:** Necesaria para integraciones con proveedores y transportistas, pero se cubre a nivel de adaptadores en la arquitectura hexagonal.
- **Priorizar Rendimiento sobre Seguridad:** Descartado porque el manejo de pagos y datos personales exige cumplimiento normativo obligatorio (PCI-DSS).

## Decisión
Las características priorizadas son (Top 3 marcadas):
- **★ Escalabilidad:** Fundamental para soportar picos de tráfico y crecimiento de proveedores.
- **★ Disponibilidad:** Clave para la confianza y continuidad del negocio.
- **★ Seguridad:** Obligatoria por normativas y protección de datos sensibles.
- Rendimiento: Importante para la experiencia de usuario y conversión.
- Extensibilidad: Permite agregar nuevas funcionalidades y adaptarse al mercado.
- Mantenibilidad: Facilita la colaboración y reduce costos operativos.

Características implícitas identificadas: Viabilidad y Observabilidad.

Cada característica está justificada en la matriz de priorización y se refleja en las decisiones arquitectónicas tomadas.

## Consecuencias
- La arquitectura se orienta a soportar crecimiento, cambios y alta demanda.
- Se priorizan patrones y tecnologías que faciliten la escalabilidad, resiliencia y seguridad.
- El diseño modular y desacoplado permite evolucionar el sistema sin grandes reescrituras.
