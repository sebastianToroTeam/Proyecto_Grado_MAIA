**Guía de Anotación — Clasificación de Función de Cita**

**Proyecto:** Recomendación local de citas y clasificación de función de cita

**Versión:** rev3.1 (17 de octubre de 2026; reglas rev1.1 aprobadas por los grupos 14 y 15; corresponde al prompt v11.1 de A2, congelado). Une la rev3 y la revisión del equipo sobre la rev2: se eliminan la submuestra sin pre-etiqueta y la marca de inconsistencia dentro de un grupo de citas; se conserva la sección de citas agrupadas; las etiquetas secundarias quedan solo para casos extremos; los marcadores del texto pasan a inglés, como los ven los jueces y los anotadores. **Actualizada el 23 de octubre de 2026 sin cambios en las reglas:** las definiciones (Sección 3) y las reglas de decisión (Sección 6) no cambian; solo se actualizan textos de estado, nombres de campos y una nota aclaratoria (Secciones 2, 5, 6.1, 7, 8 y 9) para reflejar el proceso en curso. El procedimiento operativo (muestreo, rondas, olas, reserva, adjudicación) está en el contrato de datos.

**Nota:** Esta guía fue elaborada con la asistencia de IA (Claude), y fue complementada y revisada por el equipo.

**1\. Propósito de esta guía**

Esta guía define el procedimiento y los criterios que deben seguir (a) los modelos de lenguaje utilizados en el pre-etiquetado automático (A2) y (b) los integrantes de los grupos durante la validación humana (A3), para asignar a cada instancia de cita una de las 9 categorías de función de cita definidas en la propuesta del proyecto. El objetivo es que ambos procesos —automático y humano— apliquen exactamente el mismo criterio de decisión, para que las discrepancias entre el pre-etiquetado y la validación humana reflejen errores reales del modelo y no diferencias en el criterio de anotación.

Las reglas de la Sección 6 son las mismas que lee cada juez de A2 en el prompt v11.1 (en inglés); entre paréntesis, junto a cada regla, se indica a qué regla del prompt corresponde.

**2\. Material disponible para la anotación**

Tanto el pre-etiquetado automático (A2) como la validación humana (A3) tienen acceso al mismo conjunto de información:

| Elemento | Incluido | Cómo se muestra al anotador (A3) |
| :---- | :---- | :---- |
| Contexto de cita (párrafo citante), con la cita objetivo marcada | Sí | Siempre visible; la oración de la cita objetivo, resaltada |
| Número de citas co-citadas con la cita objetivo (`group_siblings`) | Sí | Siempre visible (Sección 5) |
| Título del artículo citado | Sí | Siempre visible |
| Resumen (*abstract*) del artículo citado | Sí | Siempre visible |
| Top-3 *chunks* recuperados por A1, con su sección de origen | Sí | Plegados: se abren con un clic cuando hacen falta (Sección 4) |
| Pre-etiqueta de A2 (`primary_pre_label`) | Sí | Visible desde el inicio, para confirmarla o corregirla |
| Votos de los jueces, conjunto de prueba, identidad del artículo citante | No | Nunca se muestran |

**Test sets (A3):** cada grupo (14 y 15) tiene su propio test set (T\_A y T\_B, disjuntos: no comparten instancias ni artículos citantes). Cada instancia la anotan dos personas: un integrante del grupo dueño del conjunto, cuya anotación es la validación de ese grupo, y un integrante del otro grupo, que aporta la segunda anotación independiente para medir el acuerdo. Ambos reciben el material en un **paquete congelado** idéntico (contrato de datos, Sección 5). En el paquete, la cita que se está clasificando aparece como `[[TARGET]]`; las demás citas aparecen como `[CITE]`, las fórmulas como `[FORMULA]`, las figuras como `[FIGURE]` y las tablas como `[TABLE]` (los mismos marcadores que leen los jueces de A2). La anotación se hace en dos rondas (ronda 1: Background, Comparison, Gap, Evidence, Application e Identification of the Originator; ronda 2: Basis, Improvement/Modification y Further Reading), con las mismas reglas (contrato de datos, Sección 6).

**Hoja de instrucciones.** Durante la anotación, cada anotador tiene abierta, al lado de su lote, la *Hoja de instrucciones A3*: un resumen de esta guía (pasos, etiquetas, reglas de la Sección 6, cuándo abrir los *chunks* y ejemplos resueltos). Ante cualquier diferencia entre la hoja y esta guía, manda la guía.

**Anotación en un paso: confirmar o corregir.** Tal como pide A3 de la propuesta ("confirmando o corrigiendo la etiqueta sugerida por los modelos"), cada anotador ve la pre-etiqueta de A2 y la confirma o la corrige. Las dos anotaciones de cada instancia son independientes: ningún anotador ve la etiqueta del otro. Para no dejarse arrastrar por la pre-etiqueta:

1. Leer primero la oración de `[[TARGET]]` y decidir qué hace el texto con la cita (Sección 6, R0).
2. Solo entonces compararlo con la pre-etiqueta: si coincide, confirmar; si no, corregir con la regla que aplica.
3. Abrir los *chunks* cuando la etiqueta dependa del contenido del artículo citado (Sección 4).

La pre-etiqueta proviene de jueces con alta confianza (al menos 2 de 3), pero se equivoca a menudo en las fronteras de la Sección 6. Confirmarla no es la respuesta por defecto: es una decisión del anotador.

**Calidad del material del artículo citado.** Todos los artículos citados tienen al menos 1.000 palabras de cuerpo extraído, y se excluyeron los que solo contenían el apéndice (menos de 2.000 palabras y sin sección de introducción). Aun así, algunos artículos más largos pueden venir solo con su apéndice o material suplementario (p. ej. secciones como "Implementation Details" o "Additional Results"). Si los *chunks* no reflejan la contribución principal del artículo citado, el anotador debe apoyarse en el título y el resumen, bajar la confianza si la categoría depende del contenido del artículo (Sección 4) y dejarlo indicado en `notes`.

**3\. Las 9 categorías de función de cita**

Definiciones y criterios operativos, tomados directamente de la propuesta del proyecto (adaptados del marco de referencia citado en la bibliografía del proyecto).

