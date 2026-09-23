# 5. Revisión del modelo conceptual acordado

Entidades acordadas por el equipo: `programa_academico`, `asignatura`, `coordinador_academico`, `estudiante`, `bloque_contenido`, `horario`, `horario_bloque`, `aula`, `ocupacion_historica`, `asistencia`, `solicitud_cambio_horario`, `revision_horario`, `administrador_sistema`, `auditoria`.

Los problemas están ordenados por gravedad.

## 5.1 Problemas críticos

### A. `auditoria` no resuelve el requisito que intenta cubrir
El enunciado dice que el administrador debe **evitar** bloques duplicados e **impedir** la doble asignación. Si la auditoría registra "asignaciones duplicadas para que el admin decida cuál eliminar", el duplicado **ya existe** en la base de datos. En ese caso el requisito no se cumple, solo se detecta la violación después.

**Propuesta:**
- La prevención va con restricciones de BD (RN-05, RN-06): `UNIQUE`, restricción de exclusión o trigger. El `INSERT` duplicado falla.
- `auditoria` pasa a ser una **bitácora real** (quién, qué, cuándo, valor anterior/nuevo). En ella también se registran los **intentos rechazados** de duplicado. Así el admin mantiene su pantalla de revisión, pero sin datos corruptos.
- Solo si el profesor confirma que "duplicado" incluye casos **similares** (no idénticos) tiene sentido una cola de revisión manual. Esa cola sería una tabla `posible_duplicado`, distinta de la auditoría.

### B. Falta el actor **jefe de departamento**
`revision_horario` necesita una FK al revisor, y la consulta 3 filtra por "un revisor determinado". Hay que decidir si el jefe es otra entidad o un coordinador con rol adicional (P-04). Mientras tanto, se propone `jefe_departamento` como entidad.

### C. Faltan las tablas de relaciones N:M que exige el enunciado
| Tabla | Relación | Por qué es obligatoria |
|-------|----------|------------------------|
| `programa_asignatura` | programa ↔ asignatura | "lista de asignaturas a cubrir" (cobertura, consulta 5) |
| `coordinador_programa` | coordinador ↔ programa | "programas para los que está autorizado" (RN-03, RN-04) |
| `estudiante_programa` | estudiante ↔ programa | "programas en los que está matriculado" |
| `asignacion_horario` | estudiante ↔ horario | Sin ella **no se puede impedir la doble asignación**, que es justo la regla que hoy intenta cubrir `auditoria` |

### D. `ocupacion_historica` y `asistencia`: falta decidir en qué motor vive cada una
- `asistencia` **transaccional** (el registro diario) → PostgreSQL. El enunciado exige "garantizar el almacenamiento de cada asistencia", y eso necesita ACID.
- **Histórico** de asistencia y ocupación → ClickHouse, desnormalizado (`hist_asistencia`, `hist_ocupacion`), alimentado por carga periódica.
- `ocupacion_historica` **no debe ser una tabla de PostgreSQL**. Si lo es, se incumple RNF-01.

### E. No hay nada que represente el "rendimiento" del estudiante
Las consultas 4 y 6 hablan de "desempeño" y "rendimiento promedio", pero el modelo no tiene notas. O el rendimiento se define como tasa de asistencia, o falta una entidad de evaluación (P-01). **Es la ambigüedad que más afecta la consulta 6.**

## 5.2 Entidades o atributos que faltan

