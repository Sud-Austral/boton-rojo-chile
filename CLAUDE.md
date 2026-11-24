# CLAUDE.md — BR-HR (boton-rojo-chile)

Qué es, cómo se corre y cómo se despliega: [`README.md`](README.md), que manda en eso. Este archivo es para quien va a
modificar el código. Creado el 2026-11-24 leyendo el repositorio; lo no ejecutado se marca así.

## Antes de tocar nada

- Las reglas que no se negocian están en [`docs/09_DECISIONES_NO_NEGOCIABLES.md`](docs/09_DECISIONES_NO_NEGOCIABLES.md)
  y en [`.antigravity/rules.md`](.antigravity/rules.md). Las que más se rompen sin querer:
  `insumos/` es inmutable; BR-CONAF (`src/baseline/`) es una línea base separada que no se "mejora"; el test ciego
  2022–2024 no se usa para seleccionar ni ajustar modelos; nada de leakage temporal; no interpolar flags/categorías con
  bilinear; credenciales de GEE/R2/Railway nunca en git ni en el navegador.
- El proyecto se construyó con Google Antigravity: `GEMINI.md`, `AGENTS.md`, `SUPERPROMPT_ANTIGRAVITY.md`, `prompts/` y
  `README_PRIMERO.md` son el paquete de arranque. `AGENTS.md` describe roles conceptuales, no herramientas.
  `PLAN_IMPLEMENTACION.md` (2026-08-29) es la propuesta original; el código fue más allá (API, frontend, pipeline diario).

## Trampas

- Los recuentos "106 rojas / 114 amarillas" de los commits del 2026-08-30 se obtuvieron fijando una ventana exacta de 24 h
  de un solo día en el script de GEE (`7d065d3`, `257810e`). Cambiar la ventana o el filtro de bandas cambia la
  comparación con el Botón Rojo oficial.
- `daily_inference.yml` commitea a `main` con `[skip ci]`; el `push` y el `commit` llevan `|| echo`, así que un fallo de
  publicación sale verde. Desde el 2026-08-30 no hay commits del bot en `data/r2_export/` (comprobado con `git log` el
  2026-11-24; por qué, no lo comprobé).
- El `Dockerfile` copia `insumos/` a la imagen; `insumos/Boton_Rojo.zip` no está versionado.
- Tres módulos de prueba importan `h3`; sin él la recolección falla (visto el 2026-11-24: 22 pasan, 3 errores de colección).

## Git

Rama por defecto `main`. Cada push a `main` publica GitHub Pages (`deploy_pages.yml`).
