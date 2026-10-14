# Análisis experimental de algoritmos

**Proyecto académico individual | INF-221 Algoritmos y Complejidad | UTFSM**
**Autor:** Felipe Gajardo

Implementé y comparé algoritmos de ordenamiento y multiplicación de matrices mediante experimentos automatizados en C++ y Python. El proyecto combina implementación de algoritmos, medición de tiempo y uso de memoria, generación de datos y visualización de resultados; el objetivo fue observar cómo se comportan en la práctica con distintos tamaños y tipos de entrada.

## Qué demuestra este proyecto

- Implementación en C++17 de algoritmos clásicos y sus variantes, incluida una optimización de QuickSort para entradas grandes y con valores repetidos.
- Diseño de programas experimentales que procesan lotes de entradas, guardan resultados y registran mediciones en CSV.
- Automatización de la generación de datos y gráficos con Python.
- Comparación crítica entre complejidad teórica y resultados observados, considerando tiempo y memoria.
- Documentación y análisis de resultados en un mini-informe en LaTeX.

## Algoritmos implementados

### Ordenamiento de arreglos

Se comparan cuatro implementaciones:

| Algoritmo | Enfoque | Complejidad temporal típica |
|---|---|---:|
| Merge Sort | Divide y combina subarreglos ordenados | O(n log n) |
| QuickSort | Partición con pivote mediana de tres y partición de tres vías | O(n log n) esperado |
| Patience Sort | Construcción de pilas y mezcla mediante cola de prioridad | O(n log n) |
| `std::sort` | Implementación de la biblioteca estándar de C++ | O(n log n) |

La versión de QuickSort procesa primero la partición más pequeña de forma recursiva y continúa iterativamente con la mayor. Esto limita el crecimiento de la pila; la partición de tres vías también evita trabajo redundante cuando hay muchos valores iguales.

### Multiplicación de matrices

| Algoritmo | Enfoque | Complejidad temporal |
|---|---|---:|
| Naive | Multiplicación convencional con tres ciclos | O(n³) |
| Strassen | Divide las matrices en bloques y reduce las multiplicaciones recursivas a siete | O(n^log₂7), aproximadamente O(n²·⁸⁰⁷) |

Strassen usa multiplicación convencional como caso base para dimensiones de hasta 64 y completa las dimensiones con ceros hasta la siguiente potencia de dos. Así se puede experimentar con distintos tamaños de matrices cuadradas.

## Diseño experimental

Los generadores crean tres muestras (`a`, `b`, `c`) para cada escenario:

- **Ordenamiento:** tamaños de 10, 1.000, 100.000 y 10.000.000 elementos; entradas aleatorias, ascendentes y descendentes; dominios de valores D1 (0–9) y D7 (0–10.000.000).
- **Matrices:** dimensiones 16, 64, 256 y 1.024; matrices densas, diagonales y dispersas; dominios D0 (0–1) y D10 (0–9).

Los programas en C++ guardan los resultados de los algoritmos y registran mediciones en archivos CSV. En ordenamiento también registran si la salida quedó ordenada. Los scripts Python agrupan las tres muestras por escenario y generan gráficos de tiempo y memoria frente al tamaño de entrada.

## Resultados observados

Los resultados de este repositorio muestran que no hay un ganador universal para todos los escenarios:

- En ordenamiento, `std::sort` y QuickSort fueron competitivos en tiempo, mientras que Merge Sort y Patience Sort tuvieron costes de memoria adicionales en varias pruebas.
- En el escenario guardado de matrices densas D10 de 1.024 × 1.024 (`a`), Strassen registró **621 ms** frente a **2.366 ms** de Naive; el uso de memoria incremental medido fue aproximadamente **33,8 MB** y **1,3 MB**, respectivamente.
- Esta diferencia ilustra el intercambio observado: Strassen puede reducir el tiempo en tamaños grandes, a costa de usar más memoria; en tamaños pequeños, el coste de dividir y combinar matrices puede cambiar la comparación.

