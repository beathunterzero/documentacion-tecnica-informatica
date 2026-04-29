## 1. Identificación

**Proyecto:** CARNADA  
**Tipo de nota:** Ejemplos prácticos  
**Área:** Python / CLI / Ciberseguridad defensiva  
**Estado:** Documentación base  
**Autor:** Rhodyn Ildefonso (@beathunterzero)  
**Relación con:** Proyecto personal CARNADA  
**Enfoque:** Casos prácticos de ejecución desde la shell.

---

## 2. Propósito

Esta nota reúne ejemplos prácticos de uso de **CARNADA**.

El objetivo es documentar comandos reales que pueden ejecutarse desde la terminal para generar contraseñas, usar perfiles, obtener salida limpia, generar JSON y analizar contraseñas localmente.

A diferencia de una guía de uso general, esta nota está orientada a escenarios concretos.

---

## 3. Generar una contraseña por defecto

Comando:

```bash
python3 carnada.py
```

Este comando usa el perfil por defecto:

```text
strong
```

Salida esperada:

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

Caso de uso:

- generar una contraseña fuerte de uso general;
    
- obtener una credencial temporal;
    
- probar la herramienta rápidamente.
    

---

## 4. Generar una contraseña más larga

Comando:

```bash
python3 carnada.py -l 24
```

Equivalente:

```bash
python3 carnada.py --length 24
```

Salida esperada:

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

Caso de uso:

- generar credenciales más robustas;
    
- usar contraseñas largas en sistemas modernos;
    
- aumentar entropía aproximada.
    

---

## 5. Generar una contraseña para sistemas restrictivos

Comando:

```bash
python3 carnada.py --profile legacy
```

Salida esperada:

```text
CARNADA — Secure Password Generator
------------------------------------------
Password : V7kQm92tLx8pRzW4
Profile  : legacy
Length   : 16
Charset  : 54 characters
Entropy  : ~92.08 bits
Rating   : strong
```

Caso de uso:

- sistemas antiguos;
    
- paneles que no aceptan símbolos;
    
- appliances con políticas restrictivas;
    
- formularios que rechazan caracteres especiales.
    

Observación:

Si el sistema permite mayor longitud, es recomendable aumentar el tamaño:

```bash
python3 carnada.py --profile legacy -l 24
```

---

## 6. Generar una contraseña para Wi-Fi

Comando:

```bash
python3 carnada.py --profile wifi
```

Salida esperada:

```text
CARNADA — Secure Password Generator
------------------------------------------
Password : V7kQm92tLx8pRzW4sA6nB8
Profile  : wifi
Length   : 24
Charset  : 54 characters
Entropy  : ~138.12 bits
Rating   : very strong
```

Caso de uso:

- redes Wi-Fi domésticas;
    
- redes de laboratorio;
    
- contraseñas que se escriben manualmente;
    
- dispositivos con teclados limitados.
    

Este perfil evita símbolos complejos para facilitar el ingreso manual en dispositivos como:

- teléfonos;
    
- televisores;
    
- routers;
    
- consolas;
    
- dispositivos IoT.
    

---

## 7. Generar un PIN numérico

Comando:

```bash
python3 carnada.py --profile pin
```

Salida esperada:

```text
CARNADA — Secure Password Generator
------------------------------------------
Password : 583920
Profile  : pin
Length   : 6
Charset  : 10 characters
Entropy  : ~19.93 bits
Rating   : weak
```

Generar un PIN de 8 dígitos:

```bash
python3 carnada.py --profile pin -l 8
```

Salida esperada:

```text
CARNADA — Secure Password Generator
------------------------------------------
Password : 84027193
Profile  : pin
Length   : 8
Charset  : 10 characters
Entropy  : ~26.58 bits
Rating   : weak
```

Consideración:

Un PIN tiene menor entropía que una contraseña completa. Debe usarse solo cuando el sistema requiere valores numéricos.

---

## 8. Generar un token hexadecimal

Comando:

```bash
python3 carnada.py --profile hex
```

Salida esperada:

```text
CARNADA — Secure Password Generator
------------------------------------------
Password : a3f91c0b8e7d42f6a9c350de11b48c2f
Profile  : hex
Length   : 32
Charset  : 16 characters
Entropy  : ~128.0 bits
Rating   : very strong
```

Caso de uso:

- laboratorios técnicos;
    
- pruebas de scripts;
    
- generación de tokens de ejemplo;
    
