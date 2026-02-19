# Distancia desde la Secuencia Principal

La métrica de distancia desde la secuencia principal permite evaluar el equilibrio estructural de los componentes críticos del sistema, identificando zonas de dolor (acoplamiento excesivo) o inutilidad (abstracción sin uso).

## Definiciones

- **Abstracción (A):** Proporción de clases abstractas e interfaces respecto al total de clases en el componente.
- **Inestabilidad (I):** Medida de dependencia hacia otros componentes (Ce) y dependencias recibidas (Ca):

    I = Ce / (Ca + Ce)

- **Distancia (D):** Distancia desde la secuencia principal (A + I = 1):

    D = |A + I - 1|

## Ejemplo de cálculo (Microservicio Órdenes)

| Componente         | Ca  | Ce  | I    | Na  | Nc  | A    | D    | Análisis                                 |
|--------------------|-----|-----|------|-----|-----|------|------|------------------------------------------|
| Order.Domain       | 15  | 1   | 0.06 | 8   | 10  | 0.80 | 0.14 | Excelente: Muy estable y abstracto        |
| Catalog.Infrastructure | 2   | 12  | 0.85 | 1   | 15  | 0.06 | 0.09 | Bueno: Inestable y concreto               |
| Payment.Gateways.Core | 25  | 2   | 0.07 | 0   | 10  | 0.00 | 0.93 | Zona de Dolor: Estable y concreto         |

- Un valor de D cercano a 0 indica buen balance.
- Un valor cercano a 1 indica zona de dolor o inutilidad.

## Interpretación y recomendaciones

- Los componentes con D bajo (ej. Order.Domain) son balanceados, fáciles de mantener y evolucionar.
- Los componentes con D alto (ej. Payment.Gateways.Core) requieren refactorización: introducir interfaces, reducir dependencias o modularizar.
- El análisis debe realizarse periódicamente para detectar rigideces estructurales y anticipar problemas de calidad.

**Justificación:**
La aplicación de esta métrica permite tomar decisiones informadas sobre refactorización, modularización y evolución de la arquitectura, asegurando que los componentes críticos permanezcan flexibles y robustos ante cambios futuros.