Las cifras corresponden a las mediciones incluidas en el repositorio, no a una garantía de rendimiento en otros equipos. El informe discute los gráficos y sus limitaciones.

### Visualizaciones de muestra

**Ordenamiento: tiempo y memoria**

![Tiempo de ordenamiento en D7, entradas descendentes](code/sorting/data/plots/lineas_D7_descendente.png)

![Memoria de ordenamiento en D7, entradas descendentes](code/sorting/data/plots/memoria_lineas_D7_descendente.png)

**Multiplicación de matrices**

![Tiempo de multiplicación de matrices densas en D10](code/matrix_multiplication/data/plots/tiempo_matrices_D10_densa.png)

![Memoria de multiplicación de matrices densas en D10](code/matrix_multiplication/data/plots/memoria_matrices_D10_densa.png)

## Estructura del repositorio

```text
.
├── assignment_statement/       # Enunciado de la tarea
├── code/
│   ├── sorting/
│   │   ├── algorithms/         # Merge, Quick, Patience y std::sort
│   │   ├── scripts/            # Generación de entradas y gráficos
│   │   └── data/               # Mediciones y gráficos de ordenamiento
│   └── matrix_multiplication/
│       ├── algorithms/         # Naive y Strassen
│       ├── scripts/            # Generación de entradas y gráficos
│       └── data/               # Mediciones y gráficos de matrices
└── report/                     # Fuentes y PDF del mini-informe
```

## Tecnologías

- **C++17** para los algoritmos y los ejecutables de medición.
- **Python 3**, NumPy, pandas y Matplotlib para generar datos y visualizar resultados.
- **GNU Make / MinGW Make** para compilar y ejecutar experimentos en Windows.
- **LaTeX** para el mini-informe.

## Cómo ejecutar

En Windows, instala un compilador `g++` compatible con C++17, MinGW Make y Python 3. Para la generación de datos y gráficos, instala las dependencias de Python:

```powershell
py -m pip install numpy pandas matplotlib
```

Los comandos siguientes se ejecutan desde el directorio indicado. `mingw32-make experimento` genera las entradas y ejecuta los algoritmos; `mingw32-make generar_plots` crea los gráficos a partir del CSV ya generado.

### Experimento de ordenamiento

```powershell
cd code\sorting
mingw32-make experimento
mingw32-make generar_plots
```

### Experimento de matrices

```powershell
cd code\matrix_multiplication
mingw32-make experimento
mingw32-make generar_plots
```

Los experimentos pueden tardar, especialmente con arreglos de diez millones de elementos. La ejecución vuelve a generar los datos y sobrescribe los archivos de mediciones asociados a cada módulo.

## Alcance y consideraciones de medición

El tiempo se registra en milisegundos con `std::chrono`; cada entrada se mide una vez y los gráficos promedian las tres muestras (`a`, `b`, `c`). Los generadores no fijan una semilla aleatoria, por lo que una nueva ejecución no reproduce exactamente los mismos datos. La memoria es una estimación del incremento máximo de memoria residente del proceso, obtenido mediante muestreo durante la ejecución; no equivale a una medición exacta de todas las asignaciones del algoritmo. Los resultados dependen del equipo, del compilador y de las condiciones de ejecución. Para una comparación controlada entre equipos convendría registrar el entorno, fijar semillas, repetir las ejecuciones y resumir la variabilidad.

## Informe

El mini-informe en LaTeX presenta la metodología, los gráficos y las conclusiones del experimento: [abrir PDF](report/report/report.pdf).

## Referencias

- Cormen, T. H. et al. *Introduction to Algorithms*, 3.ª ed., MIT Press, 2009.
- Strassen, V. “Gaussian elimination is not optimal”. *Numerische Mathematik*, 13(4), 354–356, 1969.
- Mallows, C. L. “Patience Sorting”. *Bell System Technical Journal*, 1963.
