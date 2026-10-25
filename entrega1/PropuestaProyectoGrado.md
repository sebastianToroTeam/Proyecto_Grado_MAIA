# Título del Proyecto

Recuperación semántica y clasificación de la función de citas académicas en Computer Science.

---

# Integrantes del equipo

Camilo Bejarano, German Rodriguez, Jose Arteaga, Sebastian Toro

---

# Organización y grupo de investigación

- **Organización:** Grupo de investigación FLAG — TICSW, Departamento de Ingeniería de Sistemas y Computación, Universidad de los Andes.
- **Experto de dominio:** PhD. Juan Camilo Sanguino — Machine Learning Engineer and AI Researcher, Departamento de Ingeniería de Sistemas y Computación, Universidad de los Andes.
- **Rol de acompañamiento:** asesoramiento en la definición del problema a resolver, estrategia del proyecto, y resultados esperados; orientación en la bibliografía, validación de las decisiones clave.

---

# 1. Contexto, problema e impacto

## 1.1 Situación actual

La producción científica ha alcanzado una escala en la que su seguimiento manual es inviable: solo arXiv supera los **3,1 millones de artículos** acumulados, con decenas de miles de trabajos nuevos cada mes, y Computer Science es uno de los subdominios de mayor volumen. En este escenario, la cita —el mecanismo con que un artículo se conecta con el conocimiento previo— es el principal instrumento para organizar, evaluar y navegar la literatura.

Sin embargo, la práctica dominante en bibliometría reduce la cita a un **conteo**, tratando por igual una que sienta la base metodológica de un estudio, una que solo lo menciona como lectura complementaria y una que lo critica. Esta homogeneización es una simplificación problemática, pues las prácticas de citación dependen del campo y del propósito del autor, y los conteos no capturan *por qué* se cita. La información que distingue esos matices —la **función retórica** de la cita— está latente en el texto, distribuida en secciones y párrafos específicos del artículo citado.

El Procesamiento del Lenguaje Natural (PLN) aplicado a textos académicos ofrece herramientas para recuperar esa señal mediante dos tareas complementarias: la **recomendación local de citas** (*Local Citation Recommendation*), que alinea un contexto de cita con los fragmentos relevantes del artículo citado, y la **clasificación de la función de la cita** (*Citation Function Classification*), que caracteriza su propósito discursivo. Pese a la abundancia de corpus en inglés, identificar con precisión la función de una cita sigue siendo un reto abierto.

## 1.2 Problema

Hoy no existe una forma automática y confiable de determinar **qué función cumple una cita** dentro de un artículo científico. Este problema es particularmente relevante en el área de Computer Science, que constituye el dominio de estudio de este proyecto. Los enfoques predominantes tratan todas las citas como equivalentes, y la señal que distingue su propósito no está en el marcador de cita, sino dispersa en el cuerpo del artículo citado, lo que hace la tarea ambigua y difícil de automatizar. Este problema general se descompone en tres sub-problemas:

- **Ausencia de datos:** no se dispone de un dataset balanceado y documentado de funciones de cita en Computer Science que permita entrenar y evaluar modelos.
- **Evidencia dispersa en el artículo citado:** dado un contexto de cita, la información que justifica su función se encuentra distribuida en secciones y párrafos específicos del artículo citado, sin estar localizada ni alineada con dicho contexto.
- **Ambigüedad de la clasificación:** una cita puede cumplir más de una función y los contextos suelen ser breves; el proyecto se enfoca en asignar la **función más relevante** (una sola clase), lo que exige criterios consistentes para resolver los casos ambiguos.

## 1.3 Impacto esperado

