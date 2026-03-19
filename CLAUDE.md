# Prediccion Precio Spot del Banano - Ecuador

## Objetivo
Predecir el precio spot semanal del banano en Ecuador (USD/caja) con horizonte de 4 semanas usando XGBoost.

## Arquitectura
- `model/` - Notebook principal con todo el pipeline (Google Colab)
- `old/` - Modelo SARIMAX previo en R, datasets MVP, documentos de analisis
- `.claude/skills/` - Skills de senior-data-scientist y senior-data-engineer

## Fuentes de Datos
- **Google Drive (Excel):** Enfunde (Ecuador, Colombia, Costa Rica), Precios Spot, Clima, Exportaciones
- **FRED API:** Diesel Gulf (DDFUELUSGULF), WTI (DCOILWTICO), Brent (DCOILBRENTEU), Fertilizante Nitrogenado (PCU325311325311A), PPI Fletes Maritimos (PCU483111483111)
- **Yahoo Finance:** Baltic Dry Index (BDRY)

## Pipeline
1. Carga de datos multi-fuente (Excel + APIs)
2. Consolidacion en df_master (~270 filas x 19 columnas base)
3. Imputacion de valores faltantes
4. Feature engineering: temporales, lags (Granger causality), ciclicas (sin/cos)
5. Seleccion de features por importancia
6. XGBoost con walk-forward validation (expanding window)
7. Forecast de 4 semanas con intervalos de confianza
8. Visualizacion interactiva con Plotly

## Decisiones de Diseno
- Walk-forward validation para respetar dependencia temporal
- Lags basados en analisis de Granger causality y cross-correlation
- Fechas ISO 8601 semanales alineadas a domingo
- Target: Precio (USD/caja)

## Ejecucion
- Corre en Google Colab con mount a Google Drive
- Ruta Drive: `/content/drive/MyDrive/PRECIO SPOT BANANO/`
- Ver `requirements.txt` para dependencias

## Datos del Dataset
- ~218 observaciones utiles (2021-2026) despues de crear lags
- Frecuencia: semanal (domingos)

## Problemas Conocidos
- Dataset pequeno (218 obs para 47 features)
- Overfitting: R2 train ~0.94 vs R2 test ~0.51