**Nota sobre alcance:** el marco de referencia original define 10 categorías de "motivación científica"; este proyecto excluye deliberadamente la décima, *Assessment* (la postura evaluativa del citante — positiva, negativa o mixta — sobre el trabajo citado), ya que corresponde a una tarea de detección de postura distinta de la clasificación estructural de función que aquí se aborda. Si una cita transmite claramente una valoración crítica o elogiosa pero no encaja con claridad en ninguna de las 9 categorías siguientes, **no** debe forzarse una categoría por defecto — ver la regla de **"unclear"** en la Sección 6\.

**Background:** Citas usadas para dar contexto, resumir el trasfondo general de un tema de investigación, o trazar la historia de un campo o idea.

**Criterios:**

* Revisa la literatura existente para dar comprensividad.
* Cita artículos de revisión o estudios previos relevantes para describir el estado actual del campo.
* Resalta hallazgos importantes o predominantes en el campo.
* Traza el desarrollo histórico o presenta ideas clave del área temática.

**Gap:** Citas que ayudan a identificar vacíos de investigación o áreas no exploradas, justificando la elección del tema de investigación del autor.

**Criterios:**

* Resalta qué se ha hecho o no se ha hecho en el área de investigación.
* Identifica vacíos de conocimiento o áreas para investigación futura.
* Justifica la relevancia e importancia de la investigación actual contrastándola con trabajo existente.

**Basis:** Citas que proporcionan un fundamento para la investigación actual, ya sea a nivel macro (influencia amplia) o micro (contribuciones específicas clave).

**Criterios:**

* Sirve como punto de partida intelectual de la investigación.
* El trabajo citado moldea significativamente la idea o hipótesis de investigación.
* La investigación actual se construye sobre, continúa o expande el trabajo citado.

**Comparison:** Citas usadas para comparar el trabajo actual con estudios citados, o para establecer comparaciones entre estudios citados.

**Criterios:**

* Resalta similitudes o diferencias entre la investigación actual y trabajos citados.
* Compara metodologías, hallazgos, algoritmos, datos o conceptos teóricos.
* Puede señalar ventajas del estudio actual sobre trabajos previos, o establecer vínculos entre distintos trabajos citados.

**Application:** Citas que emplean directamente un método, técnica o herramienta del trabajo citado, sin modificación.

**Criterios:**

* Utiliza métodos, algoritmos, instrumentos o datos existentes para la investigación actual.
* Emplea el trabajo citado como herramienta práctica (ej. ecuaciones, métodos de análisis) para cálculo o experimentación.
* Aplica el método citado sin modificación.

**Improvement / Modification:** Citas en las que métodos o herramientas del trabajo citado son adaptados, extendidos o modificados para la investigación actual.

**Criterios:**

* El método citado es adaptado, mejorado o extendido para ajustarse a nuevas condiciones experimentales u objetivos de investigación.
* Usa el trabajo citado como fundamento, pero lo modifica para mejorar precisión o alcance.

**Evidence:** Citas usadas para respaldar afirmaciones, hipótesis o hallazgos de la investigación actual.

**Criterios:**

* Respalda argumentos, hipótesis o afirmaciones factuales.
* Justifica el diseño de investigación, metodologías o procedimientos experimentales.
* Sustenta o explica hallazgos, especialmente en la sección de discusión.
* Provee evidencia para mitigar limitaciones o respaldar sugerencias de investigación futura.

**Identification of the Originator:** Citas usadas para reconocer la fuente original de una idea, concepto, método o teoría.

**Criterios:**

* Identifica la publicación original donde una idea o método clave fue introducido por primera vez.
* Reconoce a los pioneros del campo.
* Da crédito a la prioridad de un trabajo citado, demostrando una deuda intelectual.

**Further Reading:** Citas que dirigen al lector hacia literatura adicional o complementaria para información o contexto más detallado.

**Criterios:**

* Alerta al lector sobre fuentes de información nuevas, diferentes o relevantes.
* Provee detalles más completos sobre datos, métodos o trasfondo no cubiertos totalmente en el trabajo actual.
* Frecuentemente usada para referir al lector a fuentes externas para una comprensión más profunda.

**3.1 Aclaraciones del marco de referencia (Lyu et al., 2021)**

Las definiciones anteriores son las de la propuesta del proyecto, que resume el marco de Lyu et al. (2021). Las siguientes aclaraciones provienen de la descripción completa de cada categoría en ese artículo (sección "Scientific motivation", pp. 3252–3255), parafraseada de cerca; las frases entre comillas son de Lyu et al. Se aplican igual en el pre-etiquetado automático (A2, donde van en inglés en el prompt) y en la validación humana (A3).