- **Recurso de datos:** un dataset trazable de funciones de cita en Computer Science, balanceado entre las clases frecuentes y con mínimos diferenciados documentados para las minoritarias, con guía de anotación y métricas de acuerdo interanotador, transferible a la comunidad.
- **Evidencia metodológica:** medición cuantificada del aporte de los fragmentos recuperados del artículo citado a la clasificación, comparando un clasificador supervisado y un modelo de lenguaje.
- **Aplicaciones:** apoyo a revisiones sistemáticas de literatura, análisis de impacto bibliométrico cualitativo, verificación de afirmaciones y herramientas de escritura académica asistida.

---

# 2. Objetivos

## 2.1 Objetivo General

Desarrollar un sistema de PLN que, para un contexto de cita en inglés dentro del área de Computer Science, recupere los fragmentos más relevantes del artículo citado y clasifique la función de la cita en una de nueve categorías —*Background, Gap, Basis, Comparison, Application, Improvement/Modification, Evidence, Identification of the Originator y Further Reading*—, evaluando el aporte de los fragmentos recuperados al desempeño de la clasificación.

## 2.2 Objetivos Específicos

1. **Obtener un corpus** de artículos de Computer Science desde unarXive, con contextos de cita identificados, artículos citados resolubles y fragmentos segmentados por oración y sección.
2. **Construir un dataset trazable** de funciones de cita en Computer Science sobre citas *auto-referenciales* (el artículo citado está presente en el corpus con su texto completo), con una meta de 2.000 ejemplos por categoría en las clases frecuentes y mínimos diferenciados documentados para las clases minoritarias, guía de anotación, etiquetado inicial del conjunto completo mediante un esquema de jueces de pesos abiertos con voto por mayoría, validación humana del 15% y acuerdo interanotador.
3. **Implementar la recuperación densa** basada en SciBERT para obtener, por cada contexto de cita, el Top-3 de fragmentos más similares del artículo citado y su sección retórica.
4. **Comparar el desempeño de un clasificador supervisado** basado en SciBERT bajo dos configuraciones de entrada —contexto de cita con el título y el abstract del artículo citado, frente a contexto de cita con la sección retórica y los fragmentos Top-3 recuperados—, y contrastarlo con un modelo de lenguaje comercial de frontera, para determinar si los fragmentos recuperados mejoran la clasificación de la función de cita.
5. **Desplegar una aplicación web** que integre la recuperación del Top-3 y la clasificación de la función de cita, permita seleccionar el clasificador entre un encoder fine-tuned (SciBERT), un modelo open-weight (1–8B) y un modelo comercial vía API, y visualice la función con su confianza.

---

# 3. Estado del arte

El análisis de citas ha migrado del **conteo** hacia la **caracterización semántica** de por qué se cita. Esta sección revisa cuatro frentes —taxonomías de función de cita, conjuntos de datos anotados, métodos de clasificación y recuperación, y el uso emergente de modelos de lenguaje— y contrasta sus límites con la propuesta del grupo.

**Taxonomías de función de cita.** Lyu, Ruan, Xie y Cheng (2021) realizaron una meta-síntesis de 38 estudios y consolidaron 35 conceptos en 13 temas, agrupados en *motivaciones científicas* (retóricas y detectables en el texto) y *motivaciones tácticas* (sociales, no capturables por análisis textual). Su límite es que es un marco conceptual, no un recurso computable: no provee datos etiquetados ni modelos. La propuesta operacionaliza su rama de motivaciones científicas en las **nueve funciones** definidas y construye el dataset que la meta-síntesis no ofrece.

**Conjuntos de datos anotados.** Cohan et al. (2019) introdujeron *SciCite*, un dataset multidominio de intención de cita reducido a **tres clases** (*background, method, result*), insuficientes para el matiz de nueve funciones. Lauscher et al. (2022) propusieron *MultiCite*, aportando dos ideas que este proyecto adopta: los contextos pueden abarcar **varias oraciones** y expresar **varias intenciones a la vez** (multietiqueta); su límite es el dominio estrecho (*computational linguistics*) y la ausencia de vínculo con el texto completo del citado. Frente a ambos, la propuesta cubre las nueve funciones sobre Computer Science y enriquece cada instancia con evidencia recuperada del artículo citado.

