# Optimización de Cobertura de Estaciones de Radio

## Descripción del Problema
Este proyecto aborda un problema clásico de optimización en Inteligencia Artificial conocido como "_Set Covering Problem_".

El objetivo es encontrar el número mínimo de estaciones de radio necesarias para cubrir todos los estados de Estados Unidos (específicamente, los estados del oeste y centro del país).

Está basado en el ejercicio propuesto en el capítulo 8 _Greedy Algorithms_ del libro grokking [_Algorithms: An illustrated guide for programmers and other curious people_ de Aditya Y. Bhargava](https://github.com/egonSchiele/grokking_algorithms)

## Instalación y Configuración

### Requisitos Previos
- Python ≥ 3.11
- [uv](https://github.com/astral-sh/uv) (gestor de paquetes de Python)

### Instalación

1. Clonar el repositorio:
```bash
git clone https://github.com/dfleta/greedy-search.git
cd greedy-search
```

2. Crear y activar un entorno virtual con uv:

```bash
python -m pip install uv

uv venv

source .venv/bin/activate  # En Linux/MacOS
# o
.venv\Scripts\activate     # En Windows
```

3. Instalar las dependencias:
```bash
uv sync
```

4. (Opcional) Instalar dependencias de desarrollo:
```bash
uv sync --all-groups
```

o 

```bash
uv add --group dev
```

### Dependencias Principales
- matplotlib ≥ 3.10.1

#### Dependencias de desarrollo
- pytest >= 9.0.1
- ruff ≥ 0.9.9 (para linting, opcional)

### Uso

`uv run main.py`

o

`python3 main.py`

### Testing

`uv run pytest`

Con _markers_ listados en [pyproject.toml](./pyproject.toml)

`uv run pytest -m "greedy_search_global"`

`uv run pytest -m "find_best_station"`


### Contexto
- Necesitamos cobertura de radio en un conjunto de estados, todos los situados al oeste del río Mississippi para promocionar el disco _country_ de Beyoncé _Cowboy Carter_.
- Existen varias estaciones de radio, cada una cubriendo diferentes conjuntos de estados.
- Queremos seleccionar el menor número de estaciones posible que cubran todos los estados requeridos, porque Beyoncé -en modo gano o muero- paga la promoción del disco de su bolsillo. Las estaciones al oeste del Misisipi tienen un código que empieza por la letra `K`.

### Cobertura de Estaciones

La siguiente tabla muestra los estados que cubre cada estación de radio:

| Estación   | Estados Cubiertos |
|------------|------------------|
| kone       | Idaho (ID), Nevada (NV), Utah (UT) |
| ktwo       | Washington (WA), Idaho (ID), Montana (MT) |
| kthree     | Oregon (OR), Nevada (NV), California (CA) |
| kfour      | Nevada (NV), Utah (UT) |
| kfive      | California (CA), Arizona (AZ) |
| ksix       | New Mexico (NM), Texas (TX), Oklahoma (OK) |
| kseven     | Oklahoma (OK), Kansas (KS), Colorado (CO) |
| keight     | Kansas (KS), Colorado (CO), Nebraska (NE) |
| knine      | Nebraska (NE), South Dakota (SD), Wyoming (WY) |
| kten       | North Dakota (ND), Iowa (IA) |
| keleven    | Minnesota (MN), Missouri (MO), Arkansas (AR) |
| ktwelve    | Louisiana (LA) |
| kthirteen  | Missouri (MO), Arkansas (AR) |

![K/W Call Letters in the United States](https://earlyradiohistory.us/kwbigmap.gif)
*K/W Call Letters in the United States*

![Lista de estados USA y abreviaciones](https://upload.wikimedia.org/wikipedia/commons/6/6c/US_state_abbrev_map.png) 

*List of U.S. state and territory abbreviations*

## Implementación

El proyecto implementa dos estrategias diferentes de búsqueda:

### 1. Búsqueda Ávida Global (`greedy_search_global`)

Esta función implementa una estrategia de búsqueda voraz (_greedy_) que:
- En cada paso, selecciona la estación que cubre el mayor número de estados no cubiertos hasta el momento.
- Mantiene un registro de los estados cubiertos y las estaciones seleccionadas.
- Escapa de mínimos locales al considerar siempre la mejor opción global disponible.

![Evolución de la búsqueda ávida global](doc/greedy_search_global.png)
*La gráfica muestra cómo cada estación seleccionada contribuye a la cobertura total de estados*

### 2. Búsqueda Ávida Local (`greedy_search_local`)

Esta función demuestra el problema de los mínimos locales:
- Realiza múltiples intentos aleatorios seleccionando 10 estaciones en cada iteración.
- No considera la optimización global, sino que se basa en selecciones aleatorias.
- Queda atrapada en mínimos locales, resultando en soluciones subóptimas.

![Mínimos locales y global en la búsqueda](doc/f_minimo_global.png)
*La gráfica muestra cómo diferentes iteraciones quedan atrapadas en mínimos locales, aún alcanzando en una de ellas el mínimo global.*

![Mínimos locales en la búsqueda](doc/f_minimos_locales.png)
*La gráfica muestra cómo diferentes iteraciones quedan atrapadas en mínimos locales, sin alcanzar la cobertura total.*

## Resultados
- La búsqueda ávida global encuentra una solución óptima o cercana a la óptima.
- La búsqueda local demuestra cómo las estrategias no optimizadas pueden quedar atrapadas en soluciones subóptimas.
- Las gráficas permiten comparar la efectividad de ambos enfoques.

#### Lógica del Algoritmo 

`greedy_search_global`

**Consulta el [_notebook_ conjuntos / sets](https://github.com/dfleta/python-fundamentals-nb/blob/main/notebooks/conjuntos.ipynb) para estudiar las operaciones sobre esta colección Python** de las que vamos a hacer uso.

1. **Inicialización**:
   - Mantiene un conjunto `covered_states` para registrar los estados ya cubiertos.
   - Una lista `stations_needed` para almacenar las estaciones seleccionadas.
   - Listas `gradients` y `num_states_covered` para monitorizar el progreso.

2. **Bucle Principal**:
   Mientras existan estados sin cubrir (`covered_states < needed_states`):
   
   a. **Selección de la Mejor Estación**:
      - Para cada estación restante, calcula cuántos nuevos estados cubriría: `new_states = station_states - covered_states`.
      - Selecciona la estación que cubra el mayor número de estados nuevos
   
   b. **Actualización del Estado**:
      - Registra el gradiente (número de nuevos estados cubiertos).
      - Actualiza el conjunto de estados cubiertos.
      - Añade la estación a la lista de seleccionadas.
      - Elimina la estación seleccionada del conjunto disponible.

3. **Visualización**:
   - Genera un gráfico de barras mostrando:
     - Eje X: Estaciones seleccionadas en orden.
     - Eje Y: Número total de estados cubiertos en cada paso.

#### Características Clave
- **Optimización Global**: En cada paso, selecciona la mejor opción disponible.
- **Sin Retroceso**: Las decisiones tomadas son definitivas.
- **Monitorización**: Registra el progreso y la efectividad de cada selección.
- **Terminación**: El algoritmo se detiene cuando se cubren todos los estados necesarios.


## Referencias
- [Call Letters in the United States](https://earlyradiohistory.us/kwtrivia.htm)
- [Call signs in the United States](https://en.wikipedia.org/wiki/Call_signs_in_the_United_States)
- [List of FM radio stations in the United States by call sign (initial letters KK–KM)](https://en.wikipedia.org/wiki/List_of_FM_radio_stations_in_the_United_States_by_call_sign_(initial_letters_KK%E2%80%93KM))
- [List of U.S. state and territory abbreviations](https://en.wikipedia.org/wiki/List_of_U.S._state_and_territory_abbreviations)