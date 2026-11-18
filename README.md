# Resultados tipo Saber 11 en Antioquia (2025)

**Proyecto de Introducción a la Programación Científica**  
**Estudiante:** Juan Diego Pasos Gómez  
**Notebook:** `notebooks/analisis.ipynb`

## ¿De qué trata este trabajo?

En este trabajo reviso los resultados de 3.250 estudiantes que presentaron una prueba tipo Saber 11 en distintos municipios de Antioquia durante 2025. La idea es comparar sus puntajes y mirar qué características de los estudiantes, sus hogares y sus colegios aparecen relacionadas con esos resultados.

Esto podría servir como punto de partida para pensar dónde hacen falta más apoyos académicos, tutorías o recursos para estudiar. La base no trae el nombre de cada colegio, así que no puedo señalar instituciones específicas. Lo que sí puedo hacer es comparar los resultados por estrato, zona, tipo de colegio y jornada.


## La base que utilicé

El archivo tiene **3.250 filas y 23 columnas**. Cada fila corresponde a un estudiante e incluye datos de su contexto, sus hábitos y los puntajes de las cinco áreas. Para tener claro qué información trae, este es el resumen de las columnas:

| Columna | Tipo | Qué contiene |
|---|---|---|
| `id_estudiante` | Texto | Identificador anónimo del estudiante. |
| `municipio` | Categórica | Municipio donde está el colegio. |
| `zona` | Categórica | Zona urbana o rural. |
| `estrato` | Entero | Estrato de la vivienda, de 1 a 6. |
| `tipo_colegio` | Categórica | Oficial o privado. |
| `jornada` | Categórica | Mañana, tarde, única, completa, nocturna o sabatina. |
| `sexo` | Categórica | F o M. |
| `edad` | Entero | Edad al presentar la prueba. |
| `educacion_madre` | Categórica | Nivel educativo de la madre. |
| `ingreso_familiar_smmlv` | Numérica | Ingreso familiar expresado en salarios mínimos. Se elimina durante la limpieza. |
| `acceso_internet` | Categórica | Si tiene internet en casa. |
| `computador_hogar` | Categórica | Si tiene computador en casa. |
| `trabaja` | Categórica | Si trabaja además de estudiar. |
| `horas_trabajo_semana` | Numérica | Horas que trabaja por semana. Queda vacío cuando no trabaja. |
| `horas_estudio_semana` | Numérica | Horas de estudio por fuera de clase a la semana. |
| `inasistencias_anio` | Numérica | Días que faltó al colegio durante el año. |
| `preparacion_preicfes` | Categórica | Si tomó un curso preicfes. |
| `puntaje_matematicas` | Numérica | Puntaje de matemáticas, de 0 a 100. |
| `puntaje_lectura_critica` | Numérica | Puntaje de lectura crítica, de 0 a 100. |
| `puntaje_ciencias_naturales` | Numérica | Puntaje de ciencias naturales, de 0 a 100. |
| `puntaje_sociales_ciudadanas` | Numérica | Puntaje de sociales y ciudadanas, de 0 a 100. |
| `puntaje_ingles` | Numérica | Puntaje de inglés, de 0 a 100. |
| `puntaje_global` | Entero | Puntaje global, de 0 a 500. |

## Las preguntas que voy a responder

1. ¿Cómo cambia el puntaje global según el estrato, la zona y el tipo de colegio?
2. ¿Tener internet o computador en casa se asocia con mejores resultados?
3. ¿Los estudiantes que trabajan obtienen puntajes más bajos? ¿Depende de cuántas horas trabajan?
4. ¿Qué tan relacionados están los puntajes de las distintas áreas entre sí?
5. ¿Qué jornadas necesitan más apoyo? ¿Quiénes estudian en ellas?

## Qué encontré al revisar los datos

Antes de comparar los puntajes, revisé si había datos vacíos, registros repetidos, categorías escritas de distintas formas o valores que no tuvieran sentido. No encontré filas duplicadas ni IDs repetidos. Tampoco aparecieron códigos como `-999`, valores negativos ni puntajes por fuera de los rangos esperados.