**Métodos de clasificación y recuperación.** Beltagy, Lo y Cohan (2019) presentaron *SciBERT*, un modelo preentrenado sobre texto científico que es la base tanto del *retrieval* como de los clasificadores de este proyecto. En recuperación local de citas, Gu et al. (2022) combinaron un codificador jerárquico con un *reranking* basado en SciBERT para recuperar *qué* citar; este proyecto invierte el problema: dado un par citante–citado, recupera los fragmentos del citado que **explican** la cita para clasificar su función. Estos métodos supervisados dependen de datos etiquetados costosos, lo que aquí se mitiga con pre-etiquetado asistido y validación humana.

**Modelos de lenguaje y corpus.** El corpus *unarXive 2022* (Saier, Krause y Färber, 2023) provee texto completo estructurado de arXiv con la red de citas resuelta, y es la fuente base del proyecto. Los modelos de lenguaje de frontera permiten clasificar la intención de cita sin reentrenamiento; el proyecto los usa como jueces para el pre-etiquetado y como línea de comparación en inferencia.

---

# 4. Metodología y enfoque propuesto

## 4.1 Enfoque técnico

La solución es un pipeline de dos componentes encadenados sobre artículos en Computer Science:

1. **Recuperación semántica (retrieval):** el artículo citado se segmenta en fragmentos (*chunks*) de ≤300 palabras o ≤2 párrafos, respetando límites de oración y sección, y, mediante *embeddings* de **SciBERT** y similitud coseno, se recuperan los **Top-3** fragmentos más cercanos a cada contexto de cita, conservando la **sección retórica de origen de cada fragmento dentro del artículo citado** (p. ej., *Introduction*, *Related Work* o *Methods*).
2. **Clasificación de la función de cita:** modelos que asignan una de las **9 funciones**. Se evalúa un clasificador supervisado basado en **SciBERT**, ajustado mediante *fine-tuning* con una cabeza de clasificación y pérdida de entropía cruzada, bajo dos configuraciones de entrada: (a) contexto de cita con el título y el abstract del artículo citado y (b) contexto de cita con la sección retórica y los fragmentos Top-3 recuperados. Como comparación adicional, se evalúa un modelo comercial de lenguaje de frontera sobre la información del artículo citado.

**Justificación:** SciBERT está preentrenado sobre texto científico en inglés, lo que se ajusta al dominio; comparar una entrada con solo el título y el abstract frente a otra que incorpora la sección retórica y los fragmentos recuperados permite medir directamente el aporte de la recuperación densa, insumo central de la pregunta de investigación; la sección de origen del fragmento aporta señal discriminante —p. ej., *Basis* e *Improvement/Modification* suelen apoyarse en secciones metodológicas del citado, mientras *Background* y *Further Reading* se asocian a secciones introductorias—; y contrastar el clasificador supervisado frente a un modelo de lenguaje comercial ofrece una línea de comparación adicional.

## 4.2 Fases del Proyecto

El desarrollo es incremental y cada fase produce artefactos versionados y reutilizables:

- **F1 — Extracción, segmentación y recuperación de fragmentos.** Procesar arXiv (unarXive) para obtener texto plano en inglés, identificar marcadores y contextos de cita, segmentar el artículo citado en fragmentos y asociar con SciBERT los 3 fragmentos más similares por contexto de cita, con su sección de origen. 
- **F2 — Etiquetado asistido (sistema de jueces).** Tres modelos de pesos abiertos de ~24–27B —Qwen3.8-27B, Gemma-4-26B-A4B y Mistral-Small-3.2-24B— asignan, con un *prompt* versionado, una única función de cita a cada registro del conjunto completo. Un esquema de jueces contrasta sus respuestas por voto mayoritario y produce una etiqueta inicial de alta confianza (≥2 votos coincidentes) para apoyar la conformación del dataset; los empates quedan sin pre-etiqueta.
- **F3 — Construcción del dataset y validación humana.** Estructurar el dataset a partir del etiquetado inicial (meta de 2.000 por clase en las frecuentes y mínimos diferenciados en las minoritarias, Sección 5.3), validar manualmente una muestra del 15% seleccionada por categoría, corregir las etiquetas cuando sea necesario, reportar acuerdo interanotador (Kappa/Alpha) y dividir en train/validation/test según la estrategia de partición de la Sección 5.3 (aislamiento por documento, consideración temporal y estratificación).
- **F4 — Clasificador supervisado (fine-tuning).** Ajustar un único modelo base (SciBERT) probando dos configuraciones de entrada: (a) el título y el abstract del artículo citado y (b) la sección retórica y los fragmentos Top-3 recuperados. En ambas, la entrada concatena el contexto de cita con la información del artículo citado mediante un token especial de separación, con una cabeza de clasificación sobre las 9 categorías y pérdida de entropía cruzada.
- **F5 — Evaluación con un modelo de lenguaje.** Evaluar un único modelo comercial de lenguaje de frontera (por ejemplo, GPT-4o) en inferencia sobre la información del artículo citado, comparando dos estrategias de *prompting* (*zero-shot* y *few-shot*). El modelo entregará un puntaje entre 0.0 y 1.0 por cada una de las 9 categorías para facilitar el parseo automático, estableciendo una línea de comparación frente al clasificador supervisado.
- **F6 — Evaluación comparativa.** Comparar el clasificador supervisado y el modelo de lenguaje sobre el test validado por humanos con Precision, Recall y F1 (macro y micro), además de matrices de confusión para identificar funciones ambiguas.
- **F7 — Despliegue.** Aplicación web que integra el flujo completo: ingreso de un contexto de cita, recuperación y visualización del Top-3 con su sección, selección del clasificador entre tres opciones —encoder fine-tuned (SciBERT), modelo open-weight (1–8B) y modelo comercial vía API— y presentación de la función predicha con su confianza.

## 4.3 Plan de validación

La validación cubre tres niveles: (i) **dataset**, con acuerdo interanotador sobre el 15% validado por humanos, comparación entre las etiquetas iniciales de los modelos y las etiquetas humanas, y verificación de balance de clases y no-*leakage*; (ii) **recuperación**, con *precision@3* (P@3) calculada **manualmente** sobre una muestra de ≈150–200 contextos de cita anotada por el equipo. Como no existe *ground-truth* de relevancia, P@3 mide la proporción de fragmentos del Top-3 que son relevantes; un fragmento se considera relevante cuando contiene evidencia directamente relacionada con la función de la cita. Dos anotadores evaluarán de forma independiente los fragmentos recuperados y reportarán su acuerdo, bajo la hipótesis de que un mejor Top-3 se traduce en un mejor desempeño del clasificador que lo consume; y (iii) **clasificación** (experimento central): SciBERT bajo dos configuraciones de entrada —título y abstract, frente a sección retórica y fragmentos Top-3—, y un modelo comercial de lenguaje de frontera con estrategias *zero-shot* y *few-shot* sobre la información del artículo citado.

## 4.4 Arquitectura del pipeline

La arquitectura del pipeline se resume en el siguiente diagrama:

