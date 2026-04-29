## 1. Identificación

**Proyecto:** CARNADA  
**Tipo de nota:** Documentación de arquitectura  
**Área:** Python / CLI / Ciberseguridad defensiva  
**Estado:** Documentación base  
**Autor:** Rhodyn Ildefonso (@beathunterzero)  
**Relación con:** Proyecto personal CARNADA  
**Enfoque:** Arquitectura lógica de una herramienta CLI local para generación y evaluación de contraseñas.

---

## 2. Propósito

Esta nota documenta la arquitectura de **CARNADA** desde una perspectiva técnica y operativa.

El objetivo es explicar cómo funciona internamente la herramienta, cuáles son sus componentes principales, cómo fluye la ejecución desde la shell y qué decisiones de diseño permiten mantener una herramienta simple, local, auditable y con bajo riesgo operativo.

CARNADA no fue diseñada como una aplicación compleja ni como una suite de gestión de credenciales. Su arquitectura responde a un principio concreto:

> hacer pocas cosas, hacerlas de forma clara y evitar funciones que aumenten innecesariamente el manejo de secretos.

---

## 3. Descripción general de la arquitectura

CARNADA utiliza una arquitectura simple basada en un único script principal:

```text
carnada.py
```

Este archivo concentra la lógica necesaria para:

- recibir argumentos desde la terminal;
    
- interpretar el modo de ejecución;
    
- generar contraseñas seguras;
    
- analizar contraseñas existentes;
    
- calcular entropía aproximada;
    
- formatear la salida;
    
- finalizar sin almacenar datos sensibles.
    

La arquitectura es **local-first**. Esto significa que todas las operaciones ocurren en la máquina del usuario.

No existen:

- llamadas de red;
    
- servicios externos;
    
- bases de datos;
    
- archivos de configuración con secretos;
    
- almacenamiento persistente de contraseñas.
    

El flujo general puede resumirse así:

```text
Usuario / Shell
        ↓
Argumentos CLI
        ↓
Parser de argumentos
        ↓
Modo generate o modo check
        ↓
Procesamiento interno
        ↓
Formato de salida
        ↓
Terminal
```

---

## 4. Principios de diseño

La arquitectura de CARNADA está basada en los siguientes principios:

### Ejecución local

La herramienta funciona completamente en la máquina del usuario. No requiere conexión a internet ni servicios remotos.

### Sin almacenamiento de secretos

CARNADA no guarda contraseñas generadas ni contraseñas analizadas. La información sensible existe solo durante la ejecución.

### Sin dependencias externas

La herramienta usa únicamente librerías estándar de Python. Esto reduce fricción de instalación y riesgos asociados a dependencias de terceros.

### Salida limpia para CLI

La salida está pensada para ser legible en terminal y usable en scripts.

### Separación lógica de responsabilidades

Aunque el proyecto usa un solo archivo principal, internamente las funciones están separadas por responsabilidad:

- parsing;
    
- generación;
    
- análisis;
    
- cálculo de entropía;
    
- formato de salida.
    

### Superficie de riesgo reducida

Se evitaron funciones como cifrado, hashing, vaults o guardado de archivos porque no son necesarias para el objetivo principal del proyecto.

### Auditable por diseño

Al ser una herramienta pequeña y sin dependencias externas, el código es más fácil de revisar, mantener y explicar.

---

## 5. Componentes lógicos

Aunque CARNADA se encuentra en un solo archivo, la arquitectura puede dividirse en componentes lógicos.

```text
carnada.py
├── CLI Parser
├── Profile Resolver
├── Charset Builder
├── Secure Password Generator
├── Password Analyzer
├── Entropy Calculator
└── Output Formatter
```

---

## 6. CLI Parser

El **CLI Parser** recibe y valida los argumentos enviados desde la shell.

Su responsabilidad es:

- recibir argumentos desde la terminal;
    
- validar opciones;
    
- identificar el modo de ejecución;
    
