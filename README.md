# Módulo 3 — «Never Stop» / *La montaña enseña paciencia*

Pieza audiovisual de 10 s para la campaña **The North Face · Never Stop Exploring**.
Un montañista curtido, detenido en una arista de alta montaña, narra en primera
persona la lección de tres días de espera bajo la tormenta. El objetivo del módulo
es documentar el **pipeline técnico completo** de generación asistida por IA —de la
imagen a la locución al avatar parlante— y las decisiones de dirección de arte que
lo sostienen.

> «El primer día creímos que era cuestión de horas. El segundo, dejamos de planear.
> El tercero, entendimos por qué estábamos ahí. La montaña enseña paciencia antes
> que cualquier otra cosa.»

---

## 1. Pipeline de producción

```
Gemini (imagen fija)                    →  assets/image/kai_ridge_source.png
        │
        ├─ ElevenLabs (locución)        →  assets/audio/voiceover_rudra_narrator.mp3
        ├─ Flow / ElevenLabs (música)   →  assets/audio/music_neblina_profunda.mp3
        │
HeyGen (Photo Avatar + Precision Mode + audio sync)
        │                                  ← foto fija + voiceover
        ▼
        vídeo con lip-sync (personaje hablando)
        │
After Effects (mezcla y master de audio)
        ▼
        output/never_stop_final.mp4        (1280×720, 10 s, H.264/AAC)
```

Cada herramienta cubre una etapa concreta: **Gemini** fija el frame maestro,
**ElevenLabs** aporta voz y música, **HeyGen** convierte la foto estática en un
personaje que habla sincronizado con la locución, y **After Effects** hace el
master final de audio.

---

## 2. Estructura del repositorio

```
Modulo-3/
├── README.md                          ← este documento (reporte técnico)
├── assets/                            ← insumos de origen
│   ├── image/
│   │   └── kai_ridge_source.png       ← frame maestro (Gemini) → foto base de HeyGen
│   └── audio/
│       ├── voiceover_rudra_narrator.mp3   ← locución (ElevenLabs)
│       └── music_neblina_profunda.mp3     ← música de fondo (ambient/trip-hop)
├── output/
│   └── never_stop_final.mp4           ← entregable final (post After Effects)
└── docs/
    └── prompts.md                     ← diccionario de prompts usados en cada etapa
```

---

## 3. Etapa 1 — Imagen base (Gemini)

Frame maestro generado en Gemini: montañista de rasgos curtidos, pelo oscuro corto
y barba de días, chaqueta puffer **amarilla** The North Face, arnés y mochila
técnica, sobre una arista de roca con macizo nevado y niebla al fondo.

**Justificación estética (ver §7, manual de identidad):** el encuadre coloca al
**héroe humano pequeño frente a la escala de la montaña** (Pilar 01 · *Escala y
soledad*), con **luz natural fría y difusa de cumbre** (Pilar 02 · *Luz natural
honesta*) y **texturas técnicas reales** —niebla, roca húmeda, tejido de la puffer—
(Pilar 03 · *Textura técnica real*). El fondo desaturado de carbón y blanco glaciar
respeta la **regla 60-30-10** de la paleta maestra.

Esta imagen es doblemente insumo: es el frame final que ve el espectador **y** la
foto de origen que se carga en HeyGen como Photo Avatar.

---

## 4. Etapa 2 — Audio (ElevenLabs)

### 4.1 Locución
- **Voz:** *Rudra – Intense Documentary Narrator* (ElevenLabs).
- **Ajustes:** `stability = 50`, `similarity/style = f2-5` (identificadores del
  render `_pvc_s50_f2-5`). Registro grave, cadencia lenta y documental para
  sostener el tono meditativo de la pieza.
- **Guion:** voiceover en primera persona, ~35–45 s de copy reducido a la cápsula
  de 10 s del corte final.
- **Salida:** `assets/audio/voiceover_rudra_narrator.mp3`.

### 4.2 Música de fondo
Se iteró la dirección musical hasta alejarla del score orquestal genérico hacia un
**ambient / downtempo** de texturas analógicas (referencias de brief: Green-House,
Air, Portishead). Prompt final:

> *cinematic trip-hop ambient, mysterious dusty drums, deep sub-bass, vintage
> synthesizer pads, moody and evocative, naturalistic depth, 75 bpm*

- **Salida:** `assets/audio/music_neblina_profunda.mp3` («Neblina Profunda»).

---

## 5. Etapa 3 — HeyGen (Photo Avatar + Precision Mode + audio sync)

Núcleo técnico del módulo: convertir la **foto fija de Gemini** en un personaje que
**habla** sincronizado con la locución de ElevenLabs, sin perder la identidad
visual de la campaña.