```mermaid
flowchart LR
    subgraph IN[Entradas]
        direction TB
        CITANTE[Artículo citante]
        CITADO[Artículo citado]
    end
    CITANTE --> CTX[Contexto de cita]
    CITADO --> SEG[Segmentación en fragmentos<br/>≤300 palabras / ≤2 párrafos]

    subgraph RETR[Recuperación semántica]
        direction TB
        EMB[Embeddings SciBERT<br/>+ similitud coseno]
        TOP3[Top-3 fragmentos<br/>+ sección retórica]
        EMB --> TOP3
    end
    CTX --> EMB
    SEG --> EMB

    META[Título y abstract<br/>del citado] --> CLF

    subgraph CLASIF[Clasificación de función]
        direction TB
        CLF{Clasificador}
        SCIBERT[SciBERT fine-tuned]
        OPEN[Open-weight 1–8B]
        LLM[LLM comercial API]
        CLF --> SCIBERT
        CLF --> OPEN
        CLF --> LLM
    end
    CTX --> CLF
    TOP3 --> CLF

    CLASIF --> OUT[Función predicha 1/9<br/>+ confianza por categoría]
    OUT --> DEMO[Aplicación web<br/>Next.js / React]

    classDef src fill:#e3f2fd,stroke:#1565c0,color:#0d47a1;
    classDef proc fill:#e8f5e9,stroke:#2e7d32,color:#1b5e20;
    classDef out fill:#fff3e0,stroke:#ef6c00,color:#e65100;
    class CITANTE,CITADO,CTX,SEG,META src;
    class EMB,TOP3,CLF,SCIBERT,OPEN,LLM proc;
    class OUT,DEMO out;
```

El detalle de herramientas se presenta en el **Apéndice**.

## 4.5 Cronograma

El proyecto se ejecuta en **8 semanas**; las fases se solapan parcialmente y cada una cierra con un artefacto o hito verificable.

| Fase | S1 | S2 | S3 | S4 | S5 | S6 | S7 | S8 | Hito / entregable |
|---|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|---|
| F1 — Extracción, segmentación y retrieval | ● | ● |  |  |  |  |  |  | Corpus + Top-3 por contexto de cita |
| F2 — Etiquetado asistido (jueces) |  | ● | ● |  |  |  |  |  | Pool de pre-etiquetas |
| F3 — Dataset + validación humana |  |  | ● | ● | ● |  |  |  | Dataset final + acuerdo interanotador |
| F4 — Clasificador supervisado (fine-tuning) |  |  |  |  | ● | ● |  |  | SciBERT ajustado en las 2 configuraciones |
| F5 — Evaluación con modelo de lenguaje |  |  |  |  |  | ● | ● |  | Resultados *zero-shot* / *few-shot* |
| F6 — Evaluación comparativa |  |  |  |  |  |  | ● |  | Métricas (F1 macro/micro) + matrices de confusión |
| F7 — Despliegue |  |  |  |  |  |  | ● | ● | Aplicación web + informe final |

---

# 5. Exploración y descripción de los datos

## 5.1 Características de los datos

- **Fuente principal:** **unarXive 2022**, corpus generado desde fuentes LaTeX de arXiv. Cada artículo (JSONL) trae `paper_id`, `metadata`, `abstract`, `body_text` (párrafos con `section`, `sec_type`, `cite_spans`, `ref_spans`), `bib_entries` y `ref_entries`. Dominio Computer Science: ~320.000 artículos y del orden de ~2,5 M de eventos de citación.
- **Vínculo de citas:** cada marcador de `cite_spans` se enlaza por `ref_id` con `bib_entries`, que ya aporta la referencia cruda del citado (`bib_entry_raw`, con su título) y sus identificadores resueltos (`open_alex_id`, `doi`, IDs de arXiv). No requiere enriquecimiento externo para el título ni para identificar el citado.
- **Metadatos y texto del citado:** cuando el artículo citado también pertenece al corpus, su título, abstract y texto completo se obtienen directamente de unarXive por su `paper_id`. Para los citados fuera del corpus, el abstract se completa como *fallback* vía OpenAlex (`abstract_inverted_index`) y el texto completo solo está disponible si es recuperable desde arXiv; la recuperación Top-3 exige dicho texto completo.
- **Alcance auto-referencial:** el dataset se restringe a citas *auto-referenciales*: aquellas en las que el artículo citado está presente en el subconjunto de trabajo con su texto completo. Por construcción, **todo contexto de cita del dataset tiene su par citado con texto completo disponible** para la recuperación Top-3; los contextos cuyo citado no es resoluble o carece de texto completo se descartan con registro del motivo, no quedan como pares incompletos.
- **Unidad (registro):** contexto de cita + artículo citante + artículo citado + Top-3 fragmentos recuperados + etiqueta de función de cita. Cada registro se identifica de forma única por la combinación de artículo citante, párrafo, posición del marcador y artículo citado, y referencia el Top-3 mediante claves estables de fragmento (no por posición en la lista).
- **Sin etiquetas de función:** unarXive no trae la función de cita; debe generarse por anotación asistida + validación humana.
- **Licencia:** subconjunto abierto CC BY-SA 4.0; versión completa restringida por documento. No hay datos personales sensibles (literatura pública), pero debe respetarse la licencia para redistribución.

