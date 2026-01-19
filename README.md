# Modelo de Regresión Lineal: Predicción del Valor de Viviendas en Boston

## 📋 Descripción del Proyecto

Este repositorio contiene un análisis completo y documentado del conjunto de datos de viviendas de Boston, con el objetivo de desarrollar un modelo de regresión lineal que permita predecir el valor medio de las propiedades basándose en sus características socioeconómicas, físicas y ambientales.

El proyecto implementa una metodología rigurosa de análisis estadístico, que incluye exploración de datos (EDA), análisis de correlación, análisis de componentes principales (PCA), análisis de varianza (ANOVA) y modelado de regresión lineal.

---

## 📁 Estructura del Repositorio

```
Modelo-de-regresion-lineal-vivienda-boston/
│
├── README.md                              # Este archivo
├── ModelodeRegresiónLineal.ipynb          # Notebook Jupyter con análisis completo
└── 20230119_DatosBoston.csv               # Dataset con 506 observaciones
```

---

## 📊 Características del Dataset

El conjunto de datos contiene **506 observaciones** de viviendas en el área de Boston con **14 variables** numéricas:

| Variable | Descripción |
|----------|-------------|
| `crime_index` | Tasa de criminalidad por cápita de la zona |
| `urban_ratio` | Proporción del territorio dedicado a zonas urbanas no comerciales |
| `industry_ratio` | Proporción de terreno dedicado a industrias no minoristas |
| `c_river_variable` | Variable binaria (1 si la propiedad colinda con el río, 0 en otro caso) |
| `nox_concentration` | Concentración de óxidos de nitrógeno (partes por 10 millones) |
| `rooms_per_dwelling` | Número promedio de habitaciones por vivienda |
| `dwelling_age` | Proporción de viviendas construidas antes de 1940 |
| `dwelling_dist` | Distancia ponderada a los cinco centros de empleo de Boston |
| `highway_index` | Índice de accesibilidad a autopistas radiales |
| `tax_ratio` | Tasa de impuesto sobre propiedad por $10,000 |
| `teacher_ratio` | Ratio de alumnos por profesor por municipio |
| `racial_index` | Proporción de población de raza negra (en %) |
| `lower_status` | Porcentaje de población de clase baja |
| `median_value` | **[Variable objetivo]** Valor medio de las viviendas en miles de USD |

---

## 🔍 Proceso Metodológico

### 1. **Análisis Exploratorio de Datos (EDA)**

En esta fase se realizó una exploración exhaustiva del dataset para comprender sus características y patrones:

- **Verificación de integridad**: Sin valores nulos detectados, sin filas duplicadas
- **Estadísticas descriptivas**: Media, mediana, desviación estándar para todas las variables
- **Visualización de distribuciones**: Histogramas, gráficos de densidad y boxplots
- **Identificación de outliers**: Análisis visual mediante boxplots
- **Relaciones bivariadas**: Gráficos de dispersión de cada variable vs. la variable objetivo

**Hallazgos principales:**
- La mayoría de variables presentan distribuciones asimétricas
- Existen diferencias notables entre zonas en términos socioeconómicos y ambientales
- Se observan patrones claros de relación entre variables explicativas y el valor de la vivienda

### 2. **Análisis de Correlación**

Se calculó la matriz de correlación de Pearson entre todas las variables numéricas para identificar relaciones lineales:

**Correlaciones relevantes con `median_value`:**

| Variable | Correlación | Interpretación |
|----------|-------------|----------------|
| `rooms_per_dwelling` | +0.70 | **Fuerte positiva**: Más habitaciones → Más valor |
| `lower_status` | -0.74 | **Fuerte negativa**: Más pobreza → Menos valor |
| `crime_index` | -0.39 | **Negativa moderada**: Más crimen → Menos valor |
| `tax_ratio` | -0.47 | **Negativa moderada**: Más impuestos → Menos valor |
| `nox_concentration` | -0.43 | **Negativa moderada**: Más contaminación → Menos valor |

Se detectaron también **correlaciones elevadas entre predictores**, lo que indica potencial multicolinealidad que será considerada en el modelo final.

### 3. **Análisis de Componentes Principales (PCA)**

Se aplicó PCA para reducir la dimensionalidad e identificar estructuras latentes:

- **PC1** (46% varianza): Representa el entorno urbano-industrial-socioeconómico
  - Variables asociadas: `industry_ratio`, `nox_concentration`, `highway_index`, `tax_ratio`, `crime_index`

- **PC2** (12% varianza): Representa características residenciales y localización
  - Variables asociadas: `rooms_per_dwelling`, `dwelling_dist`, `c_river_variable`

- **PC3** (10% varianza): Información adicional sobre edad y otras características

**Conclusión PCA**: Las tres primeras componentes explican el 68% de la varianza, confirmando la presencia de información redundante entre algunas variables.

### 4. **Análisis de Varianza (ANOVA)**

Se realizó un ANOVA de un factor para evaluar si existen diferencias significativas en el valor de la vivienda según el nivel de `lower_status`:

- **Variable categórica**: `lower_status` agrupada en 3 niveles (Bajo, Medio, Alto)
- **Resultado**: F-estadístico elevado, p-value < 0.001
- **Conclusión**: Existen diferencias estadísticamente significativas entre grupos socioeconómicos

Este análisis validó la inclusión de `lower_status` como variable explicativa en el modelo.

