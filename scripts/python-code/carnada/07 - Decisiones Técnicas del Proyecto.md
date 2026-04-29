## 1. Identificación

**Proyecto:** CARNADA  
**Tipo de nota:** Decisiones técnicas  
**Área:** Python / CLI / Ciberseguridad defensiva  
**Estado:** Documentación base  
**Autor:** Rhodyn Ildefonso (@beathunterzero)  
**Relación con:** Proyecto personal CARNADA  
**Enfoque:** Registro de decisiones tomadas durante el rediseño técnico de la herramienta.

---

## 2. Propósito

Esta nota documenta las principales decisiones técnicas tomadas durante el rediseño de **CARNADA**.

El objetivo es dejar registro de por qué la herramienta evolucionó desde un script con múltiples funciones criptográficas hacia una CLI más simple, local, auditable y enfocada en generación y evaluación de contraseñas.

Documentar estas decisiones permite entender el criterio detrás del proyecto y evita interpretar ciertas ausencias como limitaciones accidentales.

En CARNADA, varias funciones fueron eliminadas de forma intencional para reducir riesgo y mejorar claridad.

---

## 3. Contexto del rediseño

La versión inicial de CARNADA incluía varias capacidades:

```text
generación de contraseñas
hashing con SHA-256 y SHA-512
derivación de clave desde passphrase
cifrado con AES-256-GCM
cifrado con ChaCha20-Poly1305
guardado de resultados en archivo
impresión de información criptográfica en pantalla
```

Aunque estas funciones eran útiles desde un punto de vista de laboratorio, generaban varios problemas para una herramienta CLI profesional:

- mezclaban demasiadas responsabilidades;
    
- aumentaban superficie de riesgo;
    
- podían inducir malas prácticas;
    
- complicaban el mensaje del proyecto;
    
- generaban salida excesiva en consola;
    
- requerían dependencias externas;
    
- obligaban a manejar claves maestras;
    
- podían guardar secretos accidentalmente.
    

El rediseño buscó convertir CARNADA en una herramienta más clara:

```text
CLI local para generar y evaluar contraseñas
```

No una bóveda, no un gestor de credenciales y no un laboratorio criptográfico.

---

## 4. Decisión 1: eliminar el cifrado

### Decisión

Se eliminaron las funciones de cifrado con AES-GCM y ChaCha20-Poly1305.

### Motivo

El cifrado solo aporta valor real cuando existe:

- almacenamiento de secretos;
    
- transmisión de información sensible;
    
- persistencia de datos;
    
- necesidad de proteger información en reposo o en tránsito.
    

CARNADA no almacena contraseñas ni transmite datos. Por tanto, cifrar una contraseña generada para luego imprimir datos cifrados no aporta valor operativo al objetivo actual.

### Riesgo evitado

Eliminar el cifrado evita:

- manejo de claves maestras;
    
- errores en derivación de claves;
    
- almacenamiento accidental de passphrases;
    
- falsa sensación de seguridad;
    
- dependencia de librerías externas;
    
- complejidad innecesaria.
    

### Resultado

CARNADA quedó más simple, más segura en su alcance y más fácil de auditar.

---

## 5. Decisión 2: eliminar hashing SHA-256 y SHA-512

### Decisión

Se eliminaron las funciones que generaban hashes SHA-256 y SHA-512 de la contraseña.

### Motivo

SHA-256 y SHA-512 son funciones hash rápidas. No son adecuadas por sí solas para almacenar contraseñas.

Para almacenamiento seguro de contraseñas se requieren mecanismos diseñados para ese fin, como:

```text
Argon2id
bcrypt
scrypt
PBKDF2 con parámetros adecuados
```

Como CARNADA no es un sistema de autenticación ni almacenamiento, generar hashes de contraseñas podía causar confusión.

### Riesgo evitado

Eliminar esta función evita transmitir una idea incorrecta:

```text
hash SHA-256 de contraseña = almacenamiento seguro
```

Eso no es una práctica recomendada.

### Resultado

La herramienta mantiene su foco en generación y evaluación local, sin entrar en almacenamiento de contraseñas.

---

## 6. Decisión 3: eliminar guardado en archivo

### Decisión

Se eliminó la función de guardado automático en archivos como:

```text
contrasenas_generadas.txt
```

### Motivo