### 5.1.1 Desafíos y limitaciones
- **Desbalance de clases:** funciones como *Gap*, *Identification of the Originator* o *Further Reading* son minoritarias.
- **Solapamiento entre funciones:** una cita puede cumplir más de una función; el proyecto adopta un enfoque **multiclase de etiqueta única**, asignando la función más relevante.
- **Disponibilidad del citado:** sin texto completo no hay recuperación Top-3.
- **Ruido de parsing LaTeX** y referencias no resolubles.
- **Fuga de información:** contextos que comparten citante/citado deben ir en la misma partición.

## 5.2 Estrategia de construcción del dataset

Meta de referencia: hasta **9 × 2.000 = 18.000 registros**, con balance pleno entre las clases frecuentes; las clases minoritarias se rigen por los mínimos diferenciados y el plan de contingencia de la Sección 5.3. El proceso parte del orden de millones de eventos de citación y los reduce de forma controlada —validándose primero sobre un piloto de varias subcategorías antes de escalar—; cada etapa produce un artefacto versionado y registra las razones de descarte.

| Etapa | Acción | Artefacto |
|---|---|---|
| 1 | Filtro técnico y de categoría: restringir a Computer Science (y áreas afines) por categoría primaria; conservar citas con marcador resoluble y contexto no vacío | Subconjunto técnico |
| 2 | Contextos y alcance auto-referencial: construir los contextos de cita desde el cuerpo del artículo y restringir a citas *auto-referenciales* (el citado está en el subconjunto con texto completo) | Contextos + citados |
| 3 | Limpieza y control de calidad: deduplicar fragmentos, descartar textos demasiado cortos o incompletos y contextos fuera del límite de longitud; excluir citantes no ingleses | Subconjunto limpio |
| 4 | Segmentación del artículo citado en fragmentos (≤300 palabras / límite de tokens) | Citados fragmentados |
| 5 | Recuperación Top-3 con SciBERT, conservando un contexto representativo por par citante→citado | Top-3 por instancia |
| 6 | Pre-etiquetado asistido (jueces LLM): voto mayoritario, etiqueta de alta confianza (≥2 votos), empates sin etiqueta | Pool de pre-etiquetas |
| 7 | Validación humana (15% = test) por categoría sobre instancias de alta confianza + acuerdo interanotador | Dataset final |

Principios transversales: reducción con registro de motivos, excedente controlado, balance diferido sobre etiquetas verificadas, deduplicación por par citante–citado y no fuga de información (partición por documento).

## 5.3 Partición de datos y plan de contingencia

**Partición (train/validation/test).** La partición respeta tres criterios: (i) **aislamiento por documento** —ningún contexto de un mismo artículo citante se reparte entre particiones, de modo que las instancias co-citadas y los pares citante–citado no filtren información entre *train* y *test*—; (ii) **consideración temporal** —se contempla un corte por año de publicación del citante para evaluar la generalización a literatura reciente y evitar fuga temporal—; y (iii) **estratificación** por clase y por sección retórica, para que todas las funciones y secciones estén representadas en cada partición. El conjunto de prueba se toma del 15% validado por humanos sobre instancias de alta confianza.

