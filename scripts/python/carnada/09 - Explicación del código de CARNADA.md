## 1. Identificación

**Proyecto:** CARNADA  
**Tipo de nota:** Explicación de código  
**Área:** Python / CLI / Ciberseguridad defensiva  
**Estado:** Documentación técnica  
**Autor:** Rhodyn Ildefonso (@beathunterzero)  
**Relación con:** Proyecto personal CARNADA  
**Enfoque:** Explicación bloque por bloque y línea por línea del código principal `carnada.py`.

---

## 2. Propósito

Esta nota documenta el funcionamiento interno del archivo principal de CARNADA:

```text
carnada.py
```

El objetivo es explicar para qué sirve cada bloque del código, cómo se relacionan las funciones entre sí y qué decisiones técnicas hacen que la herramienta funcione como una CLI local para generación y evaluación de contraseñas.

Esta explicación sirve como documentación personal para entender, mantener, mejorar o defender técnicamente el proyecto.

---

## 3. Vista general del script

CARNADA está escrito como un único archivo Python.

El script se encarga de:

- definir constantes globales;
    
- definir perfiles de generación;
    
- construir conjuntos de caracteres;
    
- generar contraseñas seguras;
    
- analizar contraseñas existentes;
    
- calcular entropía aproximada;
    
- formatear salida normal, quiet o JSON;
    
- procesar argumentos CLI;
    
- ejecutar el modo `generate` o el modo `check`.
    

El flujo general es:

```text
main()
  ↓
build_parser()
  ↓
parse_args()
  ↓
run_generate() o run_check()
  ↓
funciones auxiliares
  ↓
salida en terminal
```

---

# 4. Shebang del script

Código:

```python
#!/usr/bin/env python3
```

Esta línea se llama **shebang**.

Sirve para indicar al sistema operativo qué intérprete debe usar para ejecutar el archivo cuando se lanza directamente desde la shell.

Gracias a esta línea, en Linux, WSL o macOS se puede ejecutar:

```bash
chmod +x carnada.py
./carnada.py
```

En vez de escribir siempre:

```bash
python3 carnada.py
```

La parte:

```text
/usr/bin/env
```

busca el intérprete `python3` disponible en el entorno del usuario.

Esto es más portable que usar una ruta fija como:

```text
/usr/bin/python3
```

porque no todos los sistemas tienen Python instalado exactamente en la misma ubicación.

---

# 5. Docstring principal del programa

Código:

```python
"""
carnada - Local-first CLI password generator and password checker.

Genera contraseñas seguras usando aleatoriedad criptográfica.
No guarda secretos, no cifra archivos y no envía datos a internet.
"""
```

Este bloque es un **docstring de módulo**.

Sirve para describir el propósito general del archivo.

Explica que CARNADA es:

```text
Local-first CLI password generator and password checker
```

Esto significa:

- herramienta de línea de comandos;
    
- ejecución local;
    
- generación de contraseñas;
    
- análisis básico de contraseñas.
    

También deja claras tres decisiones de seguridad:

```text
No guarda secretos.
No cifra archivos.
No envía datos a internet.
```

Esta descripción es importante porque resume el alcance de la herramienta desde el propio código.

---

# 6. Importación de anotaciones futuras

Código:

```python
from __future__ import annotations
```

Esta línea activa un comportamiento moderno de Python relacionado con las anotaciones de tipos.

Permite que las anotaciones de tipos se evalúen de forma diferida.

En términos prácticos, ayuda a usar anotaciones como:

```python
dict[str, Profile]
```

o:

```python
str | None
```

de forma más limpia y compatible.

Esta línea no afecta directamente la lógica de generación de contraseñas, pero mejora la calidad del código y su compatibilidad con type hints modernos.

---

# 7. Imports estándar

Código:

```python
import argparse
import json
import math
import secrets
import string
import sys
from dataclasses import dataclass
from getpass import getpass
```

Este bloque importa únicamente módulos de la librería estándar de Python.

Eso es importante porque CARNADA no requiere dependencias externas.

---

## 7.1 `argparse`

Código:

```python
import argparse
```

`argparse` permite construir interfaces de línea de comandos.

En CARNADA se usa para procesar argumentos como:

```bash
python3 carnada.py --profile legacy
python3 carnada.py --count 5
python3 carnada.py check
python3 carnada.py --json
```

Sin `argparse`, habría que procesar manualmente `sys.argv`, lo cual sería menos limpio y más propenso a errores.

---

## 7.2 `json`

Código:

```python
import json
```

`json` permite convertir estructuras de Python en salida JSON.

Se usa para el modo:

```bash
python3 carnada.py --json
```

Ejemplo de salida generada con `json.dumps()`:

```json
{
  "profile": "strong",
  "count": 1,
  "length": 18,
  "charset_size": 82,
  "entropy_bits": 114.44,
  "rating": "very strong",
  "passwords": [
    "V7#kQm92@tLx8pRz"
  ]
}
```

---

## 7.3 `math`

Código:

```python
import math
```

`math` proporciona funciones matemáticas.

En CARNADA se usa para calcular logaritmo base 2:

```python
math.log2(charset_size)
```

Esto forma parte del cálculo de entropía aproximada.

---

## 7.4 `secrets`

Código:

```python
import secrets
```

`secrets` es el módulo correcto para generar valores criptográficamente seguros en Python.

CARNADA lo usa para seleccionar caracteres aleatorios de forma segura:

```python
secrets.choice(group)
```

y para mezclar la contraseña generada:

```python
secrets.SystemRandom().shuffle(password_chars)
```

Este módulo es preferible a `random` cuando se generan contraseñas, tokens o secretos.

---

## 7.5 `string`

Código:

```python
import string
```

`string` contiene constantes útiles de caracteres.

CARNADA usa:

```python
string.ascii_uppercase
string.ascii_lowercase
string.digits
```

Estas constantes permiten construir conjuntos de caracteres sin escribir manualmente todo el alfabeto o los dígitos.

---

## 7.6 `sys`

Código:

```python
import sys
```

`sys` permite interactuar con aspectos del sistema y del intérprete.

En CARNADA se usa principalmente para escribir errores en `stderr`:

```python
print("Error...", file=sys.stderr)
```

Esto separa los mensajes de error de la salida normal.

Es útil para scripting porque permite redirigir salida estándar y errores por separado.

---

## 7.7 `dataclass`

Código:

```python
from dataclasses import dataclass
```

`dataclass` permite crear clases simples para almacenar datos.

CARNADA lo usa para definir la clase `Profile`.

Esto evita escribir manualmente métodos como `__init__`.

---

## 7.8 `getpass`

Código:

```python
from getpass import getpass
```

`getpass` permite pedir datos al usuario sin mostrarlos en pantalla.

CARNADA lo usa en el modo `check` cuando el usuario no pasa la contraseña como argumento.

Ejemplo:

```bash
python3 carnada.py check
```

El programa solicita:

```text
Password to check:
```

pero no muestra lo que el usuario escribe.

---

# 8. Constantes generales de la aplicación

Código:

```python
APP_NAME = "carnada"
APP_VERSION = "1.0.0"
```

Estas constantes definen metadatos generales del programa.

---

## 8.1 `APP_NAME`

Código:

```python
APP_NAME = "carnada"
```

Define el nombre de la herramienta.

Se usa en:

- encabezados de salida;
    
- configuración del parser;
    
- versión del programa.
    

Ejemplo:

```python
prog=APP_NAME
```

y:

```python
print(f"{APP_NAME.upper()} — Secure Password Generator")
```

---

## 8.2 `APP_VERSION`

Código:

```python
APP_VERSION = "1.0.0"
```

Define la versión actual del programa.

Se usa para responder al argumento:

```bash
python3 carnada.py --version
```

Ejemplo de salida:

```text
carnada 1.0.0
```

---

# 9. Constantes de longitud

Código:

```python
MIN_LENGTH = 8
DEFAULT_LENGTH = 18
```

Estas constantes definen reglas generales de longitud.

---

