# Bitácora de delegación · Lab 4a

**Equipo:** Emmanuel Cortes, Sara Hurtado, Juan Jose Osorio y Mariana Sanchez

| Tarea | ¿Delegado a agente? | Justificación |
|---|---|---|
| `@daily` y `catchup=False` | Parcial | La plantilla ya traía ambas opciones. El equipo confirmó seguirlas y Codex ayudó a explicar sus consecuencias. Codex añadió `max_active_runs=1` para ejecutar los backfills en secuencia. |
| Idempotencia de `ingest_bronze` | Parcial | La guía proponía sobrescribir solo la carpeta de `ds`; el equipo aprobó esa estrategia y Codex implementó la escritura Parquet. |
| Idempotencia de `transform_silver` | Parcial | La guía proponía `MERGE` por `pedido_id`; el equipo aprobó esa estrategia y Codex implementó las ramas de actualización e inserción. |
| Checks de `validate_bronze` y umbral de nulos | Parcial | El equipo aprobó seguir la guía: más de 5 % de nulos críticos y cualquier `total <= 0` bloquean la corrida; más de cuatro canales solo genera una advertencia. Codex implementó los dos checks faltantes. |
| Casts, filtro de devueltos y agregaciones Gold | Sí | Codex programó estas transformaciones conforme a los requisitos del README y el equipo debe poder explicarlas en la sustentación. |
| Dataset, entorno, pruebas y evidencias | Sí | Codex generó el CSV determinista, preparó Airflow, ejecutó los tests y realizó las corridas y verificaciones documentadas en `dag_design.md`. |
| Redacción de `dag_design.md` | Sí | Codex redactó el documento con los resultados observados durante la ejecución; el equipo revisará que la explicación refleje sus decisiones. |
