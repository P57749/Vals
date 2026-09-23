# 3. Requerimientos no funcionales

## 3.1 Almacenamiento y rendimiento (núcleo de Bases de Datos II)

| ID | Requerimiento | Fuente | Cómo se cumple (propuesta) |
|----|---------------|--------|----------------------------|
| RNF-01 | El **histórico de ocupación y asistencia** de varios semestres debe almacenarse **de forma independiente al modelo relacional**, priorizando la velocidad de consultas agregadas (sumas, promedios, comparaciones por periodo) sobre la de operaciones individuales. | E | **ClickHouse** (columnar, OLAP). Tablas desnormalizadas `MergeTree`, `PARTITION BY periodo`, `ORDER BY (edificio, franja, fecha)`. Vistas materializadas para agregados frecuentes. |
| RNF-02 | El modelo transaccional (bloques, horarios, revisiones, solicitudes, asignaciones) debe garantizar integridad y consistencia. | E / S | **PostgreSQL** con PK/FK, `UNIQUE`, `CHECK`, restricciones de exclusión y transacciones ACID. |
| RNF-03 | La consulta de **disponibilidad de horarios y aulas** debe mantener tiempos de respuesta mínimos ante demanda concentrada en periodos de matrícula. | E | Ver nota ⚠️ abajo. Meta propuesta (S): p95 < 300 ms con 200 usuarios concurrentes. |
| RNF-04 | La carga desde PostgreSQL hacia ClickHouse no debe bloquear las operaciones transaccionales. | S | Carga asíncrona por lotes (job programado) o CDC. Latencia aceptada del histórico: ≤ 24 h (S). |
| RNF-05 | Las consultas 1–6 deben ejecutarse sobre el motor adecuado (ver doc. 6). | S | Consultas sobre datos vigentes → PostgreSQL; sobre histórico multi-semestre → ClickHouse. |

> ⚠️ **RNF-03 no queda cubierto por el stack actual.** PostgreSQL + ClickHouse no resuelven por sí solos "lecturas repetidas en picos cortos". ClickHouse no está pensado para muchas consultas puntuales concurrentes. Opciones:
> 1. **Redis** como caché de disponibilidad (clave-valor, TTL, invalidación al asignar/cambiar). Es la opción estándar y además aporta otro modelo NoSQL al proyecto.
> 2. Caché en memoria de ASP.NET Core (`IMemoryCache`/`HybridCache`) + vista materializada en PostgreSQL. Es más simple, pero no escala a varias instancias.
>
> Esto debe decidirlo el equipo de arquitectura y validarse contra el anexo de requisitos de BD (P-08).

## 3.2 Seguridad

| ID | Requerimiento | Fuente |
|----|---------------|--------|
| RNF-06 | Autenticación con credenciales cifradas (hash con sal, p. ej. PBKDF2/BCrypt) y tokens (JWT). | S |
| RNF-07 | Autorización por rol y por programa: un coordinador solo opera sobre programas autorizados, un estudiante solo ve sus propios datos. | E / S |
| RNF-08 | La API debe validar todas las entradas y no exponer detalles internos en errores. | S |

## 3.3 Calidad y proceso (requisitos de Ingeniería de Software)

| ID | Requerimiento | Fuente |
|----|---------------|--------|
| RNF-09 | Control de versiones en GitHub. | IS1 |
| RNF-10 | Planificación en herramienta CASE (GitHub Projects / Jira). | IS2 |
| RNF-11 | Sistema multiplataforma (web responsive en navegadores modernos de escritorio y móvil). | IS3 |
| RNF-12 | Cumplir todos los RF del enunciado. | IS4 |
| RNF-13 | Buenas prácticas y **docstrings en todo el código** (XML doc en C#, TSDoc en TypeScript). | IS5 |
| RNF-14 | Implementar al menos **dos patrones**. Propuesta: *Repository* + *Unit of Work* (acceso a datos), *Strategy* (algoritmo de generación intercambiable), *Specification* (filtros de reportes). | IS6 |
| RNF-15 | Arquitectura desacoplada, extensible y mantenible (Clean Architecture). | IS7 |
| RNF-16 | Pruebas unitarias en back-end (xUnit) y front-end (Vitest/Jest + Testing Library). | IS8 |
| RNF-17 | API documentada con OpenAPI/Swagger. | S |

> IS1–IS6 son **indispensables para aprobar** Ingeniería de Software.

## 3.4 Usabilidad y reportes

| ID | Requerimiento | Fuente |
|----|---------------|--------|
| RNF-18 | Todos los reportes se muestran en tabla y gráfico. | E |
| RNF-19 | Exportación PDF disponible para todo tipo de usuario, fiel a lo mostrado (incluye filtros y orden aplicados). | E |
| RNF-20 | Ordenamiento por cualquier columna. En resultados grandes, el ordenamiento se hace en el servidor (paginado). | E / S |
