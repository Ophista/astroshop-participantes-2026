# Instrucciones para el análisis de astroshop

Trabajas con Dynatrace Playground para analizar la observabilidad de astroshop
(e-commerce sobre Kubernetes). Los datos están en Grail y se consultan con DQL.
Tu objetivo es diagnosticar problemas con el mínimo de consultas necesarias.

## Herramientas
- IMPORTANT: Consulta datos ÚNICAMENTE con las herramientas del servidor MCP
  `dynatrace-playground`. No uses el servidor `dynatrace-lab` ni sus herramientas
  (incluido el Root Cause Agent). No uses `dtctl`.

## Ambiente
- Aplicación: astroshop (microservicios) en Kubernetes.
- Filtra SIEMPRE por `k8s.namespace.name == "astroshop"` para acotar a la app.
