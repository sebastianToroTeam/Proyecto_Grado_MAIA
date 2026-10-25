# Plan de trabajo para la elaboración del informe de avance

**Proyecto:** Recuperación semántica y clasificación de la función de citas académicas en Computer Science.

**Autores consignados en la propuesta:** Camilo Bejarano, German Rodriguez, Jose Arteaga y Sebastian Toro.

**Estado de este documento:** plan de redacción con precisiones del usuario incorporadas el 23 de octubre de 2026. El entregable es el informe de avance del grupo 15 (T_B), con corte al 23 de octubre. El documento IEEE corresponde al mismo proyecto; la guía vigente es la ubicada en la carpeta Folder_trabajo_Equipos_14_y_15. El informe desarrolla los cinco apartados y conserva como actividades en proceso los hitos sin cierre documental acreditado.

## Comprensión del encargo y alcance documental

El informe se elaborará siguiendo los cinco apartados de [Formato del informe de avance.md](<Formato del informe de avance.md>), con título, autores y referencias. Su extensión final deberá ajustarse al máximo de ocho páginas establecido en el formato; este requisito se comprobará en la versión paginada, puesto que Markdown no determina por sí mismo una extensión en páginas.

La propuesta plantea recuperar evidencia del artículo citado y clasificar la función de una cita en inglés dentro del dominio de Computer Science. El sistema contempla nueve categorías: Background, Gap, Basis, Comparison, Application, Improvement/Modification, Evidence, Identification of the Originator y Further Reading.

La documentación revisada permite organizar el avance alrededor de F1 —extracción, segmentación y recuperación—, F2 —pre-etiquetado asistido por jueces— y F3 —construcción del conjunto de datos y validación humana—. Existen evidencias de ejecución de F1 y F2 y de ejecución parcial de F3. Esto no equivale a acreditar el cierre integral de las tres fases ni el cumplimiento de todas sus metas de evaluación.

La imagen compartida orientará la organización del primer apartado: presentación de la solución, motivación y delimitación del avance. Sus cifras, tareas y resultados pertenecen a otro proyecto y no se incorporarán al contenido de este informe.

## 1. Descripción breve de la propuesta de solución

**Propósito de la sección.** Exponer el problema de investigación, la solución propuesta y el alcance del trabajo documentado.

**Plan de redacción.** Se preparará una síntesis en tres párrafos. El primero presentará el problema de caracterizar la función de las citas más allá de su conteo. El segundo describirá los dos componentes de la solución: recuperación de los tres fragmentos más relevantes del artículo citado mediante SciBERT y clasificación de la función de la cita en nueve categorías. El tercero delimitará el avance, con énfasis en la preparación de datos, la consolidación del sistema de jueces y la validación humana en curso.

Se distinguirá el objetivo general del sistema de los componentes cuya ejecución está documentada. El ajuste supervisado de SciBERT, la evaluación comparativa y el despliegue se describirán como actividades previstas cuando no exista evidencia de su realización. El Macro F1 de 0,68 mencionado en la propuesta se identificará, si resulta pertinente incluirlo, como antecedente del microproyecto CiteScope.

**Fuentes.** [PropuestaProyectoGrado.md](<PropuestaProyectoGrado.md>), secciones 1, 2, 4 y 7; contrato de datos y presentaciones de seguimiento de los grupos 14 y 15.

**Resultado previsto.** Una descripción breve y coherente con la propuesta, que sitúe al lector en el problema y precise qué parte de la solución se ha desarrollado.

## 2. Recolección y preparación de los datos

**Propósito de la sección.** Documentar la procedencia, selección, transformación y trazabilidad de los datos utilizados.

**Plan de redacción.** Se reconstruirá el proceso desde unarXive 2022 hasta el conjunto de instancias disponible para pre-etiquetado y anotación. Se describirán la selección del dominio, la resolución del vínculo citante–citado, la disponibilidad del texto completo, la segmentación, la limpieza, los controles de longitud y la recuperación Top-3. Se explicará que la presencia del artículo citado en el corpus no implica que ambos artículos tengan los mismos autores.

Se presentarán por separado las etapas y sus unidades de conteo: artículos, contextos, pares, fragmentos e instancias pre-etiquetadas. La ampliación a cs.LG, cs.CL y cs.DS se ubicará como una extensión posterior del corpus de trabajo. Se documentarán las versiones del conjunto de pre-etiquetas: 29.336 instancias en la versión congelada de la primera ronda y 38.780 en la versión ampliada, según el contrato del 23 de octubre.

