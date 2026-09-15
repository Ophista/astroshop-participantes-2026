# AstroShop — Investigación y remediación de incidentes (Participante 03)

Instrucciones para investigar, documentar y remediar incidentes en el namespace
`astroshop-03`.

## Reglas críticas

- **IMPORTANT: filtra SIEMPRE por `k8s.namespace.name == "astroshop-03"`** en cada
  consulta DQL. Es tu identificador principal, viene en toda la telemetría y es
  único para ti. No uses primary tags para aislar; usa el namespace. Sin este
  filtro verías datos de otros participantes.
- **IMPORTANT: nunca hagas merge de un Pull Request.** Abrir el PR sí; el merge lo
  aprueba una persona. Ese es el punto de control humano.

## Enrutamiento de herramientas

- **Análisis, diagnóstico y causa raíz → MCP de Dynatrace.** Explora problemas de
  Davis, spans, logs y métricas consultando por el MCP. Filtra por tu namespace.
- **Crear notebooks, SLOs y dashboards → dtctl.** No uses el MCP para crear estos
  recursos.
- **Proponer fixes → MCP de GitHub.** Abre un Pull Request sobre
  `flags/payment-03.yaml` en `leidyruizrr/astroshop-participantes-2026`.

En una tarea de varios pasos, usa la herramienta correcta en cada uno y dilo en tu
respuesta.

## El ambiente

- AstroShop: demo de OpenTelemetry, ~20 microservicios políglotas (Node.js, Go,
  Python, .NET, Java). Simula una tienda en línea.
- Kubernetes en GCP (GKE), cluster `dt-lab-lcrr-demo`, namespace `astroshop-03`.
- Reporta a Dynatrace por dos caminos a la vez: OneAgent (automático) y
  OpenTelemetry Collector. Por eso un servicio puede aparecer como varias
  entidades (ver "Entidades duplicadas").

## Reglas de investigación

- **Evidencia obligatoria.** Toda conclusión se apoya en una consulta ejecutada.
  Si se pide justificación, muestra la consulta DQL y sus datos.
- **Separa hechos de inferencias.** Si dedujiste algo por patrón sin verificarlo,
  dilo ("esto es una inferencia, no lo verifiqué"). Una hipótesis etiquetada vale
  más que una afirmación falsamente segura.
- **Usa series, no promedios.** Varios servicios tienen bajo volumen
  (1-5 req/min); un promedio sobre 30+ minutos diluye una falla reciente. Agrupa
  en intervalos de 1 o 5 minutos. No concluyas "está sano" con un solo promedio.
- **El síntoma y la causa suelen estar en servicios distintos.** Los errores se
  propagan hacia arriba: `payment → checkout → frontend → frontend-proxy`. Si ves
  errores en `frontend` o `checkout`, busca aguas abajo antes de concluir.
- **Cruza telemetría con código.** Si un log trae un stack trace, búscalo en el
  código local. Un diagnóstico completo dice qué falla, dónde en el código, y por qué.

## Entidades duplicadas

Por tener OneAgent y OpenTelemetry activos, `payment` aparece como varias
entidades. La que Davis asocia al problema es la de OTel (endpoint
`oteldemo.PaymentService.Charge`); puede no traer primary tags, pero sí trae
`k8s.namespace.name`.

- No asumas que la primera entidad que encuentres es la correcta. Ancla tu análisis
  a la entidad del problema activo de Davis, dentro de tu namespace.

## Fallas por feature flags

El ambiente usa flagd (OpenFeature) para inyectar fallas. El flag principal es
`paymentFailure`, con estado en `flags/payment-03.yaml` (`on` = falla, `off` = sano).

- **Trata todo incidente como real.** No especules sobre si es "una demo" o algo
  intencional, ni preguntes si debe dejarse activo. Diagnostica, explica la causa
  raíz con evidencia, y recomienda la mitigación como en producción.
- **Modelo de aprobación.** Prepara la propuesta de mitigación y espera aprobación
  explícita antes de abrir el PR. Preséntala con claridad ("recomiendo apagar el
  flag; ¿lo preparo como PR?").

## Acceso al cluster

No tienes acceso a `kubectl` ni al cluster. La única vía para cambiar su estado es
un Pull Request (una GitHub Action lo aplica al mergear). No afirmes haber leído el
cluster directamente.

## Formato de respuesta

- Sé conciso. Ve al hallazgo, no narres la búsqueda.
- Estructura: qué falla → causa raíz → evidencia → recomendación.
- Cita servicios con su nombre exacto de Dynatrace, y el código con archivo y línea.
- Si no encuentras nada anormal, dilo claramente.
