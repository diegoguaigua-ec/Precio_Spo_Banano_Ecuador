# Prediccion Precio Spot del Banano - Ecuador

## Objetivo
Predecir el precio spot semanal del banano en Ecuador (USD/caja) con horizonte de 4 semanas.

## Arquitectura
- `model/` - Notebook principal con todo el pipeline (48 celdas)
- `config.yaml` - Configuracion centralizada (rutas, hiperparametros, APIs, validacion)
- `old/` - Modelo SARIMAX previo en R, datasets MVP, documentos de analisis
- `data/` - Archivos Excel de datos (no versionados)
- `.claude/skills/` - Skills de senior-data-scientist y senior-data-engineer

## Modelo Ganador: Lasso Regression
- **RMSE**: 1.12 (11.9% mejor que Naive baseline)
- **R2**: 0.66
- **MAPE**: 12.49%
- **Directional Accuracy**: 63.2%
- XGBoost V3 (15 features) y Ensemble como modelos secundarios

## Fuentes de Datos
- **Excel (local/Drive):** Enfunde (Ecuador, Colombia, Costa Rica), Precios Spot, Clima, Exportaciones
- **FRED API:** Diesel Gulf, WTI, Brent, Fertilizante Nitrogenado, PPI Fletes Maritimos
- **Yahoo Finance:** Baltic Dry Index (BDRY)

## Pipeline
1. Carga de datos multi-fuente (Excel + APIs) con data quality checks
2. Consolidacion en df_master (~270 filas x 19 columnas base)
3. Imputacion con reporte de % valores reales vs imputados
4. Feature engineering: temporales, lags (Granger causality), ciclicas, momentum/rolling
5. Seleccion de features por importancia (top 15)
6. Modelos: Baselines (Naive, MA), Lasso/Ridge/ElasticNet, XGBoost V3, Optuna tuning, Ensemble
7. Validacion: Walk-forward (expanding window) + Held-out test (20 semanas)
8. Forecast de 4 semanas con intervalos de confianza

## Configuracion
- `config.yaml` contiene todos los parametros: rutas, APIs, lags, hiperparametros, validacion
- El notebook lee config.yaml con fallback a valores por defecto si no existe
- FRED_API_KEY: variable de entorno (local) o Colab Secrets

## Ejecucion
- **Local**: `pip install -r requirements.txt`, configurar `FRED_API_KEY` como variable de entorno, datos en `../data/`
- **Google Colab**: mount a Drive, FRED_API_KEY en Colab Secrets
- Ver `requirements.txt` para dependencias (incluye pyyaml)

## Decisiones de Diseno
- Walk-forward validation (initial_train=170, test_size=4)
- Lags basados en analisis de Granger causality y cross-correlation
- Fechas ISO 8601 semanales alineadas a domingo
- Target: Precio (USD/caja)
- Lasso supera a XGBoost porque dataset pequeno (~258 obs) favorece modelos lineales regularizados

## Datos del Dataset
- ~258 observaciones utiles (2021-2026) despues de crear lags
- Frecuencia: semanal (domingos)
- 15 features seleccionadas (de ~47 generadas)
