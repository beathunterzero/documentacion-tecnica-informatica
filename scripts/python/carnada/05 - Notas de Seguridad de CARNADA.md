## 1. Identificación

**Proyecto:** CARNADA  
**Tipo de nota:** Notas de seguridad  
**Área:** Python / CLI / Ciberseguridad defensiva  
**Estado:** Documentación base  
**Autor:** Rhodyn Ildefonso (@beathunterzero)  
**Relación con:** Proyecto personal CARNADA  
**Enfoque:** Decisiones de seguridad, límites operativos y buenas prácticas de uso.

---

## 2. Propósito

Esta nota documenta las decisiones de seguridad aplicadas en **CARNADA**.

El objetivo es explicar por qué la herramienta no almacena contraseñas, no cifra archivos, no genera hashes, no usa red y no depende de paquetes externos.

CARNADA fue rediseñada para tener una superficie de riesgo reducida. Su propósito no es manejar secretos de forma persistente, sino generar y evaluar contraseñas localmente desde la terminal.

---

## 3. Modelo de seguridad

El modelo de seguridad de CARNADA es minimalista.

La herramienta sigue estos principios:

```text
generar o evaluar contraseñas localmente
evitar almacenamiento persistente de secretos
evitar comunicación de red
evitar dependencias externas
mostrar salida limpia en terminal
finalizar después de la ejecución
```

Este modelo reduce riesgos operativos y mantiene el comportamiento de la herramienta predecible.

CARNADA no intenta proteger una contraseña después de haberla mostrado en pantalla. Esa responsabilidad pasa al usuario y al entorno donde se utilice.

---

## 4. Ejecución local-first

CARNADA funciona bajo un enfoque **local-first**.

Esto significa que todas las operaciones se ejecutan en la máquina del usuario.

La herramienta no:

- se conecta a servicios externos;
    
- envía contraseñas a internet;
    
- realiza telemetría;
    
- sincroniza datos en la nube;
    
- consulta APIs;
    
- escribe contraseñas en disco por defecto.
    

Esto la hace adecuada para flujos de trabajo locales, laboratorios de ciberseguridad y uso desde shell.

---

## 5. Sin almacenamiento de contraseñas

CARNADA no guarda contraseñas generadas ni contraseñas analizadas.

Esta decisión es intencional.

La herramienta no es:

- un gestor de contraseñas;
    
- una bóveda de secretos;
    
- una base de datos de credenciales;
    
- un sistema de recuperación de contraseñas;
    
- una herramienta de sincronización.
    

Las contraseñas existen solo durante la ejecución del programa y se imprimen en terminal cuando el usuario lo solicita.

Para credenciales permanentes, se debe usar un gestor de contraseñas dedicado.

---

## 6. Por qué no incluye cifrado

Una versión inicial del proyecto incluía funciones de cifrado con algoritmos como AES-GCM y ChaCha20-Poly1305.

En el rediseño actual, esas funciones fueron eliminadas.

Motivo:

```text
el cifrado solo aporta valor cuando existe almacenamiento, transmisión o persistencia de secretos
```

Como CARNADA no guarda secretos ni transporta datos, agregar cifrado aumentaría la complejidad sin aportar una protección real al objetivo principal.

Además, añadir cifrado implica nuevos riesgos:

- manejo incorrecto de claves;
    
- almacenamiento inseguro de claves maestras;
    
- falsa sensación de seguridad;
    
- errores de implementación criptográfica;
    
- archivos cifrados mal protegidos;
    
- necesidad de diseñar un threat model más amplio.
    

Por eso, la decisión de eliminar el cifrado mejora la claridad del proyecto.

---

## 7. Por qué no genera hashes de contraseñas

CARNADA tampoco genera hashes SHA-256 o SHA-512 de contraseñas.

Esta decisión también es intencional.

Los hashes rápidos como SHA-256 y SHA-512 no son adecuados por sí solos para almacenar contraseñas.

Para almacenamiento seguro de contraseñas se requieren mecanismos específicos como:

```text
Argon2id
bcrypt
scrypt
PBKDF2 con parámetros adecuados
```

Como CARNADA no es una herramienta de almacenamiento ni autenticación, generar hashes de contraseñas queda fuera de alcance.

Eliminar esta función evita confusión y malas interpretaciones sobre cómo debe almacenarse una contraseña de forma segura.

---

## 8. Fuente de aleatoriedad

CARNADA utiliza el módulo estándar:

```python
secrets
```

Este módulo está diseñado para generar valores criptográficamente seguros, como:

- contraseñas;
    
- tokens;
    
- claves temporales;
    
- valores impredecibles.
    

CARNADA no utiliza:

```python
random
```

La razón es simple:

```text
random  → adecuado para simulaciones, pruebas y usos generales
secrets → adecuado para generación de secretos
```