## 9.1 `MIN_LENGTH`

Código:

```python
MIN_LENGTH = 8
```

Define la longitud mínima recomendada para una contraseña general.

Si el usuario intenta generar una contraseña demasiado corta, el programa puede ajustarla automáticamente.

Ejemplo dentro de `generate_password()`:

```python
if length < MIN_LENGTH:
    length = MIN_LENGTH
```

Esto protege contra contraseñas demasiado débiles en perfiles generales.

---

## 9.2 `DEFAULT_LENGTH`

Código:

```python
DEFAULT_LENGTH = 18
```

Define una longitud por defecto general.

En el código actual, los perfiles tienen su propia longitud por defecto. Esta constante queda disponible para uso general o para mantener una referencia global.

Aunque no sea la constante más usada en el flujo final, documenta la intención original del diseño:

```text
18 caracteres como longitud segura por defecto.
```

---

# 10. Constantes de caracteres

Código:

```python
UPPERCASE = string.ascii_uppercase
LOWERCASE = string.ascii_lowercase
NUMBERS = string.digits
SYMBOLS = "!@#$%^&*()-_=+[]{};:,.<>/?"
```

Estas constantes definen los grupos de caracteres disponibles para generar contraseñas.

---

## 10.1 `UPPERCASE`

Código:

```python
UPPERCASE = string.ascii_uppercase
```

Contiene todas las letras mayúsculas del alfabeto inglés:

```text
ABCDEFGHIJKLMNOPQRSTUVWXYZ
```

Se usa cuando un perfil permite mayúsculas.

---

## 10.2 `LOWERCASE`

Código:

```python
LOWERCASE = string.ascii_lowercase
```

Contiene todas las letras minúsculas del alfabeto inglés:

```text
abcdefghijklmnopqrstuvwxyz
```

Se usa cuando un perfil permite minúsculas.

---

## 10.3 `NUMBERS`

Código:

```python
NUMBERS = string.digits
```

Contiene los dígitos:

```text
0123456789
```

Se usa cuando un perfil permite números.

---

## 10.4 `SYMBOLS`

Código:

```python
SYMBOLS = "!@#$%^&*()-_=+[]{};:,.<>/?"
```

Define manualmente el conjunto de símbolos permitidos.

Estos símbolos se usan en perfiles como:

```text
strong
```

El conjunto está controlado explícitamente para evitar usar caracteres demasiado problemáticos en shell o interfaces antiguas.

---

# 11. Caracteres ambiguos

Código:

```python
AMBIGUOUS_CHARS = set("O0I1lo")
```

Esta constante define caracteres visualmente ambiguos.

Contiene:

```text
O
0
I
1
l
o
```

Se usa para evitar contraseñas difíciles de leer o transcribir.

Ejemplo de confusión:

```text
O puede confundirse con 0
I puede confundirse con 1
l puede confundirse con 1
o puede confundirse con 0
```

La función `remove_ambiguous()` usa esta constante para eliminar esos caracteres de los grupos activos.

---

# 12. Clase `Profile`

Código:

```python
@dataclass(frozen=True)
class Profile:
    name: str
    description: str
    length: int
    use_upper: bool
    use_lower: bool
    use_numbers: bool
    use_symbols: bool
    allow_ambiguous: bool = False
```

Esta clase representa un perfil de generación.

Cada perfil define cómo debe generarse una contraseña.

---

## 12.1 Decorador `@dataclass(frozen=True)`

Código:

```python
@dataclass(frozen=True)
```

`@dataclass` convierte la clase en una estructura de datos simple.

`frozen=True` hace que las instancias sean inmutables.

Eso significa que, una vez creado un perfil, sus valores no deberían modificarse.

Ejemplo:

```python
profile.length = 20
```

no debería permitirse si el objeto está congelado.

Esto es útil porque los perfiles funcionan como configuraciones estáticas.

---

## 12.2 Definición de clase

Código:

```python
class Profile:
```

Define una nueva clase llamada `Profile`.

Esta clase actúa como plantilla para perfiles como:

```text
strong
legacy
pin
hex
wifi
```

---

## 12.3 Campo `name`

Código:

```python
name: str
```

Nombre interno del perfil.

Ejemplo:

```text
strong
```

---

## 12.4 Campo `description`

Código:

```python
description: str
```

Descripción humana del perfil.

Ejemplo:

```text
Uso general con letras, números y símbolos.
```

---

## 12.5 Campo `length`

Código:

```python
length: int
```

Longitud por defecto del perfil.

Ejemplo:

```text
18 para strong
16 para legacy
6 para pin
32 para hex
24 para wifi
```

---

## 12.6 Campo `use_upper`

Código:

```python
use_upper: bool
```

Indica si el perfil usa letras mayúsculas.

---

## 12.7 Campo `use_lower`

Código:

```python
use_lower: bool
```

Indica si el perfil usa letras minúsculas.

---

## 12.8 Campo `use_numbers`

Código:

```python
use_numbers: bool
```

Indica si el perfil usa números.

---

## 12.9 Campo `use_symbols`

Código:

```python
use_symbols: bool
```

Indica si el perfil usa símbolos.

---

## 12.10 Campo `allow_ambiguous`

Código:

```python
allow_ambiguous: bool = False
```

Indica si el perfil permite caracteres ambiguos.

El valor por defecto es:

```python
False
```

Eso significa que, salvo que un perfil indique lo contrario, CARNADA evitará caracteres ambiguos.

---

# 13. Diccionario `PROFILES`

Código:

```python
PROFILES: dict[str, Profile] = {
    ...
}
```

Este diccionario almacena todos los perfiles disponibles.

La clave es el nombre del perfil como string.

El valor es una instancia de `Profile`.

Ejemplo conceptual:

```python
"strong": Profile(...)
```

Esto permite acceder a un perfil así:

```python
profile = PROFILES[args.profile]
```

---

# 14. Perfil `strong`

Código:

```python
"strong": Profile(
    name="strong",
    description="Uso general con letras, números y símbolos.",
    length=18,
    use_upper=True,
    use_lower=True,
    use_numbers=True,
    use_symbols=True,
),
```

Este perfil es el perfil por defecto.

---

## 14.1 Nombre del perfil

Código:

```python
name="strong"
```

Define el nombre interno del perfil.

---

## 14.2 Descripción

Código:

```python
description="Uso general con letras, números y símbolos."
```

Indica que es un perfil general, con letras, números y símbolos.

---

## 14.3 Longitud

Código:

```python
length=18
```

Define 18 caracteres como longitud predeterminada.

---

## 14.4 Uso de mayúsculas

Código:

```python
use_upper=True
```

El perfil incluye letras mayúsculas.

---

## 14.5 Uso de minúsculas

Código:

```python
use_lower=True
```

El perfil incluye letras minúsculas.

---

## 14.6 Uso de números

Código:

```python
use_numbers=True
```

El perfil incluye números.

---

## 14.7 Uso de símbolos

Código:

```python
use_symbols=True
```

El perfil incluye símbolos.

---

## 14.8 Interpretación del perfil

El perfil `strong` está pensado para:

- contraseñas generales;
    
- sistemas modernos;
    
- credenciales fuertes;
    
- laboratorios;
    
- cuentas que aceptan símbolos.
    

---

# 15. Perfil `legacy`

Código:

```python
"legacy": Profile(
    name="legacy",
    description="Compatible con sistemas antiguos: letras y números.",
    length=16,
    use_upper=True,
    use_lower=True,
    use_numbers=True,
    use_symbols=False,
),
```

Este perfil está diseñado para sistemas restrictivos.

---

## 15.1 Nombre

```python
name="legacy"
```

Identifica el perfil como `legacy`.

---

## 15.2 Descripción

```python
description="Compatible con sistemas antiguos: letras y números."
```

Explica que está pensado para sistemas antiguos o limitados.

---

## 15.3 Longitud

```python
length=16
```

Usa 16 caracteres por defecto.

---

## 15.4 Mayúsculas

```python
use_upper=True
```