- enrutar hacia generación o análisis.
    

Ejemplos de entrada:

```bash
python3 carnada.py
python3 carnada.py --profile legacy
python3 carnada.py --count 5
python3 carnada.py --quiet
python3 carnada.py --json
python3 carnada.py check
```

Este componente se apoya en `argparse`, una librería estándar de Python para construir interfaces de línea de comandos.

---

## 7. Profile Resolver

El **Profile Resolver** carga el perfil seleccionado y define el comportamiento inicial de generación.

Su responsabilidad es:

- cargar el perfil seleccionado;
    
- definir longitud por defecto;
    
- definir grupos de caracteres activos;
    
- aplicar configuraciones del perfil;
    
- permitir ajustes manuales desde argumentos CLI.
    

Los perfiles principales son:

```text
strong
legacy
pin
hex
wifi
```

Cada perfil representa un caso de uso distinto. Esto permite que la herramienta no sea solo un generador genérico, sino una utilidad adaptable a escenarios concretos.

---

## 8. Charset Builder

El **Charset Builder** construye el conjunto final de caracteres disponibles para generar la contraseña.

El conjunto puede incluir:

- mayúsculas;
    
- minúsculas;
    
- números;
    
- símbolos;
    
- caracteres hexadecimales;
    
- exclusión de caracteres ambiguos.
    

Los caracteres ambiguos se excluyen por defecto para mejorar legibilidad en escenarios donde la contraseña debe copiarse o escribirse manualmente.

Ejemplos de caracteres ambiguos:

```text
O
0
I
1
l
o
```

Esta decisión mejora la usabilidad sin convertir la herramienta en algo menos seguro, siempre que se mantenga una longitud adecuada.

---

## 9. Secure Password Generator

El **Secure Password Generator** genera contraseñas usando aleatoriedad criptográficamente segura.

CARNADA utiliza:

```python
secrets
```

No utiliza:

```python
random
```

La diferencia es importante:

```text
random  → diseñado para simulaciones, pruebas y usos generales.
secrets → diseñado para generar valores impredecibles adecuados para contraseñas, tokens y secretos.
```

El generador debe garantizar que, si varios grupos de caracteres están activos, al menos un carácter de cada grupo aparezca en la contraseña final.

Ejemplo: si están activos estos grupos:

```text
mayúsculas
minúsculas
números
símbolos
```

la contraseña generada debe contener al menos:

```text
una mayúscula
una minúscula
un número
un símbolo
```

Después de seleccionar los caracteres obligatorios por grupo, el resto se completa con caracteres aleatorios del conjunto total. Finalmente, se mezcla el resultado antes de imprimirlo.

Esto evita generar contraseñas que accidentalmente no cumplan con los grupos seleccionados.

---

## 10. Password Analyzer

El **Password Analyzer** analiza una contraseña existente sin almacenarla.

Este componente se usa en el modo:

```bash
python3 carnada.py check
```

Evalúa:

- longitud;
    
- presencia de mayúsculas;
    
- presencia de minúsculas;
    
- presencia de números;
    
- presencia de símbolos;
    
- presencia de caracteres ambiguos;
    
- entropía aproximada;
    
- clasificación general.
    

Este análisis es local.

No consulta bases de datos de filtraciones, no envía la contraseña a servicios externos y no almacena el valor ingresado.

---

## 11. Entropy Calculator

El **Entropy Calculator** calcula una estimación aproximada de entropía en bits.

La fórmula conceptual es:

```text
entropía = longitud de la contraseña × log2(tamaño del conjunto de caracteres)
```

Ejemplo conceptual:

```text
longitud = 18
charset  = 82 caracteres

entropía aproximada = 18 × log2(82)
```

Esta métrica permite estimar la dificultad teórica de adivinar una contraseña generada aleatoriamente, pero no debe interpretarse como una garantía absoluta.

La clasificación aproximada puede representarse así:

```text
menos de 40 bits  → weak
40 a 69 bits      → moderate
70 a 99 bits      → strong
100 bits o más    → very strong
```

Esta clasificación sirve como referencia práctica, no como una certificación formal de seguridad.

---

## 12. Output Formatter

El **Output Formatter** presenta la información de salida según el modo seleccionado.

CARNADA contempla tres estilos de salida:

```text
normal
quiet
JSON
```

### Salida normal

Pensada para lectura humana en terminal.

Incluye:

- password;
    
- perfil;
    
- longitud;
    
- tamaño del charset;
    
- entropía aproximada;
    
- rating.
    

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

### Salida quiet

Imprime solo la contraseña.

Es útil para scripts o uso directo en shell.

Ejemplo:

```bash
python3 carnada.py --quiet
```

Salida:

```text
V7#kQm92@tLx8pRz
```

### Salida JSON

Entrega información estructurada para automatización o integración con otras herramientas.

Ejemplo:

```bash
python3 carnada.py --json
```

Salida:

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

La salida JSON no implica almacenamiento. Solo cambia el formato de presentación.

---

## 13. Modos de ejecución

CARNADA trabaja principalmente con dos modos:

```text
Generate Mode
Check Mode
```

---

## 14. Generate Mode

Es el modo principal y por defecto.

Flujo:

1. El usuario ejecuta CARNADA desde la shell.
    
2. El parser interpreta los argumentos.
    
3. Se carga el perfil seleccionado.
    
4. Se aplican ajustes del usuario.
    
5. Se construye el charset final.
    
6. Se genera la contraseña usando `secrets`.
    
7. Se calcula la entropía.
    
8. Se formatea la salida.
    
9. Se imprime en terminal.
    

Ejemplo:

```bash
python3 carnada.py --profile strong -l 24
```

Flujo interno esperado:

```text
profile   = strong
length    = 24
charset   = mayúsculas + minúsculas + números + símbolos
generator = secrets
output    = formato normal
```

---

## 15. Check Mode

Este modo analiza una contraseña existente.

Flujo:

1. El usuario ejecuta CARNADA con el subcomando `check`.
    
2. El parser detecta el modo `check`.
    
3. La contraseña se recibe como argumento o mediante prompt oculto.
    
4. El analizador evalúa su estructura.
    
5. Se calcula una entropía aproximada.
    
6. Se asigna un rating.
    
7. Se imprime el resultado.
    

Ejemplo recomendado:

```bash
python3 carnada.py check
```

Este método es preferible porque evita pasar la contraseña directamente como argumento en la shell.

Ejemplo menos recomendado:

```bash
python3 carnada.py check "Password123!"
```

Este método puede dejar la contraseña expuesta en el historial de comandos.

---

## 16. Diagrama lógico de flujo

Representación textual de la arquitectura:

```text
Usuario / Shell
       |
       v
Argumentos de línea de comandos
       |
       v
CLI Parser
       |
       +------------------------+
       |                        |
       v                        v
Generate Mode              Check Mode
       |                        |
       v                        v
Profile Resolver           Password Input
       |                        |
       v                        v
Charset Builder            Password Analyzer
       |                        |
       v                        v
Secure Password            Entropy Calculator
Generator
       |                        |
       v                        v
Entropy Calculator         Output Formatter
       |                        |
       +-----------+------------+
                   |
                   v
          Terminal Output
        normal / quiet / JSON
```

También existe una representación visual en el repositorio:

```text
docs/architecture/carnada-architecture.png
```

---

## 17. Seguridad en la arquitectura

La arquitectura de CARNADA evita manejar secretos de forma persistente.

La herramienta no incluye:

- almacenamiento de contraseñas;
    
- base de datos;
    
- vault local;
    
- cifrado de archivos;
    
- hashing de contraseñas;
    
- sincronización remota;
    
- telemetría;
    
- llamadas API;
    
- dependencias externas.
    

Esto reduce riesgos como:

- exposición accidental de archivos sensibles;
    
- mal manejo de claves maestras;
    