Guardar contraseñas generadas introduce riesgo inmediato.

Un archivo con contraseñas puede:

- quedar olvidado en el sistema;
    
- subirse accidentalmente a GitHub;
    
- ser leído por otros usuarios;
    
- exponerse en backups;
    
- quedar en máquinas de laboratorio;
    
- generar una falsa sensación de control.
    

### Riesgo evitado

Eliminar el guardado reduce la posibilidad de exposición accidental de secretos en texto claro.

### Resultado

CARNADA imprime la información en terminal y finaliza. No persiste secretos por diseño.

---

## 7. Decisión 4: eliminar clave maestra

### Decisión

Se eliminó la solicitud de clave maestra.

### Motivo

La clave maestra solo era necesaria para derivar claves de cifrado. Al eliminar el cifrado y el almacenamiento, ya no existe razón para solicitarla.

### Riesgo evitado

Eliminar la clave maestra evita:

- pedir secretos innecesarios;
    
- imprimirlos por accidente;
    
- guardarlos por error;
    
- generar dependencia de una passphrase;
    
- confundir la herramienta con una bóveda de contraseñas.
    

### Resultado

La experiencia de uso es más directa:

```bash
python3 carnada.py
```

La herramienta genera una contraseña sin solicitar credenciales adicionales.

---

## 8. Decisión 5: eliminar dependencias externas

### Decisión

Se eliminaron dependencias como:

```text
cryptography
cffi
pycparser
```

### Motivo

Estas dependencias eran necesarias para la versión con cifrado. En el nuevo diseño, CARNADA usa únicamente librerías estándar de Python.

### Riesgo evitado

Eliminar dependencias externas reduce:

- fricción de instalación;
    
- problemas de versiones;
    
- riesgo de cadena de suministro;
    
- necesidad de mantener `requirements.txt`;
    
- incompatibilidades entre sistemas;
    
- superficie de auditoría.
    

### Resultado

El usuario puede clonar el proyecto y ejecutarlo directamente:

```bash
git clone https://github.com/beathunterzero/carnada.git
cd carnada
python3 carnada.py
```

No necesita:

```bash
pip install -r requirements.txt
```

---

## 9. Decisión 6: mantener arquitectura de un solo archivo

### Decisión

Se decidió mantener el script principal como un único archivo:

```text
carnada.py
```

### Motivo

El proyecto tiene un alcance pequeño y claro. Separarlo prematuramente en múltiples módulos podría agregar complejidad innecesaria.

Una estructura como esta no es necesaria todavía:

```text
cli.py
profiles.py
generator.py
analyzer.py
formatter.py
```

### Ventajas

Mantener un solo archivo permite:

- ejecución directa;
    
- menor fricción para el usuario;
    
- mejor portabilidad;
    
- revisión rápida del código;
    
- estructura simple del repositorio;
    
- uso natural desde shell.
    

### Resultado

La arquitectura sigue siendo simple y adecuada para una CLI pequeña.

---

## 10. Decisión 7: usar `secrets` en vez de `random`

### Decisión

Se usa el módulo estándar:

```python
secrets
```

en lugar de:

```python
random
```

### Motivo

`random` no está diseñado para generar secretos. Su salida puede ser adecuada para simulaciones, juegos o pruebas, pero no para contraseñas.

`secrets` está diseñado para generar valores criptográficamente seguros.

### Resultado

La generación de contraseñas se apoya en una fuente de aleatoriedad apropiada para el caso de uso.

---

## 11. Decisión 8: agregar perfiles de generación

### Decisión

Se agregaron perfiles como:

```text
strong
legacy
pin
hex
wifi
```

### Motivo

No todos los sistemas aceptan las mismas contraseñas.

Algunos sistemas aceptan símbolos, otros no. Algunos requieren solo números. Otros escenarios necesitan valores hexadecimales o contraseñas fáciles de escribir en dispositivos.

### Valor agregado

Los perfiles hacen que CARNADA sea más útil en escenarios reales.

Ejemplos:

```bash
python3 carnada.py --profile strong
python3 carnada.py --profile legacy
python3 carnada.py --profile wifi
python3 carnada.py --profile pin -l 8
python3 carnada.py --profile hex
```

### Resultado

CARNADA deja de ser un generador genérico y se convierte en una herramienta adaptable a casos de uso concretos.

