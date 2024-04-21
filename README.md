
<h2 align="center" style="margin-bottom: 0;">Proyecto Final de Estadística <br> Análisis Predictivo de Rendimiento en Codeforces</h2>
<h3 align="center" style="margin-bottom: 2px;">Integrantes: </h3>

<p align="center">
- Ariel González Gómez C312 <br>
- Alex Samuel Bas Beovides C312 <br>
- Dario Rodríguez Llosa C312
</p>

## Introducción

El objetivo de este proyecto es construir un modelo de regresión lineal utilizando los datos de una muetra de usuarios activos de la plataforma de programación competitiva Codeforces, para predecir el **rating change** que experimentarán dentro de PREDICTION_DAYS días.

**Requisitos para incluir un usuario al conjunto de datos**:
- La cuenta de Codeforces fue registrada hace al menos PREDICTION_DAYS días, en los que el usuario haya participado en al menos REQUIRED_RATED_CONTESTS concursos reales.
- El usuario ha participado en al menos STABILIZATION_CONTESTS + REQUIRED_PREV_Rated_CONTESTS concursos antes de los últimos PREDICTION_DAYS días
- El usuario tiene al menos MIN_RATING_REQUIRED puntos de **rating**
- El **handle** del usuario no está en OUTLIERS

**Metaparámetros**:
- PREDICTION_DAYS = 60
- STABILIZATION_CONTESTS = 6
- MIN_RATING_REQUIRED = 1200
- REQUIRED_RATED_CONTESTS_DIV1 = 3
- REQUIRED_RATED_CONTESTS_NOT_DIV1 = 5
- REQUIRED_PREV_RATED_CONTESTS = 4
- DAYS_FOR_LAST_SOLVED = 30
- PROBLEM_SINGLE_PONDERATION_FUNCTION(x) = x ^ (3/2)
- PROBLEM_LIST_PONDERATION_FUNCTION([r_1, r_2, ..., r_n]) = sum (PROBLEM_SINGLE_PONDERATION_FUNCTION(r_i))
- INTERESTING_PROBLEM_SINGLE_PONDERATION_FUNCTION(x) = (x + 10) ^ (3/2)
- INTERESTING_PROBLEM_LIST_PONDERATION_FUNCTION([r_1, r_2, ..., r_n]) = sum (PROBLEM_SINGLE_PONDERATION_FUNCTION(r_i))
- INTERESTING_RANGE_L = -300
- INTERESTING_RANGE_R = +500
- USEFUL_RANGE_L = -800
* Si la calificación del usuario cuando se resolvió el problema es r, el problema es interesante si su calificación está en el rango [r + INTERESTING_RANGE_L, r + INTERESTING_RANGE_R]
- OUTLIERS = [rainboy, ruban, PAG, kaiboy]

**Características de entrada (variables independientes)** [información del usuario, anterior a los últimos PREDICTION_DAYS días]:
   - `rating`: rating actual.
   - `maxRating`: rating máximo alcanzado.
   - `daysSinceRegistered`: número de días transcurridos desde que se registró la cuenta en Codeforces.
   - `nRatedContestParticipations`: número de participaciones en concursos reales.
   - `nSolvedProblems`: cantidad de problemas distintos resueltos.
   - `nLastSolvedProblems`: cantidad de problemas distintos resueltos en los últimos DAYS_FOR_LAST_SOLVED días.
   - `solvedProblemsPonderation`: PROBLEM_LIST_PONDERATION_FUNCTION(L) (donde L es una lista de las dificultades de todos los problemas distintos resueltos).
   - `lastSolvedProblemsPonderation`: PROBLEM_LIST_PONDERATION_FUNCTION(L) (donde L es una lista de las dificultades de los problemas distintos resueltos en los últimos DAYS_FOR_LAST_SOLVED días).
   - `solvedInterestingProblemsPonderation`: INTERESTING_PROBLEM_LIST_PONDERATION_FUNCTION(L) (donde L es una lista de las dificultades de todos los problemas interesantes distintos resueltos).
   - `lastSolvedInterestingProblemsPonderation`: INTERESTING_PROBLEM_LIST_PONDERATION_FUNCTION(L) (donde L es una lista de las dificultades de los problemas interesantes distintos resueltos en los últimos DAYS_FOR_LAST_SOLVED días).
   - `contancy`: medida de la constancia en la actividad del usuario en la plataforma.
   - `solvedProblemsMean`: promedio de la dificultad de los problemas resueltos.
   - `lastSolvedProblemsMean`: promedio de la dificultad de los problemas resueltos en los últimos DAYS_FOR_LAST_SOLVED días.
   - De los últimos REQUIRED_PREV_RATED_CONTESTS concursos reales en los que participó el usuario:
     - `ratingChangeNthLatestContest`: cambio de rating que tuvo el usuario en el n-ésimo último contest real (n=1: último, n=2: penúltimo, n=3: antepenúltimo...)

**Variable de salida**: `futureRating`(implica predecir el rating que tendrá un usuario dentro de PREDICTION_DAYS días [en el caso de los usuarios del dataset esta es la calificación extraída], suponiendo que en ese tiempo participará en al menos REQUIRED_Rated_CONTESTS concursos reales)
  