Incluye mayúsculas.

---

## 15.5 Minúsculas

```python
use_lower=True
```

Incluye minúsculas.

---

## 15.6 Números

```python
use_numbers=True
```

Incluye números.

---

## 15.7 Símbolos

```python
use_symbols=False
```

No incluye símbolos.

Esto mejora compatibilidad con sistemas que rechazan caracteres especiales.

---

# 16. Perfil `pin`

Código:

```python
"pin": Profile(
    name="pin",
    description="PIN numérico.",
    length=6,
    use_upper=False,
    use_lower=False,
    use_numbers=True,
    use_symbols=False,
    allow_ambiguous=True,
),
```

Este perfil genera PINs numéricos.

---

## 16.1 Nombre

```python
name="pin"
```

Nombre del perfil.

---

## 16.2 Descripción

```python
description="PIN numérico."
```

Indica que genera códigos numéricos.

---

## 16.3 Longitud

```python
length=6
```

Genera PINs de 6 dígitos por defecto.

---

## 16.4 Mayúsculas y minúsculas

```python
use_upper=False
use_lower=False
```

No usa letras.

---

## 16.5 Números

```python
use_numbers=True
```

Usa números.

---

## 16.6 Símbolos

```python
use_symbols=False
```

No usa símbolos.

---

## 16.7 Caracteres ambiguos

```python
allow_ambiguous=True
```

Permite caracteres ambiguos.

En el caso de PIN, esto tiene sentido porque los dígitos `0` y `1` son parte natural del conjunto numérico.

Si no se permitieran ambiguos, el PIN excluiría `0` y `1`, lo cual sería menos estándar.

---

# 17. Perfil `hex`

Código:

```python
"hex": Profile(
    name="hex",
    description="Token hexadecimal.",
    length=32,
    use_upper=False,
    use_lower=False,
    use_numbers=True,
    use_symbols=False,
    allow_ambiguous=True,
),
```

Este perfil genera tokens hexadecimales.

---

## 17.1 Nombre

```python
name="hex"
```

Nombre del perfil.

---

## 17.2 Descripción

```python
description="Token hexadecimal."
```

Indica que el perfil genera valores compatibles con formato hexadecimal.

---

## 17.3 Longitud

```python
length=32
```

Genera 32 caracteres por defecto.

Como cada carácter hexadecimal representa 4 bits, 32 caracteres equivalen conceptualmente a 128 bits.

---

## 17.4 Mayúsculas y minúsculas

```python
use_upper=False
use_lower=False
```

Aunque el perfil hexadecimal usa letras `a-f`, el código maneja el caso `hex` de forma especial en `get_charset_groups()`.

Por eso estas banderas no determinan directamente el charset final del perfil `hex`.

---

## 17.5 Números

```python
use_numbers=True
```

Marca que el perfil usa números.

---

## 17.6 Símbolos

```python
use_symbols=False
```

No usa símbolos.

---

## 17.7 Caracteres ambiguos

```python
allow_ambiguous=True
```

Permite caracteres ambiguos porque hexadecimal incluye `0` y `1`.

---

# 18. Perfil `wifi`

Código:

```python
"wifi": Profile(
    name="wifi",
    description="Contraseña larga y compatible para redes Wi-Fi.",
    length=24,
    use_upper=True,
    use_lower=True,
    use_numbers=True,
    use_symbols=False,
),
```

Este perfil genera contraseñas largas y compatibles para redes Wi-Fi.

---

## 18.1 Nombre

```python
name="wifi"
```

Nombre del perfil.

---

## 18.2 Descripción

```python
description="Contraseña larga y compatible para redes Wi-Fi."
```

Indica que está pensado para claves Wi-Fi.

---

## 18.3 Longitud

```python
length=24
```

Usa 24 caracteres por defecto.

---

## 18.4 Mayúsculas, minúsculas y números

```python
use_upper=True
use_lower=True
use_numbers=True
```

Usa letras y números.

---

## 18.5 Símbolos

```python
use_symbols=False
```

No usa símbolos.

Esto mejora la facilidad de ingreso manual en teléfonos, televisores, routers, consolas y dispositivos IoT.

---

# 19. Función `remove_ambiguous`

Código:

```python
def remove_ambiguous(chars: str) -> str:
    """Elimina caracteres visualmente ambiguos."""
    return "".join(char for char in chars if char not in AMBIGUOUS_CHARS)
```

Esta función recibe un string de caracteres y devuelve otro string sin caracteres ambiguos.

---

## 19.1 Definición de función

```python
def remove_ambiguous(chars: str) -> str:
```

Define una función llamada `remove_ambiguous`.

Recibe:

```python
chars: str
```

Devuelve:

```python
str
```

Esto indica que recibe texto y devuelve texto.

---

## 19.2 Docstring

```python
"""Elimina caracteres visualmente ambiguos."""
```

Explica el propósito de la función.

---

## 19.3 Retorno

```python
return "".join(char for char in chars if char not in AMBIGUOUS_CHARS)
```

Esta línea hace varias cosas:

1. Recorre cada carácter de `chars`.
    
2. Filtra los caracteres que no están en `AMBIGUOUS_CHARS`.
    
3. Une los caracteres restantes en un nuevo string.
    

Ejemplo conceptual:

```python
remove_ambiguous("ABCDEFIO01")
```

Resultado aproximado:

```text
ABCDEF
```

porque elimina:

```text
I
O
0
1
```

---

# 20. Función `get_charset_groups`

Código:

```python
def get_charset_groups(
    use_upper: bool,
    use_lower: bool,
    use_numbers: bool,
    use_symbols: bool,
    allow_ambiguous: bool,
    profile_name: str | None = None,
) -> list[str]:
    """Construye los grupos de caracteres activos."""
```

Esta función construye la lista de grupos de caracteres que se usarán para generar contraseñas.

---

## 20.1 Parámetro `use_upper`

```python
use_upper: bool
```

Indica si se deben incluir mayúsculas.

---

## 20.2 Parámetro `use_lower`

```python
use_lower: bool
```

Indica si se deben incluir minúsculas.

---

## 20.3 Parámetro `use_numbers`

```python
use_numbers: bool
```

Indica si se deben incluir números.

---

## 20.4 Parámetro `use_symbols`

```python
use_symbols: bool
```

Indica si se deben incluir símbolos.

---

## 20.5 Parámetro `allow_ambiguous`

```python
allow_ambiguous: bool
```

Indica si se permiten caracteres ambiguos.

---

## 20.6 Parámetro `profile_name`

```python
profile_name: str | None = None
```

Permite pasar el nombre del perfil.

Puede ser un string o `None`.

Se usa especialmente para detectar el perfil:

```text
hex
```

---

## 20.7 Retorno

```python
) -> list[str]:
```

La función devuelve una lista de strings.

Cada string representa un grupo de caracteres.

Ejemplo:

```python
[
    "ABCDEFGHJKLMNPQRSTUVWXYZ",
    "abcdefghijkmnpqrstuvwxyz",
    "23456789",
    "!@#$%^&*()-_=+[]{};:,.<>/?"
]
```

---

## 20.8 Caso especial para `hex`

Código:

```python
if profile_name == "hex":
    return ["0123456789abcdef"]
```

Si el perfil es `hex`, la función devuelve directamente el conjunto hexadecimal.

Esto evita construir el charset con las reglas normales de mayúsculas, minúsculas y números.

El charset hexadecimal válido es:

```text
0123456789abcdef
```

---

## 20.9 Inicialización de grupos

Código:

```python
groups: list[str] = []
```

Crea una lista vacía llamada `groups`.

En esta lista se irán agregando los grupos activos.

---

## 20.10 Agregar mayúsculas

Código:

```python
if use_upper:
    groups.append(UPPERCASE if allow_ambiguous else remove_ambiguous(UPPERCASE))
```

Si `use_upper` es verdadero, se agregan mayúsculas.

Si `allow_ambiguous` es verdadero, se agregan todas las mayúsculas.