**Viabilidad de la meta y plan de contingencia.** La meta de 2.000 ejemplos por clase es razonable para las funciones frecuentes (p. ej., *Background*, *Comparison*, *Evidence*), pero la exploración del corpus sobre cuatro clases minoritarias —*Gap*, *Further Reading*, *Improvement/Modification* e *Identification of the Originator*— indica que estas alcanzarían alrededor del 50% de la expectativa (del orden de 1.000 ejemplos por clase) con el corpus actual. Para no condicionar el proyecto a una meta inalcanzable se adopta un plan escalonado: (1) **mínimos diferenciados por clase**, con un piso menor documentado para las minoritarias y balance pleno solo entre las frecuentes; (2) **muestreo dirigido por señales** (*cue sampling*) de frases indicadoras para elevar el reclamo de las clases escasas; (3) **ampliación del corpus citante** más allá de las subcategorías exploradas si persiste el déficit; y (4) como última opción, **relajar el balance** o la unidad de aislamiento (de documento a par + grupo de cita) y/o **fusionar categorías ambiguas**. Cada etapa de reducción del corpus registra el motivo de descarte, de modo que la disponibilidad real por clase es trazable antes de fijar las metas definitivas con el experto.

---

# 6. Consideraciones éticas

## 6.1 Impacto social y ético
La solución apoya la revisión y comprensión de literatura científica. Un mal uso podría distorsionar análisis bibliométricos o revisiones. Se comunicará el carácter asistivo de la herramienta y sus límites de confianza.

## 6.2 Sesgos y su mitigación
- **Sesgo de dominio:** el corpus es Computer Science en inglés; los resultados no generalizan a otras disciplinas ni idiomas.
- **Sesgo de sección:** los contextos se concentran en *Introduction* y *Related Work*; se aplicará muestreo distribuido por sección.
- **Sesgo del pre-etiquetado:** depender solo de la confianza del LLM puede perder clases minoritarias; se mitiga con jueces múltiples, validación humana y balance diferido.
- **Desbalance de clases:** búsqueda dirigida y muestreo estratificado para clases minoritarias.

## 6.3 Transparencia y explicabilidad
La aplicación mostrará los Top-3 fragmentos recuperados y su sección, además de la confianza por categoría, para que el usuario entienda en qué evidencia se apoya la predicción. Se documentarán modelos, prompts, semillas y métricas.

## 6.4 Privacidad y seguridad
Se trabaja con literatura pública; no hay datos personales sensibles. Se respetarán las licencias para conservar y redistribuir texto. Las claves de API y credenciales de infraestructura se manejan fuera del control de versiones.

---

# 7. Evaluación y métricas

- **Clasificación de función de cita:** Precision, Recall y **F1 macro y micro** sobre el test validado por humanos (15%); matrices de confusión para identificar funciones ambiguas.
- **Configuraciones de entrada y comparación:** se compara SciBERT con el título y el abstract del artículo citado frente a SciBERT con la sección retórica y los fragmentos Top-3 recuperados. El éxito se define como una mejora consistente del **F1 macro** de la configuración con fragmentos recuperados sobre la de solo título y abstract, y un desempeño claramente superior a la línea de azar (1/9 ≈ 11,1% en 9 clases balanceadas); la meta cuantitativa concreta se afinará con el experto según la dificultad observada.
- **Comparación de modelos:** la mejor configuración de SciBERT se compara con un modelo comercial de lenguaje de frontera (*zero-shot* y *few-shot*), utilizando la misma información del artículo citado y métricas cuando sea técnicamente posible.
- **Calidad de la anotación:** acuerdo interanotador (Cohen/Fleiss Kappa o Krippendorff Alpha).
- **Recuperación (retrieval):** al no existir *ground-truth* de relevancia, se evalúa con *precision@3* (P@3) calculada **manualmente** por dos anotadores sobre una muestra de ≈150–200 contextos de cita. P@3 es relevante porque cuantifica la calidad del Top-3 que alimenta al clasificador: mide la proporción de fragmentos recuperados que aportan evidencia directamente relacionada con la función de la cita. La hipótesis de trabajo es que un Top-3 con mayor P@3 mejora el desempeño de la configuración que incorpora los fragmentos recuperados; por ello P@3 se reporta junto con el acuerdo interanotador de la relevancia y con la coherencia de la sección retórica de origen, y su aporte se contrasta con el F1 macro de dicha configuración.
- **Eficiencia:** costo de fine-tuning frente al costo por tokens del modelo de lenguaje, latencia por inferencia y tasa de errores de formato/parseo (F7).

