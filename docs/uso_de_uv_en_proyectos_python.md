# Guía Completa de Uso de `uv` en Proyectos Python

## Tabla de Contenidos

1. [¿Qué es `uv` y por qué usarlo?](#1-qué-es-uv-y-por-qué-usarlo)
2. [Instalación de `uv`](#2-instalación-de-uv)
3. [Gestión de versiones de Python con `uv`](#3-gestión-de-versiones-de-python-con-uv)
4. [Creación y gestión de entornos virtuales](#4-creación-y-gestión-de-entornos-virtuales)
5. [Gestión de dependencias](#5-gestión-de-dependencias)
6. [Estructura recomendada de proyecto usando `uv`](#6-estructura-recomendada-de-proyecto-usando-uv)
7. [Uso de `uv` en proyectos de Machine Learning](#7-uso-de-uv-en-proyectos-de-machine-learning)
8. [Integración de `uv` con Docker](#8-integración-de-uv-con-docker)
9. [Integración de `uv` con CI/CD](#9-integración-de-uv-con-cicd)
10. [Errores comunes y soluciones](#10-errores-comunes-y-soluciones)
11. [Buenas prácticas y recomendaciones](#11-buenas-prácticas-y-recomendaciones)

---

## 1. ¿Qué es `uv` y por qué usarlo?

### 1.1 ¿Qué es `uv`?

`uv` es una herramienta de gestión de paquetes y entornos virtuales para Python, escrita en Rust, que ofrece un rendimiento excepcionalmente rápido. Fue desarrollada por **Astral** (los mismos creadores de `ruff`) con el objetivo de reemplazar y mejorar las herramientas tradicionales de Python como `pip`, `virtualenv`, `pip-tools` y `pipx`.

### 1.2 ¿Quién lo desarrolla?

`uv` es desarrollado por **Astral**, una empresa reconocida en el ecosistema Python por crear herramientas de alta calidad y rendimiento. El proyecto es de código abierto y está disponible en GitHub bajo la licencia Apache 2.0 o MIT.

### 1.3 ¿Qué problemas soluciona?

`uv` aborda varios problemas críticos en el desarrollo Python moderno:

1. **Velocidad**: Es 10-100 veces más rápido que `pip` en la instalación de paquetes
2. **Gestión unificada**: Combina la funcionalidad de múltiples herramientas en una sola
3. **Reproducibilidad**: Genera lockfiles determinísticos para garantizar entornos idénticos
4. **Gestión de versiones de Python**: Puede instalar y gestionar versiones de Python automáticamente
5. **Compatibilidad**: Es compatible con el ecosistema estándar de Python (pip, setuptools, etc.)

### 1.4 Comparación con otras herramientas

#### `uv` vs `pip`

| Característica | `pip` | `uv` |
|----------------|-------|------|
| Velocidad | Lenta (especialmente con muchas dependencias) | 10-100x más rápido |
| Gestión de entornos | Requiere `virtualenv` | Integrada |
| Lockfiles | Requiere `pip-tools` | Nativo |
| Gestión de Python | Requiere `pyenv` | Integrada |

**Ejemplo de diferencia de velocidad:**
```bash
# Con pip (puede tardar varios minutos)
pip install numpy pandas scikit-learn torch

# Con uv (segundos)
uv pip install numpy pandas scikit-learn torch
```

#### `uv` vs `virtualenv`

`virtualenv` solo crea entornos virtuales. `uv` crea entornos virtuales **y** gestiona paquetes, versiones de Python, y genera lockfiles.

#### `uv` vs `pyenv`

`pyenv` gestiona versiones de Python pero no paquetes. `uv` hace ambas cosas de forma integrada.

#### `uv` vs `poetry`

| Característica | `poetry` | `uv` |
|----------------|----------|------|
| Velocidad | Rápida | Mucho más rápida |
| Complejidad | Configuración más compleja | Más simple |
| Gestión de Python | No | Sí |
| Ecosistema | Propio | Compatible con pip |

#### `uv` vs `conda`

| Característica | `conda` | `uv` |
|----------------|---------|------|
| Enfoque | Gestión de paquetes binarios | Gestión de paquetes Python |
| Velocidad | Media | Muy rápida |
| Tamaño | Muy grande | Ligero |
| Uso principal | Data Science con dependencias binarias complejas | Desarrollo Python general |

### 1.5 Casos de uso ideales

`uv` es ideal para:

- ✅ Proyectos de desarrollo Python moderno
- ✅ Proyectos de Machine Learning y Data Science
- ✅ Equipos que necesitan reproducibilidad
- ✅ CI/CD pipelines donde la velocidad importa
- ✅ Proyectos que requieren múltiples versiones de Python
- ✅ Desarrollo con notebooks (Jupyter, VS Code)
- ✅ Proyectos containerizados (Docker)

---

## 2. Instalación de `uv`

### 2.1 Instalación en Windows

#### Opción 1: Usando PowerShell (Recomendado)

```powershell
# Instalación directa
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

#### Opción 2: Usando pip

```powershell
pip install uv
```

#### Opción 3: Usando winget

```powershell
winget install --id=astral-sh.uv -e
```

#### Opción 4: Usando Scoop

```powershell
scoop install uv
```

#### Solución de problemas: "uv no se reconoce como cmdlet"

Si después de instalar `uv` aparece el error:

```
uv : El término 'uv' no se reconoce como nombre de un cmdlet
```

Esto significa que `uv` no está en el PATH del sistema. Sigue estos pasos:

**1. Verificar si `uv` está instalado:**

```powershell
where uv
```

- Si **no aparece nada**: `uv` no está instalado. Instálalo usando la Opción 1 (PowerShell como Administrador).
- Si **aparece una ruta**: El problema es que el PATH no se ha recargado en la sesión actual.

**2. Instalación correcta (recomendada):**

Ejecuta PowerShell **como Administrador** y luego:

```powershell
irm https://astral.sh/uv/install.ps1 | iex
```

Este comando:
- Descarga `uv`
- Lo instala en tu usuario
- Agrega automáticamente `uv` al PATH

⚠️ **IMPORTANTE**: Después de la instalación, **DEBES cerrar PowerShell y abrirlo de nuevo** para que el PATH se recargue.

**3. Verificar instalación:**

Abre una **nueva ventana de PowerShell** y ejecuta:

```powershell
uv --version
```

Si ves algo como `uv 0.x.x`, la instalación fue exitosa.

**4. Si sigue sin funcionar (solución manual de PATH):**

Si el comando anterior sigue fallando, agrega `uv` al PATH manualmente:

1. Presiona `Win + R`
2. Escribe `sysdm.cpl` y presiona Enter
3. Ve a la pestaña **Opciones avanzadas**
4. Click en **Variables de entorno**
5. En **Variables de usuario**, selecciona `Path` y haz click en **Editar**
6. Agrega la ruta típica de instalación: `C:\Users\TU_USUARIO\.cargo\bin`
   (Reemplaza `TU_USUARIO` con tu nombre de usuario)
7. Acepta todos los diálogos
8. **Cierra y abre PowerShell de nuevo**
9. Prueba: `uv --version`

**5. Verificación completa:**

Ejecuta estos comandos para verificar que todo funciona:

```powershell
uv --version
uv python list
```

Si ambos funcionan, tu entorno está listo.

**Errores comunes a evitar:**

| Error | Causa | Solución |
|-------|-------|----------|
| Ejecutar `uv` sin reiniciar PowerShell | PATH no recargado | Cerrar y abrir PowerShell |
| Instalar sin permisos de administrador | No se copia el binario | Ejecutar PowerShell como Administrador |
| Usar CMD antiguo | PowerShell es necesario | Usar PowerShell moderno |
| Tener Python Store activo | Puede interferir | Desactivar o usar `uv` directamente |

**Recomendación profesional:**

Para proyectos de Machine Learning y MLOps:
- Usa **solo `uv`** como herramienta principal
- Evita mezclar con `conda`, `pyenv` o `virtualenv`
- Define siempre `.python-version` y `uv.lock` en tu proyecto
- Esto evita el 90% de errores comunes en entornos ML

### 2.2 Instalación en Linux

#### Opción 1: Script de instalación (Recomendado)

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Este script instalará `uv` en `~/.cargo/bin/uv`. Asegúrate de agregar esta ruta a tu `PATH`:

```bash
echo 'export PATH="$HOME/.cargo/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

#### Opción 2: Usando pip

```bash
pip install uv
```

#### Opción 3: Usando el gestor de paquetes del sistema

**Ubuntu/Debian:**
```bash
# Descargar el .deb
curl -LsSf https://astral.sh/uv/install.sh | sh
# O usar el repositorio oficial si está disponible
```

**Arch Linux:**
```bash
yay -S uv
# o
paru -S uv
```

### 2.3 Instalación en macOS

#### Opción 1: Script de instalación (Recomendado)

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Agregar al PATH:
```bash
echo 'export PATH="$HOME/.cargo/bin:$PATH"' >> ~/.zshrc
source ~/.zshrc
```

#### Opción 2: Usando Homebrew

```bash
brew install uv
```

#### Opción 3: Usando pip

```bash
pip install uv
```

### 2.4 Verificación de la instalación

Después de instalar, verifica que `uv` funciona correctamente:

```bash
uv --version
```

Deberías ver algo como:
```
uv 0.x.x
```

También puedes verificar las capacidades:

```bash
uv --help
```

### 2.5 Buenas prácticas post-instalación

1. **Verificar que está en el PATH**: Asegúrate de que `uv` sea accesible desde cualquier terminal
2. **Actualizar regularmente**: `uv` se actualiza frecuentemente con mejoras de rendimiento
   ```bash
   uv self update
   ```
3. **Configurar alias (opcional)**: Puedes crear alias para comandos comunes
   ```bash
   # En ~/.bashrc o ~/.zshrc
   alias uvpy='uv python'
   alias uvenv='uv venv'
   ```

---

## 3. Gestión de versiones de Python con `uv`

### 3.1 Cómo listar versiones disponibles

Para ver qué versiones de Python están disponibles para instalar:

```bash
uv python list
```

Esto mostrará todas las versiones de Python que `uv` puede instalar, incluyendo versiones estables, pre-releases y versiones específicas de plataforma.

Para ver solo versiones instaladas localmente:

```bash
uv python list --installed
```

### 3.2 Cómo instalar versiones específicas

Instalar una versión específica de Python:

```bash
# Instalar Python 3.11
uv python install 3.11

# Instalar Python 3.12.0 específicamente
uv python install 3.12.0

# Instalar la última versión de una serie
uv python install 3.12
```

Las versiones se instalan en el directorio de gestión de `uv` (típicamente `~/.uv/python/` en Linux/macOS o `%USERPROFILE%\.uv\python\` en Windows).

### 3.3 Cómo fijar versión de Python por proyecto

#### Método 1: Usando `.python-version`

Crea un archivo `.python-version` en la raíz de tu proyecto:

```bash
# En la raíz del proyecto
echo "3.11.5" > .python-version
```

Cuando uses `uv` en este directorio, automáticamente usará la versión especificada.

#### Método 2: Especificando en el comando

```bash
# Crear entorno virtual con versión específica
uv venv --python 3.11

# O usar directamente
uv run --python 3.11 script.py
```

#### Método 3: En `pyproject.toml`

```toml
[project]
requires-python = ">=3.11,<3.12"
```

### 3.4 Cómo validar versión activa

Verificar qué versión de Python está siendo usada:

```bash
# Ver versión de Python en el entorno actual
python --version

# Ver versión que uv usaría para este proyecto
uv python find

# Ver todas las versiones instaladas
uv python list --installed
```

### 3.5 Uso de `.python-version`

El archivo `.python-version` es un estándar compatible con `pyenv` y otras herramientas:

**Ejemplo de `.python-version`:**
```
3.11.5
```

**Ventajas:**
- Compatible con múltiples herramientas
- Simple y claro
- Se puede versionar en Git
- Permite especificar versiones exactas o rangos

**Mejores prácticas:**
- Usa versiones específicas (ej: `3.11.5`) para máxima reproducibilidad
- O usa versiones menores (ej: `3.11`) para flexibilidad
- Siempre versiona este archivo en Git

### 3.6 Casos prácticos reales

#### Caso 1: Proyecto nuevo con Python 3.12

```bash
# 1. Instalar Python 3.12 si no está disponible
uv python install 3.12

# 2. Crear archivo .python-version
echo "3.12" > .python-version

# 3. Crear entorno virtual
uv venv

# 4. Verificar
python --version  # Debería mostrar Python 3.12.x
```

#### Caso 2: Migrar proyecto existente a versión específica

```bash
# 1. Verificar versión actual
python --version

# 2. Instalar nueva versión
uv python install 3.11.5

# 3. Actualizar .python-version
echo "3.11.5" > .python-version

# 4. Recrear entorno virtual
rm -rf .venv  # o eliminar el entorno anterior
uv venv

# 5. Reinstalar dependencias
uv pip install -r requirements.txt
```

#### Caso 3: Múltiples proyectos con diferentes versiones

```bash
# Proyecto A (Python 3.10)
cd proyecto_a
echo "3.10" > .python-version
uv venv

# Proyecto B (Python 3.12)
cd ../proyecto_b
echo "3.12" > .python-version
uv venv
```

Cada proyecto usará automáticamente su versión especificada.

---

## 4. Creación y gestión de entornos virtuales

### 4.1 Crear entorno virtual con `uv`

#### Creación básica

```bash
# Crear entorno virtual en .venv (convención estándar)
uv venv

# Crear en ubicación específica
uv venv mi_entorno

# Crear con versión específica de Python
uv venv --python 3.11

# Crear con versión desde .python-version
uv venv  # Automáticamente usa .python-version si existe
```

#### Opciones avanzadas

```bash
# Crear entorno sin pip (útil para casos especiales)
uv venv --no-pip

# Crear entorno con sistema de paquetes (--system-site-packages)
uv venv --system-site-packages

# Especificar ubicación exacta
uv venv /ruta/completa/al/entorno
```

### 4.2 Activación y desactivación

#### Windows (PowerShell)

```powershell
# Activar
.\.venv\Scripts\Activate.ps1

# Desactivar
deactivate
```

#### Windows (CMD)

```cmd
# Activar
.venv\Scripts\activate.bat

# Desactivar
deactivate
```

#### Linux/macOS

```bash
# Activar
source .venv/bin/activate

# Desactivar
deactivate
```

#### Nota importante

Con `uv`, a menudo **no necesitas activar el entorno manualmente**. Puedes usar:

```bash
# Ejecutar comando en el entorno sin activar
uv run python script.py

# Instalar paquete sin activar
uv pip install numpy

# Ejecutar cualquier comando
uv run pytest
```

### 4.3 Ubicación del entorno

Por defecto, `uv venv` crea el entorno en `.venv/` en el directorio actual.

**Estructura típica:**
```
proyecto/
├── .venv/              # Entorno virtual
│   ├── bin/           # (Linux/macOS) o Scripts/ (Windows)
│   ├── lib/
│   └── pyvenv.cfg
├── src/
└── pyproject.toml
```

**Cambiar ubicación:**

```bash
# Especificar ruta absoluta
uv venv /ruta/absoluta/entorno

# O variable de entorno
export UV_PROJECT_ENVIRONMENT=/ruta/entorno
uv venv
```

### 4.4 Reglas para repositorios Git

#### ¿Qué versionar?

✅ **SÍ versionar:**
- `.python-version` - Versión de Python del proyecto
- `pyproject.toml` - Configuración del proyecto y dependencias
- `uv.lock` - Lockfile para reproducibilidad exacta
- `requirements.txt` - Si lo usas (opcional con `uv`)

❌ **NO versionar:**
- `.venv/` - El entorno virtual completo
- `__pycache__/` - Cache de Python
- `*.pyc` - Bytecode compilado
- `.pytest_cache/` - Cache de pytest

**Ejemplo de `.gitignore` para proyectos con `uv`:**

```gitignore
# Entornos virtuales
.venv/
venv/
env/
ENV/

# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python

# Distribución / empaquetado
dist/
build/
*.egg-info/

# IDEs
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# Proyecto específico
*.log
.pytest_cache/
.coverage
htmlcov/
```

### 4.5 Integración con VS Code / Cursor

#### VS Code / Cursor

1. **Selección automática del intérprete:**
   - VS Code/Cursor detecta automáticamente `.venv/` si existe
   - Presiona `Ctrl+Shift+P` (o `Cmd+Shift+P` en macOS)
   - Busca "Python: Select Interpreter"
   - Selecciona `.venv/bin/python` (Linux/macOS) o `.venv\Scripts\python.exe` (Windows)

2. **Configuración en `.vscode/settings.json`:**
   ```json
   {
     "python.defaultInterpreterPath": "${workspaceFolder}/.venv/bin/python",
     "python.terminal.activateEnvironment": true
   }
   ```

3. **Usar `uv` en terminal integrado:**
   - El terminal integrado puede usar `uv run` directamente
   - VS Code puede configurarse para usar `uv` como gestor de paquetes

#### PyCharm

1. Ir a Settings → Project → Python Interpreter
2. Seleccionar "Add Interpreter" → "Existing environment"
3. Seleccionar `.venv/bin/python` (o `.venv\Scripts\python.exe` en Windows)

---

## 5. Gestión de dependencias

### 5.1 Instalación de paquetes

#### Instalación básica

```bash
# Instalar un paquete
uv pip install numpy

# Instalar múltiples paquetes
uv pip install numpy pandas scikit-learn

# Instalar desde el entorno activo (si está activado)
pip install numpy  # Funciona normalmente
```

#### Instalación con versiones específicas

```bash
# Versión exacta
uv pip install numpy==1.24.3

# Versión mínima
uv pip install numpy>=1.24.0

# Rango de versiones
uv pip install "numpy>=1.24.0,<2.0.0"

# Versión desde Git
uv pip install git+https://github.com/usuario/repo.git

# Versión desde branch específico
uv pip install git+https://github.com/usuario/repo.git@branch-name
```

### 5.2 Instalación desde requirements.txt

```bash
# Instalar desde requirements.txt
uv pip install -r requirements.txt

# Generar requirements.txt desde entorno actual
uv pip freeze > requirements.txt

# Instalar con actualización de dependencias
uv pip install -r requirements.txt --upgrade
```

**Ejemplo de `requirements.txt`:**
```
numpy==1.24.3
pandas>=2.0.0
scikit-learn==1.3.0
```

### 5.3 Resolución rápida de dependencias

`uv` resuelve dependencias mucho más rápido que `pip` tradicional:

```bash
# Comparación de velocidad
time pip install numpy pandas scikit-learn torch
# Tiempo: ~2-5 minutos

time uv pip install numpy pandas scikit-learn torch
# Tiempo: ~10-30 segundos
```

**Ventajas de la resolución de `uv`:**
- Resolución paralela de dependencias
- Cache inteligente
- Resolución determinística
- Mejor manejo de conflictos

### 5.4 Generación de lockfiles

#### Usando `pyproject.toml` (Recomendado)

**1. Crear `pyproject.toml`:**
```toml
[project]
name = "mi-proyecto"
version = "0.1.0"
requires-python = ">=3.11"
dependencies = [
    "numpy>=1.24.0",
    "pandas>=2.0.0",
    "scikit-learn>=1.3.0",
]
```

**2. Sincronizar y generar lockfile:**
```bash
# Instalar dependencias y generar uv.lock
uv sync

# O solo generar lockfile sin instalar
uv lock
```

**3. Instalar desde lockfile:**
```bash
# Instalar exactamente como está en uv.lock
uv sync
```

#### Lockfile manual

Si prefieres trabajar con `requirements.txt` pero quieres un lockfile:

```bash
# Instalar y generar lockfile
uv pip install -r requirements.txt
uv pip compile requirements.txt -o requirements.lock
```

### 5.5 Reproducibilidad exacta

#### Flujo completo para reproducibilidad

**1. En el proyecto original:**
```bash
# Instalar dependencias
uv sync

# Esto genera/actualiza uv.lock con versiones exactas
```

**2. En otra máquina o CI/CD:**
```bash
# Clonar repositorio
git clone <repo>
cd <repo>

# Instalar exactamente las mismas versiones
uv sync

# Esto lee uv.lock y reproduce el entorno exacto
```

#### Verificar reproducibilidad

```bash
# Ver qué se instalará sin instalar
uv sync --dry-run

# Ver diferencias entre lockfile y entorno actual
uv pip list --outdated
```

#### Ejemplo de `uv.lock` (estructura)

El archivo `uv.lock` es un archivo TOML que contiene:
- Versiones exactas de todos los paquetes
- Hashes de verificación
- Dependencias transitivas resueltas
- Metadatos de resolución

**Importante:** `uv.lock` debe versionarse en Git para garantizar reproducibilidad.

---

## 6. Estructura recomendada de proyecto usando `uv`

### 6.1 Estructura estándar

```
proyecto/
├── src/                    # Código fuente del proyecto
│   └── mi_proyecto/
│       ├── __init__.py
│       └── modulo.py
├── notebooks/              # Jupyter notebooks (opcional)
│   └── analisis.ipynb
├── tests/                  # Tests
│   ├── __init__.py
│   └── test_modulo.py
├── docs/                   # Documentación
│   └── uso_de_uv_en_proyectos_python.md
├── pyproject.toml          # Configuración del proyecto y dependencias
├── uv.lock                 # Lockfile para reproducibilidad
├── .python-version         # Versión de Python del proyecto
├── .gitignore              # Archivos a ignorar en Git
└── README.md               # Documentación del proyecto
```

### 6.2 Explicación de cada archivo

#### `src/`
Contiene el código fuente principal del proyecto. Separar el código en `src/` es una buena práctica que evita problemas de imports y facilita la distribución.

#### `notebooks/`
Directorio opcional para Jupyter notebooks. Útil para proyectos de Data Science y ML.

#### `tests/`
Contiene los tests del proyecto. Puede usar `pytest`, `unittest`, u otros frameworks.

#### `docs/`
Documentación del proyecto. Puede incluir guías, API docs, etc.

#### `pyproject.toml`
Archivo de configuración estándar de Python que define:
- Metadatos del proyecto (nombre, versión, autores)
- Dependencias del proyecto
- Configuración de herramientas (pytest, black, etc.)
- Scripts de construcción

**Ejemplo mínimo:**
```toml
[project]
name = "mi-proyecto"
version = "0.1.0"
requires-python = ">=3.11"
dependencies = [
    "numpy>=1.24.0",
    "pandas>=2.0.0",
]
```

#### `uv.lock`
Lockfile generado automáticamente por `uv` que contiene:
- Versiones exactas de todas las dependencias
- Hashes de verificación
- Dependencias transitivas resueltas

**Importante:** Este archivo debe versionarse en Git para reproducibilidad.

#### `.python-version`
Especifica la versión de Python que debe usarse en este proyecto. Compatible con `pyenv` y otras herramientas.

**Ejemplo:**
```
3.11.5
```

#### `.gitignore`
Especifica qué archivos no deben versionarse en Git. Debe incluir `.venv/`, `__pycache__/`, etc.

#### `README.md`
Documentación principal del proyecto. Debe incluir:
- Descripción del proyecto
- Instrucciones de instalación
- Cómo ejecutar el proyecto
- Información de contribución

### 6.3 Inicializar un proyecto nuevo

```bash
# 1. Crear directorio del proyecto
mkdir mi-proyecto
cd mi-proyecto

# 2. Inicializar Git (opcional pero recomendado)
git init

# 3. Crear estructura de directorios
mkdir -p src/mi_proyecto notebooks tests docs

# 4. Crear .python-version
echo "3.11.5" > .python-version

# 5. Crear pyproject.toml (ver ejemplo en sección de archivos adicionales)

# 6. Crear .gitignore (ver ejemplo en sección de archivos adicionales)

# 7. Crear entorno virtual
uv venv

# 8. Instalar dependencias
uv sync

# 9. Crear README.md básico
```

---

## 7. Uso de `uv` en proyectos de Machine Learning

### 7.1 Instalación de librerías ML

#### Librerías básicas de Data Science

```bash
# Instalación individual
uv pip install numpy
uv pip install pandas
uv pip install matplotlib
uv pip install seaborn

# Instalación en lote
uv pip install numpy pandas matplotlib seaborn scikit-learn
```

#### Frameworks de Machine Learning

```bash
# Scikit-learn
uv pip install scikit-learn

# TensorFlow
uv pip install tensorflow
# O para GPU
uv pip install tensorflow[and-cuda]

# PyTorch
uv pip install torch torchvision torchaudio
# O con índice específico para CUDA
uv pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu118

# XGBoost
uv pip install xgboost

# LightGBM
uv pip install lightgbm
```

#### Librerías especializadas

```bash
# Procesamiento de imágenes
uv pip install pillow opencv-python

# Procesamiento de texto
uv pip install nltk spacy

# Visualización avanzada
uv pip install plotly bokeh

# MLflow para experimentación
uv pip install mlflow
```

### 7.2 Manejo de dependencias pesadas

Las librerías ML suelen ser grandes y tener dependencias complejas. `uv` maneja esto eficientemente:

#### Instalación optimizada

```bash
# uv cachea automáticamente los wheels descargados
# Las instalaciones subsecuentes son mucho más rápidas

# Primera instalación (descarga)
uv pip install torch  # ~5-10 minutos

# Reinstalación (desde cache)
uv pip install torch  # ~10-30 segundos
```

#### Gestión de espacio

```bash
# Ver qué está en cache
uv cache dir

# Limpiar cache si es necesario
uv cache clean
```

#### Dependencias opcionales

En `pyproject.toml`:

```toml
[project]
dependencies = [
    "numpy",
    "pandas",
]

[project.optional-dependencies]
gpu = [
    "tensorflow[and-cuda]",
    "torch[cuda]",
]
ml = [
    "scikit-learn",
    "xgboost",
]
all = [
    "tensorflow[and-cuda]",
    "torch[cuda]",
    "scikit-learn",
    "xgboost",
]
```

Instalar grupos opcionales:

```bash
# Instalar solo dependencias base
uv sync

# Instalar con grupo opcional
uv sync --extra gpu
uv sync --extra ml
uv sync --extra all
```

### 7.3 Compatibilidad con notebooks

#### Jupyter Notebook

```bash
# Instalar Jupyter
uv pip install jupyter notebook

# Iniciar servidor
uv run jupyter notebook
```

#### JupyterLab

```bash
# Instalar JupyterLab
uv pip install jupyterlab

# Iniciar
uv run jupyter lab
```

#### VS Code / Cursor con notebooks

1. Instalar extensión Jupyter
2. Seleccionar intérprete de `.venv`
3. Los notebooks usarán automáticamente el entorno virtual

#### IPython kernel

```bash
# Instalar ipykernel
uv pip install ipykernel

# Registrar kernel para Jupyter
uv run python -m ipykernel install --user --name=mi-proyecto --display-name="Python (mi-proyecto)"
```

### 7.4 Buenas prácticas para reproducibilidad científica

#### 1. Fijar versiones exactas

En `pyproject.toml`:

```toml
[project]
dependencies = [
    "numpy==1.24.3",
    "pandas==2.0.3",
    "scikit-learn==1.3.0",
    "torch==2.0.1",
]
```

#### 2. Usar lockfiles

```bash
# Generar lockfile
uv sync

# Versionar uv.lock en Git
git add uv.lock
```

#### 3. Documentar entorno de ejecución

En `README.md` o `docs/`:

```markdown
## Entorno de ejecución

- Python: 3.11.5
- CUDA: 11.8 (si aplica)
- OS: Ubuntu 22.04

Ver `uv.lock` para dependencias exactas.
```

#### 4. Reproducir experimentos

```bash
# En máquina nueva
git clone <repo>
cd <repo>
uv sync  # Instala exactamente las mismas versiones
```

#### 5. Versionar datos y modelos

Usar DVC (Data Version Control) o MLflow para versionar:
- Datasets
- Modelos entrenados
- Resultados de experimentos

---

## 8. Integración de `uv` con Docker

### 8.1 Uso de `uv` dentro de Docker

`uv` es ideal para usar en Docker porque:
- Instalación rápida de dependencias
- Cache eficiente
- Imágenes más pequeñas
- Builds más rápidos

### 8.2 Instalación de dependencias en imágenes

#### Dockerfile básico

```dockerfile
FROM python:3.11-slim

# Instalar uv
COPY --from=ghcr.io/astral-sh/uv:latest /uv /usr/local/bin/uv

# Configurar directorio de trabajo
WORKDIR /app

# Copiar archivos de dependencias
COPY pyproject.toml uv.lock ./

# Instalar dependencias
RUN uv sync --frozen

# Copiar código fuente
COPY src/ ./src/

# Comando por defecto
CMD ["uv", "run", "python", "src/main.py"]
```

### 8.3 Cache eficiente

#### Optimización de layers

```dockerfile
FROM python:3.11-slim

# Instalar uv (layer cacheable)
COPY --from=ghcr.io/astral-sh/uv:latest /uv /usr/local/bin/uv

WORKDIR /app

# Copiar solo archivos de dependencias primero (mejor cache)
COPY pyproject.toml uv.lock ./

# Instalar dependencias (cache si pyproject.toml/uv.lock no cambian)
RUN uv sync --frozen

# Copiar código (cambia frecuentemente, va al final)
COPY . .

CMD ["uv", "run", "python", "src/main.py"]
```

**Ventaja:** Si solo cambia el código fuente, Docker reutiliza el layer de dependencias.

### 8.4 Ejemplo de Dockerfile usando `uv`

#### Dockerfile completo para proyecto ML

```dockerfile
# Usar imagen base de Python
FROM python:3.11-slim

# Instalar dependencias del sistema necesarias
RUN apt-get update && apt-get install -y \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

# Instalar uv desde imagen oficial
COPY --from=ghcr.io/astral-sh/uv:latest /uv /usr/local/bin/uv

# Configurar variables de entorno
ENV UV_SYSTEM_PYTHON=1
ENV PYTHONUNBUFFERED=1

# Directorio de trabajo
WORKDIR /app

# Copiar archivos de configuración de dependencias
COPY pyproject.toml uv.lock ./

# Instalar dependencias del proyecto
# --frozen asegura que use exactamente uv.lock
RUN uv sync --frozen --no-dev

# Copiar código fuente
COPY src/ ./src/
COPY scripts/ ./scripts/

# Usuario no-root para seguridad
RUN useradd -m -u 1000 appuser && chown -R appuser:appuser /app
USER appuser

# Exponer puerto si es necesario
# EXPOSE 8000

# Comando por defecto
CMD ["uv", "run", "python", "src/main.py"]
```

#### Dockerfile multi-stage para producción

```dockerfile
# Stage 1: Build
FROM python:3.11-slim as builder

COPY --from=ghcr.io/astral-sh/uv:latest /uv /usr/local/bin/uv

WORKDIR /build

# Copiar archivos de dependencias
COPY pyproject.toml uv.lock ./

# Instalar dependencias en ubicación temporal
RUN uv sync --frozen --no-dev -o /app

# Stage 2: Runtime
FROM python:3.11-slim

COPY --from=ghcr.io/astral-sh/uv:latest /uv /usr/local/bin/uv

# Copiar entorno desde builder
COPY --from=builder /app /app

WORKDIR /app

# Copiar código fuente
COPY src/ ./src/

ENV PATH="/app/.venv/bin:$PATH"
ENV PYTHONUNBUFFERED=1

CMD ["python", "src/main.py"]
```

### 8.5 Buenas prácticas para producción

#### 1. Usar `--frozen` en producción

```dockerfile
RUN uv sync --frozen
```

Esto asegura que se usen exactamente las versiones de `uv.lock`, sin actualizaciones.

#### 2. Separar dependencias de desarrollo

En `pyproject.toml`:

```toml
[project.optional-dependencies]
dev = [
    "pytest",
    "black",
    "mypy",
]
```

En Dockerfile:

```dockerfile
# Solo dependencias de producción
RUN uv sync --frozen --no-dev
```

#### 3. Usar imágenes base pequeñas

```dockerfile
FROM python:3.11-slim  # En lugar de python:3.11
```

#### 4. Limpiar cache después de instalar

```dockerfile
RUN uv sync --frozen && \
    uv cache clean && \
    rm -rf /tmp/*
```

#### 5. Usar .dockerignore

Crear `.dockerignore`:

```
.venv/
__pycache__/
*.pyc
.git/
.gitignore
README.md
docs/
.pytest_cache/
```

#### 6. Variables de entorno para configuración

```dockerfile
ENV UV_SYSTEM_PYTHON=1
ENV PYTHONUNBUFFERED=1
ENV PYTHONDONTWRITEBYTECODE=1
```

---

## 9. Integración de `uv` con CI/CD

### 9.1 Uso de `uv` en pipelines

`uv` es ideal para CI/CD porque:
- Instalación muy rápida
- Reproducibilidad garantizada
- Menor tiempo de ejecución de pipelines
- Menor costo de recursos

### 9.2 Instalación rápida en runners

#### GitHub Actions

```yaml
name: Tests

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Install uv
        uses: astral-sh/setup-uv@v3
        with:
          version: "latest"
      
      - name: Set up Python
        run: uv python install 3.11
      
      - name: Install dependencies
        run: uv sync --frozen
      
      - name: Run tests
        run: uv run pytest
```

#### GitLab CI

```yaml
test:
  image: python:3.11-slim
  
  before_script:
    - pip install uv
    - uv python install 3.11
    - uv sync --frozen
  
  script:
    - uv run pytest
    - uv run black --check .
    - uv run mypy src/
```

#### Jenkins

```groovy
pipeline {
    agent any
    
    stages {
        stage('Setup') {
            steps {
                sh 'pip install uv'
                sh 'uv python install 3.11'
            }
        }
        
        stage('Install') {
            steps {
                sh 'uv sync --frozen'
            }
        }
        
        stage('Test') {
            steps {
                sh 'uv run pytest'
            }
        }
    }
}
```

### 9.3 Reproducibilidad en CI

#### Usar `--frozen` siempre

```bash
uv sync --frozen
```

Esto garantiza que CI use exactamente las mismas versiones que desarrollo.

#### Cache de dependencias

**GitHub Actions con cache:**

```yaml
- name: Cache uv dependencies
  uses: actions/cache@v3
  with:
    path: ~/.cache/uv
    key: ${{ runner.os }}-uv-${{ hashFiles('uv.lock') }}
    restore-keys: |
      ${{ runner.os }}-uv-

- name: Install dependencies
  run: uv sync --frozen
```

### 9.4 Ejemplo conceptual (GitHub Actions)

#### Pipeline completo para proyecto Python

```yaml
name: CI

on:
  push:
    branches: [ main, develop ]
  pull_request:
    branches: [ main ]

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Install uv
        uses: astral-sh/setup-uv@v3
      
      - name: Set up Python
        run: uv python install 3.11
      
      - name: Install dependencies
        run: uv sync --frozen
      
      - name: Run linters
        run: |
          uv run black --check .
          uv run isort --check .
          uv run flake8 src/

  test:
    runs-on: ${{ matrix.os }}
    strategy:
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        python-version: ["3.10", "3.11", "3.12"]
    
    steps:
      - uses: actions/checkout@v4
      
      - name: Install uv
        uses: astral-sh/setup-uv@v3
      
      - name: Set up Python ${{ matrix.python-version }}
        run: uv python install ${{ matrix.python-version }}
      
      - name: Install dependencies
        run: uv sync --frozen
      
      - name: Run tests
        run: uv run pytest --cov=src --cov-report=xml
      
      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          file: ./coverage.xml

  build:
    needs: [lint, test]
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      
      - name: Install uv
        uses: astral-sh/setup-uv@v3
      
      - name: Build package
        run: |
          uv python install 3.11
          uv sync --frozen
          uv build
```

---

## 10. Errores comunes y soluciones

### 10.1 Conflictos de versiones

#### Problema: Dependencias incompatibles

```
ERROR: Cannot install package-a==1.0.0 and package-b==2.0.0 because these package versions have conflicting dependencies.
```

**Solución:**

1. **Verificar conflictos:**
   ```bash
   uv pip check
   ```

2. **Actualizar dependencias:**
   ```bash
   uv pip install --upgrade package-a package-b
   ```

3. **Usar rangos de versiones en `pyproject.toml`:**
   ```toml
   dependencies = [
       "package-a>=1.0.0,<2.0.0",
       "package-b>=2.0.0,<3.0.0",
   ]
   ```

4. **Regenerar lockfile:**
   ```bash
   uv lock --upgrade
   ```

### 10.2 Entornos corruptos

#### Problema: Entorno virtual dañado

```
ModuleNotFoundError: No module named 'X'
```

**Solución:**

1. **Recrear entorno:**
   ```bash
   rm -rf .venv
   uv venv
   uv sync
   ```

2. **Verificar instalación:**
   ```bash
   uv pip list
   ```

3. **Reinstalar dependencias:**
   ```bash
   uv sync --reinstall
   ```

### 10.3 Problemas con wheels

#### Problema: No se encuentra wheel para plataforma

```
ERROR: Could not find a version that satisfies the requirement package-x
```

**Solución:**

1. **Instalar desde fuente:**
   ```bash
   uv pip install package-x --no-binary package-x
   ```

2. **Verificar plataforma:**
   ```bash
   python -c "import platform; print(platform.platform())"
   ```

3. **Usar índice alternativo:**
   ```bash
   uv pip install package-x --index-url https://pypi.org/simple
   ```

### 10.4 Problemas en Windows

#### Problema: "uv no se reconoce como cmdlet"

**Error:**
```
uv : El término 'uv' no se reconoce como nombre de un cmdlet, función, archivo de script o programa ejecutable.
```

**Causa:**

Este error significa que `uv` no está instalado o su ruta no está en el PATH del sistema. Windows no puede encontrar el ejecutable de `uv`.

**Solución paso a paso:**

**1. Verificar si `uv` está instalado:**

```powershell
where uv
```

- Si **no aparece nada**: `uv` no está instalado. Procede con la instalación.
- Si **aparece una ruta**: El problema es que el PATH no se ha recargado en la sesión actual.

**2. Instalación correcta (recomendada):**

Ejecuta PowerShell **como Administrador** y luego:

```powershell
irm https://astral.sh/uv/install.ps1 | iex
```

Este comando:
- Descarga `uv` automáticamente
- Lo instala en tu directorio de usuario
- Agrega automáticamente `uv` al PATH del sistema

⚠️ **IMPORTANTE**: Después de ejecutar el comando de instalación, **DEBES cerrar PowerShell completamente y abrir una nueva ventana** para que el PATH se recargue.

**3. Verificar instalación:**

Abre una **nueva ventana de PowerShell** (no la misma donde instalaste) y ejecuta:

```powershell
uv --version
```

Si ves algo como:
```
uv 0.x.x
```

✅ `uv` está correctamente instalado.

**4. Si sigue sin funcionar (solución manual de PATH):**

Si después de reiniciar PowerShell el comando sigue fallando, agrega `uv` al PATH manualmente:

**4.1. Ubicación típica de `uv` en Windows:**

Normalmente se instala en:
```
C:\Users\TU_USUARIO\.cargo\bin
```

(Reemplaza `TU_USUARIO` con tu nombre de usuario de Windows)

**4.2. Agregar al PATH manualmente:**

1. Presiona `Win + R`
2. Escribe `sysdm.cpl` y presiona Enter
3. Ve a la pestaña **Opciones avanzadas**
4. Click en **Variables de entorno**
5. En la sección **Variables de usuario**, busca `Path` y haz click en **Editar**
6. Click en **Nuevo** y agrega: `C:\Users\TU_USUARIO\.cargo\bin`
   (Reemplaza `TU_USUARIO` con tu nombre de usuario)
7. Click en **Aceptar** en todos los diálogos
8. **Cierra completamente PowerShell y ábrelo de nuevo**
9. Prueba: `uv --version`

**5. Verificación completa:**

Ejecuta estos comandos para verificar que todo funciona correctamente:

```powershell
uv --version
uv python list
```

Si ambos comandos funcionan sin errores, tu entorno está completamente configurado.

**6. Errores comunes a evitar:**

| Error | Causa | Solución |
|-------|-------|----------|
| Ejecutar `uv` sin reiniciar PowerShell | PATH no recargado en la sesión actual | Cerrar y abrir PowerShell de nuevo |
| Instalar sin permisos de administrador | El binario no se copia correctamente | Ejecutar PowerShell como Administrador |
| Usar CMD antiguo en lugar de PowerShell | CMD puede no reconocer el PATH actualizado | Usar PowerShell moderno |
| Tener Python Store activo | Puede interferir con la detección de `uv` | Desactivar Python Store o usar `uv` directamente |

**7. Recomendación profesional:**

Para proyectos de Machine Learning y MLOps:
- Usa **solo `uv`** como herramienta principal
- Evita mezclar con `conda`, `pyenv` o `virtualenv`
- Define siempre `.python-version` y `uv.lock` en tu proyecto
- Esto evita el 90% de errores comunes en entornos ML

**8. Próximo paso sugerido:**

Una vez que `uv` funcione correctamente, puedes configurar tu proyecto:

```powershell
# Instalar una versión específica de Python
uv python install 3.11

# Fijar la versión para el proyecto
echo "3.11" > .python-version

# Crear entorno virtual
uv venv

# Verificar que todo funciona
uv run python --version
```

#### Problema: Error de permisos

```
PermissionError: [WinError 5] Access is denied
```

**Solución:**

1. **Ejecutar PowerShell como administrador** (si es necesario)

2. **Verificar permisos del directorio:**
   ```powershell
   icacls .venv
   ```

3. **Usar ruta de usuario:**
   ```powershell
   $env:UV_PROJECT_ENVIRONMENT="$env:USERPROFILE\.venv"
   uv venv
   ```

#### Problema: Path demasiado largo

```
ERROR: The path is too long
```

**Solución:**

1. **Habilitar paths largos en Windows:**
   ```powershell
   # Como administrador
   New-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\FileSystem" -Name "LongPathsEnabled" -Value 1 -PropertyType DWORD -Force
   ```

2. **Usar ubicación más corta:**
   ```powershell
   uv venv C:\v\proyecto
   ```

### 10.5 Cómo limpiar y reconstruir entornos

#### Limpieza completa

```bash
# 1. Eliminar entorno virtual
rm -rf .venv

# 2. Limpiar cache de uv
uv cache clean

# 3. Recrear entorno
uv venv

# 4. Reinstalar dependencias
uv sync
```

#### Limpieza selectiva

```bash
# Limpiar solo cache
uv cache clean

# Reinstalar solo un paquete
uv pip install --force-reinstall package-name

# Desinstalar y reinstalar
uv pip uninstall package-name
uv pip install package-name
```

#### Verificar integridad

```bash
# Verificar dependencias instaladas
uv pip list

# Verificar conflictos
uv pip check

# Verificar lockfile
uv lock --check
```

---

## 11. Buenas prácticas y recomendaciones

### 11.1 Qué versionar en Git

#### ✅ SÍ versionar

1. **`pyproject.toml`** - Configuración del proyecto y dependencias
2. **`uv.lock`** - Lockfile para reproducibilidad exacta
3. **`.python-version`** - Versión de Python del proyecto
4. **`.gitignore`** - Configuración de Git
5. **`README.md`** - Documentación
6. **Código fuente** - Todo el código en `src/`, `tests/`, etc.

#### ❌ NO versionar

1. **`.venv/`** - Entorno virtual (se recrea)
2. **`__pycache__/`** - Cache de Python
3. **`*.pyc`** - Bytecode compilado
4. **`.pytest_cache/`** - Cache de pytest
5. **`dist/`**, **`build/`** - Artefactos de construcción
6. **`*.egg-info/`** - Metadatos de paquetes

### 11.2 Qué ignorar

Ver sección 4.4 para `.gitignore` completo.

### 11.3 Estrategias para equipos

#### 1. Estándarizar versión de Python

Todos los miembros del equipo deben usar la misma versión:

```bash
# Crear .python-version en la raíz
echo "3.11.5" > .python-version

# Versionar en Git
git add .python-version
```

#### 2. Usar lockfiles siempre

```bash
# Generar lockfile
uv sync

# Versionar en Git
git add uv.lock
```

#### 3. Documentar proceso de setup

En `README.md`:

```markdown
## Setup del proyecto

1. Instalar uv: https://docs.astral.sh/uv/
2. Clonar repositorio
3. Ejecutar: `uv sync`
4. Activar entorno: `source .venv/bin/activate` (Linux/macOS) o `.venv\Scripts\activate` (Windows)
```

#### 4. Pre-commit hooks

Crear `.pre-commit-config.yaml`:

```yaml
repos:
  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.4.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-added-large-files

  - repo: local
    hooks:
      - id: uv-lock-check
        name: Check uv.lock is up to date
        entry: bash -c 'uv lock --check || (echo "uv.lock is out of date. Run: uv lock" && exit 1)'
        language: system
        pass_filenames: false
```

#### 5. CI/CD para validación

Ver sección 9 para ejemplos de pipelines que validan:
- Lockfile actualizado
- Tests pasan
- Linters pasan

### 11.4 Uso en producción

#### 1. Usar `--frozen` siempre

```bash
uv sync --frozen
```

Garantiza versiones exactas.

#### 2. No instalar dependencias de desarrollo

```bash
uv sync --frozen --no-dev
```

#### 3. Validar lockfile antes de desplegar

```bash
uv lock --check
```

#### 4. Usar imágenes Docker optimizadas

Ver sección 8 para Dockerfiles optimizados.

#### 5. Monitorear actualizaciones de seguridad

```bash
# Verificar paquetes desactualizados
uv pip list --outdated

# Actualizar solo cuando sea necesario
uv lock --upgrade-package package-name
```

### 11.5 Versionado semántico

#### En `pyproject.toml`

```toml
[project]
name = "mi-proyecto"
version = "1.2.3"  # MAJOR.MINOR.PATCH
```

#### Estrategia de versionado

- **MAJOR**: Cambios incompatibles
- **MINOR**: Nuevas funcionalidades compatibles
- **PATCH**: Correcciones de bugs

#### Dependencias con versionado semántico

```toml
[project]
dependencies = [
    "numpy>=1.24.0,<2.0.0",  # Permitir parches y menores, no mayores
    "pandas~=2.0.0",          # ~= permite 2.0.x pero no 2.1.0
    "scikit-learn==1.3.0",    # Versión exacta (máxima reproducibilidad)
]
```

#### Actualizar dependencias

```bash
# Ver qué está desactualizado
uv pip list --outdated

# Actualizar todas (cuidado)
uv lock --upgrade

# Actualizar específica
uv lock --upgrade-package numpy

# Regenerar lockfile
uv sync
```

---

## Conclusión

`uv` es una herramienta poderosa que moderniza el flujo de trabajo de desarrollo Python, ofreciendo velocidad, reproducibilidad y simplicidad. Esta guía cubre los aspectos esenciales para integrar `uv` en proyectos profesionales de Python, desde desarrollo hasta producción.

### Recursos adicionales

- **Documentación oficial**: https://docs.astral.sh/uv/
- **GitHub**: https://github.com/astral-sh/uv
- **Discord**: Comunidad de Astral para soporte

### Próximos pasos

1. Instalar `uv` en tu sistema
2. Crear un proyecto de prueba siguiendo esta guía
3. Integrar `uv` en tus proyectos existentes gradualmente
4. Compartir conocimiento con tu equipo

---

*Documento generado para uso profesional en proyectos Python con `uv`.*

