# Guia del participante - Lab AstroShop AI (Copilot)

# Lab 2


* **Ambiente:** Dynatrace ulk04354 
* **Objetivo:** Autoremediación usando MCP + dtctl


## Objetivo 

En este lab aprenderás a usar el agente de IA no solo para consultar datos, sino para crear recursos en Dynatrace a partir de una investigación: partiendo de un incidente detectado, le pedirás que documente los hallazgos en un notebook, defina un SLO de disponibilidad para el servicio afectado y arme un dashboard de monitoreo. La meta es que el agente convierta el diagnóstico en artefactos concretos y reutilizables de observabilidad, usando la herramienta correcta para cada tarea hasta completar la autoremediación del problema integrandose con otras herramientas.

#
---
#


## Paso 1  - Configurar el workspace

1. Ubicar el archivo  `.vscode/mcp.json` y descomentar el bloque de `dynatrace-lab`
2. Abrir la terminal y ejecutar el siguiente comando: esto creará el instruction file de este nuevo laboratorio
  * Reemplazar el X por el identificador dado a cada participante `INSTRUCCIONES-01, INSTRUCCIONES-02, INSTRUCCIONES-03, etc`
```
cp lab2/INSTRUCCIONES-0X.md .github/copilot-instructions.md
head -1 .github/copilot-instructions.md   # debe mostrar tu número de participante
```
3. Abrir la paleta de comandos (`Ctrl+Shift+P`) y click en **MCP: List Servers**.
4. Selecciona `dynatrace-lab` → **Start**.
5. verificar que el servidor este en estado `running`
6. Abrir Copilot Chat → cambiar a Agent mode `Ctrl+Alt+I`
7. Preguntar algo como: 
```
¿A qué tenant de Dynatrace estoy conectado?
```

#
---
#

# Ejercico 1 - Investigación Inicial

`Prompt`:
```
¿Cómo está el namespace astroshop en este momento?
```



## Ejercico 2 — Analisis Causa Raiz

`Prompt`:
```
¿Cómo llegaste a esa conclusión? ¿Puedes ubicar en el código dónde se origina esto?
```

## Ejercico 3 — Documentar Hallazgos

`Prompt`:
```
Documenta este incidente para el equipo de la siguiente manera:
* Crea un notebook en el ambiente de Dynatrace con todo el analisis realizado, agrega las consultas DQL y causa raíz. 
* Deja el notebook publico para que todos en el ambiente lo puedan ver.
```

## Ejercico 3 — Crear Artefactos de Observabilidad

`Prompt`:
```
Necesitamos vigilar mejor este servicio a futuro. Crea un SLO  de disponibilidad para el servicio payment que aparece afectado en el problema activo, hazlo en el ambiente de Dynatrace.
```

`Prompt`:
```
Necesito que cada vez que este servicio se degrade me llegue una notificación a mi correo: example@gmail.com, crea una alerta en el ambiente de Dynatrace para que se notifique el problema.
```

`Prompt`:
```
Crea un Dashboard base de observabilidad en el ambiente de Dynatrace donde pueda ver la salud de mi aplicación.
Deja el dashboard publico para que todos puedan verlo.
```

## Ejercico 4 — Proponer Fix

`Prompt`:
```
Necesitamos resolver esto. Prepara el cambio para que lo revise antes de aplicarlo.
```


## Ejercico 4 — Validacion de la resolución

`Prompt`:
```
Ya apliqué el cambio. ¿Se resolvió?
```


