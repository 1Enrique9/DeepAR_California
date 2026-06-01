# DeepAR para Forecasting de Demanda en M5 (California)

## Descripción

Este proyecto implementa una réplica del modelo DeepAR propuesto por Amazon para forecasting probabilístico de series de tiempo.

La implementación utiliza el dataset M5 Forecasting y se enfoca exclusivamente en el subconjunto correspondiente al estado de California.

El objetivo es comparar DeepAR contra modelos base tradicionales y evaluar tanto el desempeño puntual como probabilístico de las predicciones.

---

## Dataset

Se utilizó el dataset:

- M5 Forecasting Accuracy

Subconjunto utilizado:

- Estado: California
- Series producto-tienda: 12,196
- Días históricos: 1,913
- Horizonte de predicción: 28 días

---

## Estructura del Proyecto

```text
notebooks/
│
├── 01_Exploracion.ipynb
├── 02_Preprocesamiento.ipynb
├── 03_Dataset_DeepAR.ipynb
└── 04_DeepAR.ipynb
```

### Notebook 01 – Exploración

- Carga de datos
- Estadísticos descriptivos
- Distribución de ventas
- Análisis de demanda intermitente

### Notebook 02 – Preprocesamiento

- Filtrado de California
- Construcción de matrices compactas
- Creación de covariables temporales
- Codificación de variables categóricas

### Notebook 03 – Dataset DeepAR

- Construcción de objetos ListDataset
- Validación de dimensiones
- Configuración del modelo

### Notebook 04 – Entrenamiento y Evaluación

- Entrenamiento DeepAR
- Forecast probabilístico
- Evaluación contra benchmarks
- Análisis de incertidumbre

---

## Modelo

La configuración final utilizada fue:

```python
DeepAREstimator(
    freq="D",
    prediction_length=28,
    context_length=84,
    num_layers=3,
    hidden_size=80,
    dropout_rate=0.1,
    distr_output=NegativeBinomialOutput(),
    batch_size=128,
    num_batches_per_epoch=300,
    lr=5e-4
)
```

Entrenamiento:

- GPU: NVIDIA T4
- Épocas: 40
- Tiempo aproximado: 31 minutos

---

## Covariables Utilizadas

### Variables Estáticas

- item_id
- dept_id
- cat_id
- store_id
- state_id

### Variables Dinámicas

- dayofweek
- dayofmonth
- weekofyear
- month
- snap_CA

---

## Resultados

| Modelo | MAE | RMSE | sMAPE |
|----------|----------:|----------:|----------:|
| Naive | 1.479 | 3.452 | 0.854 |
| Seasonal Naive | 1.342 | 2.903 | 0.843 |
| Moving Average | 1.107 | 2.260 | 1.262 |
| DeepAR | **0.995** | **2.228** | **0.703** |

### Cobertura Probabilística

| Métrica | Valor |
|----------|----------:|
| Coverage 80% | 0.909 |
| Quantile Loss 0.5 | 0.497 |
| Quantile Loss 0.9 | 0.333 |

---

## Principales Hallazgos

- DeepAR obtuvo el mejor RMSE y MAE.
- La demanda presenta una alta proporción de ceros (~66%).
- Las ventas muestran una distribución altamente sesgada.
- DeepAR logra capturar patrones compartidos entre miles de series.
- Los intervalos probabilísticos son conservadores.
- El modelo presenta una ligera tendencia a subestimar productos de alta demanda.

---

## Trabajo Futuro

- Incorporar precios (`sell_price`)
- Incorporar eventos/promociones
- Evaluar sobre todo M5
- Comparar contra TFT y N-BEATS
- Evaluar usando WRMSSE

---

## Referencias

DeepAR: Probabilistic Forecasting with Autoregressive Recurrent Networks

Amazon Research