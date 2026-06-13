---
name: graphify-usage
description: Usar el knowledge graph generado por graphify antes de crear o modificar código
---

# Uso del Knowledge Graph (graphify)

Este proyecto mantiene un grafo de conocimiento generado en `graphify-out/`.

## Cuándo usarlo
Antes de crear componentes nuevos, modificar código existente, o responder preguntas sobre la arquitectura del proyecto, consultá primero el grafo.

## Cómo consultarlo
1. Revisá `graphify-out/.graphify_ast.json` para entender estructura, relaciones entre archivos, dependencias y patrones de código.
2. Revisá `.graphify_root` para el contexto general del proyecto.
3. Si hay un comando de query disponible, usalo para preguntas puntuales sobre arquitectura antes de escribir código nuevo.

## Reglas
- Mantené coherencia con los patrones, nombres y estructura de carpetas detectados en el grafo.
- No reinventes convenciones que ya existen en el proyecto.
- Si el grafo parece desactualizado respecto a cambios recientes en `src/`, avisá al usuario que podría convenir correr `/graphify` de nuevo.