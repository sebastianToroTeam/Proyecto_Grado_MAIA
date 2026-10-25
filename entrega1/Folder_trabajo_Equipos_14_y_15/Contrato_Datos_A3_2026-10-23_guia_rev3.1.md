# Contrato de Datos Compartido — Anotación de Función de Cita (A3)

**Versión:** actualización documental del 24 de octubre de 2026, alineada con la Guía rev3.1 (actualizada el 23 de octubre de 2026, sin cambios en las reglas). Actualiza los conteos, aclara las cuotas e incorpora el calendario validado por el equipo para el informe con corte al 23 de octubre; se conserva el nombre del archivo para mantener sus referencias. Cambios en el registro al final.

**Estado:** el procedimiento de la versión del 23 de octubre fue puesto a revisión de los grupos 14 y 15, sin objeciones, e incorpora las observaciones recibidas. Se aplica desde la segunda calibración (18 de octubre de 2026). La actualización documental del 24 de octubre incorpora las cifras validadas por el equipo y distingue los paquetes activos de las etiquetas finales válidas, sin modificar las reglas de anotación ni la configuración de los jueces.

## 1. Propósito y alcance

Los grupos 14 y 15 (el "equipo extendido") construyen **dos conjuntos de prueba (test sets) disjuntos, uno por grupo (T\_A y T\_B), de ≥2.700 instancias cada uno (≥300 por categoría)**, validados por humanos, para cumplir la actividad A3. Así lo exige la aprobación del director: cada grupo cumple su propia cuota de validación con muestras diferentes, sin solapamiento.

Cada instancia se anota de forma independiente por **dos anotadores: uno del grupo dueño del conjunto, cuya anotación es la validación de ese grupo, y uno del otro grupo**, que aporta la segunda anotación que exige la propuesta para medir el acuerdo. Así, el acuerdo interanotador proviene de anotadores de grupos distintos, y ambos grupos comparten guía, formatos, calibración, métricas y adjudicación.

La anotación se hace en **dos rondas** (Sección 6): la ronda 1 cubre las seis categorías con suministro suficiente en el pool original y la ronda 2 las tres restantes, tras la extensión del corpus. En total, cada grupo realiza ≈5.940 anotaciones: ≈2.970 de su conjunto y ≈2.970 del conjunto del otro grupo (9 categorías × 330 paquetes activos), más los paquetes de reserva que se liberen.

**Cubre:** identificación de instancias, pool compartido de pre-etiquetas (A2), muestreo, material de anotación, protocolo, formato de registro, acuerdo interanotador, adjudicación, entregables, lista de exclusión, regla de partición para A4 y cronograma.

**No cubre:** los modelos de cada grupo (A4, A5) ni sus pipelines posteriores. De los datos de entrenamiento y validación, el contrato fija solo lo necesario para que no haya fuga hacia los test sets: la lista de exclusión y la regla de partición (Sección 10). Después de A3 los grupos trabajan por separado.

**Documentos de referencia:** propuesta del proyecto (A2, A3, Sección 3), *Guía de Anotación — Clasificación de Función de Cita* (rev3.1), que es la fuente única de las definiciones de categorías y de las reglas de decisión, y la *Hoja de instrucciones A3*, un resumen de la guía que los anotadores tienen abierto al lado de cada lote (si difieren, manda la guía). La guía rev3.1 corresponde al prompt v11.1 de A2.

## 2. Precondiciones

| # | Condición | Estado |
| --- | --- | --- |
| P1 | **Esquema idéntico:** mismas 9 categorías, mismas definiciones (Guía, Sección 3), exclusión de *Assessment*, mismas cadenas de etiqueta y la misma regla para `unclear` | Confirmada (25 de septiembre de 2026) |
| P2 | **Mismo corpus:** ambos grupos usan unarXive 2022 (`unarXive_230324`), subconjunto cs | Confirmada. A1 y A2 se corrieron con un pipeline común sobre `cs_subset_core` y, el 22 de octubre, sobre su extensión a cs.LG, cs.CL y cs.DS |
| P3 | **Aprobación del director:** el trabajo conjunto cumple el requisito de A3 ("15% validado manualmente por los integrantes del grupo") para cada grupo | Confirmada con condición (29 de septiembre de 2026): cada grupo cumple su cuota de validación humana con muestras diferentes, sin solaparse |

**Nota sobre P3:** con ≥18.000 instancias por grupo, 2.700 instancias equivalen al 15% del dataset de cada grupo. Cada grupo valida su propio conjunto (T\_A o T\_B), tomado de su mitad del pool compartido (Sección 6), y los dos conjuntos no comparten instancias ni artículos citantes. La propuesta exige al menos dos anotaciones independientes por muestra del test set: la segunda la aporta el otro grupo.

**Licencias (indicación del director).** El corpus completo puede usarse para entrenamiento y prueba si se publica como **identificadores más un script de reconstrucción**, no como texto. La demo usa solo artículos con licencia permisiva (campo `demo_eligible`, Sección 4).

## 3. Definición de instancia y clave canónica

Una instancia es **un par citante→citado asociado a un único `cite_span` de unarXive**. En una cita agrupada como "\[1,2,3\]", cada artículo co-citado es una instancia distinta con su propia etiqueta (Guía rev3.1, Sección 5).

La clave se construye solo con identificadores nativos de unarXive, no con identificadores internos de cada pipeline.

```
inst_key = "{citing_id}|{para_idx}|{span_start}-{span_end}|{cited_id}"
```

| Componente | Definición | Ejemplo | Campo en el pool |
| --- | --- | --- | --- |
| `citing_id` | arXiv ID del artículo citante, tal como aparece en unarXive | `2104.03841` | `citing_paper_id` |
| `para_idx` | Índice (base 0) del párrafo en la lista `body_text` original de unarXive, **antes de cualquier filtrado** | `12` | `citing_para_idx` |
| `span_start`, `span_end` | Offsets de carácter del `cite_span` dentro del texto original de ese párrafo | `341-359` | `marker_span.start`, `marker_span.end` |
| `cited_id` | arXiv ID del artículo citado, resuelto por unarXive | `1512.03385` | `cited_paper_id` |

**Clave de grupo:** `group_key = "{citing_id}|{para_idx}|{group_index}"`, con las reglas:

1. Se consideran **todos** los `cite_spans` del párrafo, estén o no resueltos a un arXiv ID. Si solo se usaran los resueltos, una referencia no resuelta en medio de "\[A, B, C\]" separaría a A y C en grupos distintos.
2. Los `cite_spans` se ordenan por su offset de inicio. Dos spans consecutivos pertenecen al mismo grupo si entre el final del primero y el inicio del segundo hay ≤5 caracteres (normalmente ", " o "; ").
3. `group_index` numera los grupos del párrafo en ese orden, empezando en 0. Un span aislado forma su propio grupo.

**Mismo artículo dos veces en un grupo.** En \~0,2% de los grupos, el mismo artículo citado aparece dos veces dentro del mismo corchete: la bibliografía del artículo citante tiene dos entradas (p. ej. versión preprint y versión publicada) que resuelven al mismo arXiv ID. Son instancias distintas (distinto `cite_span`, distinta `inst_key`), pero con el mismo contexto y el mismo artículo citado. No requiere tratamiento especial: el muestreo toma como máximo un miembro por grupo (Sección 6).

## 4. Pool compartido de pre-etiquetas (A2)