---

## 12. Decisión 9: excluir caracteres ambiguos por defecto

### Decisión

Se excluyen caracteres visualmente ambiguos por defecto.

Ejemplos:

```text
O
0
I
1
l
o
```

### Motivo

En muchos escenarios, las contraseñas deben copiarse manualmente o escribirse en dispositivos con pantallas o teclados limitados.

Evitar caracteres ambiguos reduce errores operativos.

### Ejemplo de problema evitado

Confundir:

```text
O con 0
I con 1
l con 1
o con 0
```

### Resultado

La herramienta mejora la usabilidad sin perder su enfoque de seguridad, siempre que se mantengan longitudes adecuadas.

---

## 13. Decisión 10: agregar modo `quiet`

### Decisión

Se agregó una salida silenciosa:

```bash
python3 carnada.py --quiet
```

o:

```bash
python3 carnada.py -q
```

### Motivo

Una herramienta CLI debe poder integrarse con scripts.

El modo `quiet` imprime únicamente la contraseña, sin encabezados ni metadatos.

### Ejemplo

```bash
PASSWORD=$(python3 carnada.py --quiet)
```

### Riesgo

Este modo puede exponer contraseñas si se usa mal en scripts, logs o variables de entorno.

### Resultado

El modo `quiet` agrega utilidad real para shell scripting, pero debe usarse con criterio.

---

## 14. Decisión 11: agregar salida JSON

### Decisión

Se agregó salida estructurada en JSON:

```bash
python3 carnada.py --json
```

### Motivo

La salida JSON permite automatización e integración con otras herramientas.

### Ejemplo

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

### Riesgo

Si el usuario redirige la salida a un archivo, puede guardar una contraseña en texto claro.

Ejemplo riesgoso:

```bash
python3 carnada.py --json > password.json
```

### Resultado

JSON aporta valor para automatización, pero se documentó su riesgo.

---

## 15. Decisión 12: agregar modo `check`

### Decisión

Se agregó el modo:

```bash
python3 carnada.py check
```

### Motivo

El valor de la herramienta aumenta si no solo genera contraseñas, sino que también permite evaluar contraseñas existentes de forma local.

### Qué evalúa

El modo `check` revisa:

- longitud;
    
- mayúsculas;
    
- minúsculas;
    
- números;
    
- símbolos;
    
- caracteres ambiguos;
    
- entropía aproximada;
    
- rating.
    

### Seguridad

Cuando se ejecuta sin argumento, usa entrada oculta:

```bash
python3 carnada.py check
```

Evita pasar contraseñas reales como argumento:

```bash
python3 carnada.py check "MiPasswordReal123!"
```

### Resultado

CARNADA se convierte en una herramienta de generación y análisis básico, manteniendo todo local.

---

## 16. Decisión 13: no convertir CARNADA en password manager

### Decisión

CARNADA no será presentada como gestor de contraseñas.

### Motivo

Un password manager requiere resolver problemas más amplios:

- almacenamiento seguro;
    
- cifrado robusto;
    
- manejo de claves;
    
- recuperación;
    
- sincronización;
    
- protección contra pérdida;
    
- bloqueo automático;
    
- diseño de vault;
    
- threat model específico;
    
- UX más compleja.
    

CARNADA no busca resolver esos problemas.

### Resultado

La herramienta mantiene un alcance claro:

```text
generar y evaluar contraseñas localmente desde la shell
```

---

## 17. Decisión 14: documentar límites de seguridad

### Decisión

Se documentaron explícitamente los límites del proyecto.

### Motivo

Una herramienta de seguridad debe evitar promesas excesivas.

CARNADA no garantiza seguridad absoluta. Solo genera y evalúa contraseñas bajo condiciones locales.

### Límites documentados

CARNADA no protege contra:

- terminal comprometida;
    
- keyloggers;
    
- malware local;
    
- historial de shell;
    
- exposición visual;
    
- mal uso de `--quiet`;
    
- redirección a archivos;
    
- reutilización de contraseñas;
    
- almacenamiento inseguro fuera de la herramienta.
    

### Resultado

El proyecto comunica su alcance con honestidad técnica.

---

## 18. Decisión 15: mantener documentación separada

### Decisión

Se decidió separar la documentación en:

