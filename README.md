# Informe Técnico - Fase de Despliegue Seguro (S-SDLC)

## 1. Estandarización del Entorno, Controles Preventivos (Shift-Left) y Gestión Segura de Releases

**Justificación Técnica**
Como paso previo a la contenerización y el despliegue de la aplicación web, se estableció un entorno de desarrollo estandarizado y seguro. El objetivo de esta fase es evitar discrepancias entre los entornos de desarrollo, pruebas y producción (garantizando la correcta separación de ambientes), prevenir la fuga de información sensible desde la máquina del desarrollador y asegurar un manejo controlado de *releases* y *patches*.

Para lograrlo, se implementaron las siguientes acciones en el ecosistema del proyecto:

* **Gestión de Dependencias y Entorno Reproducible (Uso de UV):**
Se inspeccionó el código base y se detectó la ausencia de un gestor de paquetes formal. Se adoptó **UV** como estándar mediante la creación del archivo `pyproject.toml` (versión `0.1.0`, `requires-python >=3.9`, dependencias `flask>=3.1`). Adicionalmente, se fijó el intérprete en `.python-version` (Python `3.12`) y se generó el archivo `uv.lock` (`uv sync --frozen`). Este enfoque congela las dependencias, garantizando construcciones (builds) reproducibles y deterministas para la futura imagen Docker.
* **Higiene del Repositorio y Prevención de Fugas (`.gitignore`):**
Se configuró un `.gitignore` estricto para evitar la exposición accidental de archivos críticos. Se excluyeron secretos (`.env`, `*.pem`, `*.key`), cachés locales (`.venv/`, `__pycache__/`) y bases de datos o carpetas de usuario (`*.db`, `uploads/`). Omitir la carpeta de subidas es vital, ya que es el vector principal de la vulnerabilidad de carga de archivos sin restricción presente en la aplicación base.


* **Controles de Pre-commit (Seguridad Preventiva):**
Se configuró una capa de seguridad en el entorno local mediante `pre-commit` (estándar análogo a Husky). Esta herramienta intercepta los commits para ejecutar análisis rápidos que fuerzan el uso de *Conventional Commits* y ejecutan escaneos (como `detect-secrets`) para bloquear la subida accidental de credenciales quemadas en el código base.
* **Integración Continua y Versionamiento Semántico (GitHub Actions):**
Se inicializó el repositorio local con `git init` (rama `main`) y un commit inicial limpio. Para el control del ciclo de vida, se implementó un flujo automatizado utilizando **Release-Please** mediante GitHub Actions. Esta práctica genera versiones semánticas y *changelogs* de forma automática, garantizando un control estricto sobre qué versión exacta de la aplicación se empaquetará, facilitando una estrategia segura de actualización y respuesta ante incidentes (manejo de releases).
