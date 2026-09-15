# Lab 1 — Enseñar a la IA a consultar (instruction files + DQL)

**Duración:** ~30 minutos
**Ambiente:** Dynatrace Playground (AstroShop) — MCP `dynatrace-playground`
**Objetivo doble:** aprender a escribir buenos instruction files Y ver cómo
transforman a un asistente genérico en uno que consulta con precisión.

---

## La idea del lab

Un asistente de IA sin contexto es como un empleado nuevo brillante que no conoce
tu empresa: sabe mucho en general, pero no sabe TU ambiente. El instruction file
es el manual que le das para que trabaje bien en TU contexto.

En este lab partes de un instruction file "pobre" y lo mejoras en 3 pasos. En
cada uno verás dos cosas: **cómo mejora la respuesta del asistente**, y **por qué
se escribe la instrucción de esa forma** (las buenas prácticas).

---

## Principios que vas a aplicar (y por qué importan)

Todo lo que escribes en el instruction file consume contexto y compite por la
atención del asistente. Un archivo inflado no solo desperdicia tokens: baja la
calidad, porque el asistente tiene más reglas que seguir y las sigue de forma
menos consistente.

Cuatro reglas guían este lab:

1. **Incluye solo lo que causaría errores si faltara.** Todo lo demás es ruido.
2. **Usa imperativos, no sugerencias.** "Filtra por X", no "sería bueno filtrar por X".
3. **Sé específico y verificable.** "Ventana de 30 minutos", no "un rango razonable".
4. **Reserva IMPORTANT para 2-3 reglas críticas.** Si todo es importante, nada lo es.

---

## Preparación

1. Arranca el MCP de playground:
   `Ctrl+Shift+P` → **MCP: List Servers** → `dynatrace-playground` → **Start**
2. Copia el instruction file inicial:
   ```
   cp labs/LAB1-instruction-inicial.md .github/copilot-instructions.md
   ```
3. Abre Copilot Chat en **Agent mode**.

> Nota sobre Copilot: al editar `.github/copilot-instructions.md`, a veces hay que
> abrir un **chat nuevo** para que relea las instrucciones. Si una mejora no se
> refleja, abre un chat nuevo y repite la pregunta.

---

## Checkpoint 0 — El punto de partida

Con el instruction file pobre (2 líneas), pregunta:

> Muéstrame los servicios de AstroShop con más errores en la última hora.

**Observa:** sin contexto, el asistente hace una consulta genérica, no sabe qué
es AstroShop, no sabe qué campo marca un error, o te pregunta demasiado. Guarda
esta respuesta: es tu línea base para comparar al final.

---

## Checkpoint 1 — Darle contexto del ambiente

**El problema:** el asistente no conoce tu aplicación ni cómo se modelan sus datos.
Eso es justo lo que "causaría errores si faltara" (regla 1). Se lo damos.

Abre `.github/copilot-instructions.md` y agrega:

```markdown
## El ambiente
- La aplicación es AstroShop: e-commerce de ~20 microservicios
  (payment, checkout, cart, frontend, currency, shipping...).
- Corre en Kubernetes. Los datos están en Grail y se consultan con DQL.
- Para analizar servicios, usa spans. Filtra por `service.name`.
- Un span fallido se marca con `request.is_failed == true`.
```

**Fíjate en la forma:** son imperativos ("usa spans", "filtra por") y datos
verificables (el nombre exacto del campo `request.is_failed`). Nada de "deberías"
ni de relleno.

Guarda. Abre un chat nuevo si es necesario, y vuelve a preguntar lo mismo:

> Muéstrame los servicios de AstroShop con más errores en la última hora.

**La mejora:** ahora el asistente usa `service.name` y `request.is_failed`. La
consulta es concreta y correcta, no genérica.

---

## Checkpoint 2 — Reglas de calidad para las consultas

**El problema:** si pides algo temporal, el asistente promedia todo y esconde los
picos recientes. Esa es una "trampa común" que hay que prevenir (regla 1).

