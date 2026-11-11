# 📊 EPH AI Explorer: Asistente Analítico de Microdatos

[![Streamlit App](https://static.streamlit.io/badges/streamlit_badge_black_white.svg)](https://eph-llm-explorer.streamlit.app/)

Prueba la aplicación en vivo aquí: **[EPH AI Explorer](https://eph-llm-explorer.streamlit.app/)**

## 🎯 Sobre el Proyecto

EPH AI Explorer es una interfaz interactiva de análisis y visualización de datos diseñada para democratizar el acceso a los microdatos de la Encuesta Permanente de Hogares (EPH - INDEC).

A diferencia de los dashboards tradicionales —que limitan al usuario a filtros y gráficos predefinidos—, esta herramienta integra Inteligencia Artificial (Google Gemini) para traducir preguntas de negocio en lenguaje natural a código SQL estructurado. El resultado es una plataforma de análisis *ad-hoc* donde los tomadores de decisiones pueden explorar la base de datos, extraer insights y generar visualizaciones complejas de forma dinámica e instantánea.

Este proyecto destaca la evolución de la presentación de datos: pasar de reportes estáticos a experiencias analíticas conversacionales, manteniendo el rigor metodológico del tratamiento de datos estructurados.

El repositorio reúne tres partes que se apoyan en la misma base de datos: la **aplicación** de consultas con IA, el **pipeline ETL** que construye esa base a partir de los datos públicos del INDEC, y dos **análisis de ejemplo** hechos sobre ella.

## 💡 Propuesta de Valor Analítico

El enfoque central de este desarrollo es el **Análisis de Datos y la Comunicación Visual**:

*   **Consultas Dinámicas (Text-to-SQL):** Permite a usuarios sin conocimientos técnicos interrogar una base de datos compleja (millones de registros) escribiendo sus preguntas en español.
*   **Visualización Automática (Plotly):** Dependiendo de la estructura de la consulta generada, la aplicación interpreta los resultados y renderiza gráficos interactivos para facilitar la comprensión de las tendencias.
*   **Rigor Metodológico Integrado:** El modelo está condicionado por un diccionario semántico estricto para aplicar correctamente los ponderadores poblacionales y las líneas de pobreza oficiales, asegurando que las inferencias estadísticas sean válidas.
*   **Arquitectura Desacoplada y Escalable:** La aplicación consulta los microdatos de la EPH a través de la base construida por el pipeline ETL de este mismo repositorio (carpeta [`etl/`](etl/)). Gracias a su diseño modular, con un pequeño cambio en el código, el sistema puede modificarse fácilmente para realizar consultas sobre cualquier otra base de datos o conectarse a otros modelos de inteligencia artificial.

## 🧩 Estructura del Repositorio

```
eph-llm-explorer/
├── app.py                         # Aplicación Streamlit (consultas en lenguaje natural)
├── requirements.txt
├── etl/                           # Pipeline ETL: descarga, valida y carga la EPH en SQLite
│   ├── README.md                  # Documentación técnica del pipeline
│   ├── eph_sql.py
│   └── EPH diseño.pdf
└── analisis/                      # Ejemplos de análisis sobre la base (Python + Stata)
    ├── descomposicion_pobreza/
    └── pobreza_universitaria/
```

Cómo se relacionan las partes:

```
INDEC (microdatos EPH y canastas)
        │
        ▼
   etl/eph_sql.py  ──►  eph.db (SQLite)  ──►  app.py (consultas con IA)
                              │
                              └──────────►  analisis/ (extractos + Stata)
```

| Carpeta | Qué contiene | Más información |
|---|---|---|
| Raíz (`app.py`) | La aplicación de Streamlit publicada en línea | Esta página |
| [`etl/`](etl/) | Descarga automática de la EPH (2016-2025), validación y carga incremental en SQLite, con vistas de pobreza | [README del ETL](etl/README.md) |
| [`analisis/`](analisis/) | Dos ejemplos que usan la misma base: descomposición del cambio en la pobreza y pobreza en estudiantes universitarios | [Ejemplos](#-análisis-de-ejemplo) |

## 🛠️ Stack Tecnológico

*   **Lenguaje:** Python
*   **Interfaz de Usuario:** Streamlit
*   **Modelado y Manipulación de Datos:** Pandas, SQL (SQLite)
*   **Visualización:** Plotly Express
*   **Integración IA:** SDK Nativo de Google Generative AI (Modelos familia Gemini Flash)
*   **Análisis de ejemplo:** Python (extractos de datos) y Stata (resultados y gráficos)

## 🚀 Instalación y Uso Local

Si deseas correr este proyecto en tu propio entorno y explorar el código, sigue estos pasos:

1. **Clonar el repositorio:**
   ```bash
   git clone https://github.com/Marco-D-Sosa/eph-llm-explorer.git
   cd eph-llm-explorer
   ```
2. **Instalar dependencias:**
   ```bash
   pip install -r requirements.txt
   ```
3. **Configurar la API Key:**

   Crea una carpeta oculta llamada `.streamlit` en la raíz del proyecto. Dentro de esa carpeta, crea un archivo `secrets.toml` y agrega tu clave de Google AI Studio con el siguiente formato:
   ```toml
   GEMINI_API_KEY = "tu_clave_api_aqui"
   ```
4. **Ejecutar la aplicación:**
   ```bash
   streamlit run app.py
   ```

La primera vez, la aplicación descarga la base de datos (~200 MB) desde el release `v1.0` del repositorio y la guarda como `eph.db` en la carpeta desde la que se ejecuta. Si quieres reconstruir la base desde las fuentes del INDEC, consulta el [README del pipeline ETL](etl/README.md).

## 📈 Análisis de Ejemplo

Como demostración de lo que se puede hacer una vez resuelta la preparación de los datos, la carpeta [`analisis/`](analisis/) incluye dos estudios que parten de la misma base que usa la aplicación.

### ¿Por qué cambió la pobreza?

Separa el cambio de un año a otro en dos causas: que **cambió el nivel de ingresos** de los hogares, o que **cambió cómo se reparten** esos ingresos. También incluye comparaciones entre períodos y escenarios hipotéticos.

<p align="center">
  <img src="analisis/descomposicion_pobreza/graficos/descomposicion_Anual.png" alt="Descomposición anual del cambio en la tasa de pobreza en efecto ingreso y efecto distribución" width="700">
</p>

Código: [`analisis/descomposicion_pobreza/`](analisis/descomposicion_pobreza/)

### Pobreza en estudiantes universitarios

Compara qué proporción de estudiantes universitarios vive en hogares pobres según estudien en universidad **pública o privada**, cómo trabajan y cómo se distribuyen por nivel de ingresos.

<p align="center">
  <img src="analisis/pobreza_universitaria/graficos/pobreza_pub_priv.png" alt="Evolución semestral de la pobreza en estudiantes universitarios, gestión pública y privada" width="700">
</p>

Código: [`analisis/pobreza_universitaria/`](analisis/pobreza_universitaria/)

> Son estimaciones propias con la metodología del INDEC, a partir de una encuesta por muestreo. Las limitaciones metodológicas están detalladas en el [README del pipeline ETL](etl/README.md#notas-metodológicas-y-limitaciones).

## 📄 Fuente de los Datos y Licencia

Fuente de los datos: INDEC (Encuesta Permanente de Hogares y canastas de pobreza), [www.indec.gob.ar](https://www.indec.gob.ar). Código bajo licencia MIT.
