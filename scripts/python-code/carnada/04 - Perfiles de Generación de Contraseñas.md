## 1. Identificación

**Proyecto:** CARNADA  
**Tipo de nota:** Documentación técnica de perfiles  
**Área:** Python / CLI / Ciberseguridad defensiva  
**Estado:** Documentación base  
**Autor:** Rhodyn Ildefonso (@beathunterzero)  
**Relación con:** Proyecto personal CARNADA  
**Enfoque:** Definición, propósito y uso operativo de los perfiles de generación.

---

## 2. Propósito

Esta nota documenta los perfiles de generación disponibles en **CARNADA**.

El objetivo es explicar qué hace cada perfil, qué conjunto de caracteres utiliza, para qué caso de uso está pensado y qué consideraciones de seguridad deben tenerse en cuenta.

Los perfiles permiten que CARNADA no sea solo un generador genérico de contraseñas, sino una herramienta adaptable a distintos contextos operativos.

---

## 3. Concepto de perfil

Un perfil define una configuración predeterminada para generar contraseñas.

Cada perfil puede establecer:

- longitud por defecto;
    
- grupos de caracteres activos;
    
- compatibilidad con sistemas restrictivos;
    
- uso o exclusión de símbolos;
    
- uso o exclusión de caracteres ambiguos;
    
- propósito operativo.
    

Ejemplo conceptual:

```text
perfil = strong
longitud = 18
grupos activos = mayúsculas + minúsculas + números + símbolos
caracteres ambiguos = excluidos por defecto
```

Esto permite que el usuario ejecute un comando simple:

```bash
python3 carnada.py --profile strong
```

sin tener que definir manualmente cada grupo de caracteres.

---

## 4. Perfiles disponibles

CARNADA contempla los siguientes perfiles principales:

```text
strong
legacy
pin
hex
wifi
```

Cada uno responde a un escenario distinto.

---

## 5. Perfil `strong`

El perfil `strong` es el perfil principal y recomendado para uso general.

Es el perfil por defecto cuando el usuario ejecuta:

```bash
python3 carnada.py
```

o:

```bash
python3 carnada.py --profile strong
```

### Características

```text
Longitud por defecto: 18 caracteres
Incluye mayúsculas: sí
Incluye minúsculas: sí
Incluye números: sí
Incluye símbolos: sí
Evita caracteres ambiguos: sí
```

### Grupos de caracteres

```text
A-Z
a-z
0-9
!@#$%^&*()-_=+[]{};:,.<>/?
```

Con la exclusión por defecto de caracteres ambiguos como:

```text
O
0
I
1
l
o
```

### Caso de uso

Este perfil está pensado para:

- contraseñas de uso general;
    
- sistemas modernos;
    
- cuentas personales o técnicas;
    
- credenciales temporales;
    
- laboratorios;
    
- pruebas de seguridad;
    
- cualquier escenario donde se acepten símbolos.
    

### Ventaja operativa

El perfil `strong` ofrece una buena relación entre:

- longitud;
    
- variedad de caracteres;
    
- entropía aproximada;
    
- compatibilidad razonable.
    

### Consideración

Algunos sistemas pueden rechazar ciertos símbolos. Si ocurre eso, se recomienda usar el perfil:

```text
legacy
```

o ejecutar:

```bash
python3 carnada.py --no-symbols
```

---

## 6. Perfil `legacy`

El perfil `legacy` está diseñado para sistemas antiguos, restrictivos o mal implementados que no aceptan símbolos especiales.

Uso:

```bash
python3 carnada.py --profile legacy
```

### Características

```text
Longitud por defecto: 16 caracteres
Incluye mayúsculas: sí
Incluye minúsculas: sí
Incluye números: sí
Incluye símbolos: no
Evita caracteres ambiguos: sí
```

### Grupos de caracteres

```text
A-Z
a-z
0-9
```

Con exclusión de caracteres ambiguos por defecto.

### Caso de uso

Este perfil es útil para:

- sistemas legacy;
    
- paneles antiguos;
    
- appliances;
    
- consolas administrativas restrictivas;
    
- formularios que rechazan símbolos;
    
- sistemas que tienen validaciones débiles o problemáticas.
    

### Ventaja operativa

Reduce errores de compatibilidad.

Una contraseña sin símbolos suele ser más aceptada por sistemas antiguos o interfaces problemáticas.

### Consideración de seguridad

Al eliminar símbolos, el conjunto de caracteres disponible se reduce. Para compensarlo, se recomienda usar longitudes mayores cuando sea posible.

Ejemplo:

```bash
python3 carnada.py --profile legacy -l 24
```

---

## 7. Perfil `pin`