Agrega:

```markdown
## Reglas de consulta
- Investiga los últimos 30 minutos por defecto.
- Para evolución en el tiempo, agrupa en intervalos con `makeTimeseries` o `bin`.
  No uses un promedio agregado único: diluye los picos recientes.
- Muestra siempre la consulta DQL que ejecutaste.
```

**Fíjate:** cada regla es específica y verificable ("30 minutos", nombres de
comandos DQL), y usa la negación imperativa donde importa ("No uses un promedio
agregado único").

Guarda (chat nuevo si hace falta). Pregunta algo temporal:

> ¿Cómo ha evolucionado la tasa de error del servicio payment en los últimos 30 minutos?

**La mejora:** en vez de un número plano, el asistente devuelve una serie temporal
que muestra la evolución real, y te enseña la consulta.

---

## Checkpoint 3 — Desambiguar entidades (una regla crítica)

**El problema:** en AstroShop, un servicio aparece con varios nombres (por OneAgent
y OpenTelemetry). Si el asistente elige la entidad equivocada, todo el diagnóstico
sale mal. Esto sí es crítico → merece un IMPORTANT (regla 4).

Agrega:

```markdown
## Entidades duplicadas
- IMPORTANT: un servicio (ej. payment) puede aparecer como varias entidades por
  tener OneAgent y OpenTelemetry a la vez. No asumas que la primera es la correcta.
- Lista las entidades que coincidan con el nombre, compara su throughput y error
  rate, y di explícitamente cuál usas y por qué.
```

**Fíjate:** es la única regla marcada como IMPORTANT en todo el archivo. Se lo
gana porque ignorarla causa un diagnóstico incorrecto. Si marcáramos todo como
IMPORTANT, el asistente no distinguiría lo crítico de lo normal.

Guarda (chat nuevo si hace falta). Pregunta:

> Analiza el servicio payment: dime su tasa de error y cuántas peticiones maneja.

**La mejora:** el asistente reconoce que hay varias entidades payment, las compara,
y te dice cuál usa. Ya no se confunde.

---

## Cierre — Compara y reflexiona

Abre tu `.github/copilot-instructions.md` final y compáralo con el inicial (2
líneas). Fíjate en tres cosas:

1. **Cuánto mejoró el asistente** con solo darle contexto y reglas.
2. **Sigue siendo corto** — cada línea gana su lugar. No hay relleno ni cosas que
   el asistente ya sabía (sintaxis de DQL, qué es un microservicio).
3. **Una sola regla es IMPORTANT** — la que de verdad rompe todo si falta.

**La lección:** el asistente no se volvió más inteligente. Le enseñaste tu
ambiente, con instrucciones imperativas, específicas y sin ruido. Eso es un buen
instruction file.

En los Labs 2 y 3 usarás `INSTRUCCIONES-0X.md`: la versión madura y completa de lo
que acabas de construir, escrita con estos mismos principios.

---

## Recordatorio de buenas prácticas (para cuando escribas los tuyos)

- Incluye solo lo que causaría errores si faltara. El resto es ruido.
- Imperativos, no sugerencias: "Usa X", no "sería bueno usar X".
- Específico y verificable: nombres de campo, comandos exactos, números concretos.
- IMPORTANT solo para 2-3 reglas críticas.
- Estructura con headers y bullets: el asistente escanea como un humano.
- Máximo ~200 líneas. Si crece más, divídelo.
- No mandes al asistente a hacer trabajo de un linter: los patrones del código
  se aprenden solos por contexto.

---

## Si te quedas atrás (checkpoints de respaldo)

Copia el checkpoint correspondiente para ponerte al día:
```
cp labs/LAB1-checkpoint-1.md .github/copilot-instructions.md
cp labs/LAB1-checkpoint-2.md .github/copilot-instructions.md
cp labs/LAB1-checkpoint-3.md .github/copilot-instructions.md
```
