# Guía de Uso de `uv` en Proyectos Python

Este repositorio contiene documentación completa y ejemplos sobre el uso de `uv` como herramienta principal para la gestión de versiones de Python, entornos virtuales, dependencias y reproducibilidad en proyectos Python.

## 📚 Documentación

La documentación completa está disponible en:

**[docs/uso_de_uv_en_proyectos_python.md](docs/uso_de_uv_en_proyectos_python.md)**

Esta guía cubre:

- ✅ Instalación de `uv` en Windows, Linux y macOS
- ✅ Gestión de versiones de Python
- ✅ Creación y gestión de entornos virtuales
- ✅ Instalación y bloqueo de dependencias
- ✅ Uso en proyectos de Machine Learning
- ✅ Integración con Docker
- ✅ Integración con CI/CD
- ✅ Errores comunes y soluciones
- ✅ Buenas prácticas profesionales

## 🚀 Inicio Rápido

### 1. Instalar `uv`

**Windows (PowerShell):**
```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

**Linux/macOS:**
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

**macOS (Homebrew):**
```bash
brew install uv
```

### 2. Clonar este repositorio

```bash
git clone <url-del-repositorio>
cd Guía_uso_uv
```

### 3. Levantar un proyecto nuevo con `uv`

```bash
# 1. Crear estructura de proyecto (opcional, si empiezas desde cero)
mkdir mi-proyecto
cd mi-proyecto

# 2. Crear archivo .python-version
echo "3.11.5" > .python-version

# 3. Crear pyproject.toml (ver ejemplos/pyproject.toml)

# 4. Crear entorno virtual
uv venv

# 5. Instalar dependencias
uv sync

# 6. Activar entorno (opcional, uv run no requiere activación)
# Windows PowerShell:
.\.venv\Scripts\Activate.ps1
# Linux/macOS:
source .venv/bin/activate
```

### 4. Usar el proyecto

```bash
# Ejecutar scripts sin activar entorno
uv run python src/main.py

# Instalar nuevos paquetes
uv pip install nombre-paquete

# Actualizar lockfile
uv sync

# Ejecutar tests
uv run pytest
```

## 📁 Estructura del Repositorio

```
Guía_uso_uv/
├── docs/
│   └── uso_de_uv_en_proyectos_python.md  # Documentación completa
├── ejemplos/
│   ├── pyproject.toml                    # Ejemplo de configuración
│   ├── uv.lock.explicado.md              # Explicación del lockfile
│   └── .python-version                   # Ejemplo de versión de Python
├── .gitignore                            # Gitignore recomendado
└── README.md                             # Este archivo
```

## 📖 Ejemplos de Uso

### Crear un nuevo proyecto

```bash
# 1. Crear directorio
mkdir mi-proyecto && cd mi-proyecto

# 2. Inicializar con Python 3.11
echo "3.11.5" > .python-version

# 3. Crear pyproject.toml (copiar de ejemplos/pyproject.toml y adaptar)

# 4. Crear entorno e instalar dependencias
uv venv
uv sync
```

### Instalar dependencias de Machine Learning

```bash
# Instalar dependencias base
uv pip install numpy pandas scikit-learn

# O desde pyproject.toml con grupo opcional
uv sync --extra ml
```

### Usar con Docker

Ver sección 8 de la documentación completa para ejemplos de Dockerfiles optimizados.

### Usar en CI/CD

Ver sección 9 de la documentación completa para ejemplos de GitHub Actions, GitLab CI, etc.

## 🔧 Comandos Útiles

```bash
# Gestión de Python
uv python list                    # Listar versiones disponibles
uv python install 3.11           # Instalar Python 3.11
uv python find                     # Ver versión que se usaría

# Gestión de entornos
uv venv                            # Crear entorno virtual
uv venv --python 3.11              # Crear con versión específica

# Gestión de dependencias
uv pip install paquete             # Instalar paquete
uv pip install -r requirements.txt # Instalar desde requirements.txt
uv sync                            # Sincronizar desde pyproject.toml
uv sync --frozen                   # Instalar exactamente como en uv.lock
uv lock                            # Generar/actualizar lockfile
uv lock --check                    # Verificar que lockfile está actualizado

# Información
uv pip list                        # Listar paquetes instalados
uv pip show paquete                # Mostrar info de un paquete
uv --version                       # Versión de uv
```

## 🎯 Casos de Uso

### Proyecto de Data Science

```bash
# 1. Crear proyecto
mkdir proyecto-ds && cd proyecto-ds
echo "3.11.5" > .python-version

# 2. Crear pyproject.toml con dependencias ML
# (ver ejemplos/pyproject.toml)

# 3. Instalar dependencias
uv sync --extra ml --extra notebooks

# 4. Iniciar Jupyter
uv run jupyter lab
```

### Proyecto con Docker

```dockerfile
FROM python:3.11-slim
COPY --from=ghcr.io/astral-sh/uv:latest /uv /usr/local/bin/uv
WORKDIR /app
COPY pyproject.toml uv.lock ./
RUN uv sync --frozen --no-dev
COPY src/ ./src/
CMD ["uv", "run", "python", "src/main.py"]
```

## 📝 Archivos Importantes

- **`pyproject.toml`**: Configuración del proyecto y dependencias
- **`uv.lock`**: Lockfile para reproducibilidad (debe versionarse en Git)
- **`.python-version`**: Versión de Python del proyecto
- **`.gitignore`**: Archivos a ignorar (incluye `.venv/`, `__pycache__/`, etc.)

## 🔗 Recursos

- **Documentación oficial de `uv`**: https://docs.astral.sh/uv/
- **GitHub de `uv`**: https://github.com/astral-sh/uv
- **Documentación completa en este repo**: [docs/uso_de_uv_en_proyectos_python.md](docs/uso_de_uv_en_proyectos_python.md)

## 🤝 Contribuir

Este es un repositorio de documentación. Si encuentras errores o tienes sugerencias:

1. Abre un issue
2. O crea un pull request con mejoras

## 📄 Licencia

Este repositorio de documentación es de uso libre. La herramienta `uv` está bajo licencia Apache 2.0 o MIT.

---

**Nota**: Esta documentación está escrita en español y está dirigida a desarrolladores Python, equipos de Data Science/ML y profesionales de MLOps.