El perfil `pin` genera códigos numéricos.

Uso:

```bash
python3 carnada.py --profile pin
```

### Características

```text
Longitud por defecto: 6 caracteres
Incluye mayúsculas: no
Incluye minúsculas: no
Incluye números: sí
Incluye símbolos: no
Evita caracteres ambiguos: no aplica de la misma forma
```

### Grupo de caracteres

```text
0-9
```

### Caso de uso

Este perfil es útil para:

- PINs temporales;
    
- códigos numéricos;
    
- pruebas de formularios;
    
- escenarios donde solo se aceptan dígitos;
    
- laboratorios de autenticación básica.
    

### Ejemplo con longitud personalizada

```bash
python3 carnada.py --profile pin -l 8
```

### Consideración de seguridad

Un PIN tiene una entropía mucho menor que una contraseña completa.

Ejemplo aproximado:

```text
PIN de 6 dígitos  → 1,000,000 combinaciones posibles
PIN de 8 dígitos  → 100,000,000 combinaciones posibles
```

Esto puede ser insuficiente si no existen controles adicionales como:

- rate limiting;
    
- bloqueo por intentos fallidos;
    
- MFA;
    
- monitoreo;
    
- controles anti-fuerza bruta.
    

El perfil `pin` debe usarse solo cuando el sistema exige valores numéricos.

---

## 8. Perfil `hex`

El perfil `hex` genera valores hexadecimales.

Uso:

```bash
python3 carnada.py --profile hex
```

### Características

```text
Longitud por defecto: 32 caracteres
Incluye mayúsculas: no
Incluye minúsculas: sí, solo a-f
Incluye números: sí
Incluye símbolos: no
Caracteres posibles: 0123456789abcdef
```

### Grupo de caracteres

```text
0123456789abcdef
```

### Caso de uso

Este perfil es útil para:

- tokens de laboratorio;
    
- pruebas técnicas;
    
- identificadores aleatorios;
    
- valores compatibles con flujos que esperan hexadecimal;
    
- simulaciones;
    
- scripting.
    

### Ejemplo

```bash
python3 carnada.py --profile hex
```

Salida conceptual:

```text
a3f91c0b8e7d42f6a9c350de11b48c2f
```

### Consideración de seguridad

Aunque el conjunto de caracteres es pequeño, una longitud suficiente puede ofrecer una entropía alta.

Ejemplo:

```text
32 caracteres hexadecimales = 128 bits aproximados
```

Esto se debe a que cada carácter hexadecimal representa 4 bits.

---

## 9. Perfil `wifi`

El perfil `wifi` genera contraseñas largas y compatibles para redes Wi-Fi.

Uso:

```bash
python3 carnada.py --profile wifi
```

### Características

```text
Longitud por defecto: 24 caracteres
Incluye mayúsculas: sí
Incluye minúsculas: sí
Incluye números: sí
Incluye símbolos: no
Evita caracteres ambiguos: sí
```

### Grupos de caracteres

```text
A-Z
a-z
0-9
```

Con exclusión de caracteres ambiguos por defecto.

### Caso de uso

Este perfil está pensado para contraseñas que posiblemente deban escribirse manualmente en:

- teléfonos;
    
- televisores;
    
- consolas;
    
- routers;
    
- laptops;
    
- tablets;
    
- dispositivos IoT.
    

### Ventaja operativa

Evita símbolos que pueden ser incómodos de ingresar en teclados virtuales o interfaces de dispositivos.

Al mismo tiempo, compensa la ausencia de símbolos con una longitud mayor.

### Ejemplo

```bash
python3 carnada.py --profile wifi
```

### Consideración de seguridad

Para redes Wi-Fi, una contraseña larga y no reutilizada suele ser más importante que una contraseña corta llena de símbolos difíciles de escribir.

---

## 10. Comparación de perfiles

Resumen comparativo:

```text
Perfil   Longitud   Mayús   Minús   Números   Símbolos   Caso de uso
strong   18         sí      sí      sí        sí         uso general
legacy   16         sí      sí      sí        no         sistemas restrictivos
pin      6          no      no      sí        no         códigos numéricos
hex      32         no      sí*     sí        no         tokens hexadecimales
wifi     24         sí      sí      sí        no         contraseñas Wi-Fi
```

Nota sobre `hex`:

```text
En el perfil hex, las minúsculas se limitan a las letras a-f.
```

---

## 11. Personalización de perfiles

Aunque los perfiles tienen valores por defecto, el usuario puede modificarlos mediante argumentos CLI.

Ejemplo: usar `strong` pero con 32 caracteres:

```bash
python3 carnada.py --profile strong -l 32
```