También se explicarán las claves de identificación, el tratamiento de citas agrupadas, la prevención de fuga de información y la conservación de localizadores para reconstruir el texto. El tratamiento de licencias se describirá conforme a la documentación: distribución de identificadores y mecanismos de reconstrucción, y selección de artículos con licencias permisivas para el demostrador.

**Fuentes.** Propuesta, sección 5; [contrato de datos del 23 de octubre](<Folder_trabajo_Equipos_14_y_15/Contrato_Datos_A3_2026-10-23_guia_rev3.1.md>), secciones 2–6 y 10; [A3 Pool Extension for Round 2.pptx](<Folder_trabajo_Equipos_14_y_15/A3 Pool Extension for Round 2.pptx>). El documento IEEE se incorporará como evidencia de ejecución únicamente si el usuario confirma que corresponde al proyecto.

**Resultado previsto.** Una descripción reproducible del flujo de preparación, acompañada de una tabla compacta de etapas, cifras, unidades y fuentes.

## 3. Descripción del proceso de construcción de los modelos o solución

**Propósito de la sección.** Explicar cómo se construyeron los componentes ejecutados y cómo se articularon con la anotación humana. Este apartado recibirá el mayor desarrollo, de acuerdo con su ponderación del 35 % en la evaluación.

**Plan de redacción.** La explicación seguirá la secuencia F1–F3. Para F1 se describirán la representación con SciBERT, la similitud coseno, la selección Top-3 y la conservación de la sección de origen. Los parámetros propuestos y los procedimientos efectivamente documentados se identificarán de manera explícita.

Para F2 se reconstruirá la evolución de los criterios de clasificación hasta la configuración congelada del prompt v11.1. Se describirán los tres jueces consignados en el contrato, el voto de las etiquetas principales, el criterio de al menos dos votos coincidentes y el tratamiento de empates, respuestas inválidas y evidencia ajena al contexto citante. Las denominaciones de modelos y versiones se transcribirán de las fuentes proporcionadas.

Para F3 se explicarán la guía de anotación, las calibraciones, los dos conjuntos de prueba disjuntos, el muestreo, los paquetes idénticos para ambos anotadores y el procedimiento independiente de confirmación o corrección con pre-etiqueta visible. Se distinguirán la preparación del protocolo, la anotación realizada y la adjudicación pendiente. El sistema de jueces se describirá como pre-etiquetado por inferencia; el entrenamiento supervisado se reportará solo si está acreditado.

**Fuentes.** Propuesta, sección 4; contrato, secciones 4–10; guía rev3.1 de la carpeta de trabajo, actualizada el 23 de octubre; presentaciones sobre prompt v11, configuración v11.1, calibración y primera ronda de A3; hoja de instrucciones de anotación.

**Resultado previsto.** Una explicación metodológica sustentada en los artefactos compartidos, con una tabla de estado por fase y, si aporta claridad, un diagrama del flujo documentado.

## 4. Resultados obtenidos

**Propósito de la sección.** Presentar los resultados disponibles con identificación de la muestra, la métrica, el procedimiento y la fuente.

**Plan de redacción.** Se organizarán los resultados en tres bloques: disponibilidad y distribución de datos; evaluación del pre-etiquetado; y calibración y avance de la anotación humana. Se comprobará la correspondencia de las cifras entre presentaciones, hojas de cálculo y versiones documentales.

Entre los resultados candidatos a inclusión están los siguientes:

| Resultado documentado | Fuente principal | Precisión necesaria para reportarlo |
| --- | --- | --- |
| 38.780 instancias y 37.254 pre-etiquetas de alta confianza en el conjunto ampliado | A2 Pool Results and A3 Plan, diapositivas 2 y 6 | La alta confianza expresa acuerdo entre jueces; su exactitud requiere una referencia humana. |
| 48 aciertos sobre 60 instancias adjudicadas para el trío con prompt v11.1 | Presentación sobre configuración congelada, diapositiva 5 | eval60 se utilizó para desarrollar las reglas; se reportará como evaluación de desarrollo. |
| κ de Cohen de 0,63 entre grupos en eval60 | Contrato, sección 7 | Una etiqueta por grupo sobre 60 instancias. |
| κ de Fleiss de 0,47 en calib30 | Contrato, sección 7; libro de acuerdo de calibración | Siete anotadores, con un protocolo distinto del aplicado en calib2. |
| κ de Fleiss de 0,87 y las 16 parejas entre grupos por encima de 0,6 en calib2 | Contrato, sección 7; presentación de calib2 | Ocho anotadores, 27 instancias y pre-etiqueta visible. |
| Primera ola: 880 anotaciones completas y tasa de corrección del 8,1 % | Contrato, secciones 7 y 9; A2 Pool Results and A3 Plan, diapositiva 12 | Corresponde a 440 paquetes con dos anotaciones cada uno; constituye un avance parcial. |

