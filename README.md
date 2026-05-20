# telecom-analysis

## Objetivo del proyecto

Este proyecto analiza el comportamiento de los clientes de ConnectaTel, una empresa de telecomunicaciones con operaciones en México y Colombia.

El objetivo principal es entender cómo los usuarios usan los servicios móviles, principalmente llamadas y mensajes, para identificar patrones de consumo, detectar valores atípicos y crear segmentos de clientes útiles para la toma de decisiones comerciales.

El análisis busca responder preguntas como:

- ¿Qué segmentos de clientes muestran mayor o menor uso de llamadas y mensajes?
- ¿Qué usuarios presentan comportamientos atípicos?
- ¿Cómo varía el uso según edad y tipo de plan?
- ¿Qué oportunidades existen para mejorar la oferta comercial y la experiencia del cliente?

## Datasets utilizados

El proyecto trabaja con tres archivos CSV:

### `plans.csv`

Contiene información de los planes actuales de ConnectaTel.

Variables esperadas:

- nombre del plan
- precio
- minutos incluidos
- GB incluidos
- costos por uso adicional

### `users_latam.csv`

Contiene información de los clientes.

Variables principales:

- `user_id`: identificador del usuario
- `first_name`: nombre del usuario
- `last_name`: apellido del usuario
- `age`: edad
- `city`: ciudad
- `reg_date`: fecha de registro
- `plan`: plan contratado
- `churn_date`: fecha de baja, si aplica

### `usage.csv`

Contiene el detalle del uso real de los servicios.

Variables principales:

- `id`: identificador del evento
- `user_id`: identificador del usuario
- `date`: fecha del evento
- `type`: tipo de evento, llamada o mensaje
- `duration`: duración de llamada
- `length`: longitud del mensaje

## Etapas del análisis

### 1. Carga y exploración inicial

Se cargaron los tres datasets con `pandas` y se revisó la estructura de cada archivo usando:

- `.head()`
- `.shape`
- `.info()`

Esto permitió identificar columnas, tipos de datos, número de registros y posibles problemas iniciales.

### 2. Identificación de problemas de calidad

Se revisaron valores nulos, sentinels y datos inconsistentes.

Hallazgos principales:

- En `age` se detectó el sentinel `-999`.
- En `city` se detectaron registros con `"?"`.
- En `reg_date` se detectaron fechas fuera de rango, con registros en 2026.
- En `usage`, las columnas `duration` y `length` contienen nulos que dependen del tipo de evento.
- En `date` de `usage` se detectaron valores nulos.

### 3. Limpieza básica de datos

Se aplicaron reglas de limpieza para preparar los datos antes del análisis:

- Se reemplazó `-999` en `age` con la mediana.
- Se reemplazó `"?"` en `city` con valores nulos.
- Se marcaron como nulas las fechas fuera del rango esperado.
- Se conservaron los nulos esperados en `duration` y `length`, ya que dependen del tipo de evento.

### 4. Resumen estadístico por usuario

Se creó una tabla agregada por `user_id` para resumir el comportamiento histórico de cada usuario.

Métricas creadas:

- `cant_mensajes`: cantidad total de mensajes
- `cant_llamadas`: cantidad total de llamadas
- `cant_minutos_llamada`: total de minutos de llamada

Después, esta tabla se combinó con `users_latam.csv` para crear una tabla base llamada `user_profile`.

### 5. Visualización de distribuciones y detección de outliers

Se analizaron histogramas y boxplots para las variables principales:

- `age`
- `cant_mensajes`
- `cant_llamadas`
- `cant_minutos_llamada`

También se usó el método IQR para detectar valores atípicos.

Hallazgos principales:

- `age` no presentó outliers relevantes después de la limpieza.
- `cant_mensajes` presentó outliers superiores.
- `cant_llamadas` presentó outliers superiores.
- `cant_minutos_llamada` presentó outliers superiores claros.

Los outliers se conservaron porque pueden representar usuarios intensivos y oportunidades comerciales.

### 6. Segmentación de clientes

Se crearon dos segmentaciones principales.

#### Segmentación por uso

Se creó la columna `grupo_uso`:

- `Bajo uso`: menos de 5 llamadas y menos de 5 mensajes
- `Uso medio`: menos de 10 llamadas y menos de 10 mensajes
- `Alto uso`: resto de casos

Distribución observada:

- Uso medio: 73.58%
- Bajo uso: 19.45%
- Alto uso: 6.98%

#### Segmentación por edad

Se creó la columna `grupo_edad`:

- `Joven`: edad menor a 30
- `Adulto`: edad menor a 60
- `Adulto Mayor`: resto de casos

La mayoría de los usuarios se concentra en el grupo Adulto, seguido de Adulto Mayor y Joven.

### 7. Insight ejecutivo

Se redactaron conclusiones enfocadas en negocio:

- La mayoría de clientes tiene un comportamiento de uso medio.
- Los clientes de alto uso son pocos, pero comercialmente relevantes.
- Los clientes de bajo uso pueden requerir campañas de activación.
- Los outliers no deben eliminarse sin evidencia de error.
- ConnectaTel puede usar los segmentos para diseñar planes, campañas y estrategias de retención.

## Cómo ejecutar el notebook

### Opción 1: Google Colab

1. Abre Google Colab.
2. Sube el notebook `.ipynb`.
3. Sube los archivos CSV requeridos.
4. Ajusta las rutas de lectura si es necesario.
5. Ejecuta las celdas en orden desde el inicio.

Ejemplo de carga si los archivos están en la carpeta `/datasets`:

```python
import pandas as pd

plans = pd.read_csv('/datasets/plans.csv')
users = pd.read_csv('/datasets/users_latam.csv')
usage = pd.read_csv('/datasets/usage.csv')
```

Si trabajas en Google Colab y subes los archivos manualmente, podrías usar:

```python
plans = pd.read_csv('plans.csv')
users = pd.read_csv('users_latam.csv')
usage = pd.read_csv('usage.csv')
```

### Opción 2: Jupyter Notebook local

1. Clona o descarga el repositorio.
2. Coloca los archivos CSV en la carpeta correspondiente.
3. Abre el notebook con Jupyter Notebook o JupyterLab.
4. Ejecuta las celdas en orden.

## Requisitos

Librerías utilizadas:

```python
pandas
numpy
matplotlib
seaborn
```

Instalación sugerida:

```bash
pip install pandas numpy matplotlib seaborn
```

## Guía breve de reproducción

1. Cargar los datasets:
   - `plans.csv`
   - `users_latam.csv`
   - `usage.csv`

2. Revisar estructura:
   - `.head()`
   - `.shape`
   - `.info()`

3. Detectar problemas de calidad:
   - valores nulos
   - sentinels
   - fechas fuera de rango
   - inconsistencias lógicas

4. Limpiar datos:
   - reemplazar sentinels
   - convertir fechas
   - marcar fechas inválidas
   - conservar nulos esperados

5. Crear tabla agregada por usuario:
   - cantidad de mensajes
   - cantidad de llamadas
   - minutos totales de llamada

6. Combinar uso con datos de clientes.

7. Visualizar distribuciones y boxplots.

8. Detectar outliers con IQR.

9. Crear segmentos:
   - `grupo_uso`
   - `grupo_edad`

10. Redactar conclusiones y recomendaciones de negocio.

## Conclusión

El proyecto permite convertir datos operativos de uso móvil en insights comerciales. La segmentación por uso y edad ayuda a ConnectaTel a entender mejor a sus clientes, detectar comportamientos atípicos y proponer acciones para mejorar planes, retención y experiencia del usuario.
