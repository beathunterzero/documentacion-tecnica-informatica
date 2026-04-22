## 1. Estándares de Estilo (PEP 8)

El código en Python debe ser legible y consistente. Seguir el **PEP 8** no es solo una cuestión estética; facilita la auditoría de scripts durante un incidente y la colaboración en repositorios técnicos.

- **Indentación:** Utilizar estrictamente **4 espacios** (evitar el uso de tabuladores).
    
- **Nombres Descriptivos:**
    
    - `snake_case` para funciones y variables (ej. `analizar_log_acceso`).
        
    - `PascalCase` para clases (ej. `IncidenteAnalizador`).
        
- **Longitud de Línea:** Mantener un máximo de **79 caracteres** para asegurar legibilidad en terminales divididas o editores tipo Vim.
    
- **Modularidad:** Mantener funciones pequeñas con una sola responsabilidad (principio de responsabilidad única).
    

## 2. El Punto de Entrada (`__main__`)

Es fundamental proteger el punto de entrada de los scripts para que el código no se ejecute accidentalmente si el archivo es importado como un módulo en otro script.

Python

```python
def main():
    # Lógica principal del script
    pass

if __name__ == "__main__":
    main()
```

- **Ventaja:** Permite la reutilización de funciones en otros proyectos sin disparar la ejecución del script original.
    

## 3. Gestión de Logs vs. Print

En herramientas de **Cyber Threat Hunting**, el uso de `print()` es insuficiente. Se debe implementar la librería `logging` para tener control sobre la severidad y el destino de los mensajes.

Python

```python
import logging

# Configuración de nivel (DEBUG, INFO, WARNING, ERROR, CRITICAL)
logging.basicConfig(level=logging.INFO, format='%(levelname)s: %(message)s')

logging.info("Conexión establecida con la base de datos de IoCs")
logging.error("Fallo al intentar parsear el archivo de logs")
```

- **Por qué usar Logging:** Permite redirigir errores a archivos específicos mientras mantienes la consola limpia, y facilita el rastreo de fallos en ejecuciones desatendidas (scripts de cron).
    

## 4. Manejo de Excepciones y Resiliencia

Un script de automatización no debe detenerse abruptamente por un error menor. Es vital implementar bloques `try-except` para capturar errores y registrarlos correctamente.

Python

```python
try:
    with open("evidencia.log", "r") as file:
        data = file.read()
except FileNotFoundError:
    logging.error("El archivo de evidencia no existe en la ruta especificada.")
except Exception as e:
    logging.error(f"Ocurrió un error inesperado: {e}")
```

## 5. Documentación y Docstrings

En ciberseguridad, un script sin documentar es una deuda técnica peligrosa. Cada función debe explicar su propósito, sus entradas y sus salidas.

Python

```python
def extraer_ips(texto):
    """
    Extrae direcciones IPv4 de un bloque de texto plano.

    Args:
        texto (str): Cadena de texto a procesar.

    Returns:
        list: Lista de direcciones IP encontradas.
    """
    # Lógica de regex aquí
    pass
```

## 6. Organización de Módulos

Para proyectos que crecen más allá de un solo archivo, sigue esta estructura jerárquica:

Plaintext

```plaintext
src/
│
├── main.py         # Orquestador principal
├── utils.py        # Funciones genéricas (regex, formateo)
└── core/           # Lógica de negocio / servicios
    └── parser.py   # Lógica específica de procesamiento
```

---

### Referencias Externas

- [Python.org: PEP 8 – Style Guide for Python Code](https://peps.python.org/pep-0008/)
    

### Documentación Relacionada
[[01 - Python para WSL]]