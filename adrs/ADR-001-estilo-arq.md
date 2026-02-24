# ADR-001: Selección de Estilo Arquitectónico

## Estado
Aceptado

## Contexto
OpenMarket Corp requiere una plataforma de e-commerce **multiplataforma** (Web y Mobile) capaz de integrar múltiples proveedores, manejar grandes volúmenes de usuarios y productos, y responder en tiempo real a eventos de negocio. Los clientes acceden vía navegador web (React/Next.js) y aplicación móvil (React Native), lo que exige un backend que soporte múltiples clientes de forma uniforme a través de una API unificada. Se evaluaron alternativas como monolito modular, microservicios, arquitectura hexagonal y arquitectura dirigida por eventos (EDA). El crecimiento esperado, la necesidad de resiliencia, la integración con sistemas externos y la facilidad de evolución futura son factores clave.

## Alternativas Consideradas

- **Layered (por capas):** Alta simplicidad pero pésima escalabilidad y tolerancia a fallos. No viable para un e-commerce multi-vendedor con picos de tráfico y múltiples equipos.
- **Monolito Modular:** Mejor separación de dominios, pero la escalabilidad y elasticidad no alcanzan para soportar el crecimiento esperado ni el despliegue independiente de servicios.
- **Microkernel:** Extensible via plugins, pero su escalabilidad y tolerancia a fallos son insuficientes para un sistema distribuido de alta demanda con múltiples integraciones externas.
- **Service-Based / Service-Oriented:** Ofrecen distribución parcial, pero carecen de la granularidad y autonomía de microservicios para equipos independientes por dominio.

## Decisión
Se selecciona una arquitectura distribuida basada en microservicios, con enfoque hexagonal (puertos y adaptadores) y comunicación dirigida por eventos (EDA). Esta combinación permite autonomía de equipos, escalabilidad independiente, desacoplamiento tecnológico y respuesta eficiente a eventos de negocio.

El patrón EDA se aplica de forma **híbrida**: REST síncrono para la comunicación cliente→API Gateway→microservicios (requerimientos de latencia baja y respuesta inmediata), y eventos asíncronos vía Kafka para la comunicación entre microservicios (resiliencia y desacoplamiento temporal). Ver ADR-005 para detalles del patrón de comunicación.

## Consecuencias
- Permite escalar servicios críticos de forma independiente.
- Facilita la integración de nuevos proveedores y tecnologías.
- Mejora la resiliencia y disponibilidad del sistema.
- Aumenta la complejidad operativa y de monitoreo.
- Requiere una cultura DevOps madura y automatización de despliegues.
- **Requiere presupuesto e infraestructura:** La arquitectura de microservicios + EDA implica mayor costo operativo (múltiples instancias, Kafka, monitoreo distribuido) comparado con un monolito. Se asume que OpenMarket Corp dispone de recursos suficientes.
