# BR-HR — Botón Rojo de Alta Resolución

Pronóstico diario de peligro de incendios forestales en Chile por celda H3 (resolución 8) y por comuna, que conserva el
Botón Rojo de CONAF como línea base (BR-CONAF) y agrega una versión recalibrada (BR-CAL) y modelos probabilísticos de
ignición (P-IGN) y de gran incendio (P-GF). Diseño científico en [`docs/`](docs/) (01 a 10); límites que no se rompen en
[`docs/09_DECISIONES_NO_NEGOCIABLES.md`](docs/09_DECISIONES_NO_NEGOCIABLES.md).

> `README_PRIMERO.md` es el instructivo del **paquete de arranque** con que se inició el proyecto en Google Antigravity
> (agosto de 2026). Lo que describe como objetivo ya está construido en `src/`; se conserva como historia.

## Arquitectura (estado del 2026-11-24, leído del código)

```text
GitHub Actions (cron 11:00 UTC, daily_inference.yml)
  └─ python src/gee/gee_inference_pipeline.py   (Earth Engine GFS + NASA FIRMS)
       └─ escribe data/r2_export/*.json y lo commitea a main
GitHub Pages (deploy_pages.yml, en cada push a main)
  └─ publica frontend/ + data/r2_export/
API FastAPI (src/api/main.py; Dockerfile + railway.json para Railway, Procfile)
  └─ /health, /api/v1/forecast/latest/{summary,communes,commune/{comuna_name},h3/{h3_id},h3-geojson},
     POST /api/v1/forecast/trigger, /api/v1/firms/active-points
```

`frontend/wrangler.toml` declara además un proyecto de Cloudflare Pages. Qué despliegue está realmente en uso (Pages de
GitHub, Cloudflare o Railway) **no lo comprobé**. Desde el 2026-08-30 no hay ningún commit del bot del pronóstico diario en
`data/r2_export/`: o el workflow no ha corrido con éxito o no produjo cambios (no lo comprobé en Actions).

## Estructura

```text
src/baseline/   BR-CONAF: réplica del Botón Rojo original (matriz PI, tablas)
src/training/   QA/H3, muestreo caso-control, modelos (BR-CAL, P-IGN, P-GF), validación y test ciego
src/gee/        scripts de Earth Engine (pipeline diario, assets de comunas y CONAF)
src/shared/     exportación a R2/PMTiles, recálculo de escenarios, utilidades de tiempo
src/api/        API FastAPI
frontend/       visor estático (index.html, app.js)
artifacts/      modelos entrenados y model cards (versionados)
data/r2_export/ salida publicada del pronóstico
docs/generated/ informes producidos por el agente (inventario, auditoría, evaluaciones, guías)
insumos/        fuentes originales: inmutables; Boton_Rojo.zip y el CSV con "(1)" no se versionan (.gitignore)
```

## Cómo correrlo

```bash
pip install -r requirements.txt
python -m pytest tests -q
uvicorn src.api.main:app --port 8000      # luego: curl http://localhost:8000/health
```

Pruebas, 2026-11-24, con un Python local sin `h3` instalado: 22 pasan y 3 módulos no se pueden recolectar por
`ModuleNotFoundError: h3` (`--continue-on-collection-errors`). Con `requirements.txt` completo no lo comprobé. Arrancar la
API y el pipeline de Earth Engine (necesita `GEE_SERVICE_ACCOUNT_JSON` y `FIRMS_MAP_KEY`) no lo comprobé. Variables de
ejemplo en `templates/.env.example`.
