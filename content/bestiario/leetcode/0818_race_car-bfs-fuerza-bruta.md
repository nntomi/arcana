---
title: LeetCode0818 - Race Car - BFS con Fuerza Bruta (no tan naive)
tags:
  - b/leetcode
---
## Técnicas utilizadas
Búsqueda en Anchura [BFS](): Exploración nivel por nivel con una [Cola](../../grimorio/data-structures/queue.md), para encontrar la ruta más corta en un espacio de estados.

## Idea de la solución
El problema se modela como un grafo dirigido donde cada nodo es un estado definido por `(posición, velocidad)` y las aristas son las decisiones `A` o `R`. Como se busca la secuencia mínima de instrucciones, aplicar BFS garantiza que la primera vez que la posición actual coincida con el `target`, se encontró la solución óptima.

Para que la búsqueda por fuerza bruta sea (masomenos) viable, se aplican dos optimizaciones:

- **Memorización de estados visitados:** Se descartan los estados `(posición, velocidad)` que ya fueron procesados, guardados en un [Set](../../grimorio/data-structures/set.md).
- **Poda direccional:** Solo se aplica la reversa (`R`) si el próximo movimiento `A` nos pasa de largo del objetivo. Es decir, si nos adelantamos `target` yendo hacia adelante (velocidad positiva), o si nos alejamos más yendo hacia atrás (velocidad negativa).

## Código

```python
from collections import deque

def racecar(target):
    cola = deque([(0, 0, 1)])
    visitados = set()

    while cola:
        ops, pos, speed = cola.popleft()

        if pos == target:
            return ops

        estado = (pos, speed)
        if estado in visitados:
            continue
        visitados.add(estado)

        cola.append((ops + 1, pos + speed, speed * 2))

        if (pos + speed > target and speed > 0) or (pos + speed < target and speed < 0):
            new_speed = -1 if speed > 0 else 1
            cola.append((ops + 1, pos, new_speed))

    return 0
```

## Traza de ejemplo

Buscamos el objetivo `target = 3`.

| Cola (`ops`, `pos`, `speed`) | Estado actual sacado | &nbsp;&nbsp;¿Es `target`?&nbsp;&nbsp; | Acción evaluada | ¿Pasa el filtro/poda? | Nuevos elementos a la cola |
|---|---|---|---|---|---|
| [(0, 0, 1)] | (0, 0, 1) | No (0 != 3) | `A` | Sí | (1, 1, 2) |
| | | | `R` | No (pos+speed no supera 3) | - |
| [(1, 1, 2)] | (1, 1, 2) | No (1 != 3) | `A` | Sí | (2, 3, 4) |
| | | | `R` | No (pos+speed no supera 3) | - |
| [(2, 3, 4)] | (2, 3, 4) | Sí (3 == 3) | - | - | - |

Resultado final: 2 operaciones (secuencia: `AA`).

## Complejidad

### Temporal
$O(T^2)$ en el peor de los casos acotados, donde `T` es el valor del `target`. Ante las dos decisiones (`A` y `R`) en el peor caso se toman ambas, es decir $2^N$. Se sabe que si avanzo hasta la condición de reversa, haré $\log_2{T}$ expansiones, luego el trayecto hasta el `target` desde ahí costará, a lo sumo, $\log_2{T}$. Finalmente me queda $2^N$, donde $N = 2 \times \log_2{T}$. Expandiendo, se simplifica la base y el logaritmo, dejando `T^2` como peor caso.

### Espacial
$O(T^2)$ también, ya que sabemos que habrá $T^2$ estados nuevos en el peor caso. Esos estados se almacenarán en un Set de visitados.

## Cuándo usar esta técnica

### Favorable cuando
- El valor del `target` es pequeño (no colapsa la memoria).

### Limitaciones
- No escala bien a `targets` altos ya que explora todas las opciones (con ligeras optimizaciones).
- Realiza cuentas innecesarias como seguir acelerando a pesar de ya haberse pasado de largo por amplia distancia.

## Comparación con Programación Dinámica
La solución con [Programación Dinámica](0818_race_car-programacion-dinamica.md) es significativamente más ligera en espacio y tiempo. DP analiza la distancia basándose en secuencias completas de aceleraciones antes de revertir, reduciendo el problema a subproblemas de menor escala y reduciendo el uso de memoria y procesamiento a costo de ser bastante más compleja a la comprensión.

## Referencias
N/A
