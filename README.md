# Réplica de DeepAR sobre el Dataset M5 (California)

## Descripción

Este proyecto presenta una reproducción académica de la metodología **DeepAR**, propuesta originalmente por Amazon Research para el pronóstico probabilístico de series temporales.

La implementación se desarrolló utilizando el **M5 Forecasting Dataset**, trabajando específicamente con las series correspondientes al estado de California. El objetivo principal fue comprender y replicar los conceptos fundamentales del artículo original, incluyendo el entrenamiento de un modelo global sobre miles de series temporales, la incorporación de variables categóricas y temporales, y la generación de pronósticos probabilísticos mediante una distribución Binomial Negativa.

Este trabajo tiene fines educativos y de investigación, por lo que no pretende reproducir exactamente la implementación interna utilizada por Amazon, sino estudiar y aplicar los principios descritos en la publicación original.

---

## Objetivos

* Comprender la arquitectura DeepAR.
* Replicar el flujo de preparación de datos para series temporales múltiples.
* Construir un modelo global para miles de productos simultáneamente.
* Incorporar covariables temporales y variables categóricas estáticas.
* Generar pronósticos probabilísticos.
* Comparar el desempeño de DeepAR contra modelos benchmark tradicionales.

---

## Dataset

Se utilizó el conjunto de datos oficial de la competencia M5 Forecasting.

Archivos originales:

* `sales_train_validation.csv`
* `calendar.csv`
* `sell_prices.csv`
* `sample_submission.csv`

Para reducir los requerimientos computacionales, se trabajó únicamente con las series pertenecientes al estado de California.

Características del subconjunto utilizado:

* 12,196 series temporales.
* 1,913 días históricos por serie.
* Más de 3,000 productos únicos.
* 4 tiendas del estado de California.
* Variables temporales derivadas del calendario oficial del M5.

---

## Arquitectura Implementada

La réplica utiliza una arquitectura DeepAR basada en:

### Red Recurrente

* Tipo: LSTM
* Número de capas: 3
* Hidden Size: 80
* Dropout: 0.1

### Distribución Probabilística

* Binomial Negativa

Esta distribución resulta adecuada para modelar ventas debido a que:

* Las observaciones son conteos enteros no negativos.
* Existe sobredispersión.
* Muchas series presentan demanda intermitente.

### Parámetros de Entrenamiento

* Batch Size: 128
* Learning Rate: 5e-4
* Epochs: 40
* Context Length: 84 días
* Prediction Length: 28 días

Entrenamiento realizado en Google Colab utilizando GPU NVIDIA T4.

---

## Estructura del Proyecto

```text
DeepAR_California/
│
├── notebooks/
│   ├── 01_exploracion_m5.ipynb
│   ├── 02_preparacion_datos_CA.ipynb
│   ├── 03_generacion_matrices.ipynb
│   └── 04_entrenamiento_evaluacion_deepar.ipynb
│
├── data/
│   ├── raw/
│   │   ├── sales_train_validation.csv
│   │   ├── calendar.csv
│   │   ├── sell_prices.csv
│   │   └── sample_submission.csv
│   │
│   └── processed/
│       ├── train_target_matrix_CA.npy
│       ├── valid_target_matrix_CA.npy
│       ├── valid_time_features_array_CA.npy
│       ├── static_cat_encoded_CA.parquet
│       ├── series_metadata_CA.parquet
│       ├── calendar_hist_CA.parquet
│       └── deepar_dataset_config_CA.pkl
│
├── reports/
│   ├── DeepAR_Report.pdf
│   └── figures/
│
├── README.md
└── requirements.txt
```

---

## Flujo Metodológico

### Notebook 1 – Exploración del Dataset

* Carga de archivos M5.
* Análisis de dimensiones.
* Revisión de estructura de ventas.
* Exploración de categorías y tiendas.
* Análisis preliminar de demanda.

### Notebook 2 – Preparación de Datos

* Filtrado de California.
* Construcción de series temporales.
* Creación de variables temporales.
* Generación de variables categóricas.
* Validación de integridad de datos.

### Notebook 3 – Construcción de Matrices

* Creación de matrices objetivo.
* Construcción de covariables temporales.
* Codificación de variables categóricas.
* Exportación de archivos para entrenamiento.

### Notebook 4 – Entrenamiento y Evaluación

* Construcción de datasets GluonTS.
* Entrenamiento DeepAR.
* Monitoreo de gradientes.
* Forecast probabilístico.
* Evaluación sobre conjunto de prueba.
* Comparación contra benchmarks.

---

## Variables Utilizadas

### Variables Estáticas

* Item ID
* Department ID
* Category ID
* Store ID
* State ID

### Variables Temporales

* Día de la semana
* Mes
* Año
* SNAP California
* Eventos especiales
* Indicadores derivados del calendario

---

## Evaluación

El modelo fue evaluado mediante una partición temporal:

```text
Train      : primeros 1857 días
Validation : siguientes 28 días
Test       : últimos 28 días
```

Esta estrategia evita fuga de información y permite evaluar el desempeño en un periodo completamente futuro respecto al entrenamiento.

Las métricas utilizadas incluyen:

* MAE
* RMSE
* sMAPE
* Bias
* Quantile Loss
* Coverage

Además, DeepAR fue comparado contra:

* Naive Forecast
* Seasonal Naive (28 días)
* Moving Average (28 días)

---

## Principales Hallazgos

* DeepAR logró resultados competitivos frente a los modelos benchmark.
* El modelo obtuvo el menor error absoluto promedio (MAE).
* La arquitectura probabilística permitió generar intervalos de confianza además de predicciones puntuales.
* La distribución Binomial Negativa se adaptó adecuadamente al comportamiento discreto e intermitente de muchas series.
* Los mayores errores se concentraron en productos con picos de demanda poco frecuentes y difíciles de anticipar.

---

## Tecnologías Utilizadas

* Python
* NumPy
* Pandas
* Matplotlib
* PyTorch
* GluonTS
* Lightning
* Google Colab

---

## Referencia Principal

Salinas, D., Flunkert, V., Gasthaus, J., & Januschowski, T.

**DeepAR: Probabilistic Forecasting with Autoregressive Recurrent Networks**

International Journal of Forecasting, 2020.

---

## Nota

Este repositorio corresponde a una reproducción académica de la metodología DeepAR con fines educativos y de aprendizaje. Todos los créditos de la arquitectura original pertenecen a los autores del artículo y a Amazon Research.
