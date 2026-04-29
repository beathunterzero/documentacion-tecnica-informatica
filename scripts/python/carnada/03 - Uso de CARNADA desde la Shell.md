## 1. Identificación

**Proyecto:** CARNADA  
**Tipo de nota:** Guía de uso operativo  
**Área:** Python / CLI / Ciberseguridad defensiva  
**Estado:** Documentación base  
**Autor:** Rhodyn Ildefonso (@beathunterzero)  
**Relación con:** Proyecto personal CARNADA  
**Enfoque:** Uso práctico de CARNADA desde la terminal.

---

## 2. Propósito

Esta nota documenta cómo usar **CARNADA** desde la shell.

El objetivo es tener una guía práctica para ejecutar la herramienta, generar contraseñas, seleccionar perfiles, producir salidas limpias para scripting, obtener salida JSON y analizar contraseñas existentes de forma local.

CARNADA está diseñada para funcionar como una herramienta CLI simple:

```text
entrada por terminal
procesamiento local
salida en consola
```

No requiere instalación de dependencias externas y no necesita conexión a internet.

---

## 3. Requisitos

CARNADA requiere:

```text
Python 3.10 o superior
```

No requiere paquetes externos.

La herramienta usa únicamente librerías estándar de Python, por ejemplo:

```text
argparse
json
math
secrets
string
sys
dataclasses
getpass
```

Esto permite clonar el proyecto y ejecutarlo directamente.

---

## 4. Clonar el proyecto

Desde una terminal Linux, WSL o macOS:

```bash
git clone https://github.com/beathunterzero/carnada.git
cd carnada
```

En Windows también puede clonarse con Git Bash, PowerShell o desde una interfaz gráfica como GitHub Desktop.

---

## 5. Ejecutar CARNADA con Python

La forma más directa de ejecutar la herramienta es:

```bash
python3 carnada.py
```

En algunos sistemas, especialmente Windows, el comando puede ser:

```powershell
python carnada.py
```

Si ambos comandos existen, se recomienda usar el que apunte a Python 3.

Para verificar la versión de Python:

```bash
python3 --version
```

o:

```powershell
python --version
```

---

## 6. Ejecutar CARNADA como script

En Linux, WSL o macOS, se puede dar permiso de ejecución al archivo:

```bash
chmod +x carnada.py
```

Luego se puede ejecutar directamente:

```bash
./carnada.py
```

Esto funciona porque el script contiene una línea shebang:

```python
#!/usr/bin/env python3
```

La línea shebang permite que el sistema use el intérprete de Python disponible en el entorno.

---

## 7. Uso básico

Para generar una contraseña segura con la configuración por defecto:

```bash
python3 carnada.py
```

Ejemplo de salida:

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

Por defecto, CARNADA usa el perfil:

```text
strong
```

Este perfil está pensado para generar contraseñas fuertes de uso general.

---

## 8. Generar una contraseña con longitud personalizada

Se puede definir la longitud con `-l` o `--length`.

Ejemplo:

```bash
python3 carnada.py -l 24
```

Equivalente:

```bash
python3 carnada.py --length 24
```

Ejemplo de salida:

```text
CARNADA — Secure Password Generator
------------------------------------------
Password : N9#rVx28@LmQ7sKpT4zW!a
Profile  : strong
Length   : 24
Charset  : 82 characters
Entropy  : ~152.59 bits
Rating   : very strong
```

A mayor longitud, mayor entropía aproximada, siempre que la contraseña sea generada aleatoriamente.

---

## 9. Usar perfiles de generación

CARNADA incluye perfiles predefinidos para distintos escenarios.

Sintaxis:

```bash
python3 carnada.py --profile <perfil>
```

Perfiles disponibles:

```text
strong
legacy
pin
hex
wifi
```

Ejemplo:

```bash
python3 carnada.py --profile legacy
```

Cada perfil ajusta longitud, grupos de caracteres y compatibilidad según el caso de uso.

---

## 10. Perfil strong

El perfil `strong` es el perfil por defecto.

Usa:

```text
mayúsculas
minúsculas
números
símbolos
```

Ejemplo:

```bash
python3 carnada.py --profile strong
```

Caso de uso:

```text
contraseñas generales para sistemas modernos
```

---

## 11. Perfil legacy

El perfil `legacy` está pensado para sistemas antiguos o restrictivos.

Usa:

```text
mayúsculas
minúsculas
números
```

No usa símbolos.

Ejemplo:

```bash
python3 carnada.py --profile legacy
```

Caso de uso:

```text
sistemas que rechazan símbolos o tienen políticas antiguas
```

