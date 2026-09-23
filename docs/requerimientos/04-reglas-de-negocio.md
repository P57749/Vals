# 4. Reglas de negocio

Para BD2 conviene que cada regla indique **dónde se garantiza**. Si una regla vive solo en C#, cualquier `INSERT` directo la rompe. Las reglas de integridad deben estar en la base de datos. La aplicación puede validar antes para dar mejores mensajes, pero la BD es la última defensa.

| ID | Regla | Fuente | Garantía propuesta |
|----|-------|--------|--------------------|
| RN-01 | Solo el coordinador que creó un bloque puede clasificarlo (asignatura, tipo, complejidad). | E | Autorización en la capa de aplicación + `bloque_contenido.creado_por` NOT NULL. Opcional: trigger que compara el usuario de sesión. |
| RN-02 | Tipo de actividad ∈ {TEORICA, PRACTICA, LABORATORIO}. Complejidad ∈ {BAJO, MEDIO, ALTO}. | E | Tipo `ENUM` de PostgreSQL o tabla catálogo con FK. |
| RN-03 | Un coordinador solo genera o valida horarios de programas para los que está autorizado. | E | FK compuesta hacia `coordinador_programa (coordinador_id, programa_id)` o trigger. |
| RN-04 | Un coordinador puede ver horarios de otros coordinadores que atienden el mismo programa. | E | Filtro por `coordinador_programa` en la consulta. |
| RN-05 | **No pueden existir bloques duplicados.** Propuesta de criterio: misma asignatura, mismo tipo de actividad y mismo título normalizado (minúsculas, sin tildes ni espacios extra). | E / S | Índice `UNIQUE (asignatura_id, tipo_actividad, titulo_normalizado)`. El criterio exacto está pendiente: P-02. |
| RN-06 | **Un estudiante no puede tener doble asignación.** Propuesta: (a) como máximo un horario por estudiante, programa y periodo; (b) ninguna franja asignada puede solaparse en tiempo con otra del mismo estudiante. | E / S | (a) `UNIQUE (estudiante_id, programa_id, periodo_id)` en `asignacion_horario`. (b) Trigger o restricción de exclusión (`btree_gist` + `tsrange`). |
| RN-07 | Un aula no puede tener dos horario-bloques en la misma franja del mismo periodo. | S | `EXCLUDE USING gist (aula_id WITH =, dia WITH =, franja WITH &&)` sobre horarios activos. |
| RN-08 | La capacidad del aula debe ser ≥ el número de estudiantes asignados. Un bloque de LABORATORIO requiere un aula de tipo laboratorio. | S | Validación en el generador + trigger de verificación. |
| RN-09 | Los parámetros de generación se guardan con el horario y **no se modifican** después. | E | Columnas/tabla sin endpoint de actualización. Opcional: trigger que rechaza `UPDATE`. |
| RN-10 | Ciclo de vida del horario: `GENERADO → EN_REVISION → APROBADO` o `→ RECHAZADO`. Solo un horario APROBADO es "final" y puede asignarse a estudiantes. | E / S | Columna `estado` + `CHECK` + validación de transiciones en el dominio. |
| RN-11 | El revisor de un horario debe ser jefe de departamento y distinto del coordinador que lo generó. | E / S | FK a `jefe_departamento` + `CHECK`/trigger. |
| RN-12 | El coordinador de apoyo de una solicitud debe ser distinto del coordinador asignado y estar autorizado para el programa. | E / S | `CHECK (coordinador_apoyo_id <> coordinador_asignado_id)` + FK a `coordinador_programa`. |
| RN-13 | Solo se registra asistencia de estudiantes asignados al horario que contiene ese bloque, en sesiones con fecha ≤ hoy. Cada registro es único por estudiante y sesión. | E / S | `UNIQUE (estudiante_id, sesion_id)` + trigger de pertenencia. |
| RN-14 | Un estudiante solo puede tener una solicitud de cambio abierta por horario. | S | Índice único parcial `WHERE estado IN ('PENDIENTE','EN_ATENCION')`. |
| RN-15 | Un estudiante solo recibe horarios de programas en los que está matriculado. | E | FK a `estudiante_programa`. |