* **Background:** sus dos formas más comunes son trazar la historia (el desarrollo del trabajo de un investigador o de un campo, y de las ideas a lo largo del tiempo) y mostrar los hallazgos predominantes o importantes, es decir, el "state of the art" de la pregunta de investigación, incluidos trabajos representativos de distintos puntos de vista. Suele aparecer en la introducción o en trabajos relacionados. Incluye también citar un artículo de revisión (*review*), teorías o métodos alternativos que el trabajo actual no utiliza, e investigación relacionada de otros campos.
* **Gap:** muestra qué se ha hecho o no en el área, para delimitar un territorio propio al trabajo actual; los vacíos surgen al evaluar el estado del área. Incluye también las citas usadas para formular las preguntas de investigación del trabajo actual.
* **Basis:** nivel macro: el trabajo citado es el punto de partida o fundamento del trabajo actual, su principal fuente de ideas, un peldaño desde el cual los autores profundizan, o el trabajo actual es una continuación de él. Nivel micro: un punto clave del trabajo actual, p. ej. su argumento se forma sobre el argumento del citado, o el citado es "the source of the derivation of a fundamental equation or a detailed description of the experimental conditions" sobre la que se construye el trabajo actual. Si una ecuación o herramienta solo se usa para calcular, es *Application*.
* **Comparison:** dos tipos. (a) Trabajo actual vs. trabajo citado: hallazgos (consistencia o contradicción, típicamente en la sección de resultados), métodos, conceptos, algoritmos o datos; con frecuencia para señalar desventajas del citado y así las ventajas del actual, y a veces solo para indicar similitudes o diferencias. (b) Entre trabajos citados, para establecer vínculos entre ellos: sus métodos, áreas, hallazgos u opiniones.
* **Application:** "unchanged use" de las herramientas de los autores citados para calcular: instrumentos, métodos de análisis, algoritmos, ecuaciones. Incluye también usar el trabajo citado como fuente de datos sin modificación: un dataset, un benchmark, estadísticas originales o resultados reportados en él (p. ej., como datos de un análisis secundario).
* **Improvement/Modification:** "use with adaptations": el trabajo actual adapta o amplía un método del citado, como un algoritmo o una técnica experimental, o lo ajusta, p. ej. cambiando su precisión o el alcance de su aplicación.
* **Evidence:** cinco tipos: respaldar una opinión, afirmación o hipótesis de los autores citantes (incluidas las hipótesis que el trabajo actual pone a prueba); justificar el diseño de la investigación (decisiones metodológicas, procedimientos, materiales); explicar o sustentar los hallazgos del trabajo actual, en especial los inesperados, normalmente en la discusión; dar por aceptable o válido un resultado numérico ("qualify the acceptability/validity of a numerical result"); y mitigar limitaciones, resaltar contribuciones o respaldar sugerencias de trabajo futuro. Incluye también las citas que ayudan a definir o explicar un concepto o enfoque en el que se apoya el texto citante. Las citas de *Evidence* suelen introducirse con "for example" o "e.g." cuando los trabajos citados son ejemplos que respaldan una afirmación (es una tendencia que describe Lyu et al., no una regla: ver R-EV en la Sección 6).
* **Identification of the Originator:** cita la publicación original donde apareció por primera vez una idea, concepto, término o método, p. ej. una ley con nombre ("la ley de Zipf [x]"), documentando la atención temprana que recibió. Incluye también rendir homenaje a los pioneros y mostrar respeto a grandes predecesores; reconocer la prioridad del citado sobre un término, idea o método; la cita que "explicitly acknowledges intellectual indebtedness to a cited work or states that the cited work has been of particular value"; y dejar claro que los autores citantes no reclaman ser los originadores. Si el trabajo actual usa el método nombrado, se prefiere *Application* como etiqueta principal.
* **Further Reading:** remite al lector al trabajo citado en lugar de describirlo, normalmente entre paréntesis o en una nota al pie y a menudo precedida por "see". Tres propósitos: alertar al lector sobre fuentes nuevas y distintas (otros artículos relevantes o interesantes); remitir a detalles más completos (p. ej., la descripción completa de los datos usados, una demostración o derivación completa); y ahorrar espacio para que el texto sea conciso.

**4\. Cuándo abrir los *chunks* del artículo citado**

La función se lee en el texto citante (R0); la información del artículo citado sirve para **verificarla**, no para decidirla. Lo que el artículo citado dice de sí mismo (p. ej., en su resumen) no indica por qué se cita, y que sea muy cercano al tema del artículo citante no lo convierte en *Basis*. En las pruebas de A2, quitar los *chunks* casi no cambió las etiquetas de los jueces: donde más ayudan es en la revisión humana. Abrirlos sobre todo en estos casos:

* **Gap vs. Evidence:** ¿el artículo citado **tiene** la limitación (Gap) o **reporta** la limitación de otro método (Evidence)? Los *chunks* suelen mostrarlo.
* **Application vs. Improvement/Modification:** confirmar qué método propone realmente el citado, y si el texto citante lo usa tal cual o lo adapta.
* **Comparison y Evidence:** confirmar que el citado aporta el método, hallazgo o dato que el texto citante le atribuye.
* **Basis vs. Background:** confirmar el grado de influencia real, cuando el texto citante sugiere que el trabajo actual se construye sobre el citado.

**5\. Citas agrupadas (co-citadas)**

unarXive representa una cita múltiple agrupada en el texto original (p. ej. "\[1,2,3\]" o "(Smith 2020; Jones 2019)") como varias instancias separadas — una por cada referencia co-citada — que comparten el mismo citation\_context pero apuntan a distintos artículos citados. Cada una de estas instancias comparte además la misma clave de grupo (`group_key`, contrato de datos, Sección 3). Los grupos se calculan por **posición** en el párrafo: citas consecutivas separadas por 5 caracteres o menos (normalmente ", " o "; ") forman un grupo, incluidas las referencias que no se resolvieron a un artículo de arXiv.

* **En los test sets, cada anotador ve como máximo un miembro de cada grupo** (contrato de datos, Sección 6). El paquete indica cuántas instancias co-citadas tiene el grupo (`group_siblings`), para que el anotador sepa que la cita forma parte de un corchete.
* **La función de cita debería ser, en general, consistente entre los miembros de un mismo grupo**, dado que comparten el mismo texto que motiva la cita — un lector humano que escribe "como se muestra en \[1,2,3\]" normalmente atribuye la misma razón a los tres trabajos co-citados. En particular, una limitación enunciada sobre todo el grupo hace *Gap* a cada uno de sus miembros, sin importar cuántos sean (R-GAP).
* **Mismo artículo dos veces en un grupo:** en ~0,2% de los grupos el mismo artículo citado aparece dos veces en el corchete, porque la bibliografía del artículo citante tiene dos entradas (p. ej. preprint y versión publicada) que apuntan al mismo arXiv ID. Es un comportamiento esperado de los datos, no un error.
* **Fuera del test set (p. ej. revisión de lotes de A2),** si un anotador encuentra varias instancias consecutivas con un citation\_context idéntico, debe verificar si comparten `group_key` antes de asumir que se trata de una duplicación.

**6\. Reglas de decisión (rev1.1)**

Cada regla resuelve una frontera entre categorías. Son las mismas que aplican los jueces de A2 (prompt v11.1); entre paréntesis, la regla del prompt que le corresponde. Ante cualquier duda, gana la regla sobre la intuición o la pre-etiqueta.