Si `allow_ambiguous` es falso, se eliminan caracteres ambiguos de `UPPERCASE`.

Ejemplo:

```text
ABCDEFGHIJKLMNOPQRSTUVWXYZ
```

sin ambiguos:

```text
ABCDEFGHJKLMNPQRSTUVWXYZ
```

---

## 20.11 Agregar minúsculas

Código:

```python
if use_lower:
    groups.append(LOWERCASE if allow_ambiguous else remove_ambiguous(LOWERCASE))
```

Si `use_lower` es verdadero, se agregan minúsculas.

Si no se permiten ambiguos, se eliminan letras como:

```text
l
o
```

---

## 20.12 Agregar números

Código:

```python
if use_numbers:
    groups.append(NUMBERS if allow_ambiguous else remove_ambiguous(NUMBERS))
```

Si `use_numbers` es verdadero, se agregan números.

Si no se permiten ambiguos, se eliminan:

```text
0
1
```

---

## 20.13 Agregar símbolos

Código:

```python
if use_symbols:
    groups.append(SYMBOLS)
```

Si `use_symbols` es verdadero, se agrega el conjunto de símbolos.

A los símbolos no se les aplica `remove_ambiguous()` porque la lista de ambiguos definida solo contiene letras y números.

---

## 20.14 Retorno final

Código:

```python
return [group for group in groups if group]
```

Devuelve únicamente grupos que no estén vacíos.

Esto evita que se agreguen strings vacíos por accidente.

---

# 21. Función `calculate_entropy`

Código:

```python
def calculate_entropy(length: int, charset_size: int) -> float:
    """Calcula entropía aproximada en bits."""
    if length <= 0 or charset_size <= 1:
        return 0.0

    return length * math.log2(charset_size)
```

Esta función calcula la entropía aproximada de una contraseña.

---

## 21.1 Definición

```python
def calculate_entropy(length: int, charset_size: int) -> float:
```

Recibe:

```text
length: longitud de la contraseña
charset_size: tamaño del conjunto de caracteres
```

Devuelve:

```text
float
```

---

## 21.2 Docstring

```python
"""Calcula entropía aproximada en bits."""
```

Explica que el resultado se expresa en bits.

---

## 21.3 Validación defensiva

Código:

```python
if length <= 0 or charset_size <= 1:
    return 0.0
```

Si la longitud es cero o negativa, no tiene sentido calcular entropía.

Si el tamaño del charset es menor o igual a 1, tampoco existe variabilidad suficiente.

Por eso devuelve:

```python
0.0
```

---

## 21.4 Cálculo de entropía

Código:

```python
return length * math.log2(charset_size)
```

Aplica la fórmula conceptual:

```text
entropía = longitud × log2(tamaño del charset)
```

Ejemplo:

```text
longitud = 18
charset = 82

entropía = 18 × log2(82)
```

El resultado representa una estimación aproximada de bits de entropía.

---

# 22. Función `rate_entropy`

Código:

```python
def rate_entropy(entropy_bits: float) -> str:
    """Clasifica la fortaleza según entropía aproximada."""
    if entropy_bits < 40:
        return "weak"
    if entropy_bits < 70:
        return "moderate"
    if entropy_bits < 100:
        return "strong"

    return "very strong"
```

Esta función clasifica la fortaleza de la contraseña según la entropía estimada.

---

## 22.1 Definición

```python
def rate_entropy(entropy_bits: float) -> str:
```

Recibe un número decimal que representa bits de entropía.

Devuelve un string con la clasificación.

---

## 22.2 Docstring

```python
"""Clasifica la fortaleza según entropía aproximada."""
```

Indica que la clasificación se basa en entropía aproximada.

---

## 22.3 Clasificación débil

```python
if entropy_bits < 40:
    return "weak"
```

Si la entropía es menor a 40 bits, se clasifica como:

```text
weak
```

---

## 22.4 Clasificación moderada

```python
if entropy_bits < 70:
    return "moderate"
```

Si no fue menor a 40, pero es menor a 70, se clasifica como:

```text
moderate
```

---

## 22.5 Clasificación fuerte

```python
if entropy_bits < 100:
    return "strong"
```

Si no fue menor a 70, pero es menor a 100, se clasifica como:

```text
strong
```

---

## 22.6 Clasificación muy fuerte

```python
return "very strong"
```

Si llega a este punto, significa que la entropía es igual o superior a 100 bits.

La clasificación será:

```text
very strong
```

---

# 23. Función `generate_password`

Código:

```python
def generate_password(
    length: int,
    groups: list[str],
) -> str:
    """
    Genera una contraseña garantizando al menos un carácter
    de cada grupo activo.
    """
```

Esta función genera una contraseña segura.

Recibe:

- longitud deseada;
    
- grupos de caracteres activos.
    

Devuelve:

- contraseña generada.
    

---

## 23.1 Definición

```python
def generate_password(
    length: int,
    groups: list[str],
) -> str:
```

La función recibe:

```python
length: int
```

y:

```python
groups: list[str]
```

Devuelve:

```python
str
```

---

## 23.2 Docstring

```python
"""
Genera una contraseña garantizando al menos un carácter
de cada grupo activo.
"""
```

Explica una característica importante:

```text
la contraseña incluirá al menos un carácter de cada grupo activo
```

Esto evita generar una contraseña que, por azar, no tenga números o símbolos aunque estén habilitados.

---

## 23.3 Validación de grupos

Código:

```python
if not groups:
    raise ValueError("Debe existir al menos un grupo de caracteres activo.")
```

Si no hay grupos activos, no se puede generar contraseña.

Ejemplo inválido:

```bash
python3 carnada.py --no-upper --no-lower --no-numbers --no-symbols
```

En ese caso, la función lanza un error.

---

## 23.4 Ajuste de longitud mínima

Código:

```python
if length < MIN_LENGTH:
    length = MIN_LENGTH
```

Si la longitud es menor que la mínima definida, se ajusta automáticamente.

Ejemplo:

```text
length = 4
MIN_LENGTH = 8
```

Resultado:

```text
length = 8
```

Esta validación ayuda a evitar contraseñas demasiado cortas.

---

## 23.5 Validación de longitud vs grupos

Código:

```python
if length < len(groups):
    raise ValueError(
        f"La longitud mínima para los grupos seleccionados es {len(groups)}."
    )
```

Esta validación asegura que la longitud sea suficiente para incluir al menos un carácter de cada grupo activo.

Ejemplo:

```text
groups = mayúsculas, minúsculas, números, símbolos
len(groups) = 4
```

La contraseña debe tener al menos 4 caracteres para incluir uno de cada grupo.

---

## 23.6 Construcción del conjunto total

Código:

```python
all_chars = "".join(groups)
```

Une todos los grupos activos en un solo string.

Ejemplo:

```python
groups = ["ABC", "abc", "123"]
```

Resultado:

```python
all_chars = "ABCabc123"
```

Este conjunto total se usará para llenar el resto de la contraseña.

---

## 23.7 Selección obligatoria por grupo

Código:

```python
password_chars = [secrets.choice(group) for group in groups]
```

Esta línea selecciona un carácter aleatorio de cada grupo activo.

Ejemplo:

```text
un carácter de mayúsculas
un carácter de minúsculas
un carácter de números
un carácter de símbolos
```

Esto garantiza composición mínima.

---

## 23.8 Longitud restante

Código:

```python
remaining_length = length - len(password_chars)
```

Calcula cuántos caracteres faltan para alcanzar la longitud solicitada.

Ejemplo:

```text
length = 18
len(password_chars) = 4
remaining_length = 14
```

---

## 23.9 Completar contraseña

Código:

```python
password_chars.extend(secrets.choice(all_chars) for _ in range(remaining_length))
```

Agrega caracteres aleatorios al resto de la contraseña.

Estos caracteres se eligen del conjunto total:

```python
all_chars
```

La variable `_` se usa porque el índice no importa; solo interesa repetir la operación.

---

## 23.10 Mezclar caracteres

Código:

```python
secrets.SystemRandom().shuffle(password_chars)
```

