# Glassdoor Tech Market Intelligence 📊🐍

¡Bienvenido al repositorio del proyecto **Glassdoor Tech Market Intelligence**! Esta plataforma avanzada de Business Intelligence ofrece una radiografía estratégica del mercado laboral tecnológico a partir de un dataset de ofertas de empleo en Glassdoor. El proyecto combina técnicas de ingeniería de datos con Python y modelado analítico dinámico en Power BI para explorar volúmenes de contratación, brechas salariales y la adopción de herramientas técnicas esenciales.

---

## 🎯 Objetivos del Proyecto
*   **Analizar la Demanda Laboral:** Identificar la concentración de vacantes por perfil analítico y su distribución geográfica.
*   **Evaluar el Panorama Financiero:** Comparar el salario promedio anual frente al techo financiero (salario máximo) e identificar el impacto de la experiencia (`seniority`).
*   **Medir la Adopción Tecnológica:** Cuantificar la presencia de habilidades críticas (Python, Excel, AWS, Spark, R) mediante un modelo de datos desdinamizado y escalable.

---

## 🛠️ Tecnologías Utilizadas
*   **Python:** Para la ingesta, limpieza y transformación estructural de las variables tecnológicas binarias.
*   **Pandas:** Biblioteca clave para la manipulación y desdinamización (*unpivot*) del dataset.
*   **Matplotlib:** Herramienta esencial para la creación de gráficos y la visualización estática de las variables del dataset.
*   **NumPy:** Base matemática del ecosistema encargada del cómputo numérico y la operación eficiente con matrices o arreglos multidimensionales.  
*   **Power BI Desktop:** Para el modelado de datos, diseño de la arquitectura de información (UI/UX) y analítica visual.
*   **DAX (Data Analysis Expressions):** Para el cálculo dinámico de métricas bajo contextos de filtro avanzados.

---

## 🏗️ Fase 1: Ingeniería de Datos (Python)
El dataset original presentaba una estructura "ancha" con columnas binarias independientes (`python_yn`, `R_yn`, `spark`, `aws`, `excel`) que contenían `1` si la oferta requería la habilidad y `0` si no. Para evitar la creación de múltiples medidas DAX redundantes y posibilitar un gráfico de barras unificado, se desarrolló un script en Python utilizando la técnica de **desdinamización** (`pd.melt`).

```python
import pandas as pd

# 1. Definición de columnas de contexto y variables tecnológicas
columnas_contexto = ['job_simp', 'job_state', 'seniority', 'hourly']
columnas_habilidades = ['python_yn', 'R_yn', 'spark', 'aws', 'excel']

# 2. Transformación de estructura ancha a estructura larga (Unpivot)
df_habilidades = pd.melt(
    df,
    id_vars=columnas_contexto,
    value_vars=columnas_habilidades,
    var_name='Herramienta',
    value_name='Requerido'
)

# 3. Limpieza estética de las etiquetas
reemplazos = {
    'python_yn': 'Python',
    'R_yn': 'R',
    'spark': 'Spark',
    'aws': 'AWS',
    'excel': 'Excel'
}
df_habilidades['Herramienta'] = df_habilidades['Herramienta'].replace(reemplazos)

# 4. Exportación del dataset derivado optimizado
df_habilidades.to_csv('glassdoor_habilidades.csv', index=False)
```

**Resultado técnico:** Esta optimización permitió consolidar todas las tecnologías en una sola columna categórica (`Herramienta`) acompañada de su indicador (`Requerido`), reduciendo la complejidad del modelo en Power BI.

---

## 🧠 Fase 2: Modelado de Datos y DAX Avanzado (Power BI)
El modelo se estructuró de manera eficiente utilizando expresiones DAX diseñadas para responder dinámicamente a los segmentadores del reporte sin saturar el rendimiento del motor analítico.

### Métricas Core Implementadas:
*   **Total de Ofertas:**
    ```dax
    Total_ofertas = COUNTROWS(glassDoor)
    ```
*   **Porcentaje de Distribución por Perfil:** (Modificación del contexto de filtro ignorando la fila actual para obtener el denominador global).
    ```dax
    Porcentaje_ofertas_perfil = 
    DIVIDE(
        [Total_ofertas], 
        CALCULATE([Total_ofertas], REMOVEFILTERS(glassDoor[job_simp]))
    )
    ```
*   **Salario Promedio Anual (Excluyendo modalidad por hora y ajustando escala):**
    ```dax
    Salario_promedio_anual = 
    CALCULATE(
        AVERAGE(glassDoor[avg_salary]),
        glassDoor[hourly] = 0
    ) / 10
    ```
*   **Promedio de Salario Máximo Anual (Techo Financiero):**
    ```dax
    Promedio_salario_maximo_anual = 
    CALCULATE(
        AVERAGE(glassDoor[max_salary]),
        glassDoor[hourly] = 0
    ) / 10
    ```
*   **Porcentaje de Adopción Tecnológica (Aplicado sobre la tabla secundaria de Python):**
    ```dax
    %_requerido_habilidades = AVERAGE(glassdoor_habilidades[Requerido])
    ```

---

## 🎨 Fase 3: Arquitectura Visual (UI/UX)
El reporte adopta un **Tema Oscuro Premium (Dark Mode)** (`#111625` y `#1B2234`) diseñado para entornos analíticos corporativos de alta concentración visual, implementando acentos en colores neón (**Cian Eléctrico, Morado y Rosa Neón**) para establecer una jerarquía de información intuitiva.

La solución se divide en tres pantallas de análisis especializado:
1.  **Mercado Laboral:** Vista general del ecosistema, volumen por estados y participación de mercado de cada rol analítico.
2.  **Salarios:** Análisis financiero profundo que compara los ingresos medios contra los techos salariales máximos, segmentado por el impacto del *seniority*.
3.  **Perfiles y Habilidades:** Radiografía interactiva impulsada por el dataset desdinamizado que revela qué tecnologías demanda cada puesto y experiencia específica.

---

## 📈 Conclusiones Clave del Análisis
1.  **Liderazgo Técnico dual:** **Python** se alza como la tecnología más solicitada en el mercado general (**52.8%**), compartiendo el liderato de cerca con **Excel** (**52.3%**), lo que demuestra la vigencia de las bases operacionales en los equipos de datos.
2.  **Dominio de Perfiles:** Las ofertas orientadas a **Data Scientist** son el motor principal del dataset, abarcando el **37.6%** de la demanda global.
3.  **Potencial Económico:** El análisis financiero expone que el perfil de **Director** lidera la compensación máxima promedio con **\$208.21K**, seguido de los profesionales de Machine Learning (**MLE**) con **\$162.50K** y **Data Scientists** con un techo promedio de **\$145.25K**.

---

## 📂 Estructura del Repositorio
*   `/data`: Datasets originales y procesados por el script.
*   `/scripts`: Archivos de código Python (`.py` o `.ipynb`).
*   `/model`: Archivo principal del reporte de Power BI (`.pbix`).

---
*Proyecto desarrollado con fines analíticos y demostración de competencias técnicas en Arquitectura e Inteligencia de Datos.*