El equipo extendido construyó **un pool compartido de pre-etiquetas** con el pipeline común de A1 y A2: archivos JSONL con una línea por instancia, deduplicados por `inst_key`. El pool es el marco de muestreo de ambos test sets (Sección 6), la base de los datos de entrenamiento de cada grupo y la referencia para medir el acuerdo de los jueces con los humanos. **El pool no contiene texto de unarXive:** solo identificadores, localizadores y etiquetas (el script de A2 lo verifica antes de escribirlo).

**Dos versiones, la primera congelada:**

| Versión | Contenido | Instancias | Uso | sha256 |
| --- | --- | --- | --- | --- |
| `a2/pool_v11.1/` | Re-juicio de producción del 15 de octubre, sin las instancias de calibración | 29.336 | Marco de muestreo de la ronda 1. **Congelado:** `manifest_round1.json` guarda su sha256 y el notebook de A3 se detiene si cambia | `b872650a…ab4e0` |
| `a2/pool_v11.1_ext/` | `pool_v11.1` byte a byte, más `pool_cue_basis` (5.539) y `pool_ext_cue_rare` (3.905) | 38.780 | Marco de muestreo de la ronda 2 y base del entrenamiento de A4 | `8fff518f…98c14` |

Cada carpeta contiene `prelabels_pool.jsonl` (todas las instancias), `high_confidence_pool.jsonl`, `pool_summary.json` y los conteos (`counts.md`, `counts_ext.md`). La extensión nunca reescribe `pool_v11.1/`: se construye copiándolo y añadiendo los lotes nuevos.

**Conteos consolidados de los archivos del pool, verificados el 24 de octubre de 2026:**

| Versión | Instancias | Alta confianza | Alta confianza y elegibles para prueba | Empates sin pre-etiqueta | Otros casos sin alta confianza ni empate |
| --- | --- | --- | --- | --- | --- |
| `pool_v11.1` | 29.336 | 28.185 | 28.058 | 1.145 | 6 |
| `pool_v11.1_ext` | 38.780 | 37.254 | 37.112 | 1.520 | 6 |

Las instancias elegibles son un subconjunto de las de alta confianza, no una categoría adicional del total. Las 127 instancias de alta confianza no elegibles de la primera versión, y las 142 de la ampliada, corresponden a artículos citantes de calibración excluidos de los conjuntos de prueba. Los totales se concilian por estado: **28.185 + 1.145 + 6 = 29.336** y **37.254 + 1.520 + 6 = 38.780**. La extensión añade **5.539 + 3.905 = 9.444 instancias**, por lo que **29.336 + 9.444 = 38.780**. Estos valores proceden del conteo directo de los archivos, recogido como referencia auxiliar en `Tablas_A1-A3_G14G15.xlsx`, hoja `A2_producción`.

**Configuración congelada de A2** (todo el pool, incluidos los lotes de la extensión, se pre-etiquetó con ella):

| Elemento | Valor |
| --- | --- |
| Prompt | v11.1 (`a2_prompt_v11.py`), con título, resumen y Top-3 *chunks* del artículo citado; mismas reglas que la Guía rev3.1 |
| Jueces | `Qwen/Qwen3.8-27B` (revisión `1d4bf0f2ff`), `google/gemma-4-26B-A4B-it` (`4d7ae4984b`), `mistralai/Mistral-Small-3.2-24B-Instruct-2506` (`95a6d26c4b`); revisiones completas en los manifiestos de ejecución |
| Decodificación | Greedy, semilla 0, salida JSON con esquema: `evidence` (cita textual), `primary` (una de las 9 o `unclear`), `secondary` (hasta 3) |
| Entorno | vLLM 0.29.0, transformers 5.16.1, torch 2.13.0 |
| Equipo de origen | `source_team = "G14+G15"` (A2 se corrió en conjunto) |

**Ejemplo de registro** (campos de votación según `a2_merge.py`; localizadores y licencias según la sección 5 del notebook de producción):

```json
{
  "inst_key": "2104.03841|12|341-359|1512.03385",
  "group_key": "2104.03841|12|3",
  "source_team": "G14+G15",
  "source_batch": "pool_cue_eg",
  "prompt_version": "v11.1",
  "citing_paper_id": "2104.03841", "citing_para_idx": 12,
  "marker_span": {"start": 341, "end": 359},
  "cited_paper_id": "1512.03385", "group_size": 3,
  "top3": [{"chunk_key": "…", "section": "…"}, {"chunk_key": "…", "section": "…"}, {"chunk_key": "…", "section": "…"}],
  "judges": [
    {"model": "Qwen/Qwen3.8-27B", "prompt_version": "v11.1", "labels": ["Application"]},
    {"model": "google/gemma-4-26B-A4B-it", "prompt_version": "v11.1", "labels": ["Application", "Basis"]},
    {"model": "mistralai/Mistral-Small-3.2-24B-Instruct-2506", "prompt_version": "v11.1", "labels": ["Background"]}
  ],
  "votes": {"Application": 2, "Background": 1},
  "primary_pre_label": "Application",
  "tied_labels": [],
  "high_confidence": true,
  "stratum": "cue:Evidence",
  "n_valid_judges": 3,
  "unanimous": false,
  "test_eligible": true,
  "citing_license": "restrictive", "cited_license": "permissive",
  "demo_eligible": false
}
```

**Reglas del formato:**

- Las etiquetas usan exactamente las cadenas en inglés de la Guía rev3.1: `Background`, `Gap`, `Basis`, `Comparison`, `Application`, `Improvement/Modification`, `Evidence`, `Identification of the Originator`, `Further Reading`.
- En `judges[].labels` la primera es la etiqueta principal del juez y las demás, sus secundarias. Solo votan las principales; las secundarias se registran pero no votan. Un juez cuya respuesta falló (JSON inválido) queda con `labels: []` y `error`, y no vota; `n_valid_judges` cuenta los votos válidos. `primary_pre_label` es la categoría con más votos. En caso de empate no hay pre-etiqueta (`primary_pre_label: null`) y las categorías empatadas se guardan en `tied_labels`; esas instancias nunca son de alta confianza. Un juez cuya cita de evidencia no proviene del párrafo citante (`evidence_in_context: false`) no vota; su respuesta queda registrada con `abstain`.
- `high_confidence` es `true` si `primary_pre_label` tiene al menos 2 de 3 votos. `unanimous` es `true` con 3 de 3.
- **Calibraciones.** Las instancias de eval60, calib30 y calib2 se eliminan del pool al combinar los jueces (las 27 de calib2 el 17 de octubre, antes del muestreo de la ronda 1). Los demás registros de sus artículos citantes permanecen en el pool con `test_eligible: false`: nunca entran a un test set y quedan fuera del entrenamiento y la validación de ambos grupos (Sección 10). Son datos de desarrollo de la guía y del prompt.
- `citing_license` y `cited_license` toman `permissive`, `restrictive`, `other/unclassified`, `missing/unknown` o `not_in_core`; `demo_eligible` es `true` solo si ambas son `permissive`. Entrenamiento y prueba usan todas las instancias; la demo filtra por `demo_eligible`.
- El pool guarda todas las instancias pre-etiquetadas; el muestreo de los test sets usa solo las que tienen `high_confidence: true` y `test_eligible: true`.

**Requisitos de elegibilidad de los candidatos.** Solo entran al pool candidatos que cumplan:

- **Artículo citado con cuerpo completo:** al menos 1.000 palabras de cuerpo tras la limpieza de chunks. Además, se descarta si tiene secciones con nombre pero ninguna de tipo introducción (introducción, resumen, antecedentes, motivación, visión general) y menos de 2.000 palabras: es la firma de una extracción que solo contiene el apéndice.
- **Contexto de cita dentro del límite de SciBERT:** ≤512 tokens (se excluye, no se trunca).
- **Sin autocitas del mismo artículo:** `citing_id` distinto de `cited_id`.
- **Artículo citante en inglés:** los artículos citantes detectados en otro idioma se excluyen al construir los lotes; los de idioma indeterminado (texto demasiado corto) se conservan.

**Cifras del pool de la ronda 1:** la corrida de producción re-juzgó 28.553 instancias el 15 de octubre. El lote `pool_cue_yield`, juzgado con la misma configuración, contiene **885 instancias**, valor validado por el equipo. Las presentaciones de resultados de los prompts v11 y v11.1 identifican esas 885 como una muestra con los casos de eval60 ya excluidos.

Los tamaños de los lotes y los conteos consolidados del pool se registran por separado. La exclusión de calibraciones se controla por `inst_key`: no corresponde restar de nuevo todas las instancias de eval60 al lote de 885. El registro de cierre de A2 consigna **29.363 instancias y 28.212 de alta confianza antes del retiro de calib2**. Tras retirar sus 27 instancias, todas de alta confianza, se obtienen **29.363 − 27 = 29.336 instancias** y **28.212 − 27 = 28.185 de alta confianza**. Se reemplaza así la conciliación anterior basada en un lote de 900 por los tamaños validados y los conteos directos de los archivos.

## 5. Paquete de anotación congelado

Cada instancia se anota a partir de **un único paquete, generado una sola vez y mostrado de forma idéntica a ambos anotadores**. Así, un desacuerdo refleja diferencias de criterio, no diferencias en el material.

| Campo | Contenido | Cómo se muestra |
| --- | --- | --- |
| `package_id` | Identificador anónimo, asignado en orden aleatorio sobre ambos conjuntos (reserva incluida) al muestrear, de modo que no revela ni el conjunto ni si el paquete es de reserva: `P0001`–`P4320` en la ronda 1; la ronda 2 continúa desde `P4321` | Visible |
| `citation_context` | Párrafo citante completo, con la cita objetivo marcada como `[[TARGET]]` y su oración resaltada | Visible; la oración de `[[TARGET]]` también en una columna aparte |
| Otros marcadores | `{{cite:…}}` → `[CITE]`, `{{formula:…}}` → `[FORMULA]`, `{{figure:…}}` → `[FIGURE]`, `{{table:…}}` → `[TABLE]` (los mismos que leen los jueces de A2) | Visible |
| `cited_title`, `cited_abstract` | Título y resumen del artículo citado, desde los metadatos de unarXive | Visible |
| `top3_chunks` | Los 3 chunks recuperados por A1, con su sección (`[Section: …]`) | Plegados; se abren con un clic |
| `group_siblings` | Número de instancias co-citadas en el mismo grupo (Guía rev3.1, Sección 5) | Visible |
| `pre_label` | `primary_pre_label` de A2 | Visible desde el inicio |

**Lo que no se muestra:** los votos de los jueces, el conjunto al que pertenece el paquete y la identidad del artículo citante. El nombre de cada hoja (`<annotator_id>_<lote>`, con lotes `L001`, `L002`… mezclados entre conjuntos) tampoco revela el conjunto.

**Limpieza solo al mostrar:** algunos chunks contienen restos de macros de encabezado LaTeX (p. ej. "10pt plus 2pt minus 2pt"). Se eliminan al generar el paquete, no en los archivos de datos: el texto almacenado debe seguir coincidiendo exactamente con lo que reconstruye su localizador.

**Chunks del A1 común:** ambos grupos ven los mismos Top-3. La etiqueta de referencia describe la relación citante→citado, no los chunks, así que sigue siendo válida si un grupo cambia después su recuperación para A4 (Config B). En las pruebas de A2, quitar los chunks del prompt no cambió las pre-etiquetas en términos netos, así que la comparación de A4 entre configuraciones no queda sesgada por cómo se hicieron las etiquetas.

**Uso de los datos:** los paquetes contienen texto de unarXive (incluido material con licencia restringida). Se generan a partir de los localizadores del pool y se entregan solo en hojas privadas de Google Drive, dentro de una carpeta raíz de A3 que no se comparte como tal (Sección 7); no se redistribuyen. Los entregables finales (Sección 10) solo contienen claves y etiquetas.

## 6. Muestreo, cuotas y reserva

### 6.1 Rondas

| Ronda | Categorías | Pool | Estado |
| --- | --- | --- | --- |
| 1 | Background, Comparison, Gap, Evidence, Application, Identification of the Originator | `pool_v11.1` | Muestreada y congelada el 18 de octubre de 2026 (`_admin/sampling/`: `sample_round1.jsonl`, `assignment_round1.jsonl`, `supply_round1.md`, `exclusion_prelim_round1.jsonl`, `manifest_round1.json`) |
| 2 | Basis, Improvement/Modification, Further Reading | `pool_v11.1_ext` | Inicio de la anotación previsto para el lunes 26 de octubre de 2026. Vista previa completa (todas las celdas con 360); confirmación documental del muestreo y congelamiento pendiente |

La ronda 2 lee la muestra de la ronda 1 desde `sample_round1.jsonl` (el archivo, nunca un recálculo) como muestra previa, para que los límites por artículo citante y por grupo valgan sobre ambas rondas.

### 6.2 Partición y muestreo

Ambos conjuntos se muestrean del pool, solo entre instancias con `high_confidence: true` y `test_eligible: true`. Primero, cada `citing_id` se asigna a la **mitad A o a la mitad B** con una regla fija; T\_A se muestrea solo de la mitad A y T\_B solo de la mitad B. Así los conjuntos quedan disjuntos por construcción, por instancia y por artículo citante. Una **celda** es un conjunto por una categoría (18 celdas en total).

| Parámetro | Valor |
| --- | --- |
| Conjuntos | T\_A (de la mitad A), dueño el **grupo 14**; T\_B (de la mitad B), dueño el **grupo 15**; disjuntos |
| Semilla | `LCR-G14G15-A3-2026` (fijada el 17 de octubre de 2026, antes de muestrear; no se cambia). De ella se derivan flujos con nombre: partición (`semilla|partition|citing_id`), orden de sorteo (`semilla|sample|inst_key`), lotes (`semilla|batches|<ronda>`) y asignación (`semilla|<ronda>|assign`) |
| Partición en mitades | `sha256(semilla + "|partition|" + citing_id)` par → A, impar → B (≈50/50). Estable: no cambia si el pool crece, y los artículos nuevos de la extensión caen en su mitad con la misma regla |
| Orden de sorteo | Dentro de cada celda, los candidatos se ordenan por `sha256(semilla + "|sample|" + inst_key)` y se toman en ese orden |
| Paquetes por celda | **360 = 330 activos + 30 de reserva**; además, un **segundo colchón** con los 30 candidatos siguientes en el orden de sorteo, que no se anota (6.4) |
| Cuota final por celda | **Al menos 300 instancias válidas después de la adjudicación** (15% de 2.000); los 330 paquetes activos incluyen el margen de sobre-muestreo |
| Estratificación | Por `primary_pre_label` del pool |
| Orden de llenado | Dentro de cada conjunto, de la categoría más escasa a la más abundante, para que las escasas tengan prioridad sobre los cupos de cada artículo citante (orden registrado en `supply_round1.md`) |
| Límites | Máximo 2 instancias por `citing_id` en cada conjunto y máximo 1 por `group_key`, contando ambas rondas, la reserva y el colchón |

