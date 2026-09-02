# Spotify Popularity ML — EP1 Machine Learning 

*Evaluación Parcial 1**: exploración y preprocesamiento de datos, primera etapa del proyecto semestral en el cual analizamos y modelamos la popularidad de canciones en Spotify.

## Integrantes

- Karla Hoch
- Sebastián Huaiquimilla

## Contexto de negocio

Una plataforma de streaming desea comprender qué características musicales influyen en la popularidad de las canciones, para apoyar estrategias de recomendación y promoción.

**Preguntas de negocio:**
- ¿Qué variables influyen en la popularidad?
- ¿Es posible clasificar canciones según su popularidad?
- ¿Qué patrones presentan los distintos géneros?

## Dataset

- **Fuente:** [Spotify Tracks Dataset — Kaggle](https://www.kaggle.com/datasets/maharshipandya/-spotify-tracks-dataset)
- **Registros:** 114.000 canciones
- **Variables:** 21 (danceability, energy, loudness, speechiness, acousticness, instrumentalness, valence, tempo, track_genre, popularity, entre otras)
- **Variable objetivo:** `popularity`

> Nota: por alcance del caso, no se aplican técnicas de NLP sobre variables textuales (nombres, artistas, álbumes, géneros). El análisis se limita a variables estructuradas (numéricas y categóricas).

## Estructura del repositorio

    spotify-popularity-ml/
    ├── README.md
    ├── notebooks/
    │   └── spotify_preprocesamiento.ipynb
    └── data/
        ├── raw/
        │   └── Spotify_Tracks_Dataset.csv
        └── processed/
            └── spotify_dataset_procesado.csv

## Cómo ejecutar

1. Clonar el repositorio y abrirlo en GitHub Codespaces (o Jupyter local).
2. Instalar dependencias:

       pip install pandas numpy matplotlib scikit-learn

3. Abrir `notebooks/spotify_preprocesamiento.ipynb` y ejecutar todas las celdas en orden (**Run All**).
4. El dataset procesado se genera automáticamente en `data/processed/spotify_dataset_procesado.csv`.

## Desarrollo (resumen del preprocesamiento)

1. **Descripción general:** carga del dataset y análisis de sus 21 variables; se descartan columnas de texto/identificadores (no se usa NLP).
2. **Selección de características:** eliminación de columnas no utilizables como predictoras (`track_id`, nombres, álbum, artista).
3. **Valores faltantes y atípicos:**
   - Sin nulos explícitos, pero se detectaron valores "disfrazados" (`tempo=0`, `duration_ms=0`), imposibles en la realidad → imputados con la mediana.
   - Outliers detectados con IQR y visualizados con boxplots. Se aplicó *capping* en `duration_ms` y `loudness`; se conservaron los outliers de variables acotadas [0,1] (`speechiness`, `instrumentalness`, `liveness`) por ser naturales de su distribución.
4. **Correlación:** se identificó y eliminó `energy` por alta redundancia con `loudness` y `acousticness`.
5. **Escalamiento y codificación:** `RobustScaler` para variables numéricas (por presencia de outliers legítimos); One-Hot Encoding para `key` y `track_genre` (variables nominales).

## Resultados y aprendizajes

- Ninguna variable de audio se correlaciona fuertemente con `popularity` (máx. -0.10) — sugiere que factores externos (artista, promoción, contexto) influyen más que el sonido en sí.
- El manejo de "valores faltantes disfrazados" (ceros que en realidad son datos ausentes) fue clave: sin revisar el significado de cada variable, se habrían pasado por alto.
- El One-Hot Encoding sobre `track_genre` (114 categorías) aumentó fuertemente la dimensionalidad (de 15 a 139 columnas), evidenciando una limitación práctica de esta técnica con variables de muchas categorías.
- El dataset queda técnicamente listo para modelar, pero probablemente se necesiten variables adicionales (fuera del dataset original) para lograr buen desempeño prediciendo popularidad.