**Orden de lectura rápido.** Una cita listada entre trabajos relacionados o como ejemplo es *Background*, **salvo que** el texto citante: diga que el trabajo actual la usa (*Application*), la adapta (*Improvement/Modification*) o se construye sobre ella (*Basis*); la compare con el trabajo actual o con otros trabajos citados (*Comparison*); la cite para respaldar una afirmación, decisión o resultado de los autores (*Evidence*); enuncie una limitación o un problema sin resolver que le aplica, sola o junto con los trabajos con los que se lista (*Gap*); la acredite como fuente original (*Identification of the Originator*); o envíe al lector a ella para más detalles (*Further Reading*).

**R0 — Alcance de lectura: solo la cita objetivo** (prompt, regla 1). La etiqueta es la función de la cita objetivo. Se empieza por la oración que contiene `[[TARGET]]`. Otra oración del párrafo cuenta solo si se refiere de vuelta a la cita objetivo —por su nombre, con "they", "these methods", "this approach", una lista que la contiene, o continuando la misma afirmación— y solo por lo que esa oración dice de ella (p. ej., en "Several detectors [1, x, 2] were proposed. However, they require dense annotations.", la segunda oración aplica a [x]). **No cuentan:** lo que se afirma de otras citas (`[CITE]`), el tema general del párrafo, la sección donde está, suposiciones sobre los objetivos del artículo citante, ni la pre-etiqueta.

**Una etiqueta principal** (prompt, regla 2). Se registra **una etiqueta principal obligatoria** (la función que aplica con más claridad a la cita objetivo) y, solo si otra función también aplica con claridad, **etiquetas secundarias** opcionales; normalmente no hay (Sección 9). El acuerdo interanotador (κ de Cohen) se calcula sobre la etiqueta principal (Sección 8).

**Background vs. Basis** (prompt, regla 3). *Background* da contexto general, incluida una cita listada como un ejemplo más entre trabajos o enfoques relacionados ("many methods have been proposed [1, 2, 3]", "X [1], Y [2] and Z [3] address this problem"). *Basis* exige que el texto citante diga o deje claro que el trabajo actual se construye sobre el citado, lo continúa o lo amplía ("we build on [x]", "following [x]", "our method extends [x]"). Si el texto no lo indica, no es *Basis*. "Based on [x]" o "builds on [x]" cuentan solo si su sujeto es el trabajo actual (we, our method, this paper); si son otros trabajos los que se basan en el citado, es *Background*.

**R-CMP — Comparison vs. Background** (prompt, regla 4). *Comparison* exige que el texto citante compare trabajos entre sí: asemeja o contrasta el trabajo actual con el citado ("unlike [x]", "compared to [x]", "outperforms [x]", "similar to [x]", "consistent with [x]", "although they …, we …"), o compara la cita objetivo con otros trabajos citados ("[x] and [y] report opposite results", "unlike [y], [x] does not need labels"). Agrupar o listar trabajos bajo un tema, problema o propiedad compartida no es comparar: en "many methods have been proposed [1, 2, 3]", "approaches can be categorized into A [1], B [2] and C [3]" o "[x] and [y] also use attention", cada trabajo listado es *Background*. También es *Background*: un dataset o modelo mencionado solo como detalle de un trabajo comparado ("[y] and [z], both trained on the dataset of [x]"), y una familia de trabajos contrastada en bloque, con la cita objetivo como uno de sus miembros.

**R-IM — Application vs. Improvement/Modification** (prompt, regla 5). *Improvement/Modification* cuando el texto citante dice que el trabajo actual adapta, extiende o modifica el método, la herramienta o los datos de la cita objetivo ("we adapt the encoder of [x]", "the dataset of [x], which we extend with depth labels"), **aunque no describa el cambio** (la definición dice "adapted, expanded, or modified"). *Application* cuando se usa tal cual, incluso si el trabajo actual aplica una modificación que introdujo el propio citado ("we modify the backbone as in [x]"). Un verbo cuyo objeto es otro trabajo citado o el método propio de los autores no convierte a la cita objetivo en *Improvement/Modification*.

**Verificar con el artículo citado** (prompt, regla 6). En *Comparison*, *Application*, *Improvement/Modification*, *Evidence* y *Basis*, comprobar que la información del artículo citado es consistente con lo que el texto citante dice de él (Sección 4).

**Citas agrupadas** (prompt, regla 7). Se clasifica la función de la cita objetivo; normalmente coincide con la de sus co-citados (Sección 5).

**Postura evaluativa y unclear** (prompt, regla 8). La postura por sí sola no es una categoría: una cita que solo afirma el trabajo citado (dice que es correcto o sólido, o está de acuerdo con él), solo lo critica, o expresa una opinión mixta es **unclear**. Pero el elogio que acredita al citado como fuente primera, original o pionera de una idea, reconoce su prioridad o reconoce la deuda intelectual del trabajo actual con él es *Identification of the Originator* ("the seminal work of [x]", "pioneered by [x]", "first introduced in [x]"). La crítica que motiva el trabajo actual es *Gap* (lo que el citado deja sin resolver) o *Comparison* (en qué se diferencia o mejora el trabajo actual respecto a él). Si ninguna categoría aplica con claridad, marcar **unclear** con una nota breve; no forzar una categoría. Los casos unclear pasan por adjudicación.

**Background vs. Identification of the Originator** (prompt, regla 9). Cuando el texto traza una historia, el trabajo acreditado como fuente primera u original es *Identification of the Originator*; los trabajos posteriores de la secuencia son *Background*.

**R-EV — Evidence vs. Background / Application / Basis** (prompt, regla 10). Preguntarse qué respalda la cita objetivo. *Evidence*: los autores citantes hacen una afirmación sobre un problema o un fenómeno, justifican una decisión propia o interpretan un resultado propio, y la cita se usa como respaldo de esa afirmación ("X degrades under noise [x]" usado para motivar el método; "we set the learning rate to 1e-4, as recommended in [x]"; "as indicated in [x], performance grows logarithmically with data"). También es *Evidence* la cita que es la fuente que reporta o muestra una limitación de **otros** métodos ("these detectors are too slow for real-time use, as shown in [x]"); los métodos que tienen la limitación son *Gap* (R-GAP). **No es Evidence:**