Mezcla la lista de caracteres.

Esto es importante porque los primeros caracteres fueron seleccionados uno por grupo. Sin mezclar, la contraseña podría tener una estructura predecible.

Ejemplo de estructura sin mezclar:

```text
Mayúscula + minúscula + número + símbolo + resto
```

Después de mezclar, el orden queda aleatorio.

---

## 23.11 Retorno final

Código:

```python
return "".join(password_chars)
```

Convierte la lista de caracteres en un string final.

Ejemplo:

```python
["V", "7", "#", "k"]
```

se convierte en:

```text
V7#k
```

---

# 24. Función `analyze_password`

Código:

```python
def analyze_password(password: str) -> dict[str, object]:
    """Evalúa una contraseña localmente."""
```

Esta función analiza una contraseña existente.

Se usa en el modo:

```bash
python3 carnada.py check
```

---

## 24.1 Definición

```python
def analyze_password(password: str) -> dict[str, object]:
```

Recibe:

```python
password: str
```

Devuelve un diccionario con resultados de análisis.

---

## 24.2 Docstring

```python
"""Evalúa una contraseña localmente."""
```

Indica que el análisis se realiza localmente.

No hay red, APIs ni almacenamiento.

---

## 24.3 Calcular longitud

Código:

```python
length = len(password)
```

Obtiene la longitud de la contraseña.

Ejemplo:

```text
Password123!
```

tiene longitud:

```text
12
```

---

## 24.4 Detectar mayúsculas

Código:

```python
has_upper = any(char.isupper() for char in password)
```

Evalúa si al menos un carácter es mayúscula.

`any()` devuelve `True` si alguna condición del recorrido se cumple.

---

## 24.5 Detectar minúsculas

Código:

```python
has_lower = any(char.islower() for char in password)
```

Evalúa si al menos un carácter es minúscula.

---

## 24.6 Detectar números

Código:

```python
has_number = any(char.isdigit() for char in password)
```

Evalúa si al menos un carácter es numérico.

---

## 24.7 Detectar símbolos

Código:

```python
has_symbol = any(char in SYMBOLS for char in password)
```

Evalúa si al menos un carácter pertenece al conjunto `SYMBOLS`.

---

## 24.8 Detectar ambiguos

Código:

```python
has_ambiguous = any(char in AMBIGUOUS_CHARS for char in password)
```

Evalúa si la contraseña contiene caracteres visualmente ambiguos.

---

## 24.9 Inicializar tamaño del charset

Código:

```python
charset_size = 0
```

Inicializa en cero el tamaño estimado del conjunto de caracteres.

Luego se va sumando según las categorías presentes.

---

## 24.10 Sumar mayúsculas al charset

Código:

```python
if has_upper:
    charset_size += len(UPPERCASE)
```

Si la contraseña tiene mayúsculas, suma el tamaño del conjunto de mayúsculas.

---

## 24.11 Sumar minúsculas al charset

Código:

```python
if has_lower:
    charset_size += len(LOWERCASE)
```

Si la contraseña tiene minúsculas, suma el tamaño del conjunto de minúsculas.

---

## 24.12 Sumar números al charset

Código:

```python
if has_number:
    charset_size += len(NUMBERS)
```

Si la contraseña tiene números, suma 10.

---

## 24.13 Sumar símbolos al charset

Código:

```python
if has_symbol:
    charset_size += len(SYMBOLS)
```

Si la contraseña tiene símbolos, suma la cantidad de símbolos definidos en `SYMBOLS`.

---

## 24.14 Calcular entropía

Código:

```python
entropy = calculate_entropy(length, charset_size)
```

Usa la función `calculate_entropy()` para estimar la entropía.

---

## 24.15 Retornar resultado

Código:

```python
return {
    "length": length,
    "uppercase": has_upper,
    "lowercase": has_lower,
    "numbers": has_number,
    "symbols": has_symbol,
    "ambiguous_chars": has_ambiguous,
    "entropy_bits": round(entropy, 2),
    "rating": rate_entropy(entropy),
}
```

Devuelve un diccionario con:

- longitud;
    
- presencia de mayúsculas;
    
- presencia de minúsculas;
    
- presencia de números;
    
- presencia de símbolos;
    
- presencia de ambiguos;
    
- entropía redondeada;
    
- rating.
    

Ejemplo conceptual:

```json
{
  "length": 12,
  "uppercase": true,
  "lowercase": true,
  "numbers": true,
  "symbols": true,
  "ambiguous_chars": true,
  "entropy_bits": 78.66,
  "rating": "strong"
}
```

---

# 25. Función `format_bool`

Código:

```python
def format_bool(value: bool) -> str:
    return "yes" if value else "no"
```

Esta función convierte valores booleanos en texto legible.

---

## 25.1 Definición

```python
def format_bool(value: bool) -> str:
```

Recibe un booleano:

```python
True
```

o:

```python
False
```

Devuelve un string.

---

## 25.2 Retorno condicional

```python
return "yes" if value else "no"
```

Si `value` es verdadero, devuelve:

```text
yes
```

Si es falso, devuelve:

```text
no
```

Esto se usa para imprimir resultados del modo `check`.

Ejemplo:

```text
Uppercase       : yes
Numbers         : no
```

---

# 26. Función `print_generation_result`

Código:

```python
def print_generation_result(
    password: str,
    profile: str,
    charset_size: int,
    entropy_bits: float,
    quiet: bool,
    as_json: bool,
) -> None:
    """Imprime el resultado de generación."""
```

Esta función imprime el resultado cuando se genera una contraseña.

---

## 26.1 Parámetros

Recibe:

```python
password: str
```

Contraseña generada.

```python
profile: str
```

Perfil usado.

```python
charset_size: int
```

Tamaño del conjunto de caracteres.

```python
entropy_bits: float
```

Entropía aproximada.

```python
quiet: bool
```

Define si se imprime solo la contraseña.

```python
as_json: bool
```

Define si se imprime en JSON.

---

## 26.2 Modo quiet

Código:

```python
if quiet:
    print(password)
    return
```

Si `quiet` es verdadero, imprime solo la contraseña y termina la función.

Ejemplo:

```bash
python3 carnada.py --quiet
```

Salida:

```text
V7#kQm92@tLx8pRz
```

---

## 26.3 Construcción de payload

Código:

```python
payload = {
    "password": password,
    "profile": profile,
    "length": len(password),
    "charset_size": charset_size,
    "entropy_bits": round(entropy_bits, 2),
    "rating": rate_entropy(entropy_bits),
}
```

Crea un diccionario con los datos relevantes de la contraseña generada.

Este payload se usa si el usuario solicita JSON.

---

## 26.4 Modo JSON

Código:

```python
if as_json:
    print(json.dumps(payload, indent=2, ensure_ascii=False))
    return
```

Si `as_json` es verdadero, convierte el diccionario a JSON.

`indent=2` hace que el JSON sea legible.

`ensure_ascii=False` permite imprimir caracteres no ASCII correctamente.

---

## 26.5 Salida normal

Código:

```python
print(f"{APP_NAME.upper()} — Secure Password Generator")
print("-" * 42)
print(f"Password : {password}")
print(f"Profile  : {profile}")
print(f"Length   : {len(password)}")
print(f"Charset  : {charset_size} characters")
print(f"Entropy  : ~{round(entropy_bits, 2)} bits")
print(f"Rating   : {rate_entropy(entropy_bits)}")
```

Imprime una salida legible para humanos.

Ejemplo:

```text
CARNADA — Secure Password Generator
------------------------------------------
Password : V7#kQm92@tLx8pRz
Profile  : strong
Length   : 18
Charset  : 82 characters
Entropy  : ~114.44 bits
Rating   : very strong
```

---

# 27. Función `print_check_result`

Código:

```python
def print_check_result(result: dict[str, object], as_json: bool) -> None:
    """Imprime el resultado del análisis de contraseña."""
```

Esta función imprime el resultado del modo `check`.

---

## 27.1 Parámetros

