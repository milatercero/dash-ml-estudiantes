# Centro de alertas académicas (Dash + scikit-learn)

Aplicación Dash que explora una cohorte sintética de estudiantes y estima el riesgo de abandono con un `Pipeline` de scikit-learn (regresión logística). Desplegada como **Web Service en Render**.

**URL pública:** https://TU-SERVICIO.onrender.com  <!-- reemplaza con tu URL -->

## Estructura

```
app.py            # Interfaz Dash, callbacks y objeto WSGI `server`
train_model.py    # Genera datos sintéticos, entrena y guarda el Pipeline
requirements.txt  # Dependencias (incluye gunicorn)
assets/style.css  # Estilos
render.yaml       # Configuración declarativa del servicio (opcional)
```

Los archivos `data/estudiantes.csv` y `model/modelo_abandono.joblib` **no** se suben al repositorio: se generan en el build de Render al ejecutar `train_model.py` (semilla 42, reproducible).

## Ejecución local

```bash
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
python train_model.py
python app.py                    # http://127.0.0.1:8050
```

Para activar el modo debug localmente: `DASH_DEBUG=true python app.py`.

## Configuración en Render

| Campo | Valor |
|---|---|
| Tipo | Web Service |
| Runtime | Python 3 |
| Branch | main |
| Build Command | `pip install -r requirements.txt && python train_model.py` |
| Start Command | `gunicorn app:server --bind 0.0.0.0:$PORT --workers 1 --timeout 120` |
| Plan | Free |
| Health Check Path | `/` |

## Limitaciones y uso responsable

- Datos 100 % sintéticos; no contiene información real de estudiantes.
- La alerta apoya una revisión humana; no debe automatizar sanciones ni exclusiones.
- Plan Free: el servicio se suspende tras 15 min sin tráfico y tarda ~1 min en reactivarse; el sistema de archivos es efímero.
- Antes de un uso real se requeriría autenticación, gobierno de datos, monitoreo y evaluación de sesgos entre grupos.