---

## 12. Perfil pin

El perfil `pin` genera códigos numéricos.

Usa:

```text
números
```

Ejemplo:

```bash
python3 carnada.py --profile pin
```

PIN de longitud personalizada:

```bash
python3 carnada.py --profile pin -l 8
```

Caso de uso:

```text
códigos numéricos temporales o escenarios donde solo se permiten dígitos
```

Un PIN tiene menos entropía que una contraseña completa. Debe usarse solo cuando el sistema lo requiera.

---

## 13. Perfil hex

El perfil `hex` genera valores hexadecimales.

Usa:

```text
0123456789abcdef
```

Ejemplo:

```bash
python3 carnada.py --profile hex
```

Caso de uso:

```text
tokens, laboratorios técnicos o valores que requieren formato hexadecimal
```

---

## 14. Perfil wifi

El perfil `wifi` genera contraseñas largas y compatibles para redes Wi-Fi.

Usa:

```text
mayúsculas
minúsculas
números
```

Evita símbolos complejos para facilitar el ingreso manual en dispositivos como:

```text
teléfonos
routers
televisores
consolas
dispositivos IoT
```

Ejemplo:

```bash
python3 carnada.py --profile wifi
```

---

## 15. Generar múltiples contraseñas

Para generar varias contraseñas, se usa `--count` o `-c`.

Ejemplo:

```bash
python3 carnada.py --count 5
```

Equivalente:

```bash
python3 carnada.py -c 5
```

Ejemplo de salida:

```text
CARNADA — Secure Password Generator
------------------------------------------
Profile  : strong
Count    : 5
Length   : 18
Entropy  : ~114.44 bits each
Rating   : very strong
------------------------------------------
1. V7#kQm92@tLx8pRz
2. B4!sKp81@TxQ9mVw
3. M8#qLn73@RvX2pZa
4. K5@wTr49#LpQ8nXs
5. Z9!vMc61@HsT3qRb
```

Este modo es útil cuando se quieren generar varias opciones y seleccionar una.

---

## 16. Modo quiet

El modo `quiet` imprime únicamente la contraseña generada.

Ejemplo:

```bash
python3 carnada.py --quiet
```

Equivalente:

```bash
python3 carnada.py -q
```

Salida:

```text
V7#kQm92@tLx8pRz
```

Este modo es útil para scripting.

Ejemplo:

```bash
PASSWORD=$(python3 carnada.py --quiet)
```

Consideración de seguridad:

```text
Una contraseña guardada en una variable de shell puede quedar expuesta si el script imprime variables, genera logs o se ejecuta en un entorno no confiable.
```

---

## 17. Salida JSON

CARNADA puede entregar salida estructurada en JSON.

Ejemplo:

```bash
python3 carnada.py --json
```

Ejemplo de salida:

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

La salida JSON es útil para integración con herramientas o scripts.

Consideración de seguridad:

```text
Redirigir esta salida a un archivo puede guardar una contraseña en texto claro.
```

Ejemplo que debe evitarse con credenciales reales:

```bash
python3 carnada.py --json > password.json
```

---

## 18. Desactivar grupos de caracteres

CARNADA permite excluir grupos específicos.

Opciones disponibles:

```text
--no-upper
--no-lower
--no-numbers
--no-symbols
```

Ejemplo: generar sin símbolos:

```bash
python3 carnada.py --no-symbols
```

Ejemplo: generar sin mayúsculas ni símbolos:

```bash
python3 carnada.py --no-upper --no-symbols
```

Ejemplo: generar sin números:

```bash
python3 carnada.py --no-numbers
```

Debe permanecer al menos un grupo de caracteres activo.

Ejemplo inválido:

```bash
python3 carnada.py --no-upper --no-lower --no-numbers --no-symbols
```

---

## 19. Caracteres ambiguos

Por defecto, CARNADA evita caracteres visualmente ambiguos.

Ejemplos:

```text
O
0
I
1
l
o
```

Esto ayuda cuando la contraseña debe escribirse manualmente.

Para permitir caracteres ambiguos:

```bash
python3 carnada.py --allow-ambiguous
```

Este modo puede aumentar ligeramente el conjunto de caracteres disponible, pero reduce la legibilidad manual.

---

## 20. Modo check

CARNADA permite analizar una contraseña existente de forma local.

Uso recomendado:

```bash
python3 carnada.py check
```

La herramienta pedirá la contraseña mediante entrada oculta:

```text
Password to check:
```

Ejemplo de salida:

```text
CARNADA — Password Check
------------------------------------------
Length          : 12
Uppercase       : yes
Lowercase       : yes
Numbers         : yes
Symbols         : yes
Ambiguous chars : yes
Entropy         : ~78.66 bits
Rating          : strong
```

Este modo no almacena la contraseña y no la envía a ningún servicio externo.

---

## 21. Analizar una contraseña desde argumento

También se puede pasar la contraseña directamente en el comando:

```bash
python3 carnada.py check "Password123!"
```

Este método es útil para pruebas o ejemplos, pero no es recomendable para contraseñas reales.

Motivo:

```text
La contraseña puede quedar registrada en el historial de la shell o ser visible temporalmente como argumento del proceso.
```

Uso recomendado para contraseñas reales:

```bash
python3 carnada.py check
```

---

## 22. Salida JSON en modo check

El modo `check` también soporta salida JSON.

Ejemplo:

```bash
python3 carnada.py check --json
```

Ejemplo de salida:

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

También se puede analizar un valor de prueba directamente:

```bash
python3 carnada.py check "Password123!" --json
```

---

## 23. Ver ayuda

Para mostrar el menú principal de ayuda:

```bash
python3 carnada.py --help
```

Para mostrar ayuda del modo generate:

```bash
python3 carnada.py generate --help
```

Para mostrar ayuda del modo check:

```bash
python3 carnada.py check --help
```

La ayuda sirve para revisar parámetros disponibles, perfiles y opciones de salida.

---

## 24. Ver versión

Para mostrar la versión de CARNADA:

```bash
python3 carnada.py --version
```

Ejemplo:

```text
carnada 1.0.0
```

---

## 25. Patrones comunes de uso

Generar una contraseña fuerte:

```bash
python3 carnada.py
```

Generar una contraseña larga:

```bash
python3 carnada.py -l 32
```

Generar contraseña para sistema restrictivo:

```bash
python3 carnada.py --profile legacy
```

Generar contraseña para Wi-Fi:

```bash
python3 carnada.py --profile wifi
```

Generar PIN:

```bash
python3 carnada.py --profile pin -l 6
```

Generar token hexadecimal:

```bash
python3 carnada.py --profile hex
```

Generar varias opciones:

```bash
python3 carnada.py --count 10
```

Salida limpia para scripts:

```bash
python3 carnada.py --quiet
```

Analizar contraseña con entrada oculta:

```bash
python3 carnada.py check
```

---

## 26. Ejemplos que deben evitarse con credenciales reales

Evitar pasar contraseñas reales directamente en la shell:

```bash
python3 carnada.py check "MiPasswordReal123!"
```

Evitar guardar salida con contraseña en un archivo plano:

```bash
python3 carnada.py --json > password.json
```

Evitar imprimir variables que contienen contraseñas:

```bash
PASSWORD=$(python3 carnada.py --quiet)
echo "$PASSWORD"
```

Estos comandos pueden ser útiles en laboratorio, pero no son recomendables para credenciales reales.

---

## 27. Solución de problemas comunes

### Python no encontrado

Probar con:

```bash
python carnada.py
```

o:

```bash
python3 carnada.py
```

Verificar versión:

```bash
python3 --version
```

---

### Permiso denegado al ejecutar `./carnada.py`

Dar permisos de ejecución:

```bash
chmod +x carnada.py
```

Ejecutar:

```bash
./carnada.py
```

---

### Perfil inválido

Usar uno de los perfiles disponibles:

```text
strong
legacy
pin
hex
wifi
```

Ejemplo:

```bash
python3 carnada.py --profile strong
```

---

### Todos los grupos de caracteres fueron desactivados

Ejemplo inválido:

```bash
python3 carnada.py --no-upper --no-lower --no-numbers --no-symbols
```

Debe quedar al menos un grupo activo.

---

## 28. Conclusión operativa

CARNADA está pensada para ejecutarse directamente desde la shell y entregar resultados claros.

Su flujo operativo principal es:

```text
generar contraseña
mostrar resultado
opcionalmente analizar contraseña
salir sin almacenar secretos
```

La herramienta mantiene una lógica simple:

- ejecución local;
    
- sin dependencias externas;
    
- sin almacenamiento;
    
- salida normal, quiet o JSON;
    
- soporte para perfiles;
    
- análisis básico de contraseñas.
    

Desde una perspectiva operativa, CARNADA funciona como una utilidad ligera para terminal, útil en laboratorios, pruebas, generación temporal de credenciales y flujos personales de ciberseguridad defensiva.

---

### Documentación relacionada

[[01 - Índice del Proyecto CARNADA]]