**Totales y distinción de unidades.** La cuota final es de **300 etiquetas humanas válidas por categoría y conjunto**: al menos 2.700 instancias por conjunto y 5.400 entre ambos. Se parte de 330 paquetes activos por celda, equivalentes a 2.970 por conjunto y **5.940 paquetes activos** en total. Cada paquete recibe dos anotaciones independientes, para **11.880 anotaciones previstas** antes de liberar reservas o colchones; cada grupo realiza 5.940 de esas anotaciones, repartidas entre su conjunto y el del otro grupo. Los 330 activos son candidatos con margen de sobre-muestreo, no una cuota de 330 etiquetas finales válidas.

**Congelamiento.** Cada ronda se calcula primero en vista previa y luego se escribe una sola vez (`WRITE = True`), con un manifiesto que guarda la semilla, los parámetros, el sha256 del pool y la versión del código. Toda ejecución posterior recalcula y **se detiene si algo difiere**, en lugar de sobrescribir: la semilla nunca se vuelve a sortear. Se publican la semilla y la asignación de los `citing_id` muestreados en la carpeta compartida.

### 6.3 Suministro y metas de entrenamiento

Instancias de alta confianza elegibles para test en el pool extendido (`counts_ext.md`, 22 de octubre de 2026). Los test sets necesitan 360 candidatos por mitad y categoría (más 30 para el colchón).

| Categoría | Alta confianza (test-eligible) | Ronda | Candidatos por mitad (A / B) | Meta de entrenamiento |
| --- | --- | --- | --- | --- |
| Background | 13.581 | 1 | holgado | ≥ 2.000 |
| Application | 4.959 | 1 | holgado | ≥ 2.000 |
| Comparison | 3.793 | 1 | holgado | ≥ 2.000 |
| Evidence | 3.605 | 1 | holgado | ≥ 2.000 |
| Gap | 3.463 | 1 | holgado | ≥ 2.000 |
| Basis | 3.206 | 2 | 1.627 / 1.579 | ≥ 2.000 |
| Identification of the Originator | 1.776 | 1 | 658 / 711 (en la ronda 1, sobre `pool_v11.1`) | ≈ 1.786 (todo el suministro) |
| Further Reading | 1.773 | 2 | 928 / 845 | ≈ 1.776 (todo el suministro) |
| Improvement/Modification | 956 | 2 | 481 / 475 | ≈ 958 (todo el suministro) |

**Metas de entrenamiento ajustadas (decisión del equipo, 22 de octubre de 2026).** Los test sets quedan cubiertos en las 9 categorías. Para la meta de ≥2.000 ejemplos de entrenamiento por categoría, el equipo trabaja con el suministro disponible: Further Reading, Identification of the Originator e Improvement/Modification quedan por debajo y su meta es el suministro del pool. Improvement/Modification no puede subirse con más corpus (≈9 hallazgos de frases clave por cada 10.000 pares en todas las subcategorías medidas). Extender el corpus a más subcategorías de cs queda como trabajo futuro, fuera de la entrega de este proyecto. Las cifras por grupo, después de su lista de exclusión, las da `exclusion_impact()` una vez muestreada la ronda 2 (Sección 10).

### 6.4 Reserva diferida (revisión del 23 de octubre de 2026)

La reserva existe para que cada celda termine con al menos 300 instancias válidas después de la adjudicación: un paquete cuya etiqueta final difiere de su pre-etiqueta pasa a otra celda, y uno marcado `unclear` sale del test set.

1. **Ola 1, solo informativa.** Tras la ola 1 de cada ronda se calcula, por celda, la tasa de corrección: anotaciones que cambian la pre-etiqueta ÷ anotaciones, contando a ambos anotadores, junto con los paquetes con al menos una corrección y con las dos. Se reporta (Sección 9); no decide la liberación.
2. **Conteo exacto.** Cuando todos los paquetes activos de la ronda están anotados y adjudicados, se cuenta por celda el número de instancias válidas según la etiqueta final (las que entran desde otras celdas cuentan; las `unclear` no).
3. **Déficit.** Para cada celda con menos de 300, se libera el déficit más un 10% de margen (redondeado hacia arriba): primero la reserva de esa celda en el orden de sorteo, después su segundo colchón en el mismo orden.
4. **Dónde se anotan.** Los paquetes liberados de la ronda 1 entran en la última ola de la ronda 2, mezclados con sus categorías; los de la ronda 2, en una ola final. Se anotan y adjudican como los demás, con las mismas reglas de asignación.
5. **Una pasada más, como máximo.** Si tras adjudicarlos una celda sigue por debajo de 300, se libera el déficit restante con la misma regla. Si el colchón se agota, la celda queda con lo disponible y su cuota menor se documenta: no se muestrea fuera del colchón, para que el conjunto de entrenamiento no cambie (Sección 10).
6. **Congelamiento.** Cada decisión de liberación se escribe una vez (archivo de liberación por ronda) y no se recalcula.

**Por qué se revisó la regla.** La regla anterior liberaba los 30 paquetes de toda celda cuya tasa de corrección en la ola 1 superara el 10%. La ola 1 cubre ≈37 paquetes por celda, así que la tasa estima con un margen amplio (≈±9 puntos) una pérdida que solo se conoce al final, y no cubre lo que ocurra en la ola 2. Contar al final libera solo lo necesario, cubre las 18 celdas y reduce las interrupciones para el equipo. La revisión cambia cuántos paquetes se anotan, nunca una etiqueta, así que no sesga los test sets. Con la regla anterior, la ola 1 habría liberado 3 celdas (Sección 9).

### 6.5 Reglas complementarias

- **Grupos:** como máximo un miembro por `group_key` entra al muestreo.
- **Reclasificación:** una instancia se asigna a la categoría de su etiqueta humana final, no a la de su pre-etiqueta.
- **`unclear`:** las instancias marcadas `unclear` tras la adjudicación no cuentan para la cuota y se excluyen del test set, pero se reportan.
- **Reserva y colchón no anotados:** siguen en la lista de exclusión de su grupo en la versión 1 del conjunto de entrenamiento (Sección 10).

## 7. Protocolo de anotación

La anotación tiene **dos pasadas independientes** por paquete, una de un integrante del grupo 14 y otra de uno del grupo 15. Cada anotador trabaja **en un solo paso**: ve el paquete con la pre-etiqueta de A2 y la **confirma o la corrige** (Guía rev3.1, Sección 2). Ningún anotador ve la etiqueta del otro, y los paquetes no se comentan hasta cerrar la anotación (las preguntas sobre las reglas sí; los ejemplos del propio lote, no).

En cada conjunto, la anotación del grupo dueño es su validación (A3), y la del otro grupo es la segunda anotación independiente que exige la propuesta para el acuerdo interanotador.

**Relación con A3.** La propuesta pide "confirmar o corregir la etiqueta sugerida por los modelos" y que las muestras del test set se anoten "de forma independiente por al menos dos evaluadores". Ambos requisitos se cumplen: la pre-etiqueta se muestra para confirmarla o corregirla, y los dos anotadores de cada paquete trabajan sin ver la etiqueta del otro. Como ambos ven la misma pre-etiqueta, el κ se reporta junto con la tasa de corrección (Sección 9).

**Lotes y asignación:**