* describir lo que hizo un trabajo citado ("Kim et al. [x] applied gating attention", "flow is learned by optimizing a photometric loss [x]") → *Background*;
* una afirmación sobre los trabajos previos mismos ("several methods also search the width, for example [x] and [y]", "methods such as X, e.g. [1, 2]") → *Background*, aunque vaya tras "e.g." o "for example";
* nombrar un dataset, benchmark, modelo, métrica o herramienta que el trabajo actual usa en sus propios experimentos ("we conduct experiments on COCO [x]", "the accuracy of FaceNet [x]" en la evaluación de los autores) → *Application*;
* nombrar un dataset o método solo como contexto ("ViT requires datasets such as JFT300M [x]") → *Background*.

Una definición es *Identification of the Originator* si se acredita al citado como el primero en introducir el término; si no, *Evidence*. Usar un método citado es *Application* ("we use Adam [x]"), pero la cita que justifica la elección es *Evidence* ("we use Adam because it converges faster [y]"). *Basis* exige que el trabajo actual sea el sujeto de "build on / follow / extend [x]".

**R-GAP — Gap vs. Evidence / Background** (prompt, regla 11). *Gap* aplica a los trabajos cuya limitación o problema sin resolver enuncia el texto citante, en la oración de la cita objetivo o en una que se refiere de vuelta a ella. Si la limitación se enuncia sobre todos los trabajos citados juntos ("existing detectors [1, 2, 3] fail under heavy occlusion", "Methods such as [4, 5, 6] were proposed. However, they require paired training data."), cada uno es *Gap*, sin importar cuántos sean. **Prueba de posición** para separar *Gap* de *Evidence*:

* la cita va **justo después de los trabajos que tienen la limitación**, antes de enunciarla ("existing trackers [1, 2, 3] drift over long sequences", "a recent method [x] still needs manual tuning") → cada trabajo citado es *Gap*;
* la cita va **al final de una afirmación sobre la limitación de otra cosa** —otro método, una familia de modelos, una técnica— ("recurrent models are known to forget long contexts [x]", "it has been shown that adversarial training slows convergence [x]") → la cita objetivo es la fuente de esa afirmación: *Evidence*.

Los trabajos solo listados junto a un enunciado sobre un vacío **del campo** (lo que no se ha hecho, sin atribuirles una limitación), o como direcciones que ha tomado el campo, son *Background*.

**R-FR — Further Reading: primero el contenido** (prompt, regla 12). Un puntero a la cita objetivo ("see [x]", "see also [x]", "we refer the reader to [x]", "[x] for a survey / more details / an example", "and references therein") es *Further Reading*, **salvo que** la misma oración también compare la cita objetivo con el trabajo actual o con otro trabajo, o haga una afirmación que la cita respalda: entonces esa función es la principal y *Further Reading* la secundaria. (Alternativa no adoptada, "primero el puntero": convertiría en *Further Reading* casos como E17, E42 y E44 de eval60.)

**6.1 Ejemplos resueltos**

Ejemplos de la calibración (C01–C30) y de eval60 (E01–E60), elegidos en las fronteras donde más nos equivocamos: *Background* fue la respuesta correcta solo el 38% de las veces, y 16 votos confundieron *Gap*, *Evidence* y *Background*. Estos artículos citantes están excluidos de ambos test sets, así que pueden usarse libremente. Entre paréntesis, la votación del equipo en la calibración (7 votantes). "También aceptable" indica una segunda lectura defendible.

Las respuestas de los ítems E son las de la referencia adjudicada de eval60; las de los ítems C, las de la clave de la calibración, aceptada por el equipo.

*Un ejemplo entre trabajos relacionados = Background*

* **C29** "this setting falls in the realm of unsupervised domain adaptation (UDA) for semantic segmentation [CITE], …, [[TARGET]], [CITE], …" → **Background**. La cita objetivo es uno de muchos trabajos que nombran el área. (Background 4, Comparison 1, Gap 1, Application 1.)
* **C16** "Unlike mainstream self-supervised feature learning work that crafts pretext tasks … (e.g., colorization [[TARGET]], jigsaw puzzles [CITE], …), in interaction-based feature learning …" → **Background** (R-CMP). Lo que se contrasta es una familia de trabajos en bloque; la cita objetivo es un ejemplo de esa familia, no se compara con el trabajo actual. (Comparison 3, Further Reading 2, Background 1, Application 1.)
* **E45** "all trained on FlyingChairs [CITE] and FlyingThings [[TARGET]]" → **Background** (R-CMP). Un dataset mencionado solo como detalle de los trabajos comparados.

*Gap vs. Evidence vs. Background*

* **C04** "The state-of-the-art facial alignment architectures [[TARGET]][CITE][CITE] have not been developed for the purpose of real-time inference on resource restricted devices." → **Gap**. La cita va justo después de los trabajos que tienen la limitación. (Gap 5, Background 2.)
* **C21** "Although CycleGAN [[TARGET]] is a popular architecture …, it still suffers from large chromatic aberration, severe artifacts and model redundancy …" → **Gap**. La limitación se enuncia sobre la cita objetivo misma. (Gap 3, Improvement/Modification 2, Background 1, Basis 1.)
* **E52** "Recently, significant progress was achieved, see for example [CITE], [[TARGET]], [CITE]. Nevertheless, these methods require a large amount of supervision." → **Gap** (R0 + R-GAP). "These methods" se refiere de vuelta a la lista que contiene la cita objetivo.
* **C30** "However, these methods cannot apply to our cross-domain VMR task because … many UDA methods aim to tackle classification-based tasks (e.g., … object detection [[TARGET]] …)" → **Gap**; también aceptable *Background*. La limitación se enuncia sobre los métodos UDA, entre los que está la cita objetivo. (Background 3, Gap 3, Comparison 1.)
* **C27** "However, PatchMatch inherently has a limited regularization power as shown in [[TARGET]], [CITE], [CITE]." → **Evidence**. La limitación es de PatchMatch; la cita objetivo es la fuente que la muestra ("as shown in"), no quien la tiene. (Evidence 5, Gap 2.)
* **C20** "However, it has been shown that the dependencies between scheduled UEs in the uplink and their associated BSs are very weak [[TARGET]]." → **Evidence**. Al final de una afirmación que los autores usan. (Evidence 4, Gap 2, Comparison 1.)
* **C10** "For the pancreas, however, …, the accuracy is still relatively low compared with other organs [CITE], …, [[TARGET]]." → **Evidence**; también aceptable *Gap*. Por la prueba de posición, la lista cierra la afirmación y es su fuente. "Compared with other organs" compara órganos, no trabajos: no es *Comparison*. (Comparison 3, Gap 2, Evidence 1, Background 1.)
* **C12** "Despite significant progress in the previous works [CITE], …, [[TARGET]] for virtual try-on, the multi-pose virtual try-on has not been well studied." → **Background**; también aceptable *Gap*. El vacío es del campo (multi-pose no estudiado); no se atribuye una limitación a los trabajos listados. (Gap 3, Background 3, Evidence 1.)