- falsa sensación de seguridad por cifrado innecesario;
    
- errores de implementación criptográfica;
    
- riesgos de cadena de suministro por paquetes externos;
    
- ruido operativo en una herramienta CLI simple.
    

La decisión de no almacenar secretos es uno de los elementos más importantes del diseño.

---

## 18. Modelo de dependencias

CARNADA usa únicamente librerías estándar de Python.

Módulos principales:

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

Esto permite que el usuario pueda clonar el repositorio y ejecutar la herramienta directamente, sin instalar paquetes externos.

Ejemplo:

```bash
git clone https://github.com/beathunterzero/carnada.git
cd carnada
python3 carnada.py
```

No requiere:

```bash
pip install -r requirements.txt
```

Esto hace que el proyecto sea más simple y reduce problemas de instalación.

---

## 19. Modelo de ejecución

CARNADA puede ejecutarse de dos formas principales.

Ejecución con Python:

```bash
python3 carnada.py
```

Ejecución directa en Linux, WSL o macOS:

```bash
chmod +x carnada.py
./carnada.py
```

Esto es posible porque el script incluye una línea shebang:

```python
#!/usr/bin/env python3
```

Esta línea permite que el sistema use el intérprete de Python disponible en el entorno.

---

## 20. Decisión de mantener un solo archivo

La arquitectura actual usa un solo archivo principal:

```text
carnada.py
```

Esto es válido porque el proyecto tiene un alcance reducido.

No existe necesidad inmediata de separar el código en múltiples módulos como:

```text
cli.py
profiles.py
generator.py
analyzer.py
formatter.py
```

Mantener un solo archivo tiene ventajas:

- facilita la ejecución directa;
    
- simplifica el uso desde shell;
    
- reduce estructura innecesaria;
    
- mejora portabilidad;
    
- facilita auditoría rápida;
    
- evita complejidad prematura.
    

Si el proyecto creciera mucho, una separación modular podría ser útil. Pero para el objetivo actual, `carnada.py` como único script es una decisión razonable.

---

## 21. Límites de la arquitectura

La arquitectura actual no busca resolver todos los problemas relacionados con contraseñas.

CARNADA no:

- valida contraseñas contra bases de datos de filtraciones;
    
- verifica políticas empresariales avanzadas;
    
- almacena credenciales;
    
- administra ciclos de vida de contraseñas;
    
- comparte secretos;
    
- protege contraseñas después de mostrarlas en pantalla;
    
- evita que el usuario copie una contraseña en un lugar inseguro;
    
- controla el historial de terminal, scrollback, logs externos o capturas de pantalla.
    

Estos límites deben estar claros porque forman parte del modelo de seguridad.

---

## 22. Valor arquitectónico del proyecto

El valor de la arquitectura está en su simplicidad controlada.

CARNADA no intenta ser una solución grande. Su fortaleza está en ser:

- local;
    
- simple;
    
- auditable;
    
- portable;
    
- sin dependencias;
    
- útil desde shell;
    
- clara en su alcance;
    
- cuidadosa con el manejo de secretos.
    

Desde una perspectiva defensiva, esto representa una buena práctica: reducir superficie de ataque y evitar funcionalidades innecesarias.

---

## 23. Conclusión operativa

La arquitectura de CARNADA está diseñada para cumplir un objetivo concreto:

> generar y evaluar contraseñas desde la terminal de forma local, sin almacenar secretos y sin depender de componentes externos.

El flujo interno es simple:

```text
entrada por shell
parser CLI
modo generate o check
procesamiento local
cálculo de entropía
formato de salida
resultado en terminal
```

La herramienta mantiene una arquitectura minimalista porque su alcance también es minimalista.

Esta decisión mejora la seguridad, facilita la auditoría y hace que el proyecto sea más profesional como herramienta CLI pequeña de ciberseguridad defensiva.

---

### Documentación relacionada

[[01 - Índice del Proyecto CARNADA]]