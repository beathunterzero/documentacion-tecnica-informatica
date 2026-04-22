## 1. Arquitectura de Proyecto Estandarizada

Mantener una estructura consistente en Python no solo facilita la organización personal, sino que asegura la compatibilidad con herramientas de empaquetado y facilita la integración en flujos de **CI/CD**. El siguiente esquema representa el estándar profesional para herramientas de automatización y scripts de seguridad.

### 1.1 Esquema de Directorios Recomendado

Plaintext

```plaintext
mi_proyecto/
│
├── .venv/               # Entorno virtual local (SIEMPRE ignorado en Git)
├── src/                 # Código fuente del proyecto
│   ├── __init__.py      # Convierte la carpeta en un paquete
│   └── main.py          # Punto de entrada principal
├── docs/                # Documentación técnica y diagramas
├── tests/               # Scripts de prueba y validación (opcional)
├── requirements.txt     # Listado de dependencias del proyecto
├── .gitignore           # Definición de archivos excluidos
└── README.md            # Manual de uso y configuración
```

## 2. Definición de Componentes Críticos

### 2.1 El Directorio `src/` (Source)

Es el contenedor de la lógica de negocio. Separar el código en este directorio evita que archivos de configuración (como el `.gitignore`) se mezclen con los módulos importables.

- **Nota para Scripts Directos:** Para scripts de una sola tarea o de uso rápido, se puede omitir `src/` para simplificar la ejecución, pero en proyectos escalables es mandatorio.
    

### 2.2 Entorno Virtual (`.venv/`)

Contiene el intérprete de Python y las librerías instaladas específicamente para este proyecto.

- **Regla de Oro:** Debe existir en la raíz pero **nunca** debe ser rastreado por Git .
    

### 2.3 Archivos de Configuración Raíz

- **requirements.txt:** Archivo esencial que lista las librerías externas. Permite replicar el entorno con: `pip install -r requirements.txt`.
    
- **.gitignore:** Debe incluir `/ .venv /`, `__pycache__ /` y archivos de variables de entorno `.env`.
    

## 3. Jerarquía de Módulos

Para asegurar que Python reconozca tus carpetas como módulos importables, es buena práctica incluir un archivo vacío llamado `__init__.py` dentro de `src/`.

Python

```python
# Ejemplo de importación interna
from src.utils import limpiador_de_logs
```

## 4. Mejores Prácticas de Organización

- **Principio de Aislamiento:** Un proyecto = un entorno virtual. Nunca instales librerías de forma global para evitar conflictos de versiones.
    
- **Punto de Entrada Único:** Mantener `main.py` como el orquestador principal que invoca funciones de otros módulos.
    
- **Separación de Entornos:** No mezclar código de pruebas (`tests/`) con código funcional de producción (`src/`).
    
- **Documentación Activa:** El `README.md` debe explicar no solo qué hace el script, sino los comandos exactos para levantar el entorno virtual e instalar las dependencias.
    

---

### Referencias Externas

- [Python Packaging User Guide: Project Structure](https://packaging.python.org/en/latest/tutorials/packaging-projects/)
    
- [The Hitchhiker's Guide to Python: Structuring Your Project](https://docs.python-guide.org/writing/structure/)
    

### Documentación Relacionada

[[01 - Python para WSL]]
[[03 - Gestión de dependencias en Python]]