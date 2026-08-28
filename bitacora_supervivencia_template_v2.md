# Bitácora de supervivencia — TrámiteFácil+

**Estudiante:** Jordan Brown S
**Sección:** 11-6
**Fecha:** 28/8/26

## Escenario

Durante la ejecución de la prueba de performance (JMeter, listado de trámites
con 800 registros simulados — ver Anexo 1), el servidor principal de
TrámiteFácil+ se satura y queda fuera de línea.

## 1. Identificación

<!-- ¿Cómo se detectó que el servidor había caído? ¿Qué señal o dato lo evidenció? -->

Durante la prueba de carga con JMeter sobre el listado de 800 registros, se detectó una pérdida de conectividad HTTP 503 / Timeout en el servidor, confirmando que el uso de CPU/Memoria llegó al 100% y dejó fuera de línea el servicio

## 2. Contención

<!-- ¿Qué acción se tomó de inmediato para limitar el impacto? -->

Se aisló el módulo afectado restringiendo temporalmente las peticiones masivas al listado general de trámites mediante el bloqueo de tráfico no prioritario e imponiendo un límite de peticiones (rate limiting) para mitigar el colapso del servidor

## 3. Recuperación

<!-- ¿Qué acción concreta permitió que el sistema siguiera operando para
     trámites urgentes? Esta acción debe reflejarse en un commit de este
     repositorio con un mensaje descriptivo. -->

Se habilitó un modo de contingencia ("Modo Trámites Urgentes") que deshabilita la carga pesada del historial completo y permite procesar únicamente las solicitudes de vencimiento del día

**Commit de recuperación:** git commit -m "Activa modo trámites-urgentes mientras se optimiza el listado de trámites"

## 4. Aprendizaje / mejora

<!-- ¿Qué estrategia complementaria (respaldo, redundancia o monitoreo)
     hubiera anticipado este resultado, en relación con el criterio de
     performance del Anexo 1 (listado de trámites en menos de 4 segundos)? -->
La falla evidenció el incumplimiento del criterio de aceptación (carga en menos de 4 segundos con 800 registros). Como estrategia complementaria, se implementará la paginación a nivel de base de datos junto con una réplica de lectura (redundancia) y monitoreo en tiempo real de latencia, asegurando respuestas óptimas bajo alta demanda