| | Ronda 1 | Ronda 2 |
| --- | --- | --- |
| Paquetes activos | 3.960 (6 categorías × 2 conjuntos × 330) | 1.980 (3 × 2 × 330) |
| Lotes de 55 | 72 (36 por conjunto), `L001`–`L072` | 36 (18 por conjunto), desde `L073` |
| Composición | 9 o 10 paquetes de cada categoría por lote | 18 o 19 de cada categoría por lote, más los paquetes de reserva liberados de la ronda 1 en su última ola |
| Por persona | 18 lotes (990 paquetes): 9 del propio conjunto y 9 del otro; cada pareja G14–G15 comparte 4 o 5 lotes | ≈9 lotes (≈495 paquetes) |

1. Cada lote de T\_A se asigna a un anotador del grupo 14 (validación) y a uno del grupo 15 (segunda anotación); cada lote de T\_B, al revés. La asignación equilibra la carga por conjunto y los lotes compartidos por pareja.
2. Ningún anotador recibe dos veces el mismo paquete, y ningún lote contiene dos paquetes del mismo artículo citante.
3. Los miembros de un mismo grupo de citas nunca se anotan (solo uno entra al muestreo; Sección 6).

**Olas de la ronda 1:**

| Ola | Lotes | Compartida | Cierre |
| --- | --- | --- | --- |
| 1 | 8 (4 por conjunto): `L002`, `L003`, `L026`, `L028`, `L044`, `L054`, `L061`, `L064`; dos por persona, una de cada conjunto | 18 oct 2026 | Completa el 21 oct 2026 (880 de 880 anotaciones) |
| 2 | Los 64 restantes, 16 por persona; no depende de la ola 1 | 19 oct 2026 | Meta conjunta: domingo 25 oct 2026, al finalizar el día |

Las olas de la ronda 2 se fijan al muestrearla; su última ola lleva los paquetes de reserva de la ronda 1 que se liberen (Sección 6.4).

**Calibración (antes de la anotación completa):**

| Ronda | Material | Resultado |
| --- | --- | --- |
| eval60 | 60 instancias, con la Guía rev1.4, una anotación por grupo | κ de Cohen 0,63 entre grupos; sirvió para escribir las reglas rev1.1 y adjudicar una referencia |
| calib30 | 30 instancias de alta confianza (≥3 por categoría), con las reglas rev1.1, sesión grupal | κ de Fleiss 0,47 (7 anotadores); solo 3 de 21 parejas ≥ 0,6. Los desacuerdos caen en las fronteras de las reglas |
| calib2 | 27 instancias de alta confianza (3 por categoría, ≤1 por artículo citante, `K01`–`K27`), extraídas el 17 de octubre; Guía rev3.1, pre-etiqueta visible, anotación individual en la misma hoja de A3 | **Superada el 18 de octubre de 2026** (8 de 8 anotadores): **16 de 16 parejas G14–G15 con κ de Cohen ≥ 0,6** (mínimo 0,71, media 0,87); κ de Fleiss 0,87. 16 de 216 respuestas corrigieron la pre-etiqueta (0–22% por persona); tres ítems (K04, K23, K13) concentran 11 de las 16, en las fronteras de las reglas 10, 3 y 5, y 4. Tiempo: 1,1–2,8 min por paquete, mediana 1,7 (5 de 8 registraron minutos) |

Los artículos citantes de las tres rondas quedan fuera de ambos test sets (`test_eligible: false`) y del entrenamiento y la validación de ambos grupos (Sección 10).

**Herramienta: hojas nativas de Google Sheets** (decidido el 17 de octubre de 2026). Cada anotador recibe una hoja por lote en su carpeta de Drive. La hoja trae los paquetes precargados; solo son editables las columnas de anotación (etiqueta principal, hasta dos secundarias, confianza, notas), con listas desplegables que solo admiten las etiquetas válidas; los chunks van en un grupo de columnas plegado. Los colores marcan lo pendiente, las correcciones (piden una nota) y lo que hay que arreglar (`unclear` sin nota, secundaria repetida). La pestaña «Resumen» registra los minutos dedicados al lote. Las hojas se generan, se comparten y se recogen con el notebook de A3 (`a3_annotation_sheets_colab.ipynb`, módulo `a3_sheets.py`), que produce los registros de la Sección 8. La Hoja de instrucciones (resumen de la guía) está en la carpeta compartida del equipo, junto con la Guía y este contrato, para tenerla abierta al lado del lote.

**Carpetas y acceso.** La raíz de A3 en Drive (`MAIA_PROYECTO_A3`) es privada y está fuera de la carpeta compartida del proyecto, porque en Drive el acceso se hereda: cualquiera con acceso a la carpeta del proyecto vería todas las hojas. Dentro de ella, cada `anotadores/<id>/` se comparte solo con su anotador (lectura), y cada hoja de lote con su anotador en modo edición; `_admin/` (muestras, asignación, anotaciones recogidas) no se comparte. El responsable del muestreo tiene acceso a todas las hojas: no abre las de otros anotadores antes de cerrar la anotación, y el notebook solo muestra conteos y agregados durante la anotación.

## 8. Registro de anotación

Cada anotación produce un registro con los campos siguientes (mismo formato que la Guía rev3.1, Sección 7).

| Campo | Tipo y valores permitidos | Lo llena |
| --- | --- | --- |
| `package_id` | `P0001`… (ronda 1: `P0001`–`P4320`; ronda 2 desde `P4321`; en calib2, `K01`…`K27`) | Precargado |
| `annotator_id` | Seudónimo, `G14-01`…`G14-04`, `G15-01`…`G15-04` | Precargado |
| `pre_label` | `primary_pre_label` de A2 | Precargado (visible) |
| `primary_label` | La pre-etiqueta si se confirma, o la etiqueta corregida: una de las 9 etiquetas o `unclear` (obligatorio). La hoja la deja vacía al inicio | Anotador |
| `secondary_labels` | Cero o más de las 9 etiquetas, distintas de la principal; solo en casos extremos (la hoja admite hasta dos) | Anotador |
| `confidence` | `Alta` / `Media` / `Baja` (se pide en todas las filas; si falta, es un aviso, no bloquea) | Anotador |
| `notes` | Texto libre; obligatorio si `primary_label = unclear`; recomendado al corregir, con la regla aplicada | Anotador |
| `guide_version` | Versión de la Guía vigente: `rev3.1` | Precargado |

**Campos calculados después, no por el anotador:** `pre_label_decision` (`confirma` / `corrige`), `inst_key`, `group_key`, `source_team`, `test_set` (`A` o `B`), `role` (`validacion` si el anotador pertenece al grupo dueño del conjunto, `segunda` si pertenece al otro grupo), y la ronda y la ola del lote. Se unen por `package_id` y `annotator_id` a partir de la asignación. Las anotaciones recogidas se guardan por lote en `_admin/annotations/`.

**Uso de la confianza.** La confianza no entra en el κ, ni en la etiqueta de referencia, ni en la liberación de la reserva. Se usa para ordenar y agilizar la adjudicación (Sección 9) y para el análisis del informe (acuerdo por nivel de confianza).

## 9. Acuerdo interanotador y adjudicación

La meta, fijada en el objetivo específico 2, es **κ de Cohen ≥ 0,6** sobre `primary_label`, calculado sobre las anotaciones **previas** a la adjudicación. Cada grupo reporta el κ de su propio conjunto (T\_A o T\_B), que es el que cuenta para su objetivo.

**Cálculo:**

