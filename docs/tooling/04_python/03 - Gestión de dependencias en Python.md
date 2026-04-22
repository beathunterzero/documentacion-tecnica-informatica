## 1. El Rol de PIP y los Entornos Virtuales

**PIP** (_Package Installer for Python_) es el gestor de paquetes estándar. Su función es descargar e instalar librerías externas. Para evitar conflictos entre proyectos (ej. un script que requiere `requests v2.0` y otro `v2.31`), se utilizan los **Entornos Virtuales (.venv)**.

## 2. Flujo Operativo Correcto

El error más común es intentar gestionar la carpeta `.venv` manualmente. Esta carpeta es de uso exclusivo del intérprete de Python; el analista interactúa con ella a través de la **activación**.

### 2.1 Activación y Contexto

No se debe entrar a la carpeta `.venv`. El comando se ejecuta desde la **raíz del proyecto**:

Bash

```bash
# 1. Navegar al proyecto
cd ~/mi_proyecto

# 2. Activar el entorno (En WSL/Linux)
source .venv/bin/activate

# 3. Validar activación (El prompt cambia)
# (.venv) rhodyn@beathunterzero:~/mi_proyecto$
```

Una vez activado, los comandos `python` y `pip` apuntan automáticamente a los binarios dentro de `.venv`, aislando cualquier instalación del sistema global.

## 3. Gestión de Requerimientos (`requirements.txt`)

Este archivo es el manifiesto de dependencias del proyecto. Permite que cualquier otro analista (o tú mismo en otra máquina) replique el entorno exacto.

- **Exportar estado actual:** Registra todas las librerías instaladas y sus versiones.
    
    `pip freeze > requirements.txt`
    
- **Instalación masiva:** Reconstruye el entorno basado en el manifiesto.
    
    `pip install -r requirements.txt`
    
- **Actualización:** Tras actualizar un paquete, es mandatorio regenerar el archivo.
    
    `pip install --upgrade nombre_paquete && pip freeze > requirements.txt`
    

## 4. Versionado Semántico

Las versiones en Python siguen el formato **MAJOR.MINOR.PATCH** (ej. `2.31.0`):

1. **MAJOR (2):** Cambios que rompen la compatibilidad (requiere revisión de código).
    
2. **MINOR (31):** Nuevas funcionalidades (retrocompatible).
    
3. **PATCH (0):** Correcciones de errores y seguridad.
    

> **Regla de Seguridad:** Evitar versiones flotantes (ej. `requests>=2.0`). Es preferible "congelar" la versión exacta (`requests==2.31.0`) para evitar que una actualización automática rompa un script de CTH crítico.

## 5. Recuperación: Regenerar Entorno desde Cero

Si el entorno virtual se corrompe o presenta comportamientos erráticos, la solución más eficiente es eliminarlo y reconstruirlo:

Bash

```bash
rm -rf .venv                      # Eliminar carpeta corrupta
python3.12 -m venv .venv          # Crear nuevo entorno limpio
source .venv/bin/activate         # Activar
pip install -r requirements.txt   # Reinstalar dependencias exactas
```

## 6. Buenas Prácticas

- **Aislamiento Total:** Nunca uses `sudo pip install`. Las instalaciones globales ensucian el sistema operativo.
    
- **Higiene de Manifiesto:** Mantén solo las dependencias estrictamente necesarias para reducir la superficie de ataque y el peso del proyecto.
    
- **Documentación:** Asegúrate de que el `README.md` indique la versión de Python recomendada para el entorno.
    

---

### Referencias Externas

- [Python.org: Installing Packages using pip and venv](https://packaging.python.org/en/latest/guides/installing-using-pip-and-virtual-environments/)
    
- [Semantic Versioning 2.0.0 Standard](https://semver.org/)
    

### Documentación Relacionada

[[01 - Python para WSL]]
[[04 - Automatización y scripting en Python]]