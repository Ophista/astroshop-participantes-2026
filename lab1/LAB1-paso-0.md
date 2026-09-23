# Instrucciones para el análisis de astroshop

Trabajas con el ambiente Dynatrace Playground para analizar la observabilidad de astroshop (e-commerce sobre Kubernetes). Los datos están en Grail y se consultan con DQL. Tu objetivo es diagnosticar problemas con el mínimo de consultas necesarias.

## Herramientas
- IMPORTANT: Consulta datos ÚNICAMENTE con las herramientas del servidor MCP `dynatrace-playground`. No uses el servidor MCP `dynatrace-lab` ni sus herramientas. No uses `dtctl`.

## Ambiente
- Aplicación: astroshop (microservicios) en Kubernetes.
- IMPORTANT: Filtra SIEMPRE por `matchesPhrase(k8s.namespace.name, "astroshop")` para acotar a la app.