# Análisis Exploratorio de Clientes de Lealtad (Módulo 3)

Este documento resume las fases de **Exploración, Limpieza y Visualización** realizadas sobre los datos de actividad y perfil de los clientes de lealtad, con el objetivo de identificar patrones de comportamiento y segmentación.

---

## 🛠️ Fase 1: Exploración y Limpieza de Datos

### 1. Unión de Datos y Estructura

Los dos conjuntos de datos (`df_activity` y `df_history`) se unieron eficientemente utilizando un **`merge` de tipo `left`** basado en la clave **`Loyalty Number`** para asegurar la retención de todos los registros de actividad.

| Detalle | Observación | Código Ejemplo |
| :--- | :--- | :--- |
| **Unión** | Se utilizó `pd.merge(df_activity, df_history, on='Loyalty Number', how='left')` | `df_u = pd.merge(df_activity, df_history, on='Loyalty Number', how='left')` |
| **Columnas** | Se convirtieron todos los nombres de columnas a minúsculas para estandarización. | `df_u.columns = df_u.columns.str.lower()` |

### 2. Tratamiento de Valores Faltantes (Nulos) y Erróneos

| Columna | Problema Detectado | Solución Aplicada | Justificación |
| :--- | :--- | :--- | :--- |
| **`salary`** | **Nulos (102,672)** (más del 25% de la base) y **Valores Negativos** (ej. $-58,486.00$). | 1. Conversión a **Valor Absoluto** (para corregir sel signo). 2. **Imputación con la Mediana** (para rellenar los nulos). | Se usó la **mediana** por su **robustez** ante los *outliers* extremos de salario (hasta $407,228). El salario negativo se asumió como un error de signo. |
| **`cancellation year/month`** | **Nulos Masivos** (355,560 registros faltantes). | **Imputación Lógica:** Se creó una columna `loyalty state` donde los `NaN` se etiquetan como **'active'** y los registros con año de cancelación se etiquetan como **'cancel'**. | El nulo es **estructural**: indica un cliente que **no ha cancelado**, lo cual es un dato valioso para el análisis de abandono (*Churn*). |

### 3. Tratamiento de Outliers (Valores Atípicos)

| Columna | Outlier Detectado | Decisión y Justificación |
| :--- | :--- | :--- |
| **`salary`** | Alto sesgo con un máximo de **$407,228** (vs. 75% en $88,612$). | **Se conserva:** El boxplot mostró que los clientes de altos ingresos se distribuyen en todas las categorías de tarjetas de lealtad, por lo que son datos válidos de segmentos de alto poder adquisitivo. |
| **`distance`** | Máximo de **6,293 km** (vs. 75% en 2,336 km). | **Se conserva:** Representa a viajeros de larga distancia/intercontinentales, un segmento clave. El boxplot posterior confirmó que la distancia está fuertemente relacionada con las tarjetas de lealtad. |
| **`points redeemed`** | El 75% de los clientes tiene **0 canjes**. | **Se conserva:** Esto no es un *outlier*, sino una **oportunidad de negocio** (clientes que acumulan pero no canjean) que debe ser investigada. |

---

## 📊 Fase 2: Visualización y Hallazgos Clave

Se utilizaron gráficos especializados para responder preguntas específicas de negocio:

| Pregunta de Negocio | Gráfico Utilizado | Hallazgo Principal |
| :--- | :--- | :--- |
| **1. Distribución de vuelos reservados por mes.** | **Gráfico de Barras** (agrupado por año) | Las reservas son más altas en los meses de **verano (6, 7, 8)** y en **diciembre** (festividades), mostrando una clara estacionalidad. El **volumen de reservas aumentó en 2018**. |
| **2. Relación entre distancia y puntos acumulados.** | **Gráfico de Dispersión** | Existe una **correlación lineal y positiva casi perfecta** entre la distancia volada y los puntos acumulados. Las **múltiples líneas** indican diferentes tasas de acumulación (fórmulas), probablemente por el tipo de tarjeta o clase de vuelo. |
| **2b. (Relación distancia vs. redención)** | **Gráfico de Dispersión** | **Correlación nula** para la redención. La mayoría de los clientes se concentran en 0 puntos redimidos, indicando una **barrera para el canje** que debe ser investigada. |
| **3. Distribución de clientes por provincia.** | **Gráfico de Conteo (`countplot`)** | La base de clientes está altamente concentrada en **Ontario**, seguido por British Columbia y Quebec. |
| **4. Salario promedio por nivel educativo.** | **Gráfico de Barras** | Existe una **relación directa** clara: a mayor nivel educativo (ej., Posgrado), mayor es el salario promedio del cliente. |
| **5. Proporción de tarjetas de lealtad.** | **Gráfico de Pastel** | La mayoría de la base de clientes posee la tarjeta de nivel más bajo: cerca del **50% tiene la tarjeta STAR**. El gráfico de barras complementario (`CLV` vs. `Loyalty Card`) confirmó que el **CLV es el factor determinante** de la clasificación. |
| **6. Distribución por estado civil y género.** | **Gráfico de Barras Agrupadas** | Tanto hombres como mujeres tienen su mayor proporción en el estado **Casado** (`Married`). |