Para una herramienta de generación de contraseñas, `secrets` es la opción correcta dentro de la librería estándar de Python.

---

## 9. Diseño del conjunto de caracteres

CARNADA trabaja con grupos de caracteres configurables:

```text
mayúsculas
minúsculas
números
símbolos
```

También contempla perfiles especiales como:

```text
pin
hex
wifi
```

Por defecto, se evitan algunos caracteres visualmente ambiguos:

```text
O
0
I
1
l
o
```

Esto mejora la usabilidad cuando una contraseña debe:

- copiarse manualmente;
    
- escribirse en otro dispositivo;
    
- leerse desde una pantalla;
    
- ingresarse en una interfaz limitada;
    
- compartirse temporalmente en un entorno controlado.
    

La exclusión de caracteres ambiguos no reemplaza una buena longitud ni un almacenamiento adecuado. Es una decisión de usabilidad.

---

## 10. Composición mínima de contraseña

Cuando se genera una contraseña con varios grupos activos, CARNADA debe garantizar que al menos un carácter de cada grupo aparezca en el resultado final.

Ejemplo: si están activos estos grupos:

```text
mayúsculas
minúsculas
números
símbolos
```

La contraseña generada debe contener al menos:

```text
una mayúscula
una minúscula
un número
un símbolo
```

Después de seleccionar esos caracteres obligatorios, el resto se completa con selección aleatoria del conjunto total.

Finalmente, la contraseña se mezcla antes de imprimirse.

Esto evita casos donde una contraseña generada aleatoriamente no cumple con las categorías seleccionadas.

---

## 11. Entropía aproximada

CARNADA muestra una estimación aproximada de entropía.

La fórmula conceptual es:

```text
entropía = longitud de la contraseña × log2(tamaño del conjunto de caracteres)
```

Ejemplo:

```text
longitud = 18
charset = 82 caracteres

entropía aproximada = 18 × log2(82)
```

Esta métrica ayuda a comparar contraseñas generadas aleatoriamente, pero no debe interpretarse como garantía absoluta de seguridad.

La seguridad real también depende de:

- si la contraseña se reutiliza;
    
- dónde se almacena;
    
- cómo se transmite;
    
- si queda en logs;
    
- si aparece en historial de terminal;
    
- si el sistema tiene rate limiting;
    
- si existe MFA;
    
- si la contraseña ya fue filtrada antes;
    
- si el entorno está comprometido.
    

---

## 12. Clasificación de fortaleza

CARNADA puede clasificar una contraseña usando umbrales de entropía aproximada.

Modelo utilizado:

```text
menos de 40 bits  → weak
40 a 69 bits      → moderate
70 a 99 bits      → strong
100 bits o más    → very strong
```

Esta clasificación sirve como guía rápida, no como análisis formal.

Una contraseña clasificada como `very strong` puede seguir siendo insegura si:

- se reutiliza;
    
- se almacena en texto plano;
    
- se comparte por canales inseguros;
    
- se pega en sistemas comprometidos;
    
- se registra en logs;
    
- se expone en capturas de pantalla;
    
- se guarda en historial de terminal;
    
- se usa sin MFA en un servicio crítico.
    

---

## 13. Riesgo del historial de shell

Pasar contraseñas reales directamente como argumento puede ser riesgoso.

Ejemplo no recomendado para contraseñas reales:

```bash
python3 carnada.py check "MiPasswordReal123!"
```

Este comando puede quedar registrado en el historial de shell.

También podría ser visible temporalmente mediante inspección de procesos, dependiendo del sistema operativo y la shell.

Uso recomendado:

```bash
python3 carnada.py check
```

En este modo, la herramienta solicita la contraseña mediante entrada oculta.

---

## 14. Riesgo de salida en terminal

CARNADA imprime contraseñas generadas en la terminal.

Esto es normal para una herramienta CLI, pero el usuario debe considerar que la salida puede quedar visible en:

- scrollback del terminal;
    
- sesiones compartidas;
    
- grabaciones de pantalla;
    
- logs de multiplexores;
    
- herramientas de monitoreo;
    
- capturas de pantalla;
    
- historial visual del entorno.
    

CARNADA no puede controlar lo que ocurre después de imprimir el resultado.

Buenas prácticas:

- usar la herramienta en una terminal confiable;
    
- evitar sesiones compartidas;
    
- limpiar la pantalla si es necesario;
    
- no generar credenciales reales durante grabaciones;
    
- no ejecutar la herramienta en sistemas comprometidos.
    

---

## 15. Riesgo del modo JSON

La salida JSON es útil para automatización.

Ejemplo:

```bash
python3 carnada.py --json
```

Pero si se redirige a un archivo, el secreto queda guardado en texto plano.

Ejemplo riesgoso con credenciales reales:

```bash
python3 carnada.py --json > password.json
```

Ese archivo contendrá la contraseña generada.