### 5.1 Photo Avatar
1. En HeyGen → **Avatars → Photo Avatar → Upload**, se carga
   `assets/image/kai_ridge_source.png` como imagen fuente.
2. HeyGen detecta el rostro y genera un avatar hablante a partir de esa única foto
   (no se usa avatar de stock: el personaje del vídeo **es** la imagen de Gemini,
   lo que preserva rostro, vestuario y encuadre de campaña).
3. Se conserva el encuadre original 3:4 → export a 1280×720 (16:9 recortado para el
   máster de vídeo).

### 5.2 Precision Mode — cómo se ajustó
**Precision Mode** es el modo de HeyGen que prioriza **fidelidad de lip-sync y
estabilidad del rostro** por encima de la expresividad exagerada. Se ajustó así:

- **Activación:** en la configuración del avatar hablante se seleccionó *Precision*
  (en lugar de *Expressive/Stable* por defecto). Motivo: la pieza necesita que la
  boca siga la locución con exactitud pero el resto del rostro y el fondo **no se
  deformen**, porque cualquier "morphing" del fondo de montaña rompería la estética
  documental.
- **Intensidad de movimiento:** reducida al mínimo funcional. La dirección de la
  pieza pide un personaje **detenido y sereno** (§7, Pilar 01), no un presentador
  gesticulando; se limita el head-movement y el pestañeo para que solo la boca y una
  micro-expresión trabajen.
- **Encuadre estable:** al fijar el avatar sobre la foto original, Precision Mode
  evita el cabeceo que arrastraría el fondo nevado y delataría la generación.
- **Resultado:** boca sincronizada con la voz de Rudra, rostro estable, fondo de
  montaña intacto → el personaje "habla" el guion sin perder el frame de Gemini.

### 5.3 Configuración de audio en HeyGen
- Fuente de voz: **audio subido** (no TTS interno de HeyGen). Se carga
  `voiceover_rudra_narrator.mp3` para que HeyGen sincronice el lip-sync **contra la
  locución ya producida en ElevenLabs**.
- Se verifica que la duración del avatar coincida con la del voiceover (10 s) antes
  de renderizar.
- Export de HeyGen: vídeo con el personaje hablando, **sin** música todavía (la
  música se mezcla después en After Effects para controlar niveles).

---

## 6. Etapa 4 — After Effects (máster de audio)

- Se importa el render de HeyGen y las dos pistas de audio.
- **Se baja el volumen de la música de fondo** («Neblina Profunda») para que la
  **locución sea la protagonista**: la voz queda al frente y el ambient trabaja como
  cama, sin competir con el mensaje.
- Se exporta el máster final: `output/never_stop_final.mp4` — **1280×720, 10 s,
  H.264 / AAC**.

---

## 7. Justificación estética — manual de identidad visual

Todas las decisiones de fondo y personaje se anclan en el
**Manual de Identidad Visual con IA · Campaña «Never Stop»** (*The North Face —
Never Stop Exploring*). Referencias directas:

| Decisión en la pieza | Fundamento en el manual |
|---|---|
| Héroe pequeño / arista amplia | **Pilar 01 · Escala y soledad** — «el ser humano siempre pequeño frente a la naturaleza». |
| Luz fría, niebla, contraluz de cumbre | **Pilar 02 · Luz natural honesta** — «nada de estudio artificial; iluminación cruda y climatológica». |
| Textura de roca, niebla y tejido técnico | **Pilar 03 · Textura técnica real** — «la materialidad como prueba de rendimiento». |
| Fondo carbón/blanco glaciar desaturado | **Paleta maestra**, regla 60-30-10 (60 % base carbón/piedra · 30 % blanco glaciar/azul alpino · 10 % rojo Summit). |
| Personaje curtido, barba de días, mirada serena off-camera | **Character sheet · «Kai»** — «montañista de 34 años, piel curtida, pelo oscuro corto, barba de días, mirada serena y decidida». |
| Acabado editorial, grano fílmico, look 85mm | **Concepto visual del moodboard** — «estilo fotográfico editorial y documental de alta montaña […] leve grano fílmico que aleja la imagen del acabado plástico de la IA». |

> Nota de color: el frame de esta pieza usa la puffer **amarilla** (variante de
> campaña) sobre base desaturada. El acento **rojo Summit** (#C41E3A) del manual se
> mantiene como regla para el resto del sistema; aquí la energía cromática recae en
> el amarillo del hero garment sobre el fondo carbón/glaciar, conservando el
> principio de *un único punto de color saturado sobre base terrosa*.

---

## 8. Entregable

`output/never_stop_final.mp4` — vídeo final generado con **HeyGen** (Photo Avatar +
Precision Mode, audio sincronizado contra la locución de ElevenLabs) y masterizado
en After Effects. 1280×720 · 10 s · H.264/AAC.