- Los dos "evaluadores" del κ de Cohen son la anotación del grupo 14 y la del grupo 15 de cada paquete. Como cada rol lo ocupan varias personas, también se reporta el **α de Krippendorff** (nominal), que no supone evaluadores fijos.
- Se reporta el κ **global** y el κ **por categoría** (una contra el resto), para alimentar las matrices de confusión de A6.
- Como análisis complementario del esquema multi-etiqueta, se reporta el α de Krippendorff con distancia MASI sobre el conjunto principal + secundarias.
- Se reporta la **tasa de corrección** de la pre-etiqueta de A2 por categoría, por conjunto y **por anotador**, y el acuerdo entre la etiqueta final (*gold*) y la pre-etiqueta. Como ambos anotadores ven la misma pre-etiqueta, el informe lo dice explícitamente: una tasa de corrección cercana a cero en una categoría indicaría confirmación automática más que revisión; la tasa por anotador permite descartarlo.
- Un solo responsable calcula todas las métricas con un script compartido y versionado, para que ambos grupos reporten las mismas cifras.

**Primer resultado: ola 1 de la ronda 1** (880 anotaciones, 440 paquetes, 21 de octubre de 2026). 71 anotaciones cambiaron la pre-etiqueta (**8,1%**; calib2: 7,4%). 58 paquetes (13,2%) tienen al menos una corrección y 13 (3,0%) las dos. Por celda, la tasa va de 0,0% (A·Evidence) a 22,2% (B·Gap); superan el 10% A·Background (12,2%), A·Comparison (19,4%) y B·Gap (22,2%). Tiempo: 1.409 minutos para 880 anotaciones, **1,60 min por paquete** (1,0–2,75 por persona). La tabla completa por celda va en `iaa_report.md`.

**Adjudicación:**

1. Si las `primary_label` de ambos anotadores coinciden, esa es la etiqueta de referencia (*gold*).
2. Si difieren, decide una **pareja adjudicadora asignada por paquete**, después de recoger las anotaciones: un integrante de cada grupo, que no sea ninguno de los dos anotadores de ese paquete ni el responsable del muestreo. La carga se reparte de forma equilibrada entre las personas elegibles. La pareja decide en sesión conjunta, con la Guía vigente como criterio.
3. La pareja ve el paquete, las dos etiquetas, las dos confianzas y las notas. La confianza ordena la cola (primero los casos con una corrección de confianza alta y una nota con la regla) pero **nunca decide**: es autodeclarada y no está calibrada.
4. Si la pareja no llega a acuerdo, la instancia se marca `unclear` y sale del test set.
5. Toda instancia marcada `unclear` por cualquiera de los anotadores pasa por adjudicación, aunque las etiquetas coincidan.
6. Se conservan las anotaciones originales y la adjudicada; nunca se sobrescriben.

**Carga esperada.** En la ola 1, 45–58 de 440 paquetes (10–13%) necesitarían adjudicación: los 45 con una sola corrección y, de los 13 con dos, los que difieren en la etiqueta nueva. Extrapolado, ≈400–520 adjudicaciones en la ronda 1. En la ronda 2 se espera una tasa mayor (solo el 42% de las nuevas pre-etiquetas de Improvement/Modification son unánimes).

## 10. Entregables, aislamiento y lista de exclusión

El cierre produce tres archivos, versionados en la carpeta compartida. Ninguno contiene texto de unarXive, solo claves y etiquetas.

| Archivo | Contenido |
| --- | --- |
| `test_set_gold.jsonl` | Por instancia: conjunto (T\_A o T\_B), ronda, `inst_key`, `group_key`, `gold_primary`, `gold_secondary`, las dos anotaciones originales, la adjudicada (si aplica) y la pre-etiqueta de A2 |
| `exclusion_list.jsonl` | Por grupo, lo que ese grupo no puede usar en entrenamiento o validación (ver abajo) |
| `iaa_report.md` | κ de Cohen y α de Krippendorff (por conjunto y ronda; global y por categoría), tasa de corrección por celda y por anotador, resultado de la ola 1, liberaciones de reserva, número de instancias `unclear` y cambios de la Guía |

**El requisito de la propuesta.** La Sección 3 de la propuesta (*Aislamiento por Documento*) dice: "Si un par de artículos (citante – citado) es asignado al conjunto de entrenamiento, ningún contexto de cita ni fragmento asociado a ese par podrá formar parte de los conjuntos de prueba o validación". El equipo lo lee así: ningún contexto ni fragmento (*chunk*) de **ninguno de los dos documentos** del par puede estar a la vez en entrenamiento y en prueba o validación. El aislamiento tiene entonces dos lados: el citante y el citado.

**Variantes medidas:**

| Variante | Se quita del entrenamiento y la validación de un grupo | Qué cubre |
| --- | --- | --- |
| **V0 (piso común)** | Toda fila cuyo `citing_id` esté en su propio test set | Lado citante; implica el aislamiento por par |
| **V1** | V0 + toda fila cuyo `cited_id` sea el artículo citado de una instancia de su propio test set | Ambos documentos del par |
| **V2** | V1 + las filas donde un artículo del test set aparece en el otro papel (citado en el test, citante en el entrenamiento, o al revés) | La lectura más estricta de "documento" |

**Regla:**

- **V0 es obligatoria para ambos grupos.** Cada grupo puede adoptar V1 o V2 para su propio entrenamiento. Las dos decisiones no interactúan, porque cada grupo excluye solo lo de su propio test set.
- `exclusion_impact()` mide las tres variantes por grupo y categoría (filas perdidas, entrenamiento + test propio frente a la meta, artículos citados quitados y concentración de la pérdida) sobre las muestras de ambas rondas en el pool extendido. Cada grupo elige su variante con esa tabla **antes de construir sus particiones** y reporta la variante elegida y la tabla.
- Si un grupo se queda en V0, reporta el F1 Macro del test set por separado para instancias cuyo `cited_id` aparece en su entrenamiento y para las que no. Con V1 o V2 no hace falta: todo artículo citado del test es nuevo para el modelo.
- **Partición entrenamiento/validación de A4.** Se agrupa por `citing_id`, y con V1 o V2 se aplica entre entrenamiento y validación la misma regla que entre entrenamiento y prueba. La propuesta exige el aislamiento también para la validación.

**Lista de exclusión de cada grupo (V0):**

- todos los `citing_id` de los paquetes de su propio conjunto, en ambas rondas: activos, de reserva y del segundo colchón, se anoten o no. Ninguna fila de esos artículos (contexto, par o grupo) puede estar en su entrenamiento o validación;
- los `citing_id` de las tres calibraciones (abajo).

Con V1 o V2, la lista añade los `cited_id` correspondientes.

**Excluidos para ambos grupos:** todos los artículos citantes presentes en cualquiera de las calibraciones (eval60, calib30 y calib2) quedan excluidos del entrenamiento, la validación y la prueba de A4 para ambos grupos. La exclusión abarca todas las instancias de esos artículos, no solo las seleccionadas para calibración. Sus `citing_id` se incorporan a las listas de exclusión de ambos grupos. Las calibraciones se reportan únicamente como datos de desarrollo de la guía y del prompt.

**Uso del test set del otro grupo.** Como los conjuntos no comparten artículos citantes (Sección 6), **cada grupo puede usar el test set del otro en su entrenamiento**: primero con las pre-etiquetas de A2 y, cuando termine la adjudicación, con las etiquetas humanas finales (`gold_primary`). Cambiar la etiqueta no cambia las filas.

**Ningún paquete anotado se desperdicia:** cada paquete es una instancia de prueba para el grupo dueño de su conjunto y una instancia de entrenamiento con etiqueta humana para el otro. Solo salen los adjudicados como `unclear`, que se reportan.