### 5. **Desarrollo del Modelo de Regresión Lineal**

#### Modelo inicial (5 variables):
```
median_value ~ rooms_per_dwelling + lower_status + crime_index + tax_ratio + nox_concentration
```

**Resultados:**
- R² ajustado: 0.649
- Variables significativas: `rooms_per_dwelling`, `lower_status`, `tax_ratio`
- Variables NO significativas: `crime_index`, `nox_concentration` (p-value > 0.05)

#### Modelo final (3 variables):
```
median_value ~ rooms_per_dwelling + lower_status + tax_ratio
```

**Resultados finales del modelo:**

| Coeficiente | Estimación | p-value | Significancia |
|-------------|-----------|---------|----------------|
| **(Intercept)** | 28.33 | <0.001 | *** |
| `rooms_per_dwelling` | 4.59 | <0.001 | *** |
| `lower_status` | -0.65 | <0.001 | *** |
| `tax_ratio` | -0.01 | <0.001 | *** |

**Métricas de rendimiento:**
- **R² ajustado**: 0.649 (64.9% de varianza explicada)
- **Error estándar residual**: 4.78
- **F-estadístico**: 304.3 (p-value < 0.001)
- **Residuos**: Distribuidos de forma aproximadamente normal

---

## 📈 Interpretación del Modelo

El modelo final revela las siguientes relaciones:

### 1. **Número de habitaciones** (`rooms_per_dwelling`)
- **Coeficiente**: +4.59
- **Interpretación**: Por cada habitación adicional promedio en una zona, el valor medio de la vivienda aumenta aproximadamente **4,590 USD** (4.59 × 1000)
- **Implicación**: Las características físicas de la vivienda son un factor crucial en la determinación del precio

### 2. **Proporción de población de clase baja** (`lower_status`)
- **Coeficiente**: -0.65
- **Interpretación**: Por cada punto porcentual adicional de población de clase baja, el valor medio disminuye aproximadamente **650 USD** (0.65 × 1000)
- **Implicación**: El contexto socioeconómico de la zona tiene un impacto negativo significativo sobre el precio

### 3. **Tasa de impuestos** (`tax_ratio`)
- **Coeficiente**: -0.01
- **Interpretación**: Por cada punto adicional en la tasa de impuestos, el valor medio disminuye aproximadamente **10 USD**
- **Implicación**: Las zonas con mayor presión fiscal tienden a tener menores precios de vivienda

---

## ✅ Validación y Evaluación

### Bondad de ajuste

El modelo explica el **64.9%** de la variabilidad total del valor de la vivienda, lo que representa un ajuste razonable para un modelo lineal simple basado en variables socioeconómicas.

### Análisis de residuos

- Los residuos muestran una distribución aproximadamente normal
- La varianza de los residuos es relativamente homogénea
- Existe cierta dispersión alrededor de la línea de predicción perfecta, indicando que el modelo lineal no captura completamente todas las variaciones

### Capacidad predictiva

El modelo predice correctamente la tendencia general del valor de la vivienda. Comparando valores predichos vs. reales se observa:
- Buen ajuste para viviendas con valores medios
- Ligera subestimación en las propiedades más caras
- Dispersión moderada en predicciones individuales

---

## 🎯 Conclusiones

1. **Variables clave**: El modelo identifica `rooms_per_dwelling`, `lower_status` y `tax_ratio` como los principales determinantes del valor de la vivienda en Boston

2. **Contexto socioeconómico**: El factor más importante es el contexto socioeconómico de la zona, reflejado en la variable `lower_status`

3. **Capacidad explicativa**: El modelo lineal explica aproximadamente 65% de la variabilidad, lo que indica una relación fuerte pero con espacio para variables adicionales o modelos más complejos

4. **Aplicabilidad**: Este modelo es útil para:
   - Entender los factores que influyen en el precio de la vivienda
   - Realizar estimaciones iniciales del valor de propiedades
   - Identificar anomalías o propiedades con precios inusuales
   - Como modelo de referencia (baseline) para comparación con modelos más avanzados

5. **Limitaciones**: Para aplicaciones de predicción real, se recomienda:
   - Validar con datos externos más recientes
   - Considerar modelos no lineales (regresión polinomial, random forests)
   - Incluir variables adicionales específicas del mercado inmobiliario actual
   - Realizar validación cruzada del modelo

---

## 🚀 Uso del Notebook

El análisis completo se encuentra documentado en el archivo `ModelodeRegresiónLineal.ipynb`. 

**Requisitos:**
- R 3.6 o superior
- Paquetes: `ggplot2`, `dplyr`, `tidyr`, `readr`, `ggcorrplot`, `FactoMineR`, `factoextra`

**Secciones del notebook:**
1. Visualización y familiarización del dataset
2. Análisis de correlación y componentes principales
3. Análisis de ANOVA
4. Desarrollo y justificación de variables
5. Estudio de regresión lineal
6. Conclusiones y evaluación del modelo

---

## 📚 Referencias

- Dataset: Boston Housing Data (originalmente de Harrison & Rubinfeld, 1978)
- Técnicas estadísticas: Regresión lineal múltiple, PCA, ANOVA
- Librerías utilizadas: tidyverse, FactoMineR, factoextra

---

## 📝 Autor

**Modelo de Regresión Lineal - Predicción del Valor de Viviendas en Boston**  
Análisis realizado: Enero 2026  
Actualizado: Enero 2026
