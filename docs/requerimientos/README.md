# Especificación de Requerimientos — Generación automática de horarios docentes

Proyecto conjunto de **Bases de Datos II** e **Ingeniería de Software** (curso 2026–2027).

## Contenido

| # | Documento | Qué contiene |
|---|-----------|--------------|
| 1 | [01-actores-y-alcance.md](01-actores-y-alcance.md) | Actores, roles, alcance y glosario |
| 2 | [02-requerimientos-funcionales.md](02-requerimientos-funcionales.md) | RF agrupados por módulo, con prioridad |
| 3 | [03-requerimientos-no-funcionales.md](03-requerimientos-no-funcionales.md) | RNF (rendimiento, almacenamiento analítico, seguridad, calidad) |
| 4 | [04-reglas-de-negocio.md](04-reglas-de-negocio.md) | Reglas de negocio y cómo se garantizan en la BD |
| 5 | [05-revision-modelo-conceptual.md](05-revision-modelo-conceptual.md) | Revisión de las entidades acordadas y correcciones propuestas |
| 6 | [06-trazabilidad-consultas.md](06-trazabilidad-consultas.md) | Matriz consultas 1–6 ↔ RF ↔ entidades ↔ motor (PostgreSQL / ClickHouse) |
| 7 | [07-preguntas-abiertas.md](07-preguntas-abiertas.md) | Ambigüedades del enunciado que hay que cerrar con los profesores |

## Convenciones

- **RF-xx**: requerimiento funcional. **RNF-xx**: no funcional. **RN-xx**: regla de negocio.
- **Prioridad** (MoSCoW): **M** = obligatorio por enunciado, **S** = debería, **C** = podría.
- **Fuente**: `E` = texto literal del enunciado, `C#` = consulta # del enunciado, `IS#` = requisito # de Ingeniería de Software, `S` = supuesto del equipo (debe validarse; ver doc. 7).
- Todo lo marcado como `S` **no es un requisito confirmado**: es una decisión nuestra hasta que el profesor la valide.

## Stack de referencia

React + TypeScript · ASP.NET Core (C#) · EF Core · PostgreSQL · ClickHouse · REST + OpenAPI · Clean Architecture · GitHub.