**Experiencia previa del equipo:** en el microproyecto CiteScope, sobre clasificación de funciones de cita en la subárea `cs.*`, SciBERT Plus alcanzó un Macro F1 de 0,68 en el conjunto de prueba. Este resultado constituye un antecedente técnico para el uso de modelos científicos en el proyecto actual.

---

# 8. Bibliografía

- Beltagy, I., Lo, K., & Cohan, A. (2019). SciBERT: A pretrained language model for scientific text. *Proceedings of EMNLP-IJCNLP 2019*, 3615–3620.
- Cohan, A., Ammar, W., van Zuylen, M., & Cady, F. (2019). Structural scaffolds for citation intent classification in scientific publications. *Proceedings of NAACL-HLT 2019*, 3586–3596.
- Gu, N., Gao, Y., & Hahnloser, R. H. R. (2022). Local citation recommendation with hierarchical-attention text encoder and SciBERT-based reranking. *Advances in Information Retrieval (ECIR 2022)*, LNCS 13185, 274–288.
- Lauscher, A., Ko, B., Kuehl, B., Johnson, S., Cohan, A., Jurgens, D., & Lo, K. (2022). MultiCite: Modeling realistic citations requires moving beyond the single-sentence single-label setting. *Proceedings of NAACL-HLT 2022*, 1875–1889.
- Lyu, D., Ruan, X., Xie, J., & Cheng, Y. (2021). The classification of citing motivations: A meta-synthesis. *Scientometrics, 126*(4), 3243–3264. https://doi.org/10.1007/s11192-021-03908-z
- Saier, T., Krause, J., & Färber, M. (2023). unarXive 2022: All arXiv publications pre-processed for NLP, including structured full-text and citation network. *Proceedings of JCDL 2023*.
- Bjerke, M. B., & Renger, R. (2017). Being smart about writing SMART objectives. *Evaluation and Program Planning, 61*, 125–127. https://doi.org/10.1016/j.evalprogplan.2016.12.009
- Centro de Español, Universidad de los Andes. (s. f.). *¿Cómo plantear objetivos?* https://leo.uniandes.edu.co/como-plantear-objetivos/
- OpenAlex. https://openalex.org/

---

<div style="page-break-before: always;"></div>

# 9. Apéndices

## 9.1 Apéndice — Herramientas

- **Datos / corpus:** unarXive 2022, OpenAlex, arXiv; Python (pandas, NumPy).
- **Recuperación y modelado:** PyTorch, Hugging Face Transformers (SciBERT `allenai/scibert_scivocab_uncased`); similitud coseno (FAISS opcional) para la recuperación.
- **Modelos de lenguaje:** un modelo comercial de lenguaje de frontera vía API (evaluado con *zero-shot* y *few-shot*) y un modelo open-weight (1–8B) disponible en el demostrador.
- **Evaluación:** scikit-learn (Precision, Recall, F1, matrices de confusión) y métricas de acuerdo interanotador (Cohen/Fleiss Kappa, Krippendorff Alpha).
- **MLOps:** Git, DVC (remoto S3), MLflow (servidor en EC2), FastAPI, PostgreSQL, Docker.
- **Aplicación web:** Next.js / React / TypeScript, integrada con las tres opciones de clasificación del demostrador.

