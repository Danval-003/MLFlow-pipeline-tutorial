# README — MLflow + scikit-learn Pipeline (Iris)

Este cuadernito muestra, de forma sencilla y reproducible, cómo **entrenar un modelo con scikit-learn**, **encapsular el preprocesamiento en un `Pipeline`** y **registrar todo en MLflow** (parámetros, métricas y el propio modelo) para que se pueda comparar, versionar y servir más adelante.

> Pensado para correr local, sin enredos. Si ya usás un tracking server remoto, también te dejo cómo apuntar el cuaderno hacia allá.

---

## 🧰 Requisitos

* Python 3.10+
* `pip` y `virtualenv` (o `venv`)
* Paquetes: `mlflow`, `scikit-learn`, `pandas`, `matplotlib`, `ipykernel`

### Instalación rápida

**Windows (PowerShell):**

```powershell
python -m venv .venv
# Si PowerShell bloquea scripts:
# Set-ExecutionPolicy -Scope CurrentUser RemoteSigned
. .\.venv\Scripts\Activate.ps1
pip install -U pip
pip install mlflow scikit-learn pandas matplotlib ipykernel
python -m ipykernel install --user --name mlflow-iris
```

**macOS / Linux (bash):**

```bash
python -m venv .venv
source .venv/bin/activate
pip install -U pip
pip install mlflow scikit-learn pandas matplotlib ipykernel
python -m ipykernel install --user --name mlflow-iris
```

---

## ▶️ Cómo ejecutar el cuaderno

1. Activa tu entorno y abre Jupyter (o VS Code):

   ```bash
   jupyter lab
   # o
   jupyter notebook
   ```

2. Abre `mlflow_pipeline.ipynb` y selecciona el kernel **mlflow-iris** (o el que creaste).

3. Corre las celdas en orden. El cuaderno:

   * Carga el dataset **Iris** como DataFrame.
   * Arma un **Pipeline** con `StandardScaler` + `RandomForestClassifier`.
   * Entrena y evalúa (accuracy, F1 macro).
   * **Registra** parámetros, métricas y el **modelo** en MLflow (con `signature` e `input_example`).

---

## 📊 Ver resultados en MLflow UI

### Opción A: archivo local (por defecto)

Si no configurás nada, MLflow usa un **file store** local (`./mlruns`). Para abrir la UI:

```bash
mlflow ui
# luego visita http://127.0.0.1:5000
```

Buscá el experimento `Iris_Pipeline_Demo` y el run `iris_rf_pipeline`.

### Opción B: tracking server

Si ya tenés un servidor (local o remoto), antes de correr el cuaderno:

```bash
# Ejemplo local con backend SQLite
mlflow server --host 127.0.0.1 --port 5000 \
  --backend-store-uri sqlite:///mlflow.db \
  --default-artifact-root ./mlruns
```

Y en el cuaderno (o terminal) apunta el tracking URI:

```bash
# Windows PowerShell
$env:MLFLOW_TRACKING_URI = "http://127.0.0.1:5000"

# macOS/Linux
export MLFLOW_TRACKING_URI="http://127.0.0.1:5000"
```

> Opcional: también podés definir `MLFLOW_REGISTRY_URI` si usás **Model Registry**.

---

## 🧱 ¿Qué hace el pipeline?

* **Preprocesa** con `StandardScaler` y **entrena** un `RandomForestClassifier` en un solo objeto `Pipeline`.
* Lo que se ajusta en entrenamiento es exatamente lo que se aplica en inferencia: **sin fugas** y con **reproducibilidad**.
* MLflow guarda:

  * **Parámetros** del modelo (p. ej., `n_estimators`).
  * **Métricas** (accuracy, F1 macro).
  * El **modelo** como artefacto, con **signature** (esquema de entrada/salida) e **input\_example**.

---

## ✅ Qué revisar en la UI

* Compará *runs* por **métricas** y **parámetros**.
* Abrí el artefacto **`model`** para ver la signature y, si querés, **cargalo** desde otro script con `mlflow.sklearn.load_model("runs:/<run_id>/model")`.
* Si usás **Model Registry**, podés registrar y promover el mismo modelo a *Staging* o *Production*.

---

## 🩹 Troubleshooting rápido

* **`RESOURCE_ALREADY_EXISTS` al crear experimento**
  El nombre ya existe (quizá “borrado”). Restauralo o usa otro nombre. En file store local podés “hard-delete” desde `mlruns/.trash`, pero lo más limpio es **restaurar** vía API.

* **Puerto 5000 ocupado**
  Cambiá el puerto (`--port 5001`) o matá el proceso que lo usa.

* **Kernel no ve MLflow**
  Asegurate de instalar paquetes **dentro** del entorno y seleccionar el **kernel** correcto en Jupyter.

* **No puedo activar el venv en PowerShell**
  Ejecutá una sola vez:

  ```powershell
  Set-ExecutionPolicy -Scope CurrentUser RemoteSigned
  ```

---

## 📦 Estructura (mínima)

```
.
├─ mlflow_pipeline.ipynb   # el cuadernito
├─ mlruns/                 # carpeta de artefactos local (si usás file store)
└─ mlflow.db               # si servís con SQLite (opcional)
```

---

## Licencia

Libre uso educativo/demostrativo. Ajustá y reutilizá lo que te sirva.