| Elemento | Motivo |
|----------|--------|
| `grupo` | El estudiante "pertenece a un grupo" y la consulta 6 compara contra el promedio del grupo. No debe ser texto libre. |
| `periodo` (semestre) | El análisis es "por periodo" y "a lo largo de varios semestres". |
| `edificio` (o atributo en `aula`) | Análisis "por edificio". |
| `franja_horaria` | Análisis "por franja horaria". Normaliza los horarios de inicio y fin. |
| `sesion` (bloque impartido) | La asistencia se registra en una fecha concreta. `horario_bloque` es la franja semanal, y sin sesión no hay forma de saber a qué día corresponde una asistencia. |
| Parámetros en `horario` | `proporcion_teorica`, `proporcion_practica`, `proporcion_laboratorio`, `cobertura_minima`, `total_bloques`, o una tabla `parametro_generacion` 1:1. Los piden la consulta 1 y la 5. |
| `horario.estado`, `horario.creado_por`, `horario.fecha_creacion`, `horario.periodo_id`, `horario.programa_id` | Consultas 1 y 2 (horarios "finales"). |
| `revision_horario.resultado`, `observaciones`, `fecha`, `criterios` | Consulta 3 y "confeccionar otro bajo sus criterios". |
| `solicitud_cambio_horario`: `coordinador_apoyo_id`, `estado`, `resultado`, `horario_nuevo_id`, fechas | "Se registra el proceso y el resultado del cambio manual". |
| `bloque_contenido.creado_por` | RN-01 y consulta 6 ("el coordinador que los creó"). |
| `usuario` (credenciales + rol) | Recomendado: una tabla base `usuario` con perfiles 1:1 (`estudiante`, `coordinador_academico`, `jefe_departamento`, `administrador_sistema`). Así no se repite el login en cuatro tablas. |

## 5.3 Modelo propuesto (resumen, PostgreSQL)

```
usuario(id, email UNIQUE, hash_password, activo)
  ├─ administrador_sistema(usuario_id PK/FK)
  ├─ coordinador_academico(usuario_id PK/FK, nombre, especialidad)
  ├─ jefe_departamento(usuario_id PK/FK, nombre, departamento)
  └─ estudiante(usuario_id PK/FK, nombre, edad CHECK>0, grupo_id FK)

programa_academico(id, nombre, plan_estudios)
asignatura(id, nombre)
programa_asignatura(programa_id, asignatura_id)                PK compuesta
coordinador_programa(coordinador_id, programa_id)              PK compuesta
estudiante_programa(estudiante_id, programa_id)                PK compuesta
grupo(id, nombre, programa_id)
periodo(id, nombre, fecha_inicio, fecha_fin)
edificio(id, nombre) · aula(id, edificio_id, codigo, capacidad, tipo)
franja_horaria(id, hora_inicio, hora_fin)

bloque_contenido(id, titulo, titulo_normalizado, asignatura_id, tipo_actividad,
                 complejidad, creado_por FK coordinador, fecha_creacion)
                 UNIQUE(asignatura_id, tipo_actividad, titulo_normalizado)

horario(id, programa_id, periodo_id, creado_por, fecha_creacion, estado,
        es_automatico, total_bloques, cobertura_objetivo,
        prop_teorica, prop_practica, prop_laboratorio,
        origen_revision_id FK NULL)
horario_bloque(id, horario_id, bloque_id, aula_id, dia_semana, franja_id)
sesion(id, horario_bloque_id, fecha)
asignacion_horario(estudiante_id, horario_id, programa_id, periodo_id)
                  UNIQUE(estudiante_id, programa_id, periodo_id)
asistencia(estudiante_id, sesion_id, presente, registrado_en)  UNIQUE(estudiante_id, sesion_id)

revision_horario(id, horario_id, revisor_id FK jefe, fecha, resultado, observaciones, criterios)
solicitud_cambio_horario(id, estudiante_id, horario_actual_id, coordinador_asignado_id,
                         coordinador_apoyo_id NULL, motivo, estado, resultado,
                         horario_nuevo_id NULL, fecha_solicitud, fecha_resolucion)
auditoria(id, usuario_id, entidad, entidad_id, operacion, datos_antes JSONB,
          datos_despues JSONB, exito, fecha)
```

**ClickHouse (histórico, desnormalizado):**
```
hist_asistencia(fecha, periodo, programa, grupo, estudiante_id, bloque_id, asignatura,
                tipo_actividad, complejidad, coordinador_creador, edificio, aula,
                franja, presente UInt8)
  ENGINE = MergeTree PARTITION BY periodo ORDER BY (programa, fecha, bloque_id)

hist_ocupacion(fecha, periodo, edificio, aula, franja, capacidad, ocupados)
  ENGINE = MergeTree PARTITION BY periodo ORDER BY (edificio, franja, fecha)
```

> Nota técnica para el equipo de backend: EF Core **no tiene proveedor oficial para ClickHouse**. Para ClickHouse se usa el cliente ADO.NET (`ClickHouse.Client` / `ClickHouse.Driver`) con Dapper, detrás de una interfaz de repositorio. De esta forma se respeta Clean Architecture.