Sí encontré algunas columnas con datos faltantes. No todos esos espacios significan que haya un error, así que revisé cada caso antes de decidir qué hacer:

| Columna | Qué encontré | Qué hice |
|---|---|---|
| `ingreso_familiar_smmlv` | Faltan 1.845 datos, el **56,77 %**. | Quité la columna porque más de la mitad está vacía y no la necesito para responder las preguntas. El estrato permite hacer algunas comparaciones, aunque no reemplaza el dato exacto del ingreso. |
| `horas_trabajo_semana` | Faltan 2.495 datos, el **76,77 %**. Coinciden con quienes dijeron que no trabajan. | Dejé esos espacios como están. Para esos estudiantes las horas de trabajo no aplican y completarlas sería inventar información. |
| `horas_estudio_semana` | Faltan 270 datos. También hay dos registros de 90 horas y dos de 168 horas semanales. | Marqué esos cuatro valores como faltantes y completé los 274 espacios con la mediana según si el estudiante trabaja o no. La mediana fue de 7 horas para quienes no trabajan y 4 para quienes sí. |
| `educacion_madre` | Faltan 344 datos, el **10,58 %**. | Completé los espacios con la categoría que más se repite dentro de cada estrato. La respuesta más común cambia entre estratos, por eso no usé una sola categoría para todos. |
| `inasistencias_anio` | Faltan 227 datos, el **6,98 %**. | Los dejé vacíos. No necesito completar esta columna para responder las cinco preguntas. |
| `puntaje_ingles` | Faltan 122 datos, el **3,75 %**. | Los dejé vacíos. Para calcular las correlaciones que incluyen inglés se usan los estudiantes que sí tienen los dos puntajes de esa pareja. |
| `acceso_internet` | Faltan 92 datos, el **2,83 %**. | No los completé. En la comparación de internet solo cuento a quienes respondieron, porque no sé si los demás tienen el servicio. |
| `edad` | Hay 264 estudiantes de 22 años o más. | Los conservé. Todos están en jornada nocturna o sabatina, así que parecen corresponder a estudiantes adultos y no a errores de digitación. |

El IQR marcó 105 registros de horas de estudio como posibles atípicos. Eso no quiere decir que todos estén mal. Por eso no los cambié en bloque. Me fijé en los valores más extremos: las dos respuestas de 90 horas y las dos de 168 horas semanales. Esos cuatro casos sí los marqué como faltantes antes de completar la columna.

Después de la limpieza quedaron **3.250 filas y 22 columnas**. No eliminé estudiantes, solo la columna de ingresos familiares. Completé **274 valores** de horas de estudio y **344** de educación de la madre. En total fueron **618 celdas**. Los demás faltantes se conservaron porque no aplican o porque no hace falta inventar un valor para poder hacer el análisis.

## Qué muestran los resultados

### 1. Puntaje según estrato, zona y tipo de colegio

Lo primero que se nota es que el promedio sube a medida que aumenta el estrato. Pasa de **232,0 puntos en el estrato 1** a **393,1 en el estrato 6**. También hay diferencia entre las zonas: en la urbana el promedio es de **269,2**, mientras que en la rural es de **227,7**. Al comparar el tipo de colegio, los privados tienen **306,3 puntos** y los oficiales **251,9**.

Cuando cruzo la zona con el tipo de colegio, el promedio más bajo aparece en los colegios oficiales rurales, con **220,2 puntos entre 415 estudiantes**. Esto ayuda a ubicar grupos en los que valdría la pena revisar las necesidades de apoyo. Hay que mirar con cuidado los resultados de los estratos 5 y 6, porque tienen solo 57 y 39 estudiantes. Además, con estos datos no puedo separar por completo qué tanto se relaciona cada característica con el puntaje.

### 2. Internet y computador en casa

Aquí aparece una diferencia clara. Quienes tienen internet en casa obtienen **280,8 puntos** en promedio, mientras que quienes no tienen obtienen **244,6**. La diferencia es de **36,2 puntos**. Con el computador pasa algo parecido: el promedio es de **282,9** para quienes tienen y de **251,2** para quienes no.

