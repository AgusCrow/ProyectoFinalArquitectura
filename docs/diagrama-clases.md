# Diagrama de Clases - Componentes Críticos

Se seleccionan los componentes del microservicio de **Compras** y del microservicio de **Pagos** por su centralidad en el dominio y su relevancia para el cálculo de métricas de distancia desde la secuencia principal.

## Diagrama de clases — Dominio de Compras

```mermaid
classDiagram
    class Pedido {
        +int id
        +DateTime fecha
        +string estado
        +decimal total
        +CalcularTotal()
        +AgregarItem()
        +Cancelar()
    }
    class Cliente {
        +int id
        +string nombre
        +string email
        +Registrar()
    }
    class Producto {
        +int id
        +string nombre
        +decimal precio
        +int stock
        +ActualizarStock()
    }
    class LineItem {
        +int cantidad
        +decimal subtotal
    }
    Pedido "1" -- "*" LineItem : contiene
    LineItem "*" -- "1" Producto : referencia
    Pedido "1" -- "1" Cliente : pertenece a
```

## Diagrama de componentes — Microservicio Compras

Este diagrama muestra las dependencias entre los componentes internos del microservicio, organizados según la arquitectura hexagonal. Es la base para el cálculo de métricas del punto 6.

```mermaid
classDiagram
    class PedidoController {
        <<Puerto de entrada>>
        +crearPedido()
        +cancelarPedido()
        +obtenerPedido()
    }
    class CrearPedidoService {
        <<Caso de uso>>
        +ejecutar()
    }
    class CancelarPedidoService {
        <<Caso de uso>>
        +ejecutar()
    }
    class Pedido {
        <<Entidad de dominio>>
        +CalcularTotal()
        +AgregarItem()
        +Cancelar()
    }
    class LineItem {
        <<Entidad de dominio>>
        +cantidad
        +subtotal
    }
    class IPedidoRepository {
        <<Interfaz / Puerto de salida>>
        +guardar()
        +buscarPorId()
        +actualizar()
    }
    class PedidoRepositoryPostgres {
        <<Adaptador de salida>>
        +guardar()
        +buscarPorId()
        +actualizar()
    }
    class PedidoEventPublisherKafka {
        <<Adaptador de salida>>
        +publicar()
    }

    PedidoController --> CrearPedidoService
    PedidoController --> CancelarPedidoService
    CrearPedidoService --> Pedido
    CrearPedidoService --> IPedidoRepository
    CrearPedidoService --> PedidoEventPublisherKafka
    CancelarPedidoService --> Pedido
    CancelarPedidoService --> IPedidoRepository
    CancelarPedidoService --> PedidoEventPublisherKafka
    Pedido --> LineItem
    PedidoRepositoryPostgres ..|> IPedidoRepository
```

**Explicación:**
- Solo se incluyen atributos y métodos esenciales para el análisis arquitectónico y de métricas.
- El diagrama de dominio muestra las entidades y sus relaciones (Pedido, LineItem, Cliente, Producto).
- El diagrama de componentes muestra las dependencias entre capas hexagonales: los controladores dependen de los servicios de aplicación, estos dependen del dominio y de los puertos de salida, y los adaptadores implementan las interfaces.
- Las flechas representan dependencias que se usan para calcular Ca (acoplamiento aferente) y Ce (acoplamiento eferente) en el punto 6.
- Se priorizó el análisis a nivel de componentes sobre clases individuales, como recomienda la actividad.

**Justificación de la elección:**
El dominio de Compras/Pedidos es crítico porque orquesta la relación entre clientes, productos y pagos, y es el punto central para la trazabilidad y el análisis de calidad estructural del sistema. Además, permite observar claramente la separación hexagonal y calcular métricas significativas.
