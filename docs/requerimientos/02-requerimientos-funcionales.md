# 2. Requerimientos funcionales

Prioridad: **M** obligatorio · **S** debería · **C** podría. Fuente: ver README.

## 2.1 Autenticación y usuarios

| ID | Requerimiento | Actor | Prio | Fuente |
|----|---------------|-------|------|--------|
| RF-01 | El sistema debe autenticar a los usuarios y asignarles uno o más roles (estudiante, coordinador, jefe de departamento, administrador). | Todos | M | S |
| RF-02 | El administrador debe poder crear, editar y desactivar usuarios y asignarles roles. | Admin | M | S |
| RF-03 | El administrador debe poder autorizar a un coordinador para uno o varios programas académicos (generar y/o validar horarios). | Admin | M | E |

## 2.2 Catálogos académicos

| ID | Requerimiento | Actor | Prio | Fuente |
|----|---------------|-------|------|--------|
| RF-04 | Gestionar programas académicos: identificador único, nombre, plan de estudios y lista de asignaturas a cubrir en el semestre. | Admin | M | E |
| RF-05 | Gestionar asignaturas. | Admin | M | E |
| RF-06 | Gestionar coordinadores: identificador único, nombre, especialidad y programas autorizados. | Admin | M | E |
| RF-07 | Gestionar estudiantes: identificador único, nombre, edad, grupo y programas en los que está matriculado. | Admin | M | E |
| RF-08 | Gestionar aulas con edificio y capacidad (el edificio es necesario para el análisis por edificio). | Admin | M | E |
| RF-09 | Gestionar periodos académicos (semestres) y franjas horarias. | Admin | M | E (análisis por periodo y franja) |
| RF-10 | Gestionar grupos de estudiantes. | Admin | M | E / C6 |

## 2.3 Banco de bloques de contenido

| ID | Requerimiento | Actor | Prio | Fuente |
|----|---------------|-------|------|--------|
| RF-11 | Un coordinador debe poder ingresar bloques de contenido al banco. El sistema registra quién lo creó. | Coordinador | M | E |
| RF-12 | Un coordinador debe poder clasificar **únicamente los bloques que él creó** por asignatura y tipo de actividad (teórica, práctica, laboratorio). | Coordinador | M | E |
| RF-13 | Un coordinador debe poder asignar el nivel de complejidad (bajo, medio, alto) a **sus** bloques. | Coordinador | M | E |
| RF-14 | El sistema debe **impedir** el alta de un bloque duplicado (ver RN-05) e informar cuál es el bloque existente. | Sistema / Admin | M | E |
| RF-15 | El administrador debe poder consultar posibles duplicados (similares, no idénticos) y fusionarlos o eliminarlos. | Admin | S | S |
| RF-16 | Buscar y filtrar bloques por asignatura, tipo, complejidad y creador. | Coordinador | M | S |

## 2.4 Generación automática de horarios

| ID | Requerimiento | Actor | Prio | Fuente |
|----|---------------|-------|------|--------|
| RF-17 | Un coordinador autorizado para un programa debe poder seleccionar bloques del banco para generar un horario de ese programa y periodo. | Coordinador | M | E |
| RF-18 | Antes de generar, el coordinador debe definir los parámetros: proporción de bloques por tipo de actividad, cobertura de asignaturas y cantidad total de bloques. | Coordinador | M | E |
| RF-19 | El sistema debe generar el horario automáticamente, ubicando cada bloque en día, franja y aula, respetando RN-07 y RN-08. | Sistema | M | E |
| RF-20 | El sistema debe almacenar, junto al horario generado, sus parámetros, su creador y su fecha de creación. Estos datos son inmutables. | Sistema | M | E / C1 |
| RF-21 | Si no existe solución que cumpla los parámetros, el sistema debe informar qué restricción no se pudo satisfacer y no guardar un horario inválido. | Sistema | S | S |
| RF-22 | Un coordinador debe poder ver los horarios de otros coordinadores que atienden el mismo programa académico. | Coordinador | M | E |

## 2.5 Revisión y aprobación

| ID | Requerimiento | Actor | Prio | Fuente |
|----|---------------|-------|------|--------|
| RF-23 | El jefe de departamento debe poder listar los horarios pendientes de revisión. | Jefe dpto. | M | E |
| RF-24 | El jefe de departamento debe poder **aprobar** un horario, registrando fecha y observaciones. | Jefe dpto. | M | E / C3 |
| RF-25 | El jefe de departamento debe poder **rechazar** un horario e indicar la confección de otro, registrando sus criterios y observaciones. | Jefe dpto. | M | E |
| RF-26 | Cuando se genera un horario a partir de un rechazo, el nuevo horario debe quedar vinculado a la revisión que lo originó. | Sistema | S | S |