Ejemplo: usar `legacy` con más longitud:

```bash
python3 carnada.py --profile legacy -l 24
```

Ejemplo: usar `strong` sin símbolos:

```bash
python3 carnada.py --profile strong --no-symbols
```

Ejemplo: usar `wifi` con 30 caracteres:

```bash
python3 carnada.py --profile wifi -l 30
```

Esto permite mantener perfiles simples, pero flexibles.

---

## 12. Caracteres ambiguos

CARNADA evita por defecto algunos caracteres visualmente ambiguos.

Ejemplos:

```text
O
0
I
1
l
o
```

Esto ayuda en escenarios donde la contraseña debe leerse o escribirse manualmente.

La exclusión de caracteres ambiguos favorece la usabilidad.

Para permitirlos, se puede usar:

```bash
python3 carnada.py --allow-ambiguous
```

### Cuándo permitir caracteres ambiguos

Puede tener sentido permitirlos cuando:

- la contraseña se copiará digitalmente;
    
- no será escrita manualmente;
    
- se desea maximizar el conjunto de caracteres;
    
- no existe riesgo de confusión visual.
    

### Cuándo evitarlos

Conviene evitarlos cuando:

- la contraseña será leída en pantalla;
    
- será compartida verbalmente;
    
- será escrita en un dispositivo externo;
    
- se usará en routers, televisores, consolas o IoT.
    

---

## 13. Relación entre perfil, longitud y entropía

La seguridad de una contraseña generada depende principalmente de:

- aleatoriedad;
    
- longitud;
    
- tamaño del conjunto de caracteres;
    
- no reutilización;
    
- almacenamiento seguro.
    

La entropía aproximada se calcula conceptualmente como:

```text
entropía = longitud × log2(tamaño del conjunto de caracteres)
```

Por eso, si un perfil usa menos tipos de caracteres, puede compensarse aumentando la longitud.

Ejemplo conceptual:

```text
Contraseña corta con símbolos     → puede ser fuerte, pero menos cómoda.
Contraseña larga sin símbolos     → puede ser fuerte y más compatible.
PIN numérico corto                → baja entropía.
Token hexadecimal largo           → buena entropía si la longitud es suficiente.
```

---

## 14. Selección recomendada por escenario

Uso general:

```bash
python3 carnada.py --profile strong
```

Sistema que rechaza símbolos:

```bash
python3 carnada.py --profile legacy
```

Red Wi-Fi:

```bash
python3 carnada.py --profile wifi
```

Código numérico:

```bash
python3 carnada.py --profile pin -l 8
```

Token hexadecimal:

```bash
python3 carnada.py --profile hex
```

Sistema restrictivo con mayor seguridad:

```bash
python3 carnada.py --profile legacy -l 24
```

Contraseña larga de uso general:

```bash
python3 carnada.py --profile strong -l 32
```

---

## 15. Riesgos de uso incorrecto

Los perfiles ayudan, pero no eliminan todos los riesgos.

Riesgos comunes:

- reutilizar contraseñas generadas;
    
- almacenar contraseñas en archivos planos;
    
- copiarlas en sistemas no confiables;
    
- compartirlas por canales inseguros;
    
- usar PINs donde se requiere una contraseña fuerte;
    
- confiar solo en el rating de entropía;
    
- pasar contraseñas reales por argumentos de shell.
    

Ejemplo a evitar con contraseñas reales:

```bash
python3 carnada.py check "MiPasswordReal123!"
```

Uso recomendado:

```bash
python3 carnada.py check
```

---

## 16. Criterio de diseño de los perfiles

Los perfiles de CARNADA no buscan cubrir todos los escenarios posibles.

Su objetivo es cubrir casos frecuentes de forma clara:

```text
strong  → seguridad general
legacy  → compatibilidad
pin     → valores numéricos
hex     → tokens técnicos
wifi    → escritura manual y compatibilidad
```

Esto mantiene la herramienta simple y evita una cantidad excesiva de opciones.

La simplicidad es parte del diseño de seguridad.

---

## 17. Conclusión operativa

Los perfiles son una de las funciones más útiles de CARNADA porque adaptan la generación de contraseñas al contexto operativo.

El perfil recomendado por defecto es:

```text
strong
```

Para compatibilidad, se recomienda:

```text
legacy
```

Para Wi-Fi o escritura manual, se recomienda:

```text
wifi
```

Para valores numéricos, se usa:

```text
pin
```

Para tokens hexadecimales, se usa:

```text
hex
```

La selección correcta del perfil permite equilibrar seguridad, compatibilidad y usabilidad.

---

### Documentación relacionada

[[01 - Índice del Proyecto CARNADA]]