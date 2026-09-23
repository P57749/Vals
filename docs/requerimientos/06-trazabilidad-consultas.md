# 6. Trazabilidad: consultas del enunciado ↔ requerimientos ↔ datos

| Consulta | RF | Entidades requeridas | Motor | Observaciones |
|----------|----|----------------------|-------|---------------|
| **C1** Horarios generados automáticamente para un programa: creador, fecha, parámetros | RF-39 | `horario` (es_automatico, parámetros, fecha_creacion), `coordinador_academico`, `programa_academico` | PostgreSQL | Si los parámetros no están en `horario`, la consulta no se puede hacer. |
| **C2** Bloques más impartidos en horarios **finales** de un programa, por complejidad y asignatura | RF-40 | `horario` (estado = APROBADO), `horario_bloque`, `bloque_contenido`, `asignatura`. "Impartido" = `sesion` | PostgreSQL (vigente) / ClickHouse (multi-semestre) | Sin estado en el horario no se puede distinguir cuál es "final". Hay que decidir si "más impartido" cuenta apariciones en horarios o sesiones reales (P-06). |
| **C3** Horarios validados por un revisor: fecha y observaciones | RF-41 | `revision_horario` (resultado = APROBADO), `jefe_departamento`, `horario` | PostgreSQL | Requiere la entidad del revisor. |
| **C4** Desempeño de estudiantes en un horario, bloques por complejidad, comparando tasas de asistencia | RF-42 | `asignacion_horario`, `horario_bloque`, `sesion`, `asistencia`, `bloque_contenido.complejidad` | PostgreSQL | `tasa = presentes / esperadas` agrupado por complejidad. |
| **C5** Comparar horarios de varios programas: distribución por asignatura y complejidad, y cumplimiento de criterios de equilibrio | RF-43 | `horario` (parámetros), `horario_bloque`, `bloque_contenido`, `programa_asignatura` | PostgreSQL | Hay que **definir "equilibrio cumplido"**: proporción real vs. objetivo con tolerancia ±X %, y cobertura = asignaturas presentes / asignaturas del programa (P-05). |
| **C6** Por programa: correlación complejidad ↔ rendimiento; top 10 bloques con mayor inasistencia + creador + programa; estudiantes con solicitud de cambio vs. promedio de su grupo | RF-44 | `hist_asistencia` (ClickHouse) o `asistencia` + `sesion` + `bloque_contenido` + `grupo` + `solicitud_cambio_horario` | ClickHouse (recomendado) | La complejidad es **ordinal** (BAJO=1, MEDIO=2, ALTO=3). Correlación: `corr()` de ClickHouse (Pearson) o Spearman por rangos. Justificar la elección en el informe. Depende de la definición de "rendimiento" (P-01). |

## Cobertura de requisitos del enunciado

| Frase del enunciado | Cubierto por |
|---------------------|--------------|
| "ingresar bloques de contenido al banco" | RF-11 |
| "sobre aquellos bloques descritos por él, clasificarlos…" | RF-12, RF-13, RN-01 |
| "generar horarios de forma automática a partir de los bloques seleccionados" | RF-17..RF-19 |
| "ver horarios de otros coordinadores que atienden el mismo programa" | RF-22, RN-04 |
| "evitar la existencia de bloques duplicados" | RF-14, RN-05 |
| "impedir la asignación doble de horarios" | RF-28, RN-06 |
| "garantizar el almacenamiento de cada asistencia" | RF-31, RN-13, RNF-02 |
| "reportes de ocupación… coordinadores y estudiantes" | RF-33 |
| "solicitar de forma virtual un cambio… apoyo de un coordinador adicional… resultado del cambio manual" | RF-35..RF-38, RN-12 |
| "parametrización almacenada junto al horario" | RF-18, RF-20, RN-09 |
| "jefe de departamento revisa y aprueba… o indica la confección de otro" | RF-23..RF-26, RN-10, RN-11 |
| "histórico… independiente del modelo relacional" | RF-34, RNF-01 |
| "tiempos de respuesta mínimos… períodos de matrícula" | RF-30, RNF-03 |
| "exportar a PDF… ordenar cada columna" | RF-46, RF-47 |
