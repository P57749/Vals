# 7. Preguntas abiertas para los profesores

Hay que llevarlas a la próxima clase. Cada respuesta cambia el modelo o una consulta.

| ID | Pregunta | Impacto | Supuesto actual |
|----|----------|---------|-----------------|
| P-01 | ¿Qué es el **"rendimiento"/"desempeño"** del estudiante? ¿Solo la tasa de asistencia, o hay que modelar calificaciones? | Consultas 4 y 6; posible nueva entidad `evaluacion` | Rendimiento = tasa de asistencia |
| P-02 | ¿Qué hace que dos bloques sean **duplicados**? ¿Idénticos (misma asignatura + tipo + título) o también "similares"? | RN-05; si son similares, hace falta una cola de revisión manual | Idénticos tras normalizar el título |
| P-03 | ¿Qué es **"asignación doble"**? ¿Dos horarios para el mismo programa y periodo, el mismo horario dos veces o franjas solapadas? | RN-06; tipo de restricción (UNIQUE vs. exclusión temporal) | Las tres situaciones están prohibidas |
| P-04 | ¿El **jefe de departamento** es una entidad propia o un coordinador con un rol adicional? | Modelo de usuarios y FK de `revision_horario` | Entidad propia |
| P-05 | ¿Cómo se mide que **"los criterios de equilibrio fueron cumplidos"**? ¿Con qué tolerancia? | Consulta 5 | Proporción real dentro de ±10 % del objetivo y cobertura del 100 % de las asignaturas del programa |
| P-06 | "Bloques **más impartidos**": ¿se cuentan apariciones en horarios aprobados o sesiones realmente dictadas? | Consulta 2 | Sesiones dictadas en horarios aprobados |
| P-07 | ¿El horario se asigna **por grupo** o **por estudiante individual**? | `asignacion_horario`, solicitudes de cambio | Por estudiante (se precarga desde el grupo) |
| P-08 | ¿Podemos ver el **anexo "Requisitos particulares de Bases de Datos"**? El enunciado lo menciona pero no lo incluye. | Puede exigir motores NoSQL concretos, procedimientos almacenados, triggers, índices, replicación, etc. | — (**bloqueante**) |
| P-09 | ¿Cuál es el volumen esperado del histórico (semestres, estudiantes, sesiones)? | Diseño de particiones en ClickHouse y datos de prueba | 4 semestres, ~2 000 estudiantes |
| P-10 | ¿El algoritmo de generación debe ser óptimo o basta con uno heurístico (voraz o *backtracking*)? | Alcance del módulo de generación | Heurístico con validación de restricciones |
