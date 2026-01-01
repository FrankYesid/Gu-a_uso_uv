# Explicación del archivo `uv.lock`

## ¿Qué es `uv.lock`?

`uv.lock` es un archivo generado automáticamente por `uv` que contiene información detallada sobre todas las dependencias del proyecto, incluyendo versiones exactas, hashes de verificación y dependencias transitivas (dependencias de dependencias).

## ¿Por qué es importante?

1. **Reproducibilidad**: Garantiza que todos los desarrolladores y entornos (desarrollo, CI/CD, producción) usen exactamente las mismas versiones de paquetes.
2. **Seguridad**: Los hashes permiten verificar la integridad de los paquetes instalados.
3. **Velocidad**: `uv` puede resolver dependencias más rápido usando el lockfile.

## Estructura del archivo

El archivo `uv.lock` es un archivo TOML con la siguiente estructura general:

```toml
version = 1

[[package]]
name = "numpy"
version = "1.24.3"
source = { registry = "https://pypi.org/simple" }
dependencies = [
    "scipy",
]
sdist = { url = "...", hash = "sha256:..." }
wheels = [
    { url = "...", hash = "sha256:..." },
]

[[package]]
name = "pandas"
version = "2.0.3"
source = { registry = "https://pypi.org/simple" }
dependencies = [
    "numpy>=1.23.2",
    "python-dateutil>=2.8.2",
    "pytz>=2020.1",
    "tzdata>=2022.1",
]
# ... más metadatos
```

## Componentes principales

### 1. Versión del lockfile

```toml
version = 1
```

Indica la versión del formato del lockfile. Esto permite a `uv` manejar diferentes versiones del formato en el futuro.

### 2. Información del paquete

Cada paquete tiene:

- **`name`**: Nombre del paquete
- **`version`**: Versión exacta instalada
- **`source`**: De dónde se obtiene (PyPI, Git, etc.)
- **`dependencies`**: Dependencias directas de este paquete
- **`sdist`**: Source distribution (archivo fuente)
- **`wheels`**: Archivos wheel precompilados para diferentes plataformas
- **`hash`**: Hash SHA256 para verificación de integridad

### 3. Dependencias transitivas

El lockfile incluye **todas** las dependencias, no solo las directas:

```
tu-proyecto
├── numpy (dependencia directa)
│   └── (sin dependencias en este ejemplo)
└── pandas (dependencia directa)
    ├── numpy>=1.23.2 (dependencia transitiva)
    ├── python-dateutil>=2.8.2
    ├── pytz>=2020.1
    └── tzdata>=2022.1
```

Todas estas aparecen en el lockfile con sus versiones exactas resueltas.

## Ejemplo real (simplificado)

Aquí hay un ejemplo simplificado de cómo se vería un `uv.lock` para un proyecto con `numpy` y `pandas`:

```toml
version = 1

[[package]]
name = "numpy"
version = "1.24.3"
source = { registry = "https://pypi.org/simple" }
dependencies = []
sdist = { 
    url = "https://files.pythonhosted.org/packages/.../numpy-1.24.3.tar.gz",
    hash = "sha256:abc123def456..."
}
wheels = [
    { 
        url = "https://files.pythonhosted.org/packages/.../numpy-1.24.3-cp311-cp311-win_amd64.whl",
        hash = "sha256:xyz789..."
    },
]

[[package]]
name = "pandas"
version = "2.0.3"
source = { registry = "https://pypi.org/simple" }
dependencies = [
    "numpy>=1.23.2",
    "python-dateutil>=2.8.2",
    "pytz>=2020.1",
    "tzdata>=2022.1",
]
sdist = { 
    url = "https://files.pythonhosted.org/packages/.../pandas-2.0.3.tar.gz",
    hash = "sha256:def456..."
}
wheels = [
    { 
        url = "https://files.pythonhosted.org/packages/.../pandas-2.0.3-cp311-cp311-win_amd64.whl",
        hash = "sha256:ghi789..."
    },
]

[[package]]
name = "python-dateutil"
version = "2.8.2"
source = { registry = "https://pypi.org/simple" }
dependencies = [
    "six>=1.5",
]
# ... más metadatos
```

## ¿Cuándo se genera?

El lockfile se genera automáticamente cuando:

1. Ejecutas `uv sync` por primera vez
2. Ejecutas `uv lock` explícitamente
3. Modificas `pyproject.toml` y ejecutas `uv sync`

## ¿Cuándo se actualiza?

El lockfile se actualiza cuando:

1. Modificas dependencias en `pyproject.toml` y ejecutas `uv sync`
2. Ejecutas `uv lock --upgrade` para actualizar todas las dependencias
3. Ejecutas `uv lock --upgrade-package nombre-paquete` para actualizar uno específico

## Comandos relacionados

```bash
# Generar/actualizar lockfile
uv lock

# Generar lockfile y sincronizar entorno
uv sync

# Verificar que lockfile está actualizado
uv lock --check

# Actualizar todas las dependencias
uv lock --upgrade

# Actualizar un paquete específico
uv lock --upgrade-package numpy

# Instalar exactamente como está en lockfile
uv sync --frozen
```

## Mejores prácticas

### ✅ SÍ hacer

1. **Versionar `uv.lock` en Git**: Esto garantiza reproducibilidad
2. **Usar `--frozen` en CI/CD y producción**: Asegura versiones exactas
3. **Regenerar después de cambios en dependencias**: Mantén el lockfile actualizado
4. **Revisar cambios en PRs**: Los cambios en `uv.lock` muestran qué dependencias cambiaron

### ❌ NO hacer

1. **Editar manualmente**: El lockfile se genera automáticamente
2. **Ignorar en Git**: Perderías reproducibilidad
3. **Actualizar sin necesidad**: Solo actualiza cuando sea necesario (seguridad, nuevas features)

## Resolución de conflictos

Si hay conflictos en el lockfile:

```bash
# Regenerar desde cero
rm uv.lock
uv lock

# O actualizar dependencias conflictivas
uv lock --upgrade-package paquete-conflicto
```

## Verificación de integridad

Los hashes en el lockfile permiten verificar que los paquetes no han sido modificados:

```bash
# uv verifica automáticamente los hashes al instalar
uv sync --frozen
```

Si un hash no coincide, `uv` rechazará la instalación por seguridad.

## Tamaño del archivo

Los lockfiles pueden ser grandes (varios KB o incluso MB) porque incluyen:
- Todas las dependencias transitivas
- Hashes para múltiples plataformas
- Metadatos completos

Esto es normal y necesario para la reproducibilidad.

## Compatibilidad

- `uv.lock` es específico de `uv`
- No es compatible directamente con `poetry.lock` o `Pipfile.lock`
- Sin embargo, puedes migrar proyectos usando `uv` con otros gestores

---

**Nota importante**: Este documento explica la estructura y uso de `uv.lock`. El archivo real se genera automáticamente y no debe editarse manualmente.