Las cifras procedentes de presentaciones se atribuirán a esos documentos. Solo se describirán como verificadas mediante recálculo cuando los archivos suministrados contengan las observaciones necesarias. Se propondrán una tabla de métricas y una visualización de disponibilidad por categoría, siempre que puedan sustentarse con los datos compartidos.

**Fuentes.** Presentaciones de resultados; contrato; libros de evaluación y calibración; referencia adjudicada de eval60. Los resultados futuros de F1 macro, F1 micro y P@3 conservarán su condición de métricas previstas si no hay resultados documentados.

**Resultado previsto.** Una presentación trazable del avance cuantitativo, con separación entre resultados de desarrollo, resultados parciales y metas pendientes.

## 5. Análisis de los resultados obtenidos. Reflexión sobre los retos encontrados y cómo proseguir

**Propósito de la sección.** Interpretar los resultados, discutir sus límites y establecer la continuidad del proyecto a partir de decisiones documentadas.

**Plan de redacción.** Se analizarán el desbalance de categorías, las fronteras ambiguas entre funciones, la calidad del texto recuperado, la carga de anotación y el aislamiento entre particiones. Cada reto se vinculará con la evidencia disponible y con la medida adoptada o prevista: ampliación del corpus, muestreo dirigido, reglas de decisión, calibración, adjudicación, reservas y variantes de exclusión.

Se examinará el aumento del acuerdo en calib2 considerando simultáneamente los cambios de guía, muestra y protocolo, incluida la visibilidad de la pre-etiqueta. No se atribuirá ese aumento a una causa única. Los ensayos con y sin fragmentos recuperados se discutirán dentro del alcance del pre-etiquetado con jueces; su resultado no permitirá anticipar el efecto del Top-3 sobre el clasificador supervisado.

Los próximos pasos se tomarán del contrato y las presentaciones vigentes para la fecha de corte: completar anotaciones, adjudicar desacuerdos, cubrir déficits por categoría, consolidar referencias humanas, elegir la variante de exclusión y continuar con las fases de modelado y evaluación previstas en la propuesta. Las fechas propuestas conservarán esa condición.

**Fuentes.** Contrato, secciones 6 y 9–12; guía actualizada; presentaciones de calibración, ampliación del corpus y planificación de A3; propuesta, secciones 4–7.

**Resultado previsto.** Una discusión académica que relacione resultados, limitaciones y continuidad metodológica sin presentar expectativas como logros.

## Precisiones resueltas y tratamiento de diferencias documentales

| Precisión | Respuesta del usuario o tratamiento aplicado | Apartados afectados |
| --- | --- | --- |
| Grupo autor | El usuario confirmó el grupo 15, cuyo conjunto de prueba es T_B. Los resultados conjuntos se identifican como tales. | 1–5 |
| Documento IEEE | El usuario confirmó que documenta resultados del mismo proyecto. Se incorpora como evidencia del perfilamiento y preparación inicial. | 2 y 3 |
| Fecha de corte | El usuario fijó el 23 de octubre de 2026 y aclaró que algunas actividades continúan en proceso. | 1, 4 y 5 |
| Guía vigente | La copia de la carpeta de trabajo es la versión más reciente. La copia de la raíz se utiliza únicamente para comparar la evolución documental. | 2–5 |
| Estado de la segunda ronda | Se explicita la diferencia entre contrato y presentación del 23 de octubre. Se reporta preparación en proceso con suministro suficiente, sin afirmar el cierre del congelamiento. | 2–5 |

## Criterios de elaboración y revisión

Se utilizará exclusivamente la documentación compartida. Las referencias bibliográficas se tomarán de las fuentes suministradas, sin añadir bibliografía externa. Cada cifra conservará su unidad, muestra y versión de origen; las discrepancias no resueltas se consultarán.

Las dos copias de la guía rev3.1 presentan diferencias: la de la carpeta de trabajo incluye una actualización del 23 de octubre, resultados de calib2 y avances de anotación ausentes en la copia de la raíz. Para el estado del proceso se utilizará la versión actualizada, conservando la distinción temporal entre documentos.

La revisión final comprobará que estén desarrollados los cinco apartados, que todas las afirmaciones tengan respaldo documental, que las actividades ejecutadas se distingan de las previstas y que las tablas concuerden con el texto. Se entregará el archivo **informe de avance.md**, con redacción académica, referencias y extensión adecuada al límite establecido.