**Versiones del conjunto de entrenamiento.** La lista de exclusión se construye cuando la ronda 2 está muestreada (incluidos su reserva y su colchón), y con ella la **versión 1** del conjunto de entrenamiento de cada grupo: el pool extendido menos su lista. A4 puede empezar con la versión 1 mientras sigue la anotación, porque ninguna liberación de reserva la cambia: los paquetes liberables ya están excluidos. Reincorporar los artículos de reserva y colchón no usados es opcional, se hace como una **versión 2** explícita, solo si una categoría escasa lo necesita, y se reporta.

**Compromiso:** cada grupo elimina de su dataset de entrenamiento y validación toda fila que coincida con su lista, **antes** de construir sus particiones, y reporta cuántas filas eliminó. Referencia de la ronda 1 (V0, peor caso con reserva): el grupo 14 excluye 2.038 y el grupo 15 2.022 de los 19.741 artículos citantes de `pool_v11.1`; las cinco categorías holgadas quedan con ≥2.833 por grupo.

## 11. Cronograma y responsables

| Fecha | Hito | Estado |
| --- | --- | --- |
| 15 oct 2026 | A2: pool completo re-etiquetado con la configuración congelada; pool combinado y conteos | Hecho |
| 17 oct 2026 | Semilla fijada; segunda calibración (calib2, 27 instancias) extraída y excluida del pool | Hecho |
| 18 oct 2026 | calib2 anotada y superada (16 de 16 parejas ≥ 0,6); ronda 1 muestreada y congelada; ola 1 compartida | Hecho |
| 19 oct 2026 | Ola 2 de la ronda 1 compartida (64 lotes) | Hecho |
| 21 oct 2026 | Ola 1 completa (880 anotaciones); tasa de corrección calculada | Hecho |
| 22 oct 2026 | Pool extendido (`pool_v11.1_ext`, 38.780 instancias); vista previa de la ronda 2 completa; metas de entrenamiento ajustadas | Hecho |
| 23 oct 2026 | Reserva diferida (Sección 6.4); este contrato y la Guía actualizados | Hecho |
| \[fecha\] | Colchón de la ronda 1; ronda 2 muestreada y congelada; lista de exclusión y conjunto de entrenamiento v1. **A4 puede empezar** | Pendiente |
| 25 oct 2026, al finalizar el día | Meta conjunta de cierre de la validación manual de la primera ronda | En curso |
| \[fecha\] | Adjudicación de la ronda 1; conteo exacto por celda | Pendiente |
| 26 oct 2026 (inicio) | Inicio de la anotación de la segunda ronda; su última ola incluirá la reserva liberada de la ronda 1 | Programado; cierre de la ronda por confirmar |
| \[fecha\] | Adjudicación de la ronda 2 y liberación final, si hace falta | Pendiente |
| \[fecha\] | Métricas de acuerdo; entregables publicados (Sección 10) | Pendiente |

**Seguimiento al corte del 23 de octubre de 2026.** La validación manual de la primera ronda presenta un avance estimado del **60–70 %**, según la actualización comunicada por el equipo. La meta de ambos grupos es finalizarla el domingo 25 de octubre, al cierre del día, para iniciar la segunda ronda el lunes 26 de octubre. La adjudicación, las reposiciones y la consolidación final continúan pendientes. Esta actualización del calendario fue aclarada por el equipo el 24 de octubre para alinear el contrato y el informe.

**Equipos:** 4 integrantes por grupo (8 en total), todos anotadores, con seudónimos `G14-01`…`G14-04` y `G15-01`…`G15-04`. **Roles:** responsable del muestreo y los paquetes: Ale (dueña de la carpeta de A3 en Drive). Pareja adjudicadora: por paquete (Sección 9). **Por asignar:** responsable de las métricas.

**Carga de anotación.** Medida: 1,60 min por paquete en la ola 1 (calib2: 1,7). Por persona: ronda 1, 990 paquetes ≈ 26 h; ronda 2, ≈495 paquetes ≈ 13 h; más la reserva liberada y las adjudicaciones que le toquen.

**Riesgos:**

- **Adjudicación:** ≈400–520 paquetes en la ronda 1 y más en la ronda 2; depende de que las parejas por paquete se asignen y se reúnan a tiempo.
- **Ronda 2:** Improvement/Modification tiene solo un 42% de pre-etiquetas unánimes y Basis vs. Application es el principal desacuerdo de los jueces; se espera una tasa de corrección mayor. La reserva diferida y el colchón lo cubren; si no alcanzan, la cuota menor se documenta.

## 12. Versionamiento, gobernanza y decisiones

**Versionamiento.** La Guía de Anotación y este contrato son documentos compartidos con un único historial de versiones. Todo cambio se pone a revisión de ambos grupos y se registra en el registro de cambios. Las reglas de la Guía rev3.1 están congeladas hasta cerrar la fase; se pueden actualizar sus secciones de estado sin cambiar la versión, como el 23 de octubre. Cada registro guarda la versión vigente (`guide_version`). La configuración de A2 (Sección 4) está congelada: cambiar el prompt o los jueces exige re-etiquetar todo el pool.

**Gobernanza.** Los dos test sets, la lista de exclusión y el reporte de acuerdo son productos conjuntos. Cualquier publicación o informe que los use reconoce a ambos grupos. Cada grupo evalúa sus modelos sobre su propio test set y puede usar el del otro grupo en su entrenamiento, sin pedir autorización adicional.

**Decisiones:**

- [x] P1, P2, P3 (Sección 2).
- [x] Margen de sobre-muestreo: 330 activos + 30 de reserva por celda, más un segundo colchón de 30 (Sección 6).
- [x] Marcado de la cita objetivo y de otros marcadores en los paquetes, en inglés como en el prompt de A2 (Sección 5).
- [x] Requisitos de elegibilidad de los candidatos (Sección 4).
- [x] Reglas del pool: los mismos tres jueces y la misma versión de prompt para todo el pool, incluida la extensión; voto solo sobre la etiqueta principal; configuración congelada (Sección 4).
- [x] A2 corrido en conjunto (`source_team = "G14+G15"`); sin reparto de lotes entre grupos.
- [x] Anotación en un paso, con la pre-etiqueta visible (Secciones 5, 7 y 8).
- [x] Calibraciones fuera de A4 y de ambos test sets, con todos sus artículos citantes (Secciones 4 y 10).
- [x] Reglas rev1.1 de la Guía rev3.1, con R-FR "primero el contenido"; respuestas de los ejemplos resueltos aceptadas.
- [x] Una etiqueta principal; secundarias solo en casos extremos.
- [x] Sin submuestra sin pre-etiqueta (17 oct 2026); el efecto se reporta con la tasa de corrección (Sección 9).
- [x] Sin marca `group_inconsistency`; en los test sets entra un solo miembro por grupo (Secciones 6 y 9).
- [x] Segunda calibración: 27 instancias, 3 por categoría; superada el 18 oct 2026 (Sección 7).
- [x] Herramienta de anotación: hojas nativas de Google Sheets, una por anotador y lote, en una raíz de Drive privada (Sección 7).
- [x] Semilla `LCR-G14G15-A3-2026`; T\_A = grupo 14, T\_B = grupo 15 (Sección 6).
- [x] Dos rondas; extensión del corpus a cs.LG, cs.CL y cs.DS para la ronda 2 (Secciones 4 y 6).
- [x] Metas de entrenamiento ajustadas al suministro para Further Reading, Identification of the Originator e Improvement/Modification; más extensión, trabajo futuro (Sección 6.3).
- [x] Reserva diferida: liberación por déficit tras la adjudicación, en la última ola de la ronda 2 (Sección 6.4).
- [x] Adjudicación por parejas asignadas por paquete; la confianza ordena, no decide (Sección 9).
- [x] Aislamiento: V0 obligatoria; V1 o V2 a elección de cada grupo, con la medición de impacto; partición entrenamiento/validación agrupada (Sección 10).
- [ ] Variante de exclusión de cada grupo (V0, V1 o V2), antes de construir sus particiones.
- [ ] Responsable de métricas.
- [ ] Fechas pendientes (Sección 11).

