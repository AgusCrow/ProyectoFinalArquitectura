# Distancia desde la Secuencia Principal

La métrica de distancia desde la secuencia principal (Robert C. Martin) permite evaluar el equilibrio estructural de los componentes críticos del sistema, identificando zonas de dolor (componentes estables pero concretos) o zonas de inutilidad (componentes abstractos pero inestables).

## Definiciones

- **Ca (Acoplamiento aferente):** Cantidad de componentes externos que dependen de este componente.
- **Ce (Acoplamiento eferente):** Cantidad de componentes externos de los que este componente depende.
- **Inestabilidad (I):** Grado de susceptibilidad al cambio.

    I = Ce / (Ca + Ce)

- **Na:** Número de clases abstractas e interfaces en el componente.
- **Nc:** Número total de clases en el componente.
- **Abstracción (A):** Proporción de abstracción del componente.

    A = Na / Nc

- **Distancia (D):** Distancia desde la secuencia principal ideal (A + I = 1):

    D = |A + I - 1|

Un valor de **D cercano a 0** indica buen balance entre abstracción e inestabilidad. Un valor **cercano a 1** indica una zona problemática.

## Cálculo aplicado a componentes críticos

Se analizan los componentes internos de los microservicios **Compras** y **Pagos**, seleccionados en el punto 5 por su centralidad en el negocio.

### Compras.Domain

- **Contenido:** Pedido, LineItem, IPedidoRepository (interfaz)
- **Ca = 3** → Los servicios CrearPedidoService, CancelarPedidoService y PedidoRepositoryPostgres dependen de este componente.
- **Ce = 0** → No depende de ningún componente externo (lógica pura).
- **I = 0 / (3 + 0) = 0.00** → Máxima estabilidad.
- **Na = 1** (IPedidoRepository es una interfaz), **Nc = 3** (Pedido, LineItem, IPedidoRepository).
- **A = 1/3 = 0.33**
- **D = |0.33 + 0.00 - 1| = 0.67**

### Compras.Application

- **Contenido:** CrearPedidoService, CancelarPedidoService
- **Ca = 1** → PedidoController depende de este componente.
- **Ce = 3** → Depende de Compras.Domain, IPedidoRepository y PedidoEventPublisherKafka.
- **I = 3 / (1 + 3) = 0.75** → Alta inestabilidad (muchas dependencias salientes).
- **Na = 0**, **Nc = 2** (ambos son clases concretas).
- **A = 0/2 = 0.00**
- **D = |0.00 + 0.75 - 1| = 0.25**

### Compras.Infrastructure

- **Contenido:** PedidoRepositoryPostgres, PedidoEventPublisherKafka
- **Ca = 0** → Ningún componente interno depende directamente de las implementaciones concretas (se usa la interfaz).
- **Ce = 2** → Depende de Compras.Domain (implementa IPedidoRepository) y de Kafka (sistema externo).
- **I = 2 / (0 + 2) = 1.00** → Máxima inestabilidad.
- **Na = 0**, **Nc = 2** (ambos son adaptadores concretos).
- **A = 0/2 = 0.00**
- **D = |0.00 + 1.00 - 1| = 0.00**

### Pagos.Infrastructure.Payment.Gateways.Core

- **Contenido:** GatewayFactory, PaymentGateway (interfaz abstracta), StripeGateway, PayPalGateway
- **Ca = 1** → ProcesarPagoService depende de este componente para procesar pagos.
- **Ce = 1** → Depende de las APIs externas de Stripe/PayPal.
- **I = 1 / (1 + 1) = 0.50**
- **Na = 1** (PaymentGateway es una interfaz), **Nc = 4** (GatewayFactory, PaymentGateway, StripeGateway, PayPalGateway).
- **A = 1/4 = 0.25**
- **D = |0.25 + 0.50 - 1| = 0.25**

## Tabla resumen

| Componente                          | Ca | Ce | I    | Na | Nc | A    | D    | Análisis                          |
|-------------------------------------|----|----|------|----|----|------|------|-----------------------------------|
| Compras.Domain                      | 3  | 0  | 0.00 | 1  | 3  | 0.33 | 0.67 | Zona de Dolor: Estable pero poco abstracto |
| Compras.Application                 | 1  | 3  | 0.75 | 0  | 2  | 0.00 | 0.25 | Bueno: Inestable y concreto       |
| Compras.Infrastructure              | 0  | 2  | 1.00 | 0  | 2  | 0.00 | 0.00 | Excelente: Sobre la secuencia principal |
| Pagos.Infrastructure.Gateways.Core  | 1  | 1  | 0.50 | 1  | 4  | 0.25 | 0.25 | Bueno: Equilibrado                |

## Interpretación y recomendaciones

- **Compras.Infrastructure (D=0.00):** Se ubica exactamente sobre la secuencia principal. Es un componente inestable (fácil de cambiar) y concreto, lo cual es ideal para adaptadores de infraestructura.
- **Compras.Application (D=0.25):** Buen balance. Los servicios de aplicación son inestables (dependen de dominio e infraestructura) y concretos. El valor bajo de D indica un diseño saludable.
- **Pagos.Infrastructure.Gateways.Core (D=0.25):** Buen equilibrio gracias a la interfaz PaymentGateway que provee abstracción. La GatewayFactory permite agregar nuevos gateways sin modificar la lógica existente.
- **Compras.Domain (D=0.67):** Es el componente que más se aleja de la secuencia principal. Se ubica en la **Zona de Dolor**: es muy estable (muchos componentes dependen de él) pero tiene baja abstracción. **Recomendación:** Introducir más interfaces o clases abstractas en el dominio (por ejemplo, una interfaz IEventPublisher o una clase abstracta base para entidades) para aumentar A y reducir D.

**Conclusión:**
La mayoría de los componentes están bien balanceados respecto a la secuencia principal. El único punto de atención es Compras.Domain, donde se recomienda aumentar la abstracción introduciendo interfaces adicionales. El análisis debe realizarse periódicamente para detectar rigideces estructurales y anticipar problemas de calidad a medida que el sistema evoluciona.