Recibe:

```python
result: dict[str, object]
```

Diccionario generado por `analyze_password()`.

Recibe también:

```python
as_json: bool
```

Indica si la salida debe imprimirse en JSON.

---

## 27.2 Modo JSON

Código:

```python
if as_json:
    print(json.dumps(result, indent=2, ensure_ascii=False))
    return
```

Si el usuario usa:

```bash
python3 carnada.py check --json
```

el resultado se imprime en formato JSON.

---

## 27.3 Salida normal del modo check

Código:

```python
print(f"{APP_NAME.upper()} — Password Check")
print("-" * 42)
print(f"Length          : {result['length']}")
print(f"Uppercase       : {format_bool(bool(result['uppercase']))}")
print(f"Lowercase       : {format_bool(bool(result['lowercase']))}")
print(f"Numbers         : {format_bool(bool(result['numbers']))}")
print(f"Symbols         : {format_bool(bool(result['symbols']))}")
print(f"Ambiguous chars : {format_bool(bool(result['ambiguous_chars']))}")
print(f"Entropy         : ~{result['entropy_bits']} bits")
print(f"Rating          : {result['rating']}")
```

Imprime una tabla legible con los resultados.

La función `format_bool()` convierte `True` o `False` en:

```text
yes
no
```

---

# 28. Función `build_parser`

Código:

```python
def build_parser() -> argparse.ArgumentParser:
```

Esta función construye el parser principal de argumentos CLI.

Es una de las funciones centrales del programa porque define cómo se usa CARNADA desde la shell.

---

## 28.1 Crear parser principal

Código:

```python
parser = argparse.ArgumentParser(
    prog=APP_NAME,
    description="Local-first CLI password generator and password checker.",
    formatter_class=argparse.RawDescriptionHelpFormatter,
    epilog=(
        "Examples:\n"
        "  python carnada.py\n"
        "  python carnada.py -l 24\n"
        "  python carnada.py --profile legacy\n"
        "  python carnada.py --count 5\n"
        "  python carnada.py --quiet\n"
        "  python carnada.py --json\n"
        "  python carnada.py check\n"
        "  python carnada.py check \"Password123!\"\n"
    ),
)
```

Este bloque crea el parser principal.

---

## 28.2 `prog`

```python
prog=APP_NAME
```

Define el nombre mostrado en la ayuda.

Como `APP_NAME` vale:

```text
carnada
```

la ayuda mostrará ese nombre.

---

## 28.3 `description`

```python
description="Local-first CLI password generator and password checker."
```

Describe la herramienta en el menú de ayuda.

---

## 28.4 `formatter_class`

```python
formatter_class=argparse.RawDescriptionHelpFormatter
```

Permite que el texto del epílogo mantenga saltos de línea y formato.

Esto hace que los ejemplos de uso se vean bien.

---

## 28.5 `epilog`

```python
epilog=(...)
```

Define ejemplos que aparecerán al final del menú de ayuda.

Ejemplo:

```text
python carnada.py --profile legacy
python carnada.py check
```

---

## 28.6 Argumento `--version`

Código:

```python
parser.add_argument(
    "--version",
    action="version",
    version=f"{APP_NAME} {APP_VERSION}",
)
```

Agrega soporte para:

```bash
python3 carnada.py --version
```

`action="version"` indica a `argparse` que debe imprimir la versión y salir.

---

## 28.7 Subparsers

Código:

```python
subparsers = parser.add_subparsers(dest="command")
```

Crea subcomandos.

CARNADA usa subcomandos para separar modos como:

```text
generate
check
```

El argumento detectado se guarda en:

```python
args.command
```

---

## 28.8 Subcomando `generate`

Código:

```python
generate_parser = subparsers.add_parser(
    "generate",
    help="Generate secure passwords.",
)
```

Crea el subcomando:

```bash
python3 carnada.py generate
```

Aunque CARNADA también permite generar sin escribir `generate`, este subcomando deja una ruta explícita.

---

## 28.9 Agregar argumentos de generación al subcomando

Código:

```python
add_generate_arguments(generate_parser)
```

Agrega al subcomando `generate` todos los argumentos relacionados con generación:

- `--profile`;
    
- `--length`;
    
- `--count`;
    
- `--quiet`;
    
- `--json`;
    
- etc.
    

---

## 28.10 Subcomando `check`

Código:

```python
check_parser = subparsers.add_parser(
    "check",
    help="Check password strength locally.",
)
```

Crea el subcomando:

```bash
python3 carnada.py check
```

Este modo analiza una contraseña existente.

---

## 28.11 Argumento posicional `password`

Código:

```python
check_parser.add_argument(
    "password",
    nargs="?",
    help="Password to check. If omitted, it will be requested securely.",
)
```

Agrega un argumento opcional llamado `password`.

`nargs="?"` significa que el argumento es opcional.

Entonces ambas formas son válidas:

```bash
python3 carnada.py check
```

y:

```bash
python3 carnada.py check "Password123!"
```

Si se omite, el programa usará `getpass()`.

---

## 28.12 JSON en modo check

Código:

```python
check_parser.add_argument(
    "--json",
    action="store_true",
    help="Print output as JSON.",
)
```

Permite usar:

```bash
python3 carnada.py check --json
```

`action="store_true"` significa que si el flag aparece, el valor será `True`.

---

## 28.13 Argumentos de generación en parser principal

Código:

```python
add_generate_arguments(parser)
```

Esta línea agrega los argumentos de generación también al parser principal.

Eso permite que el usuario ejecute:

```bash
python3 carnada.py --profile legacy
```

sin tener que escribir:

```bash
python3 carnada.py generate --profile legacy
```

Es una decisión de usabilidad.

---

## 28.14 Retorno del parser

Código:

```python
return parser
```

Devuelve el parser configurado para que `main()` pueda usarlo.

---

# 29. Función `add_generate_arguments`

Código:

```python
def add_generate_arguments(parser: argparse.ArgumentParser) -> None:
```

Esta función agrega argumentos relacionados con generación de contraseñas a un parser.

Se usa dos veces:

```python
add_generate_arguments(generate_parser)
add_generate_arguments(parser)
```

Esto permite generación con o sin subcomando explícito.

---

## 29.1 Argumento `--profile`

Código:

```python
parser.add_argument(
    "--profile",
    choices=sorted(PROFILES.keys()),
    default="strong",
    help="Password generation profile. Default: strong.",
)
```

Permite seleccionar un perfil.

Ejemplo:

```bash
python3 carnada.py --profile wifi
```

`choices=sorted(PROFILES.keys())` limita los valores válidos a los perfiles existentes.

Esto evita perfiles inválidos.

---

## 29.2 Argumento `--length` / `-l`

Código:

```python
parser.add_argument(
    "--length",
    "-l",
    type=int,
    default=None,
    help="Password length. Overrides profile default.",
)
```

Permite definir una longitud personalizada.

Ejemplo:

```bash
python3 carnada.py -l 24
```

`type=int` convierte el valor a entero.

`default=None` significa que, si el usuario no define longitud, se usará la longitud del perfil.

---

## 29.3 Argumento `--count` / `-c`

Código:

```python
parser.add_argument(
    "--count",
    "-c",
    type=int,
    default=1,
    help="Number of passwords to generate. Default: 1.",
)
```

Permite generar múltiples contraseñas.

Ejemplo:

```bash
python3 carnada.py --count 5
```

Por defecto genera una sola.

---

## 29.4 Argumento `--no-upper`

Código:

```python
parser.add_argument(
    "--no-upper",
    action="store_true",
    help="Exclude uppercase letters.",
)
```

Si se usa, desactiva letras mayúsculas.

Ejemplo:

```bash
python3 carnada.py --no-upper
```

---

## 29.5 Argumento `--no-lower`

Código:

```python
parser.add_argument(
    "--no-lower",
    action="store_true",
    help="Exclude lowercase letters.",
)
```

Si se usa, desactiva letras minúsculas.

---

## 29.6 Argumento `--no-numbers`

Código:

```python
parser.add_argument(
    "--no-numbers",
    action="store_true",
    help="Exclude numbers.",
)
```

Si se usa, desactiva números.

---

## 29.7 Argumento `--no-symbols`

Código:

```python
parser.add_argument(
    "--no-symbols",
    action="store_true",
    help="Exclude symbols.",
)
```

Si se usa, desactiva símbolos.

---

## 29.8 Argumento `--allow-ambiguous`

Código:

```python
parser.add_argument(
    "--allow-ambiguous",
    action="store_true",
    help="Allow ambiguous characters such as O, 0, I, 1, l and o.",
)
```

Permite caracteres ambiguos.

Ejemplo:

```bash
python3 carnada.py --allow-ambiguous
```

---

## 29.9 Argumento `--quiet` / `-q`

Código:

```python
parser.add_argument(
    "--quiet",
    "-q",
    action="store_true",
    help="Print only the generated password.",
)
```

Permite salida silenciosa.

Ejemplo:

```bash
python3 carnada.py --quiet
```

Imprime solo la contraseña.

---

## 29.10 Argumento `--json`

Código:

```python
parser.add_argument(
    "--json",
    action="store_true",
    help="Print output as JSON.",
)
```

Permite salida JSON.

Ejemplo:

```bash
python3 carnada.py --json
```

---

# 30. Función `run_generate`

Código:

```python
def run_generate(args: argparse.Namespace) -> int:
```

Esta función ejecuta la lógica principal de generación de contraseñas.

Recibe los argumentos procesados por `argparse`.

Devuelve un código de salida:

```text
0 = éxito
1 = error
```

---

## 30.1 Seleccionar perfil

Código:

```python
profile = PROFILES[args.profile]
```

Obtiene el perfil seleccionado por el usuario.

Ejemplo:

```bash
python3 carnada.py --profile wifi
```

Entonces:

```python
args.profile = "wifi"
profile = PROFILES["wifi"]
```

---

## 30.2 Definir longitud

Código:

```python
length = args.length if args.length is not None else profile.length
```

Si el usuario indicó longitud, se usa esa longitud.

Si no indicó longitud, se usa la longitud del perfil.

Ejemplo:

```bash
python3 carnada.py --profile wifi
```

usa:

```text
24
```

Ejemplo:

```bash
python3 carnada.py --profile wifi -l 30
```

usa:

```text
30
```

---

## 30.3 Calcular grupos activos

Código:

```python
use_upper = profile.use_upper and not args.no_upper
use_lower = profile.use_lower and not args.no_lower
use_numbers = profile.use_numbers and not args.no_numbers
use_symbols = profile.use_symbols and not args.no_symbols
```

Estas líneas combinan la configuración del perfil con los flags del usuario.

Ejemplo:

Perfil `strong` usa símbolos:

```python
profile.use_symbols = True
```

Pero si el usuario pasa:

```bash
--no-symbols
```

entonces:

```python
use_symbols = True and not True
use_symbols = False
```

---

## 30.4 Permitir caracteres ambiguos

Código:

```python
allow_ambiguous = args.allow_ambiguous or profile.allow_ambiguous
```

Los caracteres ambiguos se permiten si:

- el usuario usa `--allow-ambiguous`;
    
- o el perfil los permite por defecto.
    

Esto aplica especialmente a perfiles como:

```text
pin
hex
```

---

## 30.5 Validar `count`

Código:

```python
if args.count < 1:
    print("Error: --count must be greater than 0.", file=sys.stderr)
    return 1
```

Si el usuario pide generar cero o menos contraseñas, se muestra error.

Ejemplo inválido:

```bash
python3 carnada.py --count 0
```

El error se envía a `stderr`.

Devuelve:

```python
1
```

para indicar fallo.

---

## 30.6 Validar longitud mínima

Código:

```python
if length < MIN_LENGTH and args.profile != "pin":
    print(
        f"Warning: minimum recommended length is {MIN_LENGTH}. "
        f"Using {MIN_LENGTH}.",
        file=sys.stderr,
    )
    length = MIN_LENGTH
```

Si la longitud es menor a `MIN_LENGTH`, se ajusta.

Excepción:

```python
args.profile != "pin"
```

El perfil `pin` puede tener menos de 8 caracteres porque un PIN de 6 dígitos es un caso válido.

La advertencia se imprime en `stderr`.

---

## 30.7 Construir grupos de caracteres

Código:

```python
groups = get_charset_groups(
    use_upper=use_upper,
    use_lower=use_lower,
    use_numbers=use_numbers,
    use_symbols=use_symbols,
    allow_ambiguous=allow_ambiguous,
    profile_name=args.profile,
)
```

Llama a `get_charset_groups()` para construir los grupos activos.

Esta función devuelve una lista de grupos.

Ejemplo:

```python
[
    "ABCDEFGHJKLMNPQRSTUVWXYZ",
    "abcdefghijkmnpqrstuvwxyz",
    "23456789",
    "!@#$%^&*()-_=+[]{};:,.<>/?"
]
```

---

## 30.8 Validar grupos vacíos

Código:

```python
if not groups:
    print("Error: at least one character group must be enabled.", file=sys.stderr)
    return 1
```

Si el usuario desactiva todos los grupos, se muestra error.

Ejemplo inválido:

```bash
python3 carnada.py --no-upper --no-lower --no-numbers --no-symbols
```

---

## 30.9 Calcular tamaño de charset

Código:

```python
charset_size = len(set("".join(groups)))
```

Une todos los grupos:

```python
"".join(groups)
```

Luego convierte a set para eliminar duplicados:

```python
set(...)
```

Finalmente calcula la cantidad de caracteres únicos.

Esto evita contar caracteres repetidos si existieran.

---

## 30.10 Calcular entropía

Código:

```python
entropy_bits = calculate_entropy(length, charset_size)
```

Calcula la entropía aproximada según la longitud y el tamaño del charset.

---

## 30.11 Generar contraseñas

Código:

```python
passwords = [generate_password(length, groups) for _ in range(args.count)]
```

Genera tantas contraseñas como indique `args.count`.

Ejemplo:

```bash
python3 carnada.py --count 5
```

genera una lista con 5 contraseñas.

---

## 30.12 Salida JSON

Código:

```python
if args.json:
    payload = {
        "profile": args.profile,
        "count": args.count,
        "length": length,
        "charset_size": charset_size,
        "entropy_bits": round(entropy_bits, 2),
        "rating": rate_entropy(entropy_bits),
        "passwords": passwords,
    }
    print(json.dumps(payload, indent=2, ensure_ascii=False))
    return 0
```

Si el usuario usa `--json`, se crea un diccionario con toda la información.

Luego se imprime como JSON.

Devuelve:

```python
0
```

porque terminó correctamente.

---

## 30.13 Salida quiet

Código:

```python
if args.quiet:
    for password in passwords:
        print(password)
    return 0
```

Si el usuario usa `--quiet`, imprime solo las contraseñas.

Si hay varias, imprime una por línea.

Ejemplo:

```bash
python3 carnada.py --count 3 --quiet
```

Salida:

```text
password1
password2
password3
```

---

## 30.14 Salida normal para una contraseña

Código:

```python
if args.count == 1:
    print_generation_result(
        password=passwords[0],
        profile=args.profile,
        charset_size=charset_size,
        entropy_bits=entropy_bits,
        quiet=False,
        as_json=False,
    )
    return 0
```

Si se generó solo una contraseña, se usa `print_generation_result()`.

Esto evita duplicar lógica de impresión.

---

## 30.15 Salida normal para múltiples contraseñas

Código:

```python
print(f"{APP_NAME.upper()} — Secure Password Generator")
print("-" * 42)
print(f"Profile  : {args.profile}")
print(f"Count    : {args.count}")
print(f"Length   : {length}")
print(f"Entropy  : ~{round(entropy_bits, 2)} bits each")
print(f"Rating   : {rate_entropy(entropy_bits)}")
print("-" * 42)
```

Imprime encabezado cuando se generan varias contraseñas.