**Puntos adicionales:**

- **Demo:** 521 instancias del pool extendido tienen ambos artículos con licencia permisiva (`demo_eligible`); en Identification of the Originator (11) e Improvement/Modification (16) son muy pocas.

**Registro de cambios.**

- **24 oct 2026 (alineación del calendario con el informe):** Secciones 6.1, 7 y 11: se incorpora el avance estimado del 60–70 % en la validación manual de la primera ronda al corte del 23 de octubre; se actualiza la meta conjunta de cierre al domingo 25 de octubre, al finalizar el día, y se registra el inicio previsto de la segunda ronda el lunes 26. Las fechas de adjudicación y cierre final de la segunda ronda permanecen por confirmar; no se presenta ninguna meta como actividad terminada.
- **24 oct 2026 (conteos y cuotas validados):** Sección 4: `pool_cue_yield` se corrige a 885 instancias; se retira la conciliación basada en 900 y se distingue la muestra que ya excluye eval60 de los conteos directos del pool. Se incorporan los estados de ambos pools, los 6 casos restantes por otros motivos, las cifras de elegibilidad y la transición anterior/posterior al retiro de calib2. Sección 6.2: se confirma la cuota final de 300 instancias válidas por celda y su diferencia frente a los 330 paquetes activos; se explicitan los totales de 5.940 paquetes activos y 11.880 anotaciones previstas. Los totales de los archivos congelados y sus hashes se mantienen; no se modifican los archivos de datos ni las reglas de anotación. El Excel se utiliza como referencia auxiliar de las mediciones; este contrato es el documento rector del procedimiento y las cuotas.
- **23 oct 2026 (el contrato alcanza al proceso):** incorpora la observación de la revisión del equipo: las calibraciones excluyen del entrenamiento, la validación y la prueba a todas las instancias de sus artículos citantes, para ambos grupos, como ya decía la Sección 7 (Secciones 4 y 10). Estado: puesto a revisión, sin objeciones, aplicado desde calib2. Sección 2: P2 con la extensión del corpus. Sección 4: dos versiones del pool, `pool_v11.1` congelado y `pool_v11.1_ext`; archivos del pool; las cifras del pool (29.336); exclusión de idioma. Sección 5: `package_id` aleatorio sobre ambos conjuntos; nombres de lote neutros. Sección 6 reescrita: rondas, flujos de la semilla, orden de sorteo, orden de llenado, límites sobre ambas rondas, congelamiento, suministro del pool extendido, metas de entrenamiento ajustadas, **reserva diferida** (reemplaza la liberación por tasa > 10% en la ola 1) y segundo colchón; la reserva no usada sigue excluida en la versión 1 del entrenamiento. Sección 7: lotes, asignación y olas reales; resultado de calib2. Sección 8: rangos de `package_id`, campos calculados, uso de la confianza. Sección 9: tasa de corrección por anotador; resultado de la ola 1; adjudicación por parejas asignadas por paquete; carga esperada. Sección 10: requisito de la propuesta leído sobre ambos documentos del par; variantes V0, V1 y V2 y elección por grupo; partición entrenamiento/validación agrupada; lista de exclusión con reserva y colchón de ambas rondas; versiones del conjunto de entrenamiento; se elimina la opción de relajar el aislamiento a nivel de par. Sección 11: hitos cumplidos, carga medida, riesgos. Sección 12: decisiones actualizadas; sin intervención del director en las cuotas.
- **17 oct 2026 (semilla y dueños):** semilla `LCR-G14G15-A3-2026`, con flujos derivados para partición, muestreo y mezcla de lotes; T\_A = grupo 14, T\_B = grupo 15 (Secciones 6 y 12).
- **17 oct 2026 (alineación con la Guía rev3.1):** referencias a la Guía actualizadas a rev3.1. Sección 5: marcadores en inglés (`[[TARGET]]`, `[CITE]`, `[FORMULA]`, `[FIGURE]`, `[TABLE]`), los mismos del prompt de A2; paquetes entregados en hojas privadas de Drive. Sección 4: calib2 entre los conjuntos de calibración. Sección 6: nota sobre el efecto de excluir calib2 en el suministro. Sección 7: segunda calibración de 27 instancias (3 por categoría), extraída el 17 de octubre y anotada en la misma hoja que A3; herramienta decidida (Google Sheets nativas) y estructura de carpetas y acceso. Sección 8: seudónimos, `K01`…`K27` para calib2, `guide_version` = `rev3.1`, secundarias solo en casos extremos. Sección 11: hito de calib2, rol de responsable del muestreo asignado. Sección 12: decisiones de marcadores, una etiqueta principal, segunda calibración y herramienta.
- **17 oct 2026:** acompaña a la Guía rev3. Sin submuestra sin pre-etiqueta: todos los paquetes muestran la pre-etiqueta; se elimina `pre_label_shown` (Secciones 7, 8 y 9). Sin campo `group_inconsistency` ni revisión de inconsistencias dentro de un grupo de citas; solo `unclear` fuerza la adjudicación cuando las etiquetas coinciden (Secciones 8 y 9). Referencias a la Guía actualizadas a rev3.
- **15 oct 2026:** grupos 8 y 13 → **grupos 14 y 15** (equipo extendido). Sección 4: configuración congelada de A2 (prompt v11.1, Qwen3.8-27B + Gemma 4 26B-A4B + Mistral Small 3.2 24B con revisiones fijas), A2 corrido en conjunto (`source_team = "G14+G15"`), nuevos campos del pool, pool sin texto. Sección 2: indicación del director sobre licencias. Secciones 5, 7, 8 y 9: **anotación en un paso**, pre-etiqueta visible para confirmar o corregir; se eliminan el paso 1 ciego y `reviewed_label`; el κ se calcula sobre `primary_label` y se reporta con la tasa de corrección; chunks plegados. Sección 6: suministro del pool por categoría; muestreo solo de instancias `test_eligible`; reserva liberada tras los primeros lotes; categorías escasas según los conteos. Sección 7: calibraciones eval60 y calib30; segunda calibración como condición para iniciar A3. Sección 10: calibraciones excluidas de A4 y de ambos test sets. Sección 11: fechas por reprogramar; carga por persona. Referencias a la Guía actualizadas a rev2.
- **2 oct 2026:** en caso de empate entre jueces no hay pre-etiqueta (antes: la categoría más escasa del pool, que amplificaba al juez más ruidoso), y no vota el juez cuya cita de evidencia no proviene del párrafo citante (Sección 4). Prompt de A2 congelado en v8; referencias a la Guía actualizadas a rev1.4.
- **30 sep 2026:** dos test sets disjuntos, uno por equipo, por la condición del director; la segunda anotación de cada conjunto la aporta el otro equipo. Cada equipo excluye solo los artículos citantes de su propio test set y puede entrenar con el test set del otro; se elimina el benchmark común. Un único pool compartido (pipeline de A1 y A2 común); los dos test sets se muestrean de mitades del pool asignadas por artículo citante. Anotación en dos pasos (reemplazada el 15 oct). Referencias a la Guía actualizadas a rev1.2.
