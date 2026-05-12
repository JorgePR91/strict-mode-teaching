# Strict Mode — Sistema de enseñanza guiada para asistentes de IA

Conjunto de instrucciones para forzar a un asistente de IA (Claude, ChatGPT, Copilot, etc.) a comportarse como un **mentor técnico paso a paso** en lugar de un generador de soluciones completas.

Pensado para programadores que están **aprendiendo** y quieren mantener el control del flujo en lugar de recibir código que no entienden.

---

## Objetivo

Tener un **archivo de instrucciones referenciable** que se pueda cargar en cualquier asistente de IA para que, por defecto, **no priorice la productividad y la eficiencia por encima de la explicación y la guía**.

Los asistentes vienen optimizados para resolver rápido. Este archivo invierte esa prioridad: primero entender, después implementar. La velocidad pasa al último lugar de la lista.

---

## Por qué existe

Los asistentes de IA tienden por defecto a:

- generar soluciones completas en una sola respuesta,
- adelantar pasos que no se han pedido,
- ocultar el razonamiento detrás del código,
- y optimizar para rapidez en lugar de comprensión.

Esto es útil si ya sabes lo que haces. Pero si estás aprendiendo, produce el efecto contrario: avanzas más rápido y **entiendes menos**.

Este archivo de instrucciones invierte ese comportamiento por defecto.

Mira [`examples/before.md`](examples/before.md) y [`examples/after.md`](examples/after.md) para ver la diferencia con una conversación real.

---

## Qué hace

Al cargar `strict-mode.md` en el contexto de un asistente, este pasa a:

- **Clasificar** cada petición en una de cuatro categorías (implementación, tarea trivial, pregunta conceptual, diagnóstico de error) y aplicar el protocolo adecuado.
- **Dividir** todo trabajo en pasos pequeños (máximo 1 archivo, 1 responsabilidad, ≤40 líneas por paso).
- **Explicar** antes de implementar: objetivo, razonamiento, alternativas, riesgos.
- **Verificar** cada paso antes de continuar.
- **Detenerse** y esperar confirmación explícita entre pasos.
- **Tratar los errores** como oportunidades pedagógicas, no como urgencias que resolver en silencio.

Aunque pidas explícitamente "hazlo todo de golpe", el asistente seguirá dividiendo. Esto es intencional.

---

## Contenido del repositorio

```
strict-mode.md           Archivo principal con las reglas (versión completa)
strict-mode-anchor.md    Versión corta para reactivar el modo en cada prompt
README.md                Este archivo
LICENSE                  Licencia MIT
examples/
  before.md              Conversación sin strict mode
  after.md               Misma petición con strict mode
```

---

## Uso recomendado: carga persistente + anchor

Pegar el archivo completo en cada prompt funciona, pero es frágil: te tienes que acordar, consume tokens y no impide que el modelo se relaje en respuestas largas. El enfoque robusto combina **dos capas**:

### Capa 1 — Carga persistente (una vez)

Mete `strict-mode.md` en el mecanismo de instrucciones permanentes de tu plataforma para que se inyecte **automáticamente** en cada turno:

| Plataforma | Dónde pegarlo |
|---|---|
| Claude Code | `CLAUDE.md` (global en `~/.claude/CLAUDE.md` o de proyecto) |
| Cursor | `.cursorrules` o Settings → Rules for AI |
| ChatGPT | Settings → Personalization → Custom Instructions, o crea un Custom GPT |
| Claude.ai | Projects → System Instructions |
| API directa | System prompt |

Hecho esto, no tienes que volver a tocarlo. Cada conversación nueva ya empieza con las reglas cargadas.

### Capa 2 — Anchor (al inicio de prompts no triviales)

Las reglas de la Capa 1 pesan menos a medida que crece la conversación, porque los LLM dan más peso a los tokens recientes. Para refrescarlas sin pegar otra vez los 12 KB completos, copia el contenido de [`strict-mode-anchor.md`](strict-mode-anchor.md) (≈10 líneas) al inicio de tus prompts importantes.

Esto reactiva el framing porque queda en posición reciente del contexto, donde el modelo le da más atención.

### Cuándo re-pegar el archivo completo

Si notas que el asistente **se ha saltado un paso** o ha empezado a encadenar acciones:

```
Vuelve a aplicar strict-mode.md desde cero. Acabas de saltarte
el protocolo de [confirmación / clasificación / verificación].
```

Esto fuerza un "reset" puntual sin necesidad de empezar la conversación de nuevo.

---

## Alternativa simple: pegar el archivo en el primer prompt

Si no quieres montar carga persistente, también puedes pegar el archivo completo al inicio de la conversación:

```
Sigue las reglas definidas en strict-mode.md a partir de ahora.
```

Funciona, pero las reglas se diluyen en conversaciones largas. Para esos casos, usa además el anchor.

---

## Las 4 categorías de petición

El asistente clasifica cada petición antes de responder:

| Categoría | Ejemplo | Protocolo |
|-----------|---------|-----------|
| **A — Implementación** | "Crea un endpoint de login" | Protocolo completo (7 fases) |
| **B — Tarea trivial** | "Renombra esta variable" | Cambio mínimo + confirmación |
| **C — Pregunta conceptual** | "¿Qué es un JWT?" | Explicación, sin código |
| **D — Diagnóstico de error** | "Me sale este error" | Causa + opciones, sin tocar código aún |

---

## Las 7 fases del protocolo completo

Para tareas de Categoría A:

1. **Objetivo** — qué se va a hacer y por qué.
2. **Razonamiento técnico** — alternativas, ventajas, riesgos.
3. **Implementación mínima** — solo el fragmento necesario.
4. **Explicación detallada** — línea por línea.
5. **Consecuencias de hacerlo mal** — bugs, deuda técnica, riesgos.
6. **Verificación** — cómo comprobar que el paso funciona.
7. **Detención** — esperar confirmación explícita.

---

## Limitaciones conocidas

Hay que ser honesto: esto es un prompt, no un sistema verificable. Funciona mejor en ciertas condiciones:

- **Modelos grandes.** Funciona bien con Claude Sonnet/Opus, GPT-4 / GPT-5 y similares. Modelos pequeños (GPT-3.5, Gemini Flash, Llama pequeños) tienden a ignorar instrucciones largas.
- **Conversaciones cortas o medias.** En sesiones muy largas, las reglas se diluyen a medida que crece el contexto. Si notas que el asistente "se relaja", reinicia la conversación o vuelve a pegar las reglas.
- **Usuarios con paciencia.** El sistema es deliberadamente más lento. Si tienes prisa o estás en producción, este modo no es para ti.
- **No hay garantías.** Es una guía de comportamiento, no un mecanismo de runtime. El asistente puede saltarse pasos; en ese caso, recuérdaselo.

---

## Para quién es esto

- Personas que **están aprendiendo a programar** y usan IA como tutor.
- Personas en transición a nuevos lenguajes / frameworks que quieren entender, no copiar.
- Profesores que quieren un asistente con el que sus alumnos puedan trabajar sin que les genere la solución entera.

**No es para ti si**:

- ya tienes experiencia y solo quieres autocompletado rápido,
- necesitas prototipar a gran velocidad,
- estás en producción y la pedagogía no es prioridad.

Para esos casos, desactiva el modo estricto.

---

## Personalización

El archivo está pensado para usarse tal cual, pero puedes ajustarlo:

- Cambia el **criterio de paso mínimo** (líneas, archivos) si te queda corto o largo.
- Añade categorías propias (E, F…) si tu flujo lo necesita.
- Traduce a otro idioma manteniendo la estructura.

Los cambios más útiles suelen estar en la sección "CLASIFICACIÓN DE LA PETICIÓN" y en el "Criterio operativo de paso mínimo".

---

## Licencia

MIT. Úsalo, modifícalo y compártelo libremente.

---

## Contribuir

Si detectas un caso que las reglas actuales no cubren bien (por ejemplo: revisión de PRs, refactors grandes, debugging de concurrencia), abre un issue describiendo:

- el caso concreto,
- qué hace el asistente actualmente,
- qué te gustaría que hiciera.