*Application vs. Improvement/Modification vs. Basis*

* **C05** "… our approach implicitly reconstructs the human geometry and appearance via generalizing Neural Radiance Fields (NeRF) [[TARGET]] …" → **Improvement/Modification**; también aceptable *Basis*. "Generalizing" extiende el método de la cita objetivo, aunque no se detalle el cambio (R-IM). (Application 4, Improvement/Modification 3.)
* **C28** "Thus, we adapt the representation from [[TARGET]] and utilize the beam id …" → **Improvement/Modification**. (7 de 7.)
* **C09** "Each layer will take the output of the previous layer and the original input of the dense block as inputs, following [[TARGET]]." → **Application**; también aceptable *Basis*. La conectividad densa se usa tal cual. (Application 5.) Nota: "following [x]" es una pista de *Basis* en la regla Background vs. Basis cuando el trabajo actual sigue al citado como fundamento ("Following [x], we develop…"); aquí lo que sigue es un detalle que se usa sin cambios, por eso *Application* (R-IM).
* **E40** "we modify the stem of ResNet-101 as in [[TARGET]]" → **Application** (R-IM). La modificación la introdujo el propio citado.
* **C23** "Inspired by [[TARGET]], we extend the DC-CNN framework into a stochastic DC-CNN …" → **Basis**; también aceptable *Improvement/Modification*. Lo que se extiende es DC-CNN, no la cita objetivo; esta es la fuente de la idea. (Basis 5, Originator 2.)

*Identification of the Originator*

* **C13** "PSPNet [[TARGET]] was the first to employ a pyramid pooling module …" → **Identification of the Originator**. (7 de 7.)
* **C24** "As pioneers, Liu et al. [[TARGET]] introduced a pseudo size updating scheme …" → **Identification of the Originator**. (7 de 7.)
* **C18** "In contrast to previous methods called image-specific perturbations …, Moosavi-Dezfooli et al. [[TARGET]] proposed an image-agnostic perturbation termed universal adversarial perturbation (UAP) …" → **Identification of the Originator**; también aceptable *Comparison*. Se le acredita haber propuesto un nuevo tipo de perturbación y su término; el contraste con métodos anteriores es secundario. (Comparison 5, Evidence 1.)

*Comparison*

* **C19** "we compare our DIS5K against 19 other related datasets including: … SOC [[TARGET]] …" → **Comparison**: trabajo actual vs. cita objetivo. (Comparison 5, Background 1.)
* **C06** "[CITE] propose SharpMask … (resulting in an overall architecture similar to U-Net as developed by [[TARGET]])" → **Comparison** entre trabajos citados. (Comparison 4, Basis 3.)

*Further Reading*

* **C03** "For a detailed survey we refer the reader to [[TARGET]], [CITE], [CITE], while here we provide a brief literature review." → **Further Reading**. (Further Reading 6, Background 1.)
* **E17** "a major concern has grown about the quality of these datasets …, see, e.g., [CITE], [CITE], [[TARGET]]" → **Evidence** (R-FR). El puntero acompaña una afirmación que la cita respalda: Evidence principal.
* **E42** "note the connection of this work with recent results on …, see [CITE], [[TARGET]]" → **Comparison**, con *Further Reading* como secundaria (R-FR).

**7\. Plantilla de registro por instancia (A3)**

Para cada instancia validada manualmente, registrar (mismo formato que el contrato de datos, Sección 8):

| Campo | Descripción | Lo llena |
| :---- | :---- | :---- |
| package\_id | Identificador anónimo del paquete, sin relación con el conjunto: `P0001`–`P4320` en la ronda 1, desde `P4321` en la ronda 2 (`K01`–`K27` en calib2) | Precargado |
| annotator\_id | Seudónimo del anotador (p. ej. `G14-01`, `G15-02`) | Precargado |
| pre\_label | Pre-etiqueta de A2 (`primary_pre_label`), visible | Precargado |
| primary\_label | Etiqueta principal: la pre-etiqueta si se confirma, o la etiqueta corregida (una de las 9 o `unclear`); obligatoria | Anotador |
| secondary\_labels | Cero o más de las 9 etiquetas, distintas de la principal; solo en casos extremos (Sección 9); la hoja admite hasta dos | Anotador |
| confidence | Alta / Media / Baja (juicio del anotador), en todas las filas | Anotador |
| notes | Texto libre; obligatorio si primary\_label = unclear; recomendado al corregir, con la regla aplicada (p. ej. "R-GAP: fuente, no portador") | Anotador |
| guide\_version | Versión de esta guía vigente al anotar (`rev3.1`) | Precargado |

**Campos que se calculan después, no los llena el anotador:** `pre_label_decision` (`confirma` si primary\_label = pre\_label, si no `corrige`), `inst_key` (identificador canónico: citing\_id, índice de párrafo, span de la cita y cited\_id), `group_key`, `source_team`, `test_set` (A o B), la ronda y la ola del lote, y `role` (`validacion` si el anotador pertenece al grupo dueño del conjunto, `segunda` si pertenece al otro grupo). El campo `reviewed_label` de rev1.4 desaparece: con un solo paso, `primary_label` es a la vez la etiqueta para el κ y la que entra a la adjudicación.

**Para qué sirve la confianza.** No entra en el κ ni en la etiqueta de referencia: ordena la cola de adjudicación y permite analizar el acuerdo por nivel de confianza (Sección 8).

