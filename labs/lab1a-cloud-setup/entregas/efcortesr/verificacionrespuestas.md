# Verificación - Respuestas del notebook

## Análisis

### a) Tamaño en disco

El archivo Parquet pesó aproximadamente `185.4 KiB`, mientras que el CSV pesó aproximadamente `798.3 KiB`. En esta comparación, el CSV ocupa cerca de `4.30x` más espacio que el Parquet.

Esto confirma lo esperado: Parquet es más compacto porque guarda los datos en formato columnar y usa compresión, mientras que CSV guarda texto plano fila por fila. Para un datalake, esa diferencia importa porque menos tamaño significa menos almacenamiento, menos lectura desde S3 y normalmente menor costo al procesar.

### b) Tiempo de la consulta

En nuestra ejecución de la Celda 3, la consulta tardó:

| Formato | Tiempo |
|---|---:|
| Parquet | `0.993 s` |
| CSV | `0.716 s` |

Aunque Parquet suele ser más eficiente, en esta corrida concreta CSV terminó más rápido. No lo interpretamos como que CSV sea mejor para analítica, sino como un resultado influenciado por el tamaño pequeño del dataset y el overhead de Spark.

### c) Ratio de mejora

El ratio obtenido fue:

```text
CSV / Parquet = 0.72x
```

Ese resultado no coincide con el orden de magnitud visto en clase, donde se esperaba algo cercano a `~9x` a favor de Parquet. En nuestro caso, Parquet no mejoró el tiempo; de hecho, fue un poco más lento que CSV.

La diferencia la atribuimos principalmente a tres factores. Primero, el dataset tiene solo `10.000` filas, así que Spark gasta una parte importante del tiempo en iniciar jobs, planear la ejecución y coordinar tareas, más que en leer datos realmente grandes. Segundo, la consulta devolvió `0` filas, por lo que el trabajo efectivo de agregación fue muy pequeño. Tercero, el clúster era mínimo, con un master y un core `m5.xlarge`, suficiente para validar la arquitectura, pero no para demostrar una diferencia fuerte de rendimiento en un dataset tan pequeño.

Nuestra conclusión es que Parquet sí mostró ventaja clara en tamaño, pero no en tiempo para esta corrida puntual. Para ver una mejora más parecida a la de clase necesitaríamos un dataset más grande, varias particiones, más columnas y consultas donde el column pruning y el predicate pushdown tengan más impacto.

### d) Conexión con el Teorema CAP

S3 con replicación entre múltiples zonas de disponibilidad se entiende como una decisión **CP** porque prioriza consistencia y tolerancia a particiones. Cuando escribimos un objeto en S3, el servicio no confirma la escritura hasta que puede mantener una versión consistente y durable del dato. Después de una escritura exitosa, una lectura debe ver la versión más reciente del objeto.

En términos de CAP, S3 está protegiendo la consistencia incluso si hay fallas parciales de red o problemas entre zonas de disponibilidad. Lo que sacrifica a cambio es disponibilidad absoluta en ciertos escenarios extremos: si no puede garantizar que el dato quede consistente y replicado correctamente, puede rechazar o retrasar una operación antes que confirmar una escritura que luego deje versiones inconsistentes. Para nuestro datalake eso tiene sentido, porque preferimos que Spark lea datos correctos y consistentes desde Bronze antes que procesar una versión incompleta o desactualizada.

## Bitácora de delegación del notebook

| Tarea | ¿Delegado a agente? | Herramienta | Justificación |
|---|---|---|---|
| Boilerplate de SparkSession / lectura de S3 | Parcial | Codex | Nos apoyamos en el agente para revisar el notebook, confirmar el bucket correcto y entender que debíamos usar el kernel PySpark en EMR Studio. La estructura base de `SparkSession` y lectura desde S3 ya venía en el laboratorio. |
| Diseño de la consulta del benchmark (Celda 3) | No | No aplica | La consulta ya estaba definida por el notebook del laboratorio. Nosotros la ejecutamos como estaba para mantener la comparación esperada entre Parquet y CSV. |
| Interpretación de los resultados (Celda 4) | Parcial | Codex | El agente nos ayudó a ordenar la explicación, pero usamos nuestros resultados reales: Parquet `0.993 s`, CSV `0.716 s`, ratio `0.72x` y `0` filas de resultado. La conclusión fue nuestra: el dataset pequeño y el overhead de Spark explican por qué no vimos la mejora esperada de Parquet. |
| Troubleshooting de errores de conexión a S3 | Sí | Codex / AWS CLI | Delegamos diagnóstico operativo porque aparecieron problemas de credenciales temporales, clúster recreado, key pair inválido, aplicaciones faltantes para EMR Studio, security groups y kernel incorrecto. Eso nos ayudó a terminar la ejecución, pero no reemplazó el análisis conceptual del benchmark ni la conexión con CAP. |
