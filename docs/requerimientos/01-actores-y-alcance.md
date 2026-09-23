# 1. Actores, alcance y glosario

## 1.1 Objetivo del sistema

Aplicación web para gestionar la **generación automática de horarios docentes**: banco de bloques de contenido, generación de horarios a partir de bloques y parámetros, revisión/aprobación, asignación a estudiantes, registro de asistencia, solicitudes de cambio y reportes analíticos sobre un histórico de varios semestres.

## 1.2 Actores

| Actor | Descripción | Fuente |
|-------|-------------|--------|
| **Coordinador académico** | Ingresa bloques al banco y clasifica **los bloques que él describió**. Puede ver horarios de otros coordinadores que atienden el mismo programa. Está autorizado para uno o varios programas. | E |
| **Coordinador generador** | *No es otra entidad*: es un coordinador académico autorizado para el programa que ejecuta la generación automática. Quien crea bloques no tiene por qué ser quien genera el horario. | E |
| **Coordinador de apoyo** | Coordinador adicional (distinto del asignado) al que un estudiante pide apoyo para atender su solicitud de cambio. | E |
| **Jefe de departamento** | Revisa el horario generado: lo **aprueba** o indica confeccionar **otro bajo sus criterios**. | E |
| **Estudiante** | Consulta sus horarios por programa, consulta reportes de ocupación, solicita cambios de horario. Su asistencia queda registrada. | E |
| **Administrador del sistema** | Evita bloques duplicados e impide la doble asignación de horarios a estudiantes. Gestiona usuarios y catálogos. | E / S |

> Todos los actores pueden **exportar a PDF** lo que ven y **ordenar cualquier columna** de los resultados (E).

## 1.3 Alcance

**Dentro del alcance**
- Gestión de programas académicos, asignaturas, plan de estudios, aulas/edificios, periodos, grupos.
- Banco de bloques de contenido y su clasificación.
- Generación automática parametrizada y almacenamiento de los parámetros.
- Flujo de revisión por el jefe de departamento.
- Asignación de horarios a estudiantes y registro de asistencia.
- Solicitudes de cambio de horario y su resolución manual.
- Histórico de ocupación y asistencia en almacén analítico independiente (ClickHouse).
- Las 6 consultas/reportes del enunciado, con tablas, gráficos, exportación PDF y ordenamiento.

**Fuera del alcance (S)**
- Calificaciones/notas académicas (el enunciado no las pide; ver pregunta abierta P-01).
- Integración con sistemas externos de matrícula o identidad institucional.
- Aplicación móvil nativa (se cumple "multiplataforma" con web responsive).

## 1.4 Glosario

| Término | Definición |
|---------|------------|
| **Bloque de contenido** | Unidad de contenido a impartir. Tiene asignatura, tipo de actividad (teórica / práctica / laboratorio) y nivel de complejidad (bajo / medio / alto). Lo crea un coordinador. |
| **Horario** | Resultado de una generación para un programa y periodo. Contiene muchos `horario_bloque`. Guarda sus parámetros de generación y su estado. |
| **Horario-bloque (franja)** | Colocación de un bloque en un día, franja horaria y aula dentro de un horario. Ej.: "BD2, lunes 11:00–12:00, aula 3-204". |
| **Sesión impartida** | Ocurrencia concreta (con fecha) de un horario-bloque. Es lo que recibe asistencia. |
| **Horario final** | Horario en estado **APROBADO** por el jefe de departamento (necesario para la consulta 2). |
| **Parámetros de generación** | Proporción de bloques por tipo de actividad, cobertura de asignaturas y cantidad total de bloques. Se almacenan junto al horario. |
| **Criterio de equilibrio** | Comprobación de que la distribución real del horario respeta los parámetros objetivo (consulta 5). Definición exacta pendiente: P-05. |
| **Tasa de asistencia / inasistencia** | Asistencias (o ausencias) registradas ÷ asistencias esperadas para un bloque, estudiante, grupo u horario. |
| **Doble asignación** | Ver RN-06 y P-03. |