**8\. Acuerdo interanotador**

Por cada instancia del 15% reservado para test set, al menos dos anotadores etiquetan de forma independiente, es decir, sin ver la etiqueta del otro anotador (propuesta, Sección 3). En cada test set, una anotación la hace un integrante del grupo dueño del conjunto (su validación) y la otra un integrante del otro grupo. Cada grupo reporta el κ de su propio conjunto. La meta del objetivo específico 2 es **κ de Cohen ≥ 0,6 sobre la etiqueta principal**, calculado antes de la adjudicación. Reportar:

* **Cohen's Kappa** tomando como los dos evaluadores la anotación de cada grupo. Como cada rol lo ocupan varias personas, reportar también **Krippendorff's Alpha** (nominal), que no supone evaluadores fijos.
* **Krippendorff's Alpha con distancia MASI** sobre etiqueta principal + secundarias, como análisis complementario del esquema multi-etiqueta.
* El acuerdo **global** y **desglosado por categoría**, para identificar qué funciones de cita son más ambiguas o generan más desacuerdo (alimenta el análisis de matrices de confusión en A6).
* La **tasa de corrección de la pre-etiqueta de A2**, por categoría, por conjunto y por anotador: la fracción de anotaciones que cambian la pre-etiqueta. Primer resultado (ola 1 de la ronda 1, 880 anotaciones): 8,1% (calib2: 7,4%).

**Pre-etiqueta visible: cómo se reporta.** Ambos anotadores ven la misma pre-etiqueta, así que parte del acuerdo puede venir de confirmarla. El informe debe decirlo explícitamente y presentar el κ junto con la tasa de corrección: una tasa cercana a cero en una categoría indicaría confirmación automática más que revisión.

**Adjudicación (resumen; detalle en el contrato de datos, Sección 9).** Si las dos etiquetas principales coinciden, esa es la etiqueta de referencia. Si difieren, o si alguna es `unclear`, decide una pareja asignada por paquete: un integrante de cada grupo que no haya anotado ese paquete y que no sea el responsable del muestreo, en sesión conjunta y con esta guía como criterio. La pareja ve las dos etiquetas, las confianzas y las notas; la confianza ordena los casos, pero no decide. Sin acuerdo, la instancia queda `unclear` y sale del test set.

El mismo principio de acuerdo (Kappa/Alpha) aplica al esquema de jueces automático de A2 — ver Sección 9.

**9\. Pendiente de definir por el equipo de acuerdo con el avance del proyecto**

* \[x\] **Single-label vs. multi-label.** Tanto el pre-etiquetado automático (A2) como la validación manual (A3) asignan una etiqueta principal por instancia. Solo en casos extremos, cuando otra función también aplica con claridad, el juez o el anotador puede registrar una o más etiquetas secundarias (Sección 6); en la gran mayoría de instancias no habrá etiquetas secundarias.
* \[x\] **Umbral de acuerdo entre jueces (A2).** Decidido: acuerdo de al menos 2 de 3 modelos como filtro de "alta confianza" para que una pre-etiqueta pase al pool de candidatos (contrato de datos, Sección 4); Fleiss' Kappa o Krippendorff's Alpha se reporta como estadístico resumen del acuerdo entre jueces, no como el filtro de selección en sí.
* \[x\] **Margen de sobre-muestreo.** Decidido: 330 paquetes activos por categoría y conjunto, 30 de reserva y un segundo colchón de 30. Revisado el 23 de octubre de 2026 (reserva diferida): la reserva se libera por déficit, cuando la ronda está anotada y adjudicada, y no por la tasa de corrección de la ola 1 (contrato de datos, Sección 6.4).
* \[x\] **Anotación en un paso.** Decidido (14 de octubre de 2026): pre-etiqueta visible desde el inicio, confirmar o corregir, como pide A3; sin paso ciego (Secciones 2 y 7).
* \[x\] **Reglas rev1.1 aprobadas por los grupos 14 y 15** (Sección 6), con R-FR "primero el contenido".
* \[x\] **Calibración.** eval60 (60 instancias, κ de Cohen 0,63 entre grupos, con la guía rev1.4), calib30 (30 instancias, κ de Fleiss 0,47 entre 7 anotadores, con las reglas rev1.1 recién escritas) y **calib2** con esta guía (27 instancias, 3 por categoría, pre-etiqueta visible, anotación individual): **superada el 18 de octubre de 2026**, 16 de 16 parejas G14–G15 con κ de Cohen ≥ 0,6 (mínimo 0,71, media 0,87), κ de Fleiss 0,87. Los tres ítems divididos (K04, K23, K13) caen en las fronteras de las reglas 10, 3 y 5, y 4. Las instancias de calibración y todos sus artículos citantes quedan fuera de ambos test sets y del entrenamiento de ambos grupos.
* \[x\] **Anotación en curso.** Ronda 1 muestreada el 18 de octubre; ola 1 completa el 21 de octubre; ola 2 en curso. Ronda 2 tras la extensión del corpus (22 de octubre).

**10\. Referencias:**

Peters, M. (2024, March 24). *Mastering Fleiss' Kappa: Your ultimate guide to inter-rater reliability*. Medium. [https://blog.mirkopeters.com/mastering-fleiss-kappa-your-ultimate-guide-to-inter-rater-reliability-f19e604bea9a](https://blog.mirkopeters.com/mastering-fleiss-kappa-your-ultimate-guide-to-inter-rater-reliability-f19e604bea9a)