## 2.6 Asignación y consulta de horarios

| ID | Requerimiento | Actor | Prio | Fuente |
|----|---------------|-------|------|--------|
| RF-27 | Asignar horarios **aprobados** a los estudiantes matriculados en el programa. | Coordinador / Sistema | M | E |
| RF-28 | El sistema debe **impedir** la doble asignación de horarios a un estudiante (RN-06). | Sistema / Admin | M | E |
| RF-29 | Un estudiante debe poder consultar todos sus horarios, agrupados por programa académico. | Estudiante | M | E |
| RF-30 | Consultar disponibilidad de horarios y aulas (alta demanda en matrícula; ver RNF-03). | Estudiante / Coordinador | M | E |

## 2.7 Asistencia y ocupación

| ID | Requerimiento | Actor | Prio | Fuente |
|----|---------------|-------|------|--------|
| RF-31 | Registrar la asistencia de cada estudiante a cada sesión impartida de un horario-bloque. El sistema debe garantizar que cada registro se almacena. | Coordinador | M | E |
| RF-32 | Registrar la ocupación de aulas por franja, edificio y periodo. | Sistema | M | E |
| RF-33 | Consultar reportes de ocupación. | Coordinador / Estudiante | M | E |
| RF-34 | Trasladar la asistencia y ocupación al histórico analítico (ClickHouse) para análisis agregados por franja, edificio y periodo. | Sistema | M | E |

## 2.8 Solicitudes de cambio de horario

| ID | Requerimiento | Actor | Prio | Fuente |
|----|---------------|-------|------|--------|
| RF-35 | Un estudiante debe poder solicitar virtualmente un cambio de horario, indicando el motivo. | Estudiante | M | E |
| RF-36 | Al solicitarlo, el estudiante puede pedir el apoyo de un coordinador adicional al que tiene asignado. | Estudiante | M | E |
| RF-37 | El coordinador asignado o el de apoyo debe poder atender la solicitud y resolverla manualmente (aceptar con nuevo horario / rechazar). | Coordinador | M | E |
| RF-38 | Registrar todo el proceso (estados y fechas) y el **resultado del cambio manual**. | Sistema | M | E |

## 2.9 Reportes (consultas del enunciado)

Cada reporte se muestra en **tabla y gráfico**, con **ordenamiento por cualquier columna** y **exportación a PDF**.

| ID | Requerimiento | Actor | Prio | Fuente |
|----|---------------|-------|------|--------|
| RF-39 | Listado de horarios generados automáticamente para un programa: creador, fecha de creación y parámetros. | Coordinador / Jefe | M | C1 |
| RF-40 | Bloques más impartidos en los horarios **finales** de un programa, clasificados por complejidad y asignatura. | Coordinador / Jefe | M | C2 |
| RF-41 | Horarios validados por un revisor determinado: fecha de validación y observaciones. | Coordinador / Jefe | M | C3 |
| RF-42 | Desempeño de estudiantes en un horario: bloques clasificados por complejidad, comparando tasas de asistencia. | Coordinador / Jefe | M | C4 |
| RF-43 | Comparación de horarios de distintos programas: distribución por asignatura y complejidad, y si se cumplieron los criterios de equilibrio. | Coordinador / Jefe | M | C5 |
| RF-44 | Por programa: correlación complejidad ↔ rendimiento promedio; top 10 bloques con mayor inasistencia (con coordinador creador y programa); y comparación de estudiantes que solicitaron cambio vs. promedio de asistencia de su grupo en ese programa. | Coordinador / Jefe | M | C6 |
| RF-45 | Reportes de transparencia: creación de horarios, bloques más impartidos, distribución de complejidades y desempeño por complejidad. | Coordinador / Jefe | M | E (cubierto por RF-39..44) |
| RF-46 | Exportar a PDF cualquier resultado mostrado. | Todos | M | E |
| RF-47 | Ordenar cada columna de los resultados. | Todos | M | E |

## 2.10 Auditoría

| ID | Requerimiento | Actor | Prio | Fuente |
|----|---------------|-------|------|--------|
| RF-48 | Registrar en una bitácora de auditoría las operaciones sensibles: alta/baja de bloques, generación, revisión, asignación, cambios manuales y los intentos rechazados de duplicado o doble asignación. | Sistema | S | S |
| RF-49 | El administrador debe poder consultar la bitácora filtrando por usuario, entidad, operación y fecha. | Admin | S | S |