Si se usa redirección, el usuario debe proteger el archivo resultante o eliminarlo de forma segura.

---

## 16. Riesgo del modo quiet

El modo `quiet` imprime solo la contraseña.

Ejemplo:

```bash
python3 carnada.py --quiet
```

Esto es útil para shell scripting, pero también puede facilitar exposición accidental.

Ejemplo:

```bash
PASSWORD=$(python3 carnada.py --quiet)
```

Riesgos:

- la variable puede imprimirse accidentalmente;
    
- puede quedar en logs de scripts;
    
- puede exponerse en debugging;
    
- puede pasarse a otros comandos inseguros;
    
- puede quedar disponible en el entorno durante la sesión.
    

El modo quiet debe usarse con criterio.

---

## 17. Modelo de dependencias

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

Esto reduce:

- fricción de instalación;
    
- conflictos de versiones;
    
- riesgo de cadena de suministro;
    
- necesidad de mantener paquetes externos;
    
- superficie de ataque por dependencias innecesarias.
    

No se requiere:

```bash
pip install -r requirements.txt
```

---

## 18. Comportamiento en el sistema de archivos

CARNADA no crea archivos con contraseñas por defecto.

No crea:

- bases de datos;
    
- bóvedas;
    
- logs de secretos;
    
- archivos cifrados;
    
- configuraciones con claves;
    
- archivos de salida automáticos.
    

Si el usuario redirige la salida, eso ya pertenece al comportamiento de la shell.

Ejemplo:

```bash
python3 carnada.py --quiet > password.txt
```

Este comando crearía un archivo con la contraseña en texto plano. No es una acción automática de CARNADA, sino una redirección solicitada por el usuario.

---

## 19. Fuera de alcance

Las siguientes funciones están fuera del alcance de CARNADA:

- gestión de bóvedas;
    
- almacenamiento de credenciales;
    
- sincronización en nube;
    
- recuperación de contraseñas;
    
- compartición de secretos;
    
- cifrado de archivos;
    
- hashing para sistemas de autenticación;
    
- validación contra bases de datos de filtraciones;
    
- enforcement de políticas empresariales;
    
- rotación automática de contraseñas;
    
- monitoreo de secretos expuestos.
    

Estas funciones requieren controles adicionales, threat model propio y una arquitectura más compleja.

---

## 20. Buenas prácticas de uso

Uso recomendado:

```bash
python3 carnada.py
python3 carnada.py --profile strong
python3 carnada.py --profile legacy
python3 carnada.py --profile wifi
python3 carnada.py --count 5
python3 carnada.py --quiet
python3 carnada.py check
```

Para analizar contraseñas reales, usar:

```bash
python3 carnada.py check
```

Evitar:

```bash
python3 carnada.py check "MiPasswordReal123!"
```

Evitar guardar salidas reales en archivos planos:

```bash
python3 carnada.py --json > password.json
python3 carnada.py --quiet > password.txt
```

Usar un gestor de contraseñas para credenciales permanentes.

---

## 21. Buenas prácticas de desarrollo

Al mantener este proyecto, se recomienda:

- no añadir almacenamiento de secretos sin rediseñar el modelo de seguridad;
    
- no añadir red o APIs externas sin justificar el caso de uso;
    
- no registrar contraseñas en logs;
    
- no imprimir valores ingresados con entrada oculta;
    
- no añadir dependencias innecesarias;
    
- no volver a introducir hashing rápido de contraseñas;
    
- no presentar la herramienta como password manager;
    
- mantener clara la frontera entre generación, análisis y almacenamiento.
    

Entradas recomendadas en `.gitignore`:

```gitignore
.venv/
__pycache__/
*.pyc
.env
.DS_Store
*.log
```

---

## 22. Riesgo residual

Aunque CARNADA reduzca riesgos, no elimina todos los riesgos posibles.

Riesgos residuales:

- terminal comprometida;
    
- sistema operativo comprometido;
    
- keylogger;
    
- clipboard inseguro;
    
- usuario copiando la contraseña a un lugar inseguro;
    
- exposición visual;
    
- reutilización de contraseñas;
    
- almacenamiento manual en archivos planos;
    
- servicios sin MFA;
    
- servicios sin protección contra fuerza bruta.
    

CARNADA ayuda a generar contraseñas fuertes, pero no puede compensar un entorno inseguro.

---

## 23. Conclusión operativa

La seguridad de CARNADA se basa en su simplicidad.

La herramienta evita funciones innecesarias para reducir superficie de riesgo:

```text
no almacenamiento
no cifrado
no hashing
no red
no dependencias externas
```

Su objetivo es generar y evaluar contraseñas localmente, mostrar información útil y finalizar.

Desde una perspectiva defensiva, el diseño es correcto porque evita manejar secretos más allá del tiempo mínimo necesario para cumplir su función.

---

### Documentación relacionada

[[01 - Índice del Proyecto CARNADA]]