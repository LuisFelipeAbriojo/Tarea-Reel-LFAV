# Reel: clasificador de flujos de caja

Reel vertical de 55 s, narrado con mi voz clonada, sobre mi proyecto
[EMSA-FLUJOS-AUTOMATIZADOS](https://github.com/LuisFelipeAbriojo/EMSA-FLUJOS-AUTOMATIZADOS): un clasificador que ordena
los movimientos bancarios del mes por categoría de flujo de caja y corre dentro del navegador.

Hecho con el pipeline de la [sesión 15 · Del texto a tu voz](https://github.com/d2cml-ai/Data-Science-Python/tree/main/sessions/15-texto-a-reel)
(Universidad del Pacífico).

**🎬 Video final: [`salidas_v2/reel.mp4`](salidas_v2/reel.mp4)** · Posts: [`salidas_v2/posts.md`](salidas_v2/posts.md)

> La voz del video fue generada con IA (Qwen3-TTS) a partir de mi voz.

| | |
|---|---|
| Texto fuente | [`fuente_emsa.md`](fuente_emsa.md): README + LEEME del repo EMSA (844 palabras) |
| Guion | Claude, con el prompt de `guion.py`; cifras verificadas contra la fuente |
| Voz | Qwen3-TTS 0.6B (CPU), clonada de 17 s de mi voz, `--velocidad 1.05` |
| Subtítulos y control | Whisper large-v3-turbo; WER por escena entre 0.00 y 0.06 |

## Qué corregí del guion

1. **La primera escena no describía bien el trabajo manual.** El borrador decía que los movimientos «se clasificaban uno
   por uno»; eso lo dedujo el modelo, el texto fuente solo habla de «la clasificación del equipo». La reescribí con cómo
   se hacía de verdad: *«Cada mes copiábamos los movimientos de cada banco a Excel y los separábamos con filtros de texto.
   En algunas empresas tomaba horas.»* En pantalla: «Antes: horas de filtros en Excel».
2. **Una cifra no pasó la verificación.** `guion.py` marcó `25,698` porque la fuente escribe `25.698`; se unificó la
   notación con la del texto (también `12.623`).
3. **Duraba 64 s.** Recorté la explicación del modelo y la escena de revisión (se fue «el resto acierta 99,6 %») hasta
   ~56 s; con la escena 1 nueva, más larga, subí la velocidad 5 % para quedar en 55 s.
4. **Nada inventado:** se evitaron afirmaciones de ahorro de tiempo en cifras o de impacto que no estuvieran en el repo.

La primera versión, con la escena original, está en [`salidas/reel.mp4`](salidas/reel.mp4) para comparar.

## Archivos

| Archivo | Qué es |
|---|---|
| `salidas_v2/` | **Versión final:** `reel.mp4`, `posts.md`, `portada.png`, `reel.srt`, `guion.json` (con WER por escena) |
| `salidas/` | Primera versión |
| `respuesta_v2.txt` / `respuesta_claude.txt` | Guion final / primer guion, tal como los respondió el chat |
| `fuente_emsa.md` | Texto fuente |
| `guion.py`, `reel.py`, `hacer_reel.py` | Pipeline de la sesión 15 (con `encoding="utf-8"` añadido para Windows) |
| `ejemplo/guion_incendios.json` | Guion de ejemplo de la clase |

## Reproducir (Windows)

```bash
uv venv .venv --python 3.11
uv pip install --python .venv/Scripts/python qwen-tts faster-whisper pillow imageio-ffmpeg soundfile jiwer num2words pypdf

python guion.py --texto fuente_emsa.md --respuesta respuesta_v2.txt --salida salidas_v2/guion.json
.venv/Scripts/python hacer_reel.py --voz mi_voz.m4a --autor "Luis Felipe Abriojo" --fuente "Validación agosto 2026" \
    --tts Qwen/Qwen3-TTS-12Hz-0.6B-Base --velocidad 1.05 --salidas salidas_v2
```

Mi grabación de referencia (`mi_voz.m4a`) no se sube al repo. Con tu propia nota de voz de 10 a 25 s sirve.