```text
README.md
docs/usage.md
docs/security-notes.md
docs/architecture/overview.md
examples/usage-examples.md
```

### Motivo

El README debe ser claro y no demasiado extenso.

La documentación más detallada vive en `docs/` y `examples/`.

### Resultado

El repositorio queda más ordenado:

```text
CARNADA/
├── carnada.py
├── README.md
├── LICENSE
├── .gitignore
├── docs/
│   ├── usage.md
│   ├── security-notes.md
│   └── architecture/
│       ├── overview.md
│       └── carnada-architecture.png
└── examples/
    └── usage-examples.md
```

---

## 19. Decisión 16: eliminar `requirements.txt`

### Decisión

Se eliminó `requirements.txt`.

### Motivo

El nuevo diseño no usa dependencias externas.

Mantener un `requirements.txt` vacío o con comentarios podía generar confusión.

### Resultado

La instalación queda más directa:

```bash
git clone https://github.com/beathunterzero/carnada.git
cd carnada
python3 carnada.py
```

---

## 20. Decisión 17: mantener `.venv` fuera del repositorio

### Decisión

El entorno virtual local no forma parte del repositorio.

### Motivo

`.venv` es una herramienta del entorno de desarrollo, no un requisito de uso.

CARNADA puede ejecutarse sin crear entorno virtual porque usa solo librerías estándar.

### `.gitignore`

Se mantiene:

```gitignore
.venv/
__pycache__/
*.pyc
.env
.DS_Store
*.log
```

### Resultado

El usuario final no necesita configurar un entorno virtual para usar la herramienta.

---

## 21. Decisión 18: no incluir Roadmap en el README

### Decisión

Se eliminó el Roadmap del README.

### Motivo

El proyecto está pensado como una herramienta pequeña y funcional, no como una plataforma en expansión.

Incluir un roadmap podía generar expectativas o compromisos futuros que no son prioridad, especialmente porque el foco principal del autor está en Threat Hunting.

### Resultado

El README queda más sobrio y presenta CARNADA como una herramienta compacta, usable y terminada en su alcance actual.

---

## 22. Decisión 19: documentar arquitectura visual

### Decisión

Se agregó un diagrama de arquitectura:

```text
docs/architecture/carnada-architecture.png
```

### Motivo

Un diagrama ayuda a explicar el flujo de la herramienta de forma rápida:

```text
User / Shell
CLI Parser
Generate Mode
Check Mode
Output Formatter
Terminal Output
```

### Resultado

El proyecto se ve más profesional y más fácil de entender para alguien que revisa el repositorio.

---

## 23. Resultado final del rediseño

El proyecto pasó de:

```text
generador + hashing + cifrado + clave maestra + guardado + salida extensa
```

a:

```text
CLI local para generar y evaluar contraseñas
```

La nueva versión tiene:

- propósito claro;
    
- salida limpia;
    
- perfiles útiles;
    
- análisis local;
    
- sin dependencias externas;
    
- sin almacenamiento;
    
- sin red;
    
- documentación técnica;
    
- arquitectura visual;
    
- límites explícitos.
    

---

## 24. Valor técnico de las decisiones

El valor técnico del proyecto no está en agregar muchas funciones, sino en haber eliminado correctamente funciones innecesarias.

Desde una perspectiva de ciberseguridad defensiva, esto es importante.

Una herramienta segura no siempre es la que más funciones tiene, sino la que reduce riesgos y hace bien su tarea principal.

CARNADA demuestra:

- criterio de reducción de superficie de ataque;
    
- selección correcta de librerías estándar;
    
- documentación de límites;
    
- uso responsable de generación aleatoria;
    
- diseño CLI simple;
    
- separación entre utilidad real y laboratorio criptográfico.
    

---

## 25. Conclusión operativa

Las decisiones técnicas de CARNADA responden a una idea central:

```text
hacer una herramienta pequeña, local, auditable y útil desde la shell
```

La eliminación de cifrado, hashing, guardado y dependencias externas no representa una pérdida de valor. Representa una mejora de diseño.

El proyecto quedó más claro, más seguro en su alcance y más profesional.

CARNADA ahora cumple una función específica:

```text
generar y evaluar contraseñas localmente sin almacenar secretos
```

---

### Documentación relacionada

[[01 - Índice del Proyecto CARNADA]]