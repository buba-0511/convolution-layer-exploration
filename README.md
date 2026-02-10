# Clasificación de CIFAR-100: MLP vs Custom CNN

Este proyecto analiza y compara la eficiencia de las Redes Neuronales Densas (MLP) frente a las Redes Neuronales Convolucionales (CNN) utilizando el dataset CIFAR-100.

---

## 1. Problem Description
El reto consiste en clasificar imágenes de baja resolución ($32 \times 32$ píxeles) en **100 clases** distintas. Dada la alta complejidad del dataset y la baja cantidad de muestras por clase, el problema busca demostrar cómo el aprovechamiento de la **estructura espacial** de las imágenes permite obtener mejores resultados con menos recursos computacionales.

## 2. Dataset Description
Se utiliza el dataset **CIFAR-100**, el cual presenta las siguientes características:
* **Volumen:** 60,000 imágenes a color (RGB).
* **Resolución:** $32 \times 32$ píxeles.
* **División:** 50,000 imágenes para entrenamiento y 10,000 para evaluación.
* **Diversidad:** 100 clases (ej. animales, vehículos, flores) agrupadas en 20 superclases.
* **Preprocesamiento:** Normalización de valores de píxeles al rango $[0, 1]$.

## 3. Architecture Diagrams

### Baseline (MLP)
Una arquitectura rígida que ignora la topología de la imagen:
`Input (3072) -> Dense (512) -> BN -> ReLU -> Dense (256) -> BN -> ReLU -> Output (100)`

### Custom CNN (Propuesta)
Una arquitectura modular basada en la extracción jerárquica de rasgos:

`Bloque 1 (32 filtros) -> Bloque 2 (64 filtros) -> Bloque 3 (128 filtros) -> GAP -> Dropout -> Output (100)`

![alt text](image.png)

---

## 4. Experimental Results

### Comparativa de Eficiencia Estructural
| Modelo | Parámetros Totales | Accuracy (Test) | Ratio de Eficiencia |
| :--- | :--- | :--- | :--- |
| **Baseline (MLP)** | 1,730,404 | ~21.8% | 1x (Base) |
| **Custom CNN** | **106,596** | **38.4%** | **16.2x más ligero** |

### Experimento de Profundidad (Depth)
Se evaluó el impacto de añadir bloques convolucionales manteniendo los hiperparámetros fijos:



* **1 Bloque:** 14% Accuracy - La red es demasiado simple para el dataset.
* **2 Bloques:** 24% Accuracy - Mejora significativa al detectar formas intermedias.
* **3 Bloques:** **36-38% Accuracy** - Punto óptimo de abstracción visual.

---

## 5. Interpretation & Reasoning

* **Superioridad Convolucional:** Las CNN superan al MLP porque no intentan memorizar la posición exacta de cada píxel, sino que aprenden **conceptos visuales** (bordes, texturas, formas) que son válidos en cualquier parte de la imagen.
* **Sesgo Inductivo:** La arquitectura introduce **localidad espacial** (procesa píxeles vecinos juntos) e **invariancia a la traslación** (reutiliza filtros mediante *weight sharing*), lo que permite generalizar mejor con un 94% menos de parámetros que el modelo denso.
* **Limitaciones:** La convolución es ideal para datos con estructura de rejilla (imágenes), pero no sería apropiada para datos tabulares desordenados o problemas donde no existe una relación de vecindad entre las variables de entrada.

---