No muestra `charset_size` en esta salida, pero sí muestra perfil, cantidad, longitud, entropía y rating.

---

## 30.16 Enumerar contraseñas

Código:

```python
for index, password in enumerate(passwords, start=1):
    print(f"{index:02d}. {password}")
```

Recorre la lista de contraseñas y las imprime numeradas.

`start=1` hace que la numeración empiece en 1.

`{index:02d}` imprime el índice con dos dígitos.

Ejemplo:

```text
1. password
2. password
3. password
```

---

## 30.17 Retorno exitoso

Código:

```python
return 0
```

Indica que la función terminó correctamente.

---

# 31. Función `run_check`

Código:

```python
def run_check(args: argparse.Namespace) -> int:
```

Esta función ejecuta el modo de análisis de contraseñas.

---

## 31.1 Obtener contraseña

Código:

```python
password = args.password
```

Obtiene la contraseña pasada como argumento.

Ejemplo:

```bash
python3 carnada.py check "Password123!"
```

Entonces:

```python
args.password = "Password123!"
```

---

## 31.2 Solicitar contraseña si no fue pasada

Código:

```python
if password is None:
    password = getpass("Password to check: ")
```

Si el usuario no pasó contraseña, se solicita de forma oculta.

Ejemplo:

```bash
python3 carnada.py check
```

Esto evita exponer la contraseña en el historial de shell.

---

## 31.3 Validar contraseña vacía

Código:

```python
if not password:
    print("Error: password cannot be empty.", file=sys.stderr)
    return 1
```

Si la contraseña está vacía, se muestra error y se devuelve código 1.

---

## 31.4 Analizar contraseña

Código:

```python
result = analyze_password(password)
```

Llama a `analyze_password()` para obtener un diccionario con el análisis.

---

## 31.5 Imprimir resultado

Código:

```python
print_check_result(result, as_json=args.json)
```

Imprime el resultado en modo normal o JSON según `args.json`.

---

## 31.6 Retorno exitoso

Código:

```python
return 0
```

Indica ejecución correcta.

---

# 32. Función `main`

Código:

```python
def main() -> int:
    parser = build_parser()
    args = parser.parse_args()

    if args.command == "check":
        return run_check(args)

    return run_generate(args)
```

Esta función es el punto central de ejecución del programa.

---

## 32.1 Crear parser

```python
parser = build_parser()
```

Construye el parser CLI.

---

## 32.2 Parsear argumentos

```python
args = parser.parse_args()
```

Lee los argumentos pasados desde la shell.

Ejemplo:

```bash
python3 carnada.py --profile wifi
```

se transforma en un objeto `args`.

---

## 32.3 Detectar modo check

```python
if args.command == "check":
    return run_check(args)
```

Si el subcomando es `check`, ejecuta el modo de análisis.

---

## 32.4 Ejecutar generación por defecto

```python
return run_generate(args)
```

Si no es `check`, ejecuta generación.

Esto permite que el comando más simple funcione:

```bash
python3 carnada.py
```

---

# 33. Bloque de entrada principal

Código:

```python
if __name__ == "__main__":
    try:
        raise SystemExit(main())
    except KeyboardInterrupt:
        print("\nInterrupted by user.", file=sys.stderr)
        raise SystemExit(130)
```

Este bloque permite que el archivo funcione como script ejecutable.

---

## 33.1 Verificación `__name__`

Código:

```python
if __name__ == "__main__":
```

Esta condición se cumple cuando el archivo se ejecuta directamente.

Ejemplo:

```bash
python3 carnada.py
```

Si el archivo fuera importado desde otro script, este bloque no se ejecutaría automáticamente.

---

## 33.2 Bloque `try`

Código:

```python
try:
    raise SystemExit(main())
```

Ejecuta `main()`.

`main()` devuelve un código de salida.

`SystemExit()` termina el programa con ese código.

Ejemplo:

```text
0 = éxito
1 = error controlado
```

---

## 33.3 Manejo de interrupción con teclado

Código:

```python
except KeyboardInterrupt:
```

Captura una interrupción del usuario.

Normalmente ocurre cuando se presiona:

```text
Ctrl + C
```

---

## 33.4 Mensaje de interrupción

Código:

```python
print("\nInterrupted by user.", file=sys.stderr)
```

Imprime un mensaje de interrupción en `stderr`.

---

## 33.5 Código de salida 130

Código:

```python
raise SystemExit(130)
```

Sale con código `130`.

En sistemas Unix/Linux, `130` suele indicar que el proceso fue interrumpido por `Ctrl + C`.

---

# 34. Flujo completo de generación

Cuando el usuario ejecuta:

```bash
python3 carnada.py --profile strong -l 24
```

ocurre este flujo:

```text
1. Se ejecuta el bloque __main__.
2. Se llama a main().
3. main() construye el parser con build_parser().
4. parse_args() interpreta los argumentos.
5. Como no es modo check, se llama a run_generate(args).
6. run_generate() carga el perfil strong.
7. Define longitud 24.
8. Determina grupos activos.
9. Construye charset con get_charset_groups().
10. Calcula charset_size.
11. Calcula entropía.
12. Genera la contraseña con generate_password().
13. Imprime el resultado.
14. Retorna 0.
15. SystemExit termina el proceso correctamente.
```

---

# 35. Flujo completo de análisis

Cuando el usuario ejecuta:

```bash
python3 carnada.py check
```

ocurre este flujo:

```text
1. Se ejecuta el bloque __main__.
2. Se llama a main().
3. main() construye el parser.
4. parse_args() detecta command = check.
5. main() llama a run_check(args).
6. run_check() revisa si hay contraseña como argumento.
7. Como no hay, usa getpass().
8. El usuario escribe la contraseña sin mostrarla.
9. analyze_password() analiza composición y entropía.
10. print_check_result() imprime el resultado.
11. run_check() retorna 0.
12. SystemExit termina el programa correctamente.
```

---

# 36. Relación entre funciones

Mapa funcional:

```text
main()
├── build_parser()
│   └── add_generate_arguments()
├── run_generate()
│   ├── get_charset_groups()
│   │   └── remove_ambiguous()
│   ├── calculate_entropy()
│   ├── generate_password()
│   ├── rate_entropy()
│   └── print_generation_result()
│       └── rate_entropy()
└── run_check()
    ├── getpass()
    ├── analyze_password()
    │   ├── calculate_entropy()
    │   └── rate_entropy()
    └── print_check_result()
        └── format_bool()
```

Este mapa muestra que el programa está separado en responsabilidades claras.

---

# 37. Buenas decisiones presentes en el código

El código tiene varias decisiones correctas:

```text
usa secrets para generación segura
no usa random
no guarda contraseñas
no usa red
no usa dependencias externas
soporta perfiles
soporta salida quiet
soporta salida JSON
usa getpass para entrada sensible
maneja Ctrl + C
usa stderr para errores
mantiene una arquitectura simple
```

---

# 38. Consideraciones técnicas

Aunque el código está bien para el alcance actual, hay puntos que se pueden considerar en el futuro:

```text
agregar tests unitarios
validar mejor combinaciones de flags
separar módulos si el proyecto crece
añadir pyproject.toml si se quiere instalar como comando carnada
añadir type checking con mypy si se desea más rigor
añadir linting con ruff
```

No son requisitos inmediatos.

Para el alcance actual, el script único es razonable.

---

# 39. Conclusión operativa

El código de CARNADA está diseñado para ser simple, local y auditable.

Su funcionamiento se apoya en una cadena clara:

```text
argumentos CLI
perfil
charset
generación segura
entropía
formato de salida
terminal
```

El valor técnico del código está en que evita complejidad innecesaria.

No almacena secretos, no cifra, no genera hashes, no depende de paquetes externos y no se conecta a internet.

Desde una perspectiva de ciberseguridad defensiva, el código cumple una función clara y mantiene una superficie de riesgo reducida.

---

### Documentación relacionada

[[01 - Índice del Proyecto CARNADA]]