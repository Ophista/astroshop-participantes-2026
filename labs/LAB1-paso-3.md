# Instrucciones para el análisis de astroshop

Trabajas con el ambiente Dynatrace Playground para analizar la observabilidad de astroshop (e-commerce sobre Kubernetes). Los datos están en Grail y se consultan con DQL. Tu objetivo es diagnosticar problemas con el mínimo de consultas necesarias.

## Herramientas
- IMPORTANT: Consulta datos ÚNICAMENTE con las herramientas del servidor MCP `dynatrace-playground`. No uses el servidor MCP `dynatrace-lab` ni sus herramientas. No uses `dtctl`.

## Ambiente
- Aplicación: astroshop (microservicios) en Kubernetes.
- Filtra SIEMPRE por `matchesPhrase(k8s.namespace.name, "astroshop")` para acotar a la app.

## Método de análisis (síguelo en orden)
1. Empieza por lo ya detectado: revisa problemas y eventos detectados por Davis (la IA de Dynatrace) antes de explorar a ciegas.
2. Identifica las entidades afectadas (servicio, workload, pod, application).
3. Cuantifica el impacto con las golden signals por servicio: tasa de peticiones, tasa de errores y latencia (p50/p90/p99).
4. Aísla el origen: compara la ventana del problema contra el estado previo y baja de servicio → span → log.
5. Confirma la causa raíz con evidencia concreta (spans fallidos, logs de error, saturación de CPU/memoria).

## Fuentes de datos (confirma los nombres exactos en tu Playground)
- Problemas/eventos detectados: `dt.davis.problems`, `dt.davis.events`
- Trazas, latencia y errores por request: `spans`
- Logs de aplicación: `logs`
- Métricas de recursos/infra (CPU, memoria, saturación): `timeseries` sobre métricas
- Series temporales a partir de registros: `makeTimeseries` o `bin`

## Tips de consulta
- Si no sabes qué campo contiene el dato, explóralo con `search`: `fetch spans | search "keyword" | limit 10`. No inventes nombres de campos.
- Los logs se asocian a entidades de infraestructura (pods, workloads, hosts, procesos, etc), no a servicios. No filtres logs por `dt.entity.service`; filtra por `k8s.namespace.name`.

## Reglas de eficiencia
- IMPORTANT: Acota siempre el timeframe (la ventana del problema o, por defecto, las últimas 2 h). Nunca consultes rangos abiertos.
- Agrega antes de traer registros crudos: usa `summarize`/`count`/`timeseries` para ver la forma del problema; baja a registros individuales solo cuando ya identificaste el servicio y la ventana.
- Añade `limit` (y `sort` cuando aplique) a toda consulta que devuelva registros.
- Filtra lo antes posible en el pipeline (namespace, servicio, severidad) para reducir el escaneo de datos.

## Rigor (para que el análisis sea verificable)
- IMPORTANT: Basa cada conclusión solo en datos que una consulta haya devuelto. Si un dato no aparece, dilo; no lo asumas ni lo inventes.
- No inventes nombres de campos ni de entidades. Si no conoces la estructura, explora primero con una muestra (`limit 5`) antes de la consulta final.
- Si una consulta falla o vuelve vacía repetidamente, corrige la consulta o el timeframe; o si es necesario pide mas informacion, no   fabriques resultados.
- Muestra siempre la consulta DQL que ejecutaste.

## Formato de cada hallazgo
- Síntoma: qué se observa, con el valor/métrica.
- Evidencia: la consulta DQL y su resultado clave.
- Entidad afectada: servicio / workload / pod / frontend.
- Causa raíz (hipótesis).
- Recomendación / siguiente paso.
- Apéndice: al final, lista cada consulta DQL que ejecutaste y devolvió datos. Por cada una, incluye la consulta y una línea de qué hace y qué aportó. Omite las que no aportaron.