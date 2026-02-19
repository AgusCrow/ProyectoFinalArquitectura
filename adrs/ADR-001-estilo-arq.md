# ADR-001: Selección de Estilo Arquitectónico

## Estado
Aceptado

## Contexto
OpenMarket Corp requiere una plataforma de e-commerce capaz de integrar múltiples proveedores, manejar grandes volúmenes de usuarios y productos, y responder en tiempo real a eventos de negocio. Se evaluaron alternativas como monolito modular, microservicios, arquitectura hexagonal y arquitectura dirigida por eventos (EDA). El crecimiento esperado, la necesidad de resiliencia, la integración con sistemas externos y la facilidad de evolución futura son factores clave.

## Decisión
Se selecciona una arquitectura distribuida basada en microservicios, con enfoque hexagonal (puertos y adaptadores) y comunicación dirigida por eventos (EDA). Esta combinación permite autonomía de equipos, escalabilidad independiente, desacoplamiento tecnológico y respuesta eficiente a eventos de negocio.

## Consecuencias
- Permite escalar servicios críticos de forma independiente.
- Facilita la integración de nuevos proveedores y tecnologías.
- Mejora la resiliencia y disponibilidad del sistema.
- Aumenta la complejidad operativa y de monitoreo.
- Requiere una cultura DevOps madura y automatización de despliegues.