En estos datos, tener internet o computador aparece relacionado con mejores resultados. Por eso, revisar quiénes no cuentan con esos recursos puede ayudar a encontrar estudiantes que necesitan apoyo. Esto no quiere decir que tener internet sea la única razón de la diferencia, porque también pueden influir las condiciones del hogar y del colegio.

### 3. Estudiantes que trabajan

Los estudiantes que no trabajan tienen un promedio de **272,0 puntos** y son 2.495. Quienes sí trabajan promedian **232,3 puntos** y son 755. Entre los dos grupos hay una diferencia de **39,7 puntos**.

También revisé la relación entre las horas trabajadas y el puntaje, pero solo para quienes tienen horas registradas. La correlación es de **-0,15**, lo que muestra una relación negativa débil. En general, el puntaje tiende a bajar un poco cuando aumentan las horas de trabajo, pero la relación no es fuerte.

Esto deja una pista para revisar la situación de quienes estudian y trabajan al mismo tiempo. De todas formas, no puedo decir que el trabajo sea la causa de sus puntajes más bajos. También pueden influir la edad y las condiciones familiares.

### 4. Relación entre las áreas

Las correlaciones entre las cinco áreas están entre **0,66 y 0,69**. Todas son positivas y bastante parecidas. Esto quiere decir que, en esta base, los estudiantes que obtienen buenos resultados en un área suelen tener también buenos resultados en las demás.

Por eso, las diferencias no parecen estar concentradas en una sola materia. Podría tener sentido pensar en apoyos académicos generales y, al mismo tiempo, revisar qué necesita cada área. Los 122 puntajes de inglés que faltan no se inventaron. Las correlaciones que incluyen esa materia se calculan con los estudiantes que sí tienen ambos puntajes, mientras que las relaciones entre las otras cuatro áreas usan los 3.250 registros.

### 5. Jornadas y estudiantes que están en ellas

Los promedios más bajos aparecen en las jornadas **sabatina (225,1 puntos)** y **nocturna (230,7)**. Entre las dos reúnen **320 estudiantes** y su promedio conjunto es de **228,9 puntos**. En las demás jornadas el promedio es de **266,5**.

Al revisar quiénes estudian allí, se ve que son principalmente estudiantes adultos. La edad promedio es de **29,7 años en nocturna** y **30,6 en sabatina**. Además, trabaja el **75,2 %** de quienes estudian en nocturna y el **69,8 %** de quienes están en sabatina. Todos los estudiantes de estas dos jornadas pertenecen a colegios oficiales.

Esto ayuda a entender por qué los apoyos para estas jornadas tendrían que considerar los horarios y el hecho de que muchos estudiantes también trabajan. Podrían servir tutorías en horarios flexibles. No puedo decir que la jornada sea la causa de los puntajes más bajos, porque también cambian la edad y la situación laboral de quienes estudian en ella.

## Con qué me quedo de este análisis

Al comparar los grupos, las diferencias más marcadas aparecen por estrato, zona y tipo de colegio. También se ven diferencias entre quienes tienen internet o computador y quienes no, y entre quienes trabajan y quienes no.

Las jornadas nocturna y sabatina llaman especialmente la atención. Tienen los promedios más bajos y reúnen a estudiantes de mayor edad, muchos de los cuales también trabajan. Por eso, serían grupos importantes para tener en cuenta al pensar en tutorías y apoyos con horarios flexibles. La conectividad también aparece como un tema que vale la pena revisar.

Estos resultados sirven para orientar las gráficas de la segunda parte del proyecto. No demuestran que una característica sea la causa de otra y tampoco representan la situación real de Antioquia, porque la base es sintética. Además, algunos grupos tienen pocos estudiantes, así que sus promedios se deben mirar con cuidado.

## Cómo abrir el notebook

El archivo principal es `notebooks/analisis.ipynb`. El CSV debe estar en la carpeta `data/` con el nombre `educacion_saber11_antioquia_2025.csv`.

Si se abre el notebook en Google Colab, hay que subir el CSV a una carpeta llamada `data` en el panel de archivos. Después se puede ejecutar **Entorno de ejecución → Reiniciar y ejecutar todas** para comprobar que todo corre desde el principio.

Para este análisis se utilizan `pandas` y `numpy`, que ya están disponibles en Google Colab.