Lyu, D., Ruan, X., Xie, J., & Cheng, Y. (2021). The classification of citing motivations: a meta-synthesis. *Scientometrics, 126*, 3243–3264. [https://doi.org/10.1007/s11192-021-03908-z](https://doi.org/10.1007/s11192-021-03908-z)

Zapf, A., Castell, S., Morawietz, L. *et al.* Measuring inter-rater reliability for nominal data – which coefficients and confidence intervals are appropriate? *BMC Med Res Methodol* **16**, 93 (2016). [https://doi.org/10.1186/s12874-016-0200-9](https://doi.org/10.1186/s12874-016-0200-9)

**Registro de cambios**

| Versión | Fecha | Cambio |
| :---- | :---- | :---- |
| rev3.1 (act.) | 23 oct 2026 | Sin cambios en las reglas ni en las definiciones. Versión: nota de la actualización. Sección 2: dos rondas; Hoja de instrucciones como resumen operativo (manda la guía). Sección 5: `citation_group_id` → `group_key`. Sección 6.1: nota en C09 sobre "following [x]" (pista de Basis en la regla 3; Application cuando lo que sigue se usa tal cual). Sección 7: rangos de `package_id`, hasta dos secundarias en la hoja, confianza en todas las filas, `source_team`, ronda y ola entre los campos calculados; uso de la confianza. Sección 8: primer resultado de la tasa de corrección; resumen de la adjudicación por parejas asignadas por paquete. Sección 9: calib2 superada; margen de sobre-muestreo con reserva diferida y segundo colchón; anotación en curso. |
| rev3.1 | 17 oct 2026 | Une la rev3 y la revisión del equipo sobre la rev2 (en ambas: sin submuestra sin pre-etiqueta y sin marca de inconsistencia dentro de un grupo de citas). Se conserva la Sección 5 (citas agrupadas), sin el punto sobre la marca `group_inconsistency`, y la regla "Citas agrupadas" (regla 7 del prompt). Sección 2: marcadores en inglés (`[[TARGET]]`, `[CITE]`, `[FORMULA]`, `[FIGURE]`, `[TABLE]`), los mismos que leen los jueces y que muestran las hojas de anotación; nueva fila para `group_siblings`. Secciones 6 y 6.1: marcadores en inglés en R0 y en los ejemplos. "Una etiqueta principal": sin la nota sobre modelado. Sección 7: `guide_version` = `rev3.1`; secundarias solo en casos extremos; `group_key` se mantiene entre los campos calculados (lo usan el muestreo y la lista de exclusión). Sección 9: "Single-label vs. multi-label" resuelto (una etiqueta principal; secundarias solo en casos extremos); segunda calibración de 27 instancias (3 por categoría). |
| rev3 | 17 oct 2026 (borrador) | Sin submuestra sin pre-etiqueta: todos los paquetes muestran la pre-etiqueta y su efecto se reporta con la tasa de corrección (Secciones 7, 8 y 9; se elimina `pre_label_shown`). Sin marca `group_inconsistency` ni revisión de inconsistencias dentro de un grupo de citas: cada cita se etiqueta por lo que el texto dice de ella (Secciones 5, 7 y 9); `notes` es obligatorio solo con `unclear`. |
| rev2 | 15 oct 2026 (borrador) | Corresponde al prompt v11.1 de A2, congelado. Sección 6 reescrita con las reglas rev1.1, alineadas regla por regla con el prompt: R0 (alcance de lectura: solo la oración de la cita objetivo y las que se refieren de vuelta a ella), R-CMP (familia contrastada en bloque y detalle de dataset = Background), R-IM (adaptar, extender o modificar = Improvement/Modification aunque no se describa el cambio; "modify X as in [x]" = Application), R-EV (la fuente que reporta la limitación de otro método = Evidence), R-GAP (limitación enunciada sobre una lista = Gap para cada miembro; prueba de posición), R-FR (primero el contenido), orden de lectura rápido. Nueva Sección 6.1 con ejemplos resueltos de calib30 y eval60. Sección 2: anotación en un paso (pre-etiqueta visible, confirmar o corregir), *chunks* plegados. Sección 4 reescrita: cuándo abrir los *chunks*. Sección 7: sin `reviewed_label`; `pre_label_decision` se calcula. Sección 8: tasa de corrección, reporte con pre-etiqueta visible y submuestra de hasta 30 instancias sin pre-etiqueta. Sección 9: reglas rev1.1 aprobadas (R-FR "primero el contenido"), estado de la calibración y segunda calibración. Grupos 14 y 15. |
| rev1.4 | 2 oct 2026 | Sección 6, tras las pruebas de los prompts v5 a v8 de A2 (20 y 300 instancias, 3 jueces): nueva regla "solo la cita objetivo" (leer primero su oración; lo atribuido a otra cita no cuenta); Comparison vs. Background exige comparar trabajos entre sí (agrupar o listar bajo una propiedad compartida es Background); Evidence vs. Background: lista de lo que no es Evidence (describir un trabajo citado, afirmaciones sobre trabajos previos aunque vayan tras "e.g.", datasets o herramientas usados por el trabajo actual = Application, nombrados como contexto = Background); nueva regla de Gap (solo el trabajo cuya limitación se enuncia). Corresponde al prompt v8 de A2, congelado. |
| rev1.3 | 2 oct 2026 | Sección 3.1: aclaraciones de Lyu et al. (2021) ampliadas a las 9 categorías (los cinco tipos de Evidence, incluida la definición de conceptos y la ejemplificación con "e.g."; historia y *state of the art* en Background; vínculos entre trabajos citados en Comparison; homenaje, prioridad y deuda intelectual en Originator; los tres propósitos de Further Reading, no solo "see"). Sección 6: Comparison vs. Background reformulada (vincular trabajos citados por un método, hallazgo u opinión cuenta como Comparison); nueva regla de postura evaluativa (el elogio de tipo originador no es postura; la crítica que motiva el trabajo es Gap o Comparison); nueva regla historia vs. originador; nueva regla Evidence vs. Background (qué respalda la cita). Corresponde al prompt v5 de A2. |
| rev1.2 | 30 sep 2026 | Sección 3.1: aclaraciones de Lyu et al. (2021); la de *Evidence* queda para la calibración. Secciones 2, 5, 7 y 8: dos test sets disjuntos, uno por grupo; la segunda anotación de cada conjunto la hace el otro grupo. Sección 6: criterios prácticos para Background vs. Basis y Comparison vs. Background (una cita listada entre trabajos relacionados es Background; Basis exige que el texto indique que el trabajo se construye sobre el citado; Comparison exige un contraste explícito). Secciones 2, 7 y 8: anotación en dos pasos (etiqueta ciega para el κ; confirmación o corrección de la pre-etiqueta para el *gold*). |
| rev1.1 | 27 sep 2026 | Alineada con el contrato de datos compartido y con los datos limpios v1. |
