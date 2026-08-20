# Evolución de Arquitecturas Agentivas

## AGENTS.md: el monolito contextual

Todo empezó con `AGENTS.md`: un archivo markdown gigante con TODAS las reglas del proyecto — estilo, patrones, anti-patrones, convenciones, workflows. El agente lo leía al inicio y "sabía" cómo trabajar.

**Características:**

- **Carga única al inicio de sesión:** El agente consume el archivo completo como parte del prompt de sistema o primer mensaje.
- **Simplicidad conceptual:** Un solo punto de configuración. Fácil de entender, fácil de versionar.
- **Contenido típico:** Estilo de código, estructura de proyecto, comandos de build, reglas de linting, convenciones de naming, frameworks usados, gotchas del repo.

**Problema:** Cada nueva convención engordaba el archivo. En proyectos reales, `AGENTS.md` puede llegar a miles de líneas. Se come la ventana de contexto antes de que escribas tu primer prompt. El agente arranca con el tanque de contexto medio vacío y lo paga en cada turno de la conversación.

---

## AGENTS.md crece infinito

El problema de fondo no es el archivo en sí, sino la premisa de *precarga total de contexto*: leer TODO por si acaso se necesita ALGO.

**Síntomas del monolito descontrolado:**

- **Bloat contextual:** 2000+ líneas de reglas donde quizás solo 50 son relevantes para tu tarea. Tokens desperdiciados en cada request.
- **Fricción de mantenimiento:** El equipo evita documentar nuevas convenciones porque "ya está muy largo". El archivo se estanca, la brecha entre lo documentado y lo real crece.
- **Ambigüedad por saturación:** Demasiada información difusa compite por la atención del modelo. Reglas contradictorias entre secciones escritas con meses de diferencia.
- **Costo acumulativo:** En modelos con pricing por token (GPT-4, Claude), cada turno paga por re-leer el monolito completo.

---

## Primera evolución: Skills

En vez de un archivo gigante, un *router* liviano (`AGENTS.md`) que apunta a `SKILL.md` específicos según la tarea. Solo se carga el contexto que necesitas para la tarea actual. Contexto dinámico bajo demanda.

**Cómo funciona:**

- **AGENTS.md se convierte en un índice:** Contiene únicamente una tabla de skills disponibles con su descripción y ubicación. El agente lo lee al inicio (costo mínimo) y decide cuál cargar según la tarea del usuario.
- **Cada skill es un `SKILL.md` autónomo:** Un archivo por dominio con instrucciones precisas, ejemplos, workflows y referencias. Carga *on-demand*: solo cuando el agente detecta que la tarea coincide.
- **Estructura típica de skills:**
  - `git/SKILL.md` — Flujo de commits, branches, PRs, convenciones de mensajes.
  - `testing/SKILL.md` — Frameworks, comandos, cobertura mínima, mocks permitidos.
  - `db/SKILL.md` — Migraciones, ORM, naming de tablas, reglas de índice.
  - `deploy/SKILL.md` — CI/CD, entornos, variables, health checks.

**Ventajas:**

- **Contexto preciso:** El agente solo paga tokens por lo que va a usar. Si vas a escribir tests, no cargas reglas de deploy.
- **Mantenibilidad:** Cada skill tiene un dueño claro. Agregar una nueva no toca las demás.
- **Escalabilidad horizontal:** El número de skills puede crecer sin degradar la eficiencia contextual.

**Limitación:** Skills resuelve QUÉ contexto cargar, pero no el ruido acumulado en la conversación. Un solo agente manejando múltiples skills en una sesión larga sigue acumulando tokens de historial.

---

## Segunda evolución: Sub-agentes

Skills resuelve *qué* contexto cargar. Pero un solo agente sigue acumulando ruido. La solución: delegar cada fase a un sub-agente efímero con contexto fresco. Nace, ejecuta, reporta y muere.

**Cómo funciona:**

- **Orquestador + workers:** Un agente principal (orquestador) recibe la tarea del usuario, la descompone en sub-tareas, y lanza sub-agentes independientes para cada una.
- **Sub-agente efímero:** Cada sub-agente recibe un contexto limpio (instrucciones de la sub-tarea + skill relevante), ejecuta su trabajo, y devuelve un resumen al orquestador. Luego desaparece — no acumula historial.
- **Paralelismo real:** Sub-agentes independientes pueden ejecutarse en paralelo (ej. uno investiga la documentación mientras otro busca patrones en el código).

**Comparación con el modelo monolítico:**

| Aspecto | Agente único | Sub-agentes |
|---|---|---|
| **Contexto acumulado** | Crece lineal con cada acción | Cada sub-agente arranca fresco |
| **Paralelismo** | Secuencial | Tareas independientes en paralelo |
| **Especialización** | Un agente sabe de todo | Cada sub-agente recibe skill específica |
| **Ruido** | Decisiones previas contaminan | Aislado, no ve decisiones de otros |
| **Costo** | Tokens crecientes por turno | Tokens fijos por sub-tarea |

**Limitación:** Los sub-agentes no comparten estado. Si dos sub-agentes necesitan la misma información base, la cargan dos veces. Y el orquestador debe ser lo suficientemente inteligente para descomponer y consolidar correctamente.

---

## El patrón actual: todo junto

La arquitectura que resuelve las limitaciones del LLM con ingeniería, combinando los cuatro elementos:

```
Orquestador (coordina)
    ├── Skills (contexto preciso bajo demanda)
    ├── Sub-agentes (ejecución limpia y efímera)
    └── Memoria persistente (continuidad entre sesiones)
```

**Componentes del stack actual:**

### 1. Orquestador
El agente principal que recibe la tarea del usuario, la descompone, decide qué skills y sub-agentes lanzar, y consolida los resultados. Es el *cerebro coordinador*. No ejecuta tareas pesadas directamente — delega.

### 2. Skills (contexto preciso)
El router ligero (`AGENTS.md` como índice) + `SKILL.md` bajo demanda. Resuelve el problema de "qué necesita saber el agente para esta tarea específica". El contexto se carga solo cuando el orquestador o un sub-agente lo solicita.

### 3. Sub-agentes (ejecución limpia)
Workers efímeros que nacen con contexto fresco (instrucciones + skill), ejecutan una sub-tarea concreta, reportan al orquestador, y mueren. No acumulan ruido de conversaciones previas. Pueden ejecutarse en paralelo.

### 4. Memoria persistente (continuidad)
Archivos de estado, bases de conocimiento o *memory banks* que sobreviven a la sesión del agente. Permiten que el orquestador y los sub-agentes recuerden decisiones previas, aprendan del feedback, y mantengan coherencia entre sesiones sin recargar todo el historial.

**Por qué funciona:**

- **Rompe la ventana de contexto:** Skills cargan solo lo necesario. Sub-agentes no acumulan historial. Memoria guarda lo importante fuera del prompt.
- **Escala con el proyecto:** Más reglas = más skills, no más tokens por turno. Más complejidad = más sub-agentes, no más ruido.
- **Paralelismo real:** Tareas independientes se ejecutan simultáneamente en lugar de secuencialmente.
- **Costo predecible:** Tokens por tarea en lugar de tokens crecientes por sesión.

**El principio fundamental:** No intentes que un solo LLM lo sepa todo y lo recuerde todo. En su lugar, dale la herramienta correcta (skill), el foco correcto (sub-agente), y la memoria correcta (persistencia) para cada momento. La inteligencia no está en el modelo — está en la arquitectura que lo rodea.
