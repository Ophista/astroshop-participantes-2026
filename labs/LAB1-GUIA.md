# Lab 1 — Enseñar a la IA a analizar (instruction files + DQL)

* **Ambiente:** Dynatrace Playground (AstroShop) — MCP `dynatrace-playground`
* **Objetivo:** aprender a construir un instruction file que convierte a un agente genérico en un analista de observabilidad riguroso y eficiente.

---

## La idea del lab

Un agente de IA sin contexto es como un analista nuevo brillante que no conoce tu
ambiente: sabe DQL en general, pero no sabe TU aplicación, ni tu método, ni tus
reglas. El instruction file es el manual que le das.

En este lab construyes ese manual en **5 pasos**. En cada paso agregas una capa y
observas cómo el agente analiza mejor: más enfocado, más eficiente y más honesto.

## Buenas prácticas que aplicarás

- Incluye solo lo que causaría errores si faltara. El resto es ruido.
- Usa imperativos ("Filtra por X"), no sugerencias ("sería bueno filtrar").
- Sé específico y verificable (nombres de campo, comandos, números).
- Reserva IMPORTANT para 2-3 reglas críticas. Si todo es importante, nada lo es.

---

## Preparación

1. Arranca solo el MCP de playground:
   `Ctrl+Shift+P` → **MCP: List Servers** → `dynatrace-playground` → **Start**
2. Copia el instruction file base:
   ```
   cp labs/LAB1-paso-0.md .github/copilot-instructions.md
   ```
3. Abre Copilot Chat en **Agent mode**. `Ctrl+Shift+P`

> IMPORTANTE sobre Copilot: cada vez que edites `.github/copilot-instructions.md`,
> **abre un chat NUEVO** para que relea el archivo. Si no, sigue usando la versión
> anterior.

---

## El prompt de referencia

En cada paso usarás la misma pregunta, para comparar cómo mejora la respuesta:

> Analiza astroshop e identifica el problema más crítico en las últimas 24 horas. Explícame la causa raíz con evidencia

---

## Paso 0 — Base (Herramientas + Ambiente)

Ya copiaste `LAB1-paso-0.md`. Este archivo solo le dice al agente dos cosas: que
use el MCP de playground (no dtctl ni el otro tenant), y que filtre por el
namespace `astroshop`.

Abre un chat nuevo y lanza el prompt de referencia.

**Observa:** el agente ya consulta el playground y se acota a AstroShop, pero su
análisis es improvisado: puede saltar directo a datos crudos, no seguir un método,
o traer rangos enormes. Guarda esta respuesta como punto de partida.

---

## Paso 1 — Darle un método y las fuentes de datos

**El problema:** el agente no sabe POR DÓNDE empezar ni qué fuentes existen. Se lo
enseñamos.

Reemplaza tu instruction file por el del paso 1:
```
cp labs/LAB1-paso-1.md .github/copilot-instructions.md
```
(O agrega tú mismo las secciones "Método de análisis" y "Fuentes de datos".)

Abre chat nuevo, lanza el prompt de referencia.

**La mejora:** ahora el agente sigue un método — empieza por los problemas de
Davis, identifica entidades afectadas, cuantifica con golden signals. El análisis
deja de ser improvisado y se vuelve estructurado.

---

## Paso 2 — Reglas de eficiencia

**El problema:** el agente puede hacer consultas costosas (rangos abiertos, traer
registros crudos sin agregar). En un tenant real eso cuesta tiempo y dinero.

```
cp labs/LAB1-paso-2.md .github/copilot-instructions.md
```

Abre chat nuevo, lanza el prompt de referencia.

**La mejora:** ahora el agente acota el timeframe, agrega antes de traer registros
crudos, y pone `limit`. Las consultas son más rápidas y baratas. Fíjate en cómo
cambian las queries que muestra.

---

## Paso 3 — Rigor (anti-alucinación)

**El problema crítico:** un agente puede afirmar cosas con seguridad sin haberlas
verificado, o inventar nombres de campo. Esto es lo más peligroso en un análisis.

```
cp labs/LAB1-paso-3.md .github/copilot-instructions.md
```

Abre chat nuevo, lanza el prompt de referencia.

**La mejora:** ahora el agente basa cada conclusión en datos reales, explora la
estructura con `limit 5` antes de asumir, y si algo no aparece, lo dice en vez de
inventarlo. Muestra siempre la query. Este paso es el que más confianza aporta.

---

## Paso 4 — Formato del hallazgo

**El toque final:** que cada hallazgo se presente de forma consistente y accionable.

```
cp labs/LAB1-paso-4.md .github/copilot-instructions.md
```

Abre chat nuevo, lanza el prompt de referencia.

**La mejora:** el agente ahora estructura cada hallazgo (síntoma → evidencia →
entidad → causa raíz → recomendación). El resultado es un análisis que un equipo
puede leer y accionar de inmediato.

---

## Cierre — Compara

Abre tu instruction file final (paso 4) y compáralo con el paso 0. Fíjate:

1. **Cuánto mejoró el análisis** — de improvisado a metódico, eficiente y verificable.
2. **Cada sección tiene un propósito** — método, eficiencia, rigor, formato. Nada
   de relleno.
3. **Solo lo crítico es IMPORTANT** — la herramienta, el timeframe, el anti-alucinación.

**La lección:** el agente no se volvió más inteligente. Le diste un método, reglas
y rigor. Eso es un buen instruction file, y es lo que separa un asistente que
adivina de uno en el que puedes confiar.

En los Labs 2 y 3 usarás `INSTRUCCIONES-0X.md`: la versión de este manual aplicada
a TU namespace, para crear recursos y remediar incidentes.

---

## Si te quedas atrás

Copia el paso correspondiente y continúa:
```
cp labs/LAB1-paso-0.md .github/copilot-instructions.md
cp labs/LAB1-paso-1.md .github/copilot-instructions.md
cp labs/LAB1-paso-2.md .github/copilot-instructions.md
cp labs/LAB1-paso-3.md .github/copilot-instructions.md
cp labs/LAB1-paso-4.md .github/copilot-instructions.md
```