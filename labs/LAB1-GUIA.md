# Lab 1 — Enseñar a la IA a consultar (instruction files + DQL)

**Duración:** ~30 minutos
**Ambiente:** Dynatrace Playground (AstroShop) — MCP `dynatrace-playground`
**Objetivo:** entender cómo un instruction file transforma un asistente genérico
en uno que consulta con precisión tu observabilidad.

---

## La idea del lab

Un asistente de IA sin contexto es como un empleado nuevo brillante pero que no
conoce tu empresa: sabe mucho en general, pero no sabe TU ambiente. El
instruction file es el manual que le das para que trabaje bien en TU contexto.

En este lab vas a partir de un instruction file "pobre" y lo vas a mejorar en 3
pasos (checkpoints). En cada paso verás cómo el asistente pasa de dar respuestas
genéricas a consultas DQL precisas.

---

## Preparación

1. Arranca el MCP de playground:
   - `Ctrl+Shift+P` → **MCP: List Servers** → `dynatrace-playground` → **Start**
2. Copia el instruction file inicial:
   ```
   cp labs/LAB1-instruction-inicial.md .github/copilot-instructions.md
   ```
3. Abre Copilot Chat en **Agent mode**.

---

## Checkpoint 0 — Punto de partida (el asistente "en blanco")

Con el instruction file pobre, pregunta:

> Muéstrame los servicios de AstroShop con más errores en la última hora.

**Observa:** el asistente probablemente hace una consulta genérica, no sabe qué
es AstroShop, no filtra bien, o pregunta demasiado. Ese es el punto de partida.

Guarda mentalmente qué tan buena (o mala) fue la respuesta. Vas a compararla al
final.

---

## Checkpoint 1 — Darle contexto del ambiente

Abre `.github/copilot-instructions.md` y agrega esta sección:

```markdown
## El ambiente
- La aplicación es AstroShop: una tienda de e-commerce de ~20 microservicios
  (payment, checkout, cart, frontend, currency, shipping, etc.).
- Corre en Kubernetes. Los datos están en Grail y se consultan con DQL.
- Para servicios, la telemetría principal son spans. Filtra por `service.name`.
- Los errores de un span se marcan con `request.is_failed == true`.
```

Guarda el archivo. En Copilot, si es necesario, recarga las instrucciones
(a veces basta con seguir en la misma conversación; si no, abre un chat nuevo).

Vuelve a preguntar:

> Muéstrame los servicios de AstroShop con más errores en la última hora.

**Observa la mejora:** ahora el asistente sabe qué es AstroShop, usa `service.name`
y `request.is_failed`. La consulta es más precisa.

---

## Checkpoint 2 — Reglas de calidad para las consultas

Agrega esta sección al instruction file:

```markdown
## Reglas de consulta
- Por defecto, investiga los últimos 30 minutos, no más.
- Para ver evolución en el tiempo, agrupa en intervalos (por ejemplo, cada
  1 o 5 minutos) usando `makeTimeseries` o `bin`. NO uses un solo promedio
  agregado: diluye los picos recientes.
- Muestra siempre la consulta DQL que ejecutaste, para que sea verificable.
```

Guarda. Ahora pregunta algo temporal:

> ¿Cómo ha evolucionado la tasa de error del servicio payment en los últimos 30 minutos?

**Observa la mejora:** en vez de un número promedio plano, el asistente ahora
devuelve una serie temporal que muestra la evolución real. Y te muestra la
consulta.

---

## Checkpoint 3 — Desambiguar entidades

En AstroShop, un servicio puede aparecer con varios nombres (por OneAgent y
OpenTelemetry). Agrega esta sección:

```markdown
## Entidades duplicadas
- Un mismo servicio (ej. payment) puede aparecer como varias entidades en
  Dynatrace, por tener OneAgent y OpenTelemetry a la vez.
- Cuando investigues un servicio, no asumas que la primera entidad es la
  correcta. Lista las que coincidan con el nombre y compara su throughput y
  error rate antes de concluir. Di explícitamente cuál estás usando.
```

Guarda. Pregunta:

> Analiza el servicio payment: dime su tasa de error y cuántas peticiones maneja.

**Observa la mejora:** el asistente ahora reconoce que hay varias entidades
payment, las compara, y te dice cuál está usando y por qué. Ya no se confunde.

---

## Cierre — Compara

Abre tu `.github/copilot-instructions.md` final y compáralo con el inicial (que
era 2 líneas). Fíjate cuánto cambió la calidad de las respuestas del asistente
con solo agregar contexto y reglas.

**La lección:** el asistente no se volvió más inteligente. Le enseñaste tu
ambiente. Eso es un instruction file, y es lo que hace la diferencia entre un
asistente genérico y uno que conoce TU observabilidad.

En los Labs 2 y 3 usarás un instruction file ya completo (INSTRUCCIONES-0X.md),
que es la versión madura de lo que acabas de construir.

---

## Si te quedas atrás (checkpoints de respaldo)

Si en algún punto tu instruction file se desordena, puedes copiar el checkpoint
correspondiente para ponerte al día:
- `labs/LAB1-checkpoint-1.md` (tras Checkpoint 1)
- `labs/LAB1-checkpoint-2.md` (tras Checkpoint 2)
- `labs/LAB1-checkpoint-3.md` (tras Checkpoint 3)

Cópialo con:
```
cp labs/LAB1-checkpoint-X.md .github/copilot-instructions.md
```
