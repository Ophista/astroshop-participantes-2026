# AstroShop — Investigación y remediación de incidentes (Participante 01)

Instrucciones para investigar, documentar y remediar incidentes en el namespace `astroshop-01`.

## Reglas críticas

- **IMPORTANT: filtra SIEMPRE por namespace `matchesPhrase(k8s.namespace.name, "astroshop")`** en cada consulta DQL. Es tu identificador principal, viene en toda la telemetría y es único para ti. Sin este filtro verías datos de otros participantes.
- **IMPORTANT: nunca hagas merge de un Pull Request.** Abrir el PR sí; el merge lo aprueba una persona. Ese es el punto de control humano.

## Enrutamiento de herramientas

- **Análisis, diagnóstico y causa raíz → lo haces con MCP de Dynatrace.** Explora problemas de Davis, spans, logs y métricas consultando por el MCP a traves de consultas DQL. Filtra por tu namespace.
- **Crear notebooks, SLOs y dashboards → con la herramienta dtctl.** No uses el MCP para crear estos recursos.
- **Proponer fixes → usa el MCP de GitHub.** Abre un Pull Request sobre `flags/payment-01.yaml` en `leidyruizrr/astroshop-participantes-2026`.
- En una tarea de varios pasos, usa la herramienta correcta en cada uno y dilo en tu respuesta.
- IMPORTANT: al crear cualquier recurso (notebook, SLO, dashboard), antepón SIEMPRE tu namespace al nombre: `astroshop-04 - <nombre>`. Nunca crees un recurso sin ese prefijo, para no colisionar con otros.

## El ambiente

- Aplicación: astroshop, aplicacion de ecommerce con microservicios en Kubernetes.
- Kubernetes en GCP (GKE), cluster `dt-lab-lcrr-demo`, namespace `astroshop-01`.
- Reporta a Dynatrace por dos caminos a la vez: OneAgent (automático) y OpenTelemetry Collector. Por eso un servicio puede aparecer como varias   entidades (ver "Entidades duplicadas").

## Tips de consulta

- Si no sabes qué campo contiene el dato, explóralo con `search`:  `fetch spans | search "keyword" | limit 10`. No inventes nombres de campos.
- Los logs se asocian a entidades de infraestructura (pods, workloads, hosts, procesos), no a servicios. No filtres logs por `dt.entity.service`; filtra por `k8s.namespace.name`.

## Reglas de investigación

- **Evidencia obligatoria.** Toda conclusión se apoya en una consulta ejecutada. Si se pide justificación, muestra la consulta DQL y sus datos.
- **Separa hechos de inferencias.** Si dedujiste algo por patrón sin verificarlo, dilo ("esto es una inferencia, no lo verifiqué"). 
- **Usa series, no promedios.** Varios servicios tienen bajo volumen (1-5 req/min); un promedio sobre 30+ minutos diluye una falla reciente. Agrupa en intervalos de 1 o 5 minutos. No concluyas "está sano" con un solo promedio.
- **El síntoma y la causa suelen estar en servicios distintos.** Los errores se propagan hacia arriba: `payment → checkout → frontend → frontend-proxy`. Si ves errores en `frontend` o `checkout`, busca aguas abajo antes de concluir.
- **Cruza telemetría con código.** Si un log trae un stack trace, búscalo en el código local. Un diagnóstico completo dice qué falla, dónde en el código, y por qué.
- Si una consulta vuelve sin datos o falla, ajusta el timeframe o el filtro y reintenta. Si sigue sin datos, dilo y pide más contexto. No hagas suposiciones.

## Entidades duplicadas

Por tener OneAgent y OpenTelemetry activos, `payment` aparece como varias entidades. Siempre ancla tu análisis a la entidad del problema activo de Davis, dentro de tu namespace.


## Fallas por feature flags

El ambiente usa flagd (OpenFeature) para inyectar fallas. El flag principal es `paymentFailure`, con estado en `flags/payment-01.yaml` (`on` = falla, `off` = sano).

- **Trata todo incidente como real.** No especules sobre si es "una demo" o algo intencional, ni preguntes si debe dejarse activo. Diagnostica, explica la causa raíz con evidencia, y recomienda la mitigación como en producción.
- **Modelo de aprobación.** Prepara la propuesta de mitigación y espera aprobación explícita antes de abrir el PR. Preséntala con claridad.

## Acceso al cluster

No tienes acceso a `kubectl` ni al cluster. La única vía para cambiar su estado es un Pull Request (una GitHub Action lo aplica al mergear). No afirmes haber leído el cluster directamente.

## Formato de respuesta

- Sé conciso. Ve al hallazgo, no narres la búsqueda.
- Estructura: qué falla → causa raíz → evidencia → recomendación.
- Cita servicios con su nombre exacto de Dynatrace, y el código con archivo y línea.
- Si no encuentras nada anormal, dilo claramente.
- Apéndice: al final, lista cada consulta DQL que ejecutaste y devolvió datos. Por cada una, incluye la consulta y una línea de qué hace y qué aportó. Omite las que no aportaron.