- flujos donde se requiere formato hexadecimal.
    

---

## 9. Generar múltiples contraseñas

Comando:

```bash
python3 carnada.py --count 5
```

Equivalente:

```bash
python3 carnada.py -c 5
```

Salida esperada:

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

Caso de uso:

- generar varias opciones;
    
- seleccionar una contraseña visualmente cómoda;
    
- crear credenciales temporales para laboratorio.
    

---

## 10. Usar salida silenciosa

Comando:

```bash
python3 carnada.py --quiet
```

Equivalente:

```bash
python3 carnada.py -q
```

Salida esperada:

```text
V7#kQm92@tLx8pRz
```

Caso de uso:

- scripting;
    
- copiar una contraseña sin metadatos;
    
- integrar la herramienta con otros comandos.
    

Ejemplo:

```bash
PASSWORD=$(python3 carnada.py --quiet)
```

Consideración:

Evitar imprimir variables que contienen contraseñas reales:

```bash
echo "$PASSWORD"
```

Ese comando puede exponer el secreto en terminal, logs o grabaciones.

---

## 11. Generar salida JSON

Comando:

```bash
python3 carnada.py --json
```

Salida esperada:

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

Caso de uso:

- automatización;
    
- integración con scripts;
    
- procesamiento estructurado;
    
- pruebas técnicas.
    

Consideración:

Evitar redirigir salida real a archivos planos:

```bash
python3 carnada.py --json > password.json
```

Ese archivo contendría la contraseña generada en texto claro.

---

## 12. Generar varias contraseñas en JSON

Comando:

```bash
python3 carnada.py --count 3 --json
```

Salida esperada:

```json
{
  "profile": "strong",
  "count": 3,
  "length": 18,
  "charset_size": 82,
  "entropy_bits": 114.44,
  "rating": "very strong",
  "passwords": [
    "V7#kQm92@tLx8pRz",
    "B4!sKp81@TxQ9mVw",
    "M8#qLn73@RvX2pZa"
  ]
}
```

Caso de uso:

- generar varias opciones estructuradas;
    
- pruebas automatizadas;
    
- validación de salida JSON.
    

---

## 13. Generar contraseña sin símbolos

Comando:

```bash
python3 carnada.py --no-symbols
```

Salida esperada:

```text
CARNADA — Secure Password Generator
------------------------------------------
Password : V7kQm92tLx8pRzW4
Profile  : strong
Length   : 18
Charset  : 54 characters
Entropy  : ~103.59 bits
Rating   : very strong
```

Caso de uso:

- sistemas que rechazan símbolos;
    
- formularios problemáticos;
    
- credenciales que se escribirán manualmente.
    

---

## 14. Generar contraseña sin números

Comando:

```bash
python3 carnada.py --no-numbers
```

Salida esperada:

```text
CARNADA — Secure Password Generator
------------------------------------------
Password : Qm#tLx@pRzW!sA
Profile  : strong
Length   : 18
Charset  : 74 characters
Entropy  : ~111.77 bits
Rating   : very strong
```

Caso de uso:

- pruebas de políticas específicas;
    
- sistemas con restricciones particulares;
    
- validación de combinaciones de grupos.
    

---

## 15. Permitir caracteres ambiguos

Por defecto, CARNADA evita caracteres visualmente ambiguos como:

```text
O
0
I
1
l
o
```

Para permitirlos:

```bash
python3 carnada.py --allow-ambiguous
```

Salida esperada:

```text
CARNADA — Secure Password Generator
------------------------------------------
Password : O7#kQm92@tLx8pRz
Profile  : strong
Length   : 18
Charset  : 88 characters
Entropy  : ~116.31 bits
Rating   : very strong
```

Caso de uso:

- cuando la contraseña será copiada digitalmente;
    
- cuando no se escribirá manualmente;
    
- cuando se desea ampliar el conjunto de caracteres.
    

---

## 16. Analizar una contraseña de forma segura

Comando recomendado:

```bash
python3 carnada.py check
```

La herramienta solicita la contraseña con entrada oculta:

```text
Password to check:
```

Salida esperada:

```text
CARNADA — Password Check
------------------------------------------
Length          : 18
Uppercase       : yes
Lowercase       : yes
Numbers         : yes
Symbols         : yes
Ambiguous chars : no
Entropy         : ~114.44 bits
Rating          : very strong
```

Caso de uso:

- evaluar una contraseña localmente;
    
- revisar composición;
    
- obtener una estimación de entropía;
    
- evitar exponer la contraseña en el historial de shell.
    

---

## 17. Analizar una contraseña de prueba desde la línea de comandos

Comando:

```bash
python3 carnada.py check "Password123!"
```

Salida esperada:

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

Caso de uso:

- pruebas;
    
- ejemplos;
    
- valores no sensibles;
    
- validación del modo check.
    

Advertencia:

No usar este método con contraseñas reales, porque pueden quedar en el historial de comandos.

---

## 18. Analizar una contraseña con salida JSON

Comando:

```bash
python3 carnada.py check --json
```

Salida esperada:

```json
{
  "length": 18,
  "uppercase": true,
  "lowercase": true,
  "numbers": true,
  "symbols": true,
  "ambiguous_chars": false,
  "entropy_bits": 114.44,
  "rating": "very strong"
}
```

También puede usarse con una contraseña de prueba:

```bash
python3 carnada.py check "Password123!" --json
```

Caso de uso:

- automatización;
    
- pruebas de análisis;
    
- validación estructurada.
    

---

## 19. Ver ayuda principal

Comando:

```bash
python3 carnada.py --help
```

Caso de uso:

- revisar opciones disponibles;
    
- recordar sintaxis;
    
- validar perfiles;
    
- consultar subcomandos.
    

---

## 20. Ver ayuda del modo generate

Comando:

```bash
python3 carnada.py generate --help
```

Caso de uso:

- revisar opciones específicas de generación;
    
- consultar flags como `--profile`, `--count`, `--quiet` y `--json`.
    

---

## 21. Ver ayuda del modo check

Comando:

```bash
python3 carnada.py check --help
```

Caso de uso:

- revisar cómo funciona el análisis de contraseñas;
    
- consultar el uso de `--json` en modo check.
    

---

## 22. Ver versión

Comando:

```bash
python3 carnada.py --version
```

Salida esperada:

```text
carnada 1.0.0
```

Caso de uso:

- verificar versión instalada;
    
- documentar pruebas;
    
- confirmar ejecución correcta.
    

---

## 23. Ejecutar como script en Linux, WSL o macOS

Dar permisos de ejecución:

```bash
chmod +x carnada.py
```

Ejecutar:

```bash
./carnada.py
```

Ejemplo con argumentos:

```bash
./carnada.py --profile wifi -l 28
```

Caso de uso:

- uso directo desde shell;
    
- evitar escribir `python3` en cada ejecución;
    
- facilitar pruebas en entornos Linux o WSL.
    

---

## 24. Patrones rápidos de uso

Generar contraseña general:

```bash
python3 carnada.py
```

Generar contraseña larga:

```bash
python3 carnada.py -l 32
```

Generar contraseña compatible con sistemas restrictivos:

```bash
python3 carnada.py --profile legacy
```

Generar contraseña para Wi-Fi:

```bash
python3 carnada.py --profile wifi
```

Generar PIN:

```bash
python3 carnada.py --profile pin -l 8
```

Generar token hexadecimal:

```bash
python3 carnada.py --profile hex
```

Generar múltiples contraseñas:

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

## 25. Ejemplos que deben evitarse con credenciales reales

Evitar pasar contraseñas reales directamente en la shell:

```bash
python3 carnada.py check "MiPasswordReal123!"
```

Motivo:

```text
Puede quedar registrada en el historial de comandos.
```

Evitar guardar salida con contraseña en archivos planos:

```bash
python3 carnada.py --json > password.json
python3 carnada.py --quiet > password.txt
```

Motivo:

```text
El archivo generado contendría la contraseña en texto claro.
```

Evitar imprimir variables que contienen contraseñas:

```bash
PASSWORD=$(python3 carnada.py --quiet)
echo "$PASSWORD"
```

Motivo:

```text
La contraseña queda visible en terminal, logs o grabaciones.
```

---

## 26. Conclusión operativa

Estos ejemplos muestran cómo utilizar CARNADA en escenarios comunes desde la shell.

La herramienta puede usarse para:

```text
generar contraseñas seguras
generar varias opciones
usar perfiles específicos
producir salida limpia
producir JSON
analizar contraseñas localmente
evitar almacenamiento de secretos
```

El uso recomendado es mantener CARNADA como una utilidad local y temporal, no como un almacén de credenciales.

---

### Documentación relacionada

[[01 - Índice del Proyecto CARNADA]]