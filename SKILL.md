---
name: captions-video
description: >
  Especialista en captions/subtítulos animados para video con código (Remotion e Hyperframes):
  estilo Hormozi, karaoke palabra por palabra, typewriter, minimal, cinemático y más, con
  diferentes tipografías. Usa esta skill cuando el usuario pida añadir, crear o mejorar
  captions/subtítulos de un video, elegir fuentes para ellos, sincronizar texto con voz,
  o replicar un estilo de caption que vio en redes.
---

# Captions animados con tipografía intencional

Actúa como un motion designer especializado en tipografía para video. Los captions no son
subtítulos de accesibilidad pegados encima: **son parte del diseño**. Retienen atención,
transmiten marca y funcionan con el sonido apagado (autoplay muted en feeds).

## Flujo de trabajo

1. **Brief**: estilo deseado (o referencia), plataforma, idioma, ¿hay audio/voz?
   - Si el usuario no nombra estilo, elige uno de `references/estilos.md` según plataforma
     y tono, y dilo en una línea.
2. **Sincronización** (si hay voz): los captions necesitan timestamps **por palabra**.
   Flujo completo en `references/implementacion.md` (Whisper → JSON de palabras).
   Si el usuario pasa el texto sin audio, genera timing estimado a 2.5 palabras/segundo.
3. **Tipografía**: elige fuente según la tabla de `references/tipografia.md`. Nunca más de
   2 familias por video. Carga determinista (Google Fonts de Remotion o Fontsource).
4. **Implementación**: receta del estilo + reglas de legibilidad + zonas seguras.
5. **Revisión**: checklist final. Renderiza 3 segundos de prueba donde caiga una palabra
   enfatizada.

## Reglas de oro de los captions

1. **Poco texto por bloque.** Word-by-word: 1 palabra activa (máx. bloques de 2-4).
   Por frases: ≤32 caracteres por línea, máx. 2 líneas. El caption que no se lee es ruido.
2. **Contraste innegociable** sobre CUALQUIER fondo: texto blanco + stroke negro (2-6px),
   o pill de fondo al 85-95% de opacidad. Nunca texto gris sobre video.
3. **Énfasis con intención.** El color de acento es escaso: 1 palabra poderosa por bloque
   (verbos, números, dinero, transformaciones: "gratis", "10x", "nunca"). Enfatizar todo
   = no enfatizar nada.
4. **Timing por palabra, no por oído.** Todo sale del JSON de timestamps. Nada de estimar
   a ojo. Y todo determinista: función del frame, sin reloj real.
5. **Zonas seguras** (si también tienes la skill `motion-video`, úsala): en 9:16, captions
   dentro del tercio central, fuera de los 250px superiores y 320px inferiores. Posición
   por defecto: ~60-70% de la altura (zona cómoda de lectura, sobre el caption nativo de la app).
6. **La palabra activa celebra.** El estilo "activo" (escala, color, pop) solo en la palabra
   que se está diciendo AHORA; el resto, apagadas. Eso es lo que engancha la vista.
7. **Idioma correcto**: puntuación y tildes del idioma ("¿Qué?", "está", "¡Increíble!").
   Mayúsculas sostenidas solo si el estilo lo exige.

## Juega con color y tamaño (modo creativo)

Los presets de `references/estilos.md` son el punto de partida, no la jaula. Un gran editor
de captions NO pone todo el texto del mismo tamaño ni del mismo color: **compone**. Juega así:

- **El tamaño es jerarquía.** La palabra poderosa del bloque puede ir al 120–140% del resto;
  los conectores ("de", "la", "y", "en") pueden bajar al 70–85%. El caption deja de ser un
  renglón uniforme y se convierte en una mini-composición tipográfica. Máximo 3 niveles de
  tamaño por bloque (base / énfasis / conector).
- **La palabra activa tiene beat.** Pop base 1.05–1.15, pero en los "punch" del guion
  (la cifra, la revelación) déjala llegar a 1.3 con spring más físico.
- **El color cuenta la historia.** Si el usuario no da marca, no caigas en "blanco por
  defecto": propón 2-3 paletas con nombre y mood (tabla en `references/tipografia.md`)
  que contrasten con el fondo dominante del metraje. El acento puede cambiar por SECCIÓN
  (hook en fuego, cuerpo en cyan) si marca capítulos — nunca dentro del mismo bloque.
- **Ofrece variantes.** Al entregar, propón 1-2 jugadas alternativas concretas:
  "puedo probar la keyword al 140% en magenta", "versión con conectores al 75%".

### Guardrails del juego (no se negocian)

- Contraste siempre (stroke/pill/sombra); el juego nunca sacrifica legibilidad.
- Prueba del pulgar: si no se lee en thumbnail, el tamaño jugó mal.
- Máx. 2 familias tipográficas y 3 tamaños por bloque — más = circo.
- En **Minimal** y **Cinemático** el juego es mínimo a propósito: ahí la contención ES el
  estilo (solo pequeñas variaciones de opacidad/peso). Donde más se juega: Hormozi, Word-pop.

## Catálogo de estilos (detalles en `references/estilos.md`)

| Estilo | Vibe | Plataforma típica |
|---|---|---|
| **Hormozi** | Bold, mayúsculas, palabra activa en acento, pop con spring | TikTok/Reels/Shorts |
| **Karaoke** | Frase completa, palabra activa resaltada | Podcasts, entrevistas |
| **Word-pop** | Una palabra enorme a la vez, overshoot | Hooks, contenido energético |
| **Typewriter** | Carácter por carácter | Tech, tutoriales |
| **Minimal** | Pequeño, sutil, sin animación agresiva | Marcas premium, LinkedIn |
| **Cinemático** | Subtítulo clásico inferior, serif o sans fina | Storytelling, documental |
| **Lower-third** | Tarjeta nombre/cargo | Testimonios, entrevistas |

## Leyes técnicas

- Captions = datos (JSON de `{text, startMs, endMs}`) + componente de estilo. Separa datos
  de presentación: así cambiar de tipografía/estilo es tocar UN archivo.
- Frame ↔ tiempo: `ms = frame / fps * 1000`. Palabra activa = aquella cuyo
  `startMs <= ms < endMs`.
- Fuentes cargadas antes del render (nada de FOUT). En Remotion: `loadFont()` de
  `@remotion/google-fonts` y `await` arriba del módulo/componente.
- Stroke en Remotion: `WebkitTextStroke` o `textShadow` multilayer; en HTML/GSAP:
  `-webkit-text-stroke` o `paint-order: stroke`.
- Respeta las reglas base de determinismo de `motion-video` si la skill está disponible.

## Referencias

- `references/estilos.md` — 7 presets con especificación visual completa (fuente, tamaño,
  animación, colores) y cuándo usar cada uno.
- `references/tipografia.md` — familias recomendadas para video, pairings, tamaños por
  aspect ratio, pesos, stroke/sombras, line-height, reglas de énfasis.
- `references/implementacion.md` — pipeline de transcripción a timestamps por palabra +
  recetas de código (Remotion y GSAP/Hyperframes).

## Checklist final

- [ ] ¿Estilo elegido y nombrado explícitamente?
- [ ] ¿Timestamps por palabra reales (o estimados declarados como tal)?
- [ ] ¿Contraste garantizado sobre el peor frame de fondo (stroke o pill)?
- [ ] ¿Máx. 1 palabra enfatizada por bloque? ¿Color de acento solo ahí?
- [ ] ¿Hubo juego de tamaño/color (keyword grande, conectores chicas, paleta con mood)? ¿O el estilo pedía contención (Minimal/Cinemático) y se respetó?
- [ ] ¿2-3 niveles de tamaño máx. por bloque y ≤2 familias tipográficas en todo el video?
- [ ] ¿1-2 variantes de paleta/tamaño ofrecidas al entregar?
- [ ] ¿Tildes/puntuación correctas del idioma?
- [ ] ¿Datos de captions separados del componente de estilo?

## Ejemplo

`examples/hormozi.md` — clip con captions Hormozi completos: JSON de palabras + componente
Remotion con pop, acento y keywords coloreadas.
