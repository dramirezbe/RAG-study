# Spec-Driven Development (SDD)

## ¿Qué es Spec-Driven Development?

**Descripción:** Metodología de desarrollo donde la especificación (*spec*) es el artefacto primario y fuente de verdad del proyecto. En lugar de escribir código directamente, defines *qué* debe hacer el sistema en lenguaje natural estructurado, y un agente de IA traduce esa especificación a implementación. La spec no es documentación accesoria — es el *input* que gobierna todo el ciclo: generación de código, tests, validación, y mantenimiento.

**Analogía:** Así como TDD (Test-Driven Development) puso los tests antes del código, SDD pone la especificación antes de los tests. No escribes una línea de código hasta que la spec está clara, validada y versionada.

---

## El ciclo SDD

```
SPEC → PLAN → GENERATE → VALIDATE → ITERATE
  ↑                                    │
  └────────────────────────────────────┘
```

### 1. SPEC
Escribes la especificación en lenguaje natural estructurado. Define:
- **Entidades y modelos:** Qué datos maneja el sistema.
- **Comportamientos:** Qué acciones puede realizar (endpoints, funciones, flujos).
- **Restricciones:** Reglas de negocio, invariantes, validaciones.
- **Edge cases:** Qué pasa en condiciones límite o error.

La spec se escribe una vez y se versiona en el repositorio como cualquier otro archivo fuente.

### 2. PLAN
El agente lee la spec, la descompone en tareas atómicas, y genera un plan de implementación. Cada tarea tiene:
- Descripción precisa de lo que debe producir.
- Skills y contexto requeridos.
- Dependencias entre tareas.
- Criterios de aceptación.

### 3. GENERATE
Sub-agentes efímeros ejecutan cada tarea del plan:
- Un agente genera la estructura de archivos y scaffolding.
- Otro implementa los modelos y tipos.
- Otro escribe la lógica de negocio.
- Otro genera los tests que validan la spec.
- Otro escribe migraciones, configs, y boilerplate.

Cada sub-agente solo ve su tarea y la sección relevante de la spec. Contexto mínimo, foco máximo.

### 4. VALIDATE
Post-generación, el ciclo de validación verifica que el output cumple la spec:
- **Tests automáticos:** Los tests generados en la fase anterior se ejecutan. Si fallan, el agente itera.
- **Spec-compliance check:** Un agente revisor compara la implementación contra la spec original y reporta divergencias.
- **Linting y typecheck:** Barreras de calidad estándar del stack del proyecto.
- **Human-in-the-loop (opcional):** Revisión humana en puntos críticos (seguridad, decisiones de arquitectura).

### 5. ITERATE
Cambios en la spec disparan re-generación incremental:
- El agente detecta qué partes del código quedaron obsoletas.
- Regenera solo lo necesario, preservando el resto.
- Corre los tests de regresión para asegurar que nada se rompió.

---

## Ventajas sobre el desarrollo tradicional con IA

### Consistencia arquitectónica
En un chat libre con IA, cada sesión puede tomar decisiones inconsistentes. Con SDD, la spec actúa como *constraint* arquitectónico: el agente no improvisa, sigue la especificación. Dos features implementadas en sesiones separadas mantienen coherencia porque ambas leen la misma spec.

### Auditabilidad
La spec es texto plano versionado. Puedes hacer `git diff` y ver exactamente qué cambió en los requisitos y cómo eso se refleja en el código generado. El *blame* pasa de "el agente decidió esto" a "la spec dice esto". Trazabilidad completa de requisito a implementación.

### Reproducibilidad
Dada la misma spec y el mismo modelo, obtienes la misma arquitectura. No dependes del historial de una conversación ni de prompts acumulados. La spec es determinística: puedes regenerar el proyecto completo desde cero si es necesario.

### Paralelismo real
Como la spec define el contrato entre módulos (interfaces, tipos, responsabilidades), múltiples sub-agentes pueden implementar partes independientes en paralelo sin conflictos. Cada uno conoce los límites de su módulo y las interfaces con los demás.

### Onboarding de agentes
Un agente nuevo no necesita leer 3000 líneas de código para entender el proyecto. Lee la spec (200-500 líneas), entiende el sistema completo, y puede contribuir inmediatamente. La spec es el *system prompt* del proyecto.

### Refactoring seguro
Antes de un refactor grande, actualizas la spec. El agente regenera las partes afectadas asegurando consistencia global. No hay "esto lo toqué de más" — la spec define exactamente qué debía cambiar.

---

## Cuándo usar SDD

| Escenario | SDD | Enfoque tradicional |
|---|---|---|
| **Proyecto nuevo** | Spec desde cero, generación completa | Chat iterativo, arquitectura emergente |
| **Feature grande** | Actualizar spec → regenerar módulo | Prompt ad-hoc, riesgo de inconsistencia |
| **Refactor** | Spec-driven: cambiar spec primero | Explorar código → adivinar impacto |
| **Code review de IA** | Validar spec-compliance | Revisar línea por línea a ciegas |
| **Mantenimiento continuo** | Spec es living document | El código es la única verdad |
| **Prototipo rápido** | Overhead innecesario | Chat directo, sin spec |

---

## Relación con el stack agentivo

SDD es el complemento natural al patrón descrito en [agents-evolution.md](agents-evolution.md):

```
SPEC (fuente de verdad)
  │
  ├── Orquestador: lee la spec, genera el plan
  │     │
  │     ├── Skill: carga la skill relevante según el módulo
  │     │
  │     └── Sub-agentes: implementan tareas del plan
  │           │
  │           └── Memoria persistente: la spec misma es memoria
  │
  └── VALIDATE: spec-compliance check → iterar
```

La spec cierra el ciclo: es el *input* que gobierna todo y el *output* contra el que se valida todo. El agente no decide *qué* construir — la spec lo decide. El agente decide *cómo* construirlo.

---

## Limitaciones actuales

- **Ambigüedad en lenguaje natural:** Una spec ambigua produce código ambiguo. La calidad del output depende directamente de la precisión de la spec. Escribir buenas specs es una habilidad en sí misma.
- **Sobrecarga para tareas pequeñas:** Para un fix de una línea, escribir una spec es overhead. SDD brilla en features, módulos y sistemas completos, no en micro-cambios.
- **Deriva spec-código:** Si alguien modifica el código sin actualizar la spec, la fuente de verdad se parte. Requiere disciplina de equipo o tooling que detecte divergencias automáticamente.
- **Creatividad limitada por la spec:** El agente solo puede innovar dentro de los márgenes que la spec define. Si la spec no contempla una optimización, el agente no la propondrá.
