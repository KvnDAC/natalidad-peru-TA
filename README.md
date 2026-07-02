# Predicción Trimestral de la Tasa de Natalidad en el Perú
### Comparación de Modelos de Machine Learning a Nivel Departamental y Provincial

Este repositorio contiene el desarrollo completo del proyecto orientado a predecir la tasa de natalidad trimestral en el Perú utilizando modelos de Machine Learning aplicados a datos oficiales del Registro de Nacidos Vivos (2018–2025). El sistema integra análisis espacio-temporal, ingeniería de características y validación temporal.

## 1. Descripción General

La tasa de natalidad peruana presenta una tendencia descendente con fuertes diferencias entre departamentos y provincias. Este proyecto desarrolla un modelo predictivo capaz de capturar estas variaciones mediante paneles trimestrales construidos a partir de millones de registros individuales.

El modelo **XGBoost** obtuvo el mejor desempeño, alcanzando un **R² de 0.91** en el conjunto de prueba a nivel departamental, indicando una excelente capacidad de generalización.

## 2. Objetivos del Proyecto

**Objetivo general:** Desarrollar y comparar modelos de Machine Learning para predecir la tasa de natalidad trimestral a nivel departamental y provincial en el Perú.

**Objetivos específicos:**
- Procesar y transformar datos individuales de nacimientos para formar paneles trimestrales por unidad geográfica.
- Implementar modelos de predicción respetando la secuencia temporal.
- Evaluar y comparar el desempeño mediante métricas estandarizadas (R², MAE, RMSE).
- Realizar un estudio de ablación para cuantificar la contribución de variables temporales frente a sociodemográficas.
- Extender el análisis al nivel provincial.

## 3. Datos Utilizados

**3.1. Registros de Nacidos Vivos (2018–2025)**
Más de 4.8 millones de registros con información sociodemográfica, de salud y ubicación geográfica.

**3.2. Catálogo de Ubigeo del INEI**
Utilizado para la asignación geográfica correcta de cada nacimiento.

**3.3. Proyecciones de Población del INEI (2018–2026)**
Población anual oficial por distrito, agregada a nivel departamental y provincial, usada para estimar la población trimestral mediante interpolación lineal. Se verifica que la suma distrital reproduce el total nacional oficial (32,131,400 habitantes en 2019).

## 4. Proceso de Preprocesamiento

**4.1. Limpieza de datos**
- Eliminación de duplicados (975 registros).
- Normalización de valores especiales.
- Eliminación de campos no relevantes.

**4.2. Integración geográfica**
- Unión por código Ubigeo.
- Identificación del departamento y provincia correspondientes.

**4.3. Ingeniería de características**
- Estadísticas de edad materna y variables reproductivas.
- Proporciones de categorías sociodemográficas agrupadas.
- Variables temporales: Lag1, Lag4, MA4.

**4.4. Construcción del panel trimestral**
- Agregación por año, trimestre y unidad geográfica.
- Incorporación de población trimestral real (INEI).
- Cálculo de la tasa de natalidad por cada 1,000 habitantes.

## 5. Modelos Implementados

Se evaluaron cinco algoritmos:
- Regresión Lineal
- K-Nearest Neighbors (k = 5 y k = 10)
- Árbol de Decisión (profundidad máxima = 6)
- Random Forest (100 árboles)
- XGBoost (modelo de mejor desempeño)

Todos evaluados mediante validación cruzada temporal (TimeSeriesSplit, 5 folds).

## 6. Resultados Principales

### 6.1. Validación Cruzada Temporal (promedio, departamental)

| Modelo | R² | MAE | RMSE |
|--------|------|------|------|
| Random Forest | 0.7833 | 0.2704 | 0.3778 |
| XGBoost | 0.7704 | 0.2834 | 0.3885 |
| Regresión Lineal | 0.7457 | 0.2772 | 0.3919 |
| Árbol de Decisión | 0.6969 | 0.3482 | 0.4593 |
| KNN (k = 10) | 0.5980 | 0.4404 | 0.5322 |

### 6.2. Desempeño en el Conjunto de Prueba (2024–2025)

Modelo final (XGBoost):
- **R²:** 0.9044
- **RMSE:** 0.2197
- **MAE:** 0.1645

## 7. Estudio de Ablación

| Configuración | R² CV | R² Test |
|---------------|-------|---------|
| Solo Temporal | 0.7974 | 0.9095 |
| Solo Sociodemográfico | 0.2016 | 0.5305 |
| Completa | 0.7704 | 0.9044 |

La configuración solo temporal supera a la completa, indicando que las variables temporales concentran el poder predictivo.

## 8. Importancia de Variables

Importancia acumulada por categoría (XGBoost):
- **Temporal:** 84.36 %
- **Sociodemográfico:** 6.04 %
- **Geográfico:** 3.94 %
- **Edad materna:** 3.87 %
- **Salud reproductiva:** 1.79 %

Las variables temporales Lag1 y MA4 fueron las más influyentes.

## 9. Extensión Provincial y Exclusión del COVID-19

- **Nivel provincial:** R² = 0.8210 en prueba, confirmando la escalabilidad del enfoque.
- **Exclusión del COVID-19:** el modelo sin COVID (R² = 0.9044) supera al modelo con COVID (R² = 0.8784), debido a los comportamientos atípicos de la pandemia.

## 10. Conclusiones

- Los modelos basados en árboles superaron ampliamente a los lineales.
- XGBoost alcanzó el mejor desempeño, capturando patrones no lineales y dependencias temporales.
- La categoría temporal concentra el 84.36 % del poder predictivo; las variables sociodemográficas aisladas son insuficientes.
- La natalidad peruana subnacional está gobernada principalmente por su inercia trimestral.

## 11. Reproducción

1. Descargar el Registro de Nacidos Vivos desde la Plataforma Nacional de Datos Abiertos.
2. Descargar el Excel de población del INEI (2018–2026).
3. Ejecutar `Calculo_Poblacion_TRIMESTRAL.ipynb` para generar los CSV de población (ya incluidos).
4. Abrir el notebook principal y ejecutar las celdas en orden.

## 12. Autores

Proyecto desarrollado por: **Kevin Alcca, Jack Mateo, Joel Yanamango**
