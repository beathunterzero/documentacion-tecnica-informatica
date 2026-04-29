## 1. Identificación

**Proyecto:** CARNADA  
**Tipo de nota:** Lecciones aprendidas  
**Área:** Python / CLI / Ciberseguridad defensiva  
**Estado:** Documentación base  
**Autor:** Rhodyn Ildefonso (@beathunterzero)  
**Relación con:** Proyecto personal CARNADA  
**Enfoque:** Reflexiones técnicas y operativas obtenidas durante el rediseño del proyecto.

---

## 2. Propósito

Esta nota documenta las principales lecciones aprendidas durante el desarrollo y rediseño de **CARNADA**.

El objetivo no es repetir la documentación técnica, sino registrar el criterio adquirido durante el proceso: qué decisiones mejoraron el proyecto, qué funciones fueron eliminadas, qué riesgos se redujeron y qué aprendizajes pueden reutilizarse en futuros proyectos de ciberseguridad, automatización o scripting con Python.

---

## 3. Contexto inicial

CARNADA comenzó como un script de Python orientado a generación de contraseñas, hashing, cifrado y guardado de resultados.

La versión inicial incluía varias funciones:

```text
generación de contraseñas
hashing SHA-256
hashing SHA-512
derivación de clave con passphrase
cifrado AES-256-GCM
cifrado ChaCha20-Poly1305
guardado en archivo
impresión de datos criptográficos
```

Aunque estas funciones eran interesantes para laboratorio, el proyecto tenía un problema de enfoque: hacía demasiadas cosas al mismo tiempo.

El rediseño permitió convertirlo en una herramienta más clara:

```text
CLI local para generar y evaluar contraseñas
```

Ese cambio fue la base del upgrade del proyecto.

---

## 4. Lección 1: Más funciones no siempre significan mejor herramienta

Una de las lecciones más importantes fue entender que agregar capacidades criptográficas no hace automáticamente que una herramienta sea más profesional.

La versión inicial tenía cifrado, hashing y guardado, pero eso aumentaba:

- complejidad;
    
- riesgo operativo;
    
- dependencias externas;
    
- posibilidad de malas prácticas;
    
- dificultad para explicar el proyecto;
    
- ruido en la salida de consola.
    

La versión final quedó mejor al hacer menos cosas, pero hacerlas de forma más clara.

Conclusión:

```text
Una herramienta pequeña, bien delimitada y segura en su alcance puede ser más profesional que una herramienta con muchas funciones mal justificadas.
```

---

## 5. Lección 2: El alcance reducido puede ser una decisión de seguridad

Eliminar funciones no siempre significa perder valor.

En CARNADA, eliminar estas capacidades mejoró el diseño:

```text
cifrado
hashing
guardado en archivo
clave maestra
dependencias externas
```

El alcance reducido ayudó a evitar:

- almacenamiento accidental de secretos;
    
- mala gestión de claves maestras;
    
- uso incorrecto de hashes rápidos;
    
- falsa sensación de seguridad;
    
- complejidad innecesaria;
    
- dependencia de paquetes externos.
    

Conclusión:

```text
Reducir alcance también puede reducir superficie de ataque.
```

---

## 6. Lección 3: El cifrado debe tener una razón operativa

El cifrado es útil cuando existe una necesidad concreta:

- proteger datos en reposo;
    
- proteger datos en tránsito;
    
- construir una bóveda;
    
- almacenar secretos;
    
- transportar información sensible.
    

En CARNADA, al decidir que la herramienta no almacenaría secretos, el cifrado dejó de tener sentido.

Mantener cifrado solo para mostrar `ciphertext`, `salt` y `nonce` habría convertido el proyecto en un laboratorio criptográfico, no en una CLI práctica.

Conclusión:

```text
No se debe agregar cifrado solo para que un proyecto parezca más avanzado.
```

---

## 7. Lección 4: Los hashes de contraseñas pueden confundir si no hay contexto

La versión inicial generaba hashes SHA-256 y SHA-512 de las contraseñas.

El problema es que esos algoritmos no son adecuados por sí solos para almacenar contraseñas.

Para almacenamiento seguro se usan mecanismos como:

```text
Argon2id
bcrypt
scrypt
PBKDF2 con parámetros adecuados
```

Como CARNADA no es un sistema de autenticación ni almacenamiento, esa función podía transmitir una idea equivocada.

Conclusión:

```text
Una función técnicamente correcta puede ser incorrecta para el contexto del proyecto.
```

---

## 8. Lección 5: Una CLI profesional debe tener salida limpia

La versión inicial imprimía demasiada información:

```text
hashes
salt
nonce
ciphertext
explicaciones largas
clave maestra
datos sensibles
```

Eso dificultaba el uso práctico.

Una herramienta CLI debe mostrar solo lo necesario por defecto.

En la versión rediseñada, la salida quedó dividida en:

```text
normal
quiet
JSON
```

Cada una responde a un caso de uso distinto:

- `normal`: lectura humana;
    
- `quiet`: scripting;
    
- `JSON`: automatización.
    

Conclusión:

```text
La salida de una herramienta CLI debe ser útil, predecible y limpia.
```

---

## 9. Lección 6: `secrets` es la opción correcta para contraseñas en Python

Python ofrece varios módulos para trabajar con aleatoriedad, pero no todos son adecuados para seguridad.

Para contraseñas, tokens o secretos, se debe usar:

```python
secrets
```

No se debe usar:

```python
random
```

Diferencia conceptual:

```text
random  → simulaciones, pruebas, selección general
secrets → generación de valores impredecibles para seguridad
```

Conclusión:

```text
La selección correcta de una librería estándar puede ser una decisión de seguridad importante.
```

---

## 10. Lección 7: La compatibilidad también es parte del diseño

No todos los sistemas aceptan las mismas contraseñas.

Algunos sistemas modernos permiten símbolos y longitudes largas. Otros sistemas restrictivos o antiguos pueden fallar con caracteres especiales.

Por eso los perfiles agregan valor real:

```text
strong
legacy
pin
hex
wifi
```

Cada perfil responde a un escenario concreto.

Conclusión:

```text
Una herramienta útil no solo debe generar valores fuertes, también debe considerar el contexto donde esos valores serán usados.
```

---

## 11. Lección 8: Excluir caracteres ambiguos mejora la operación

Caracteres como estos pueden generar errores manuales:

```text
O
0
I
1
l
o
```

Excluirlos por defecto mejora la usabilidad en escenarios donde la contraseña se debe:

- leer;
    
- copiar manualmente;
    
- escribir en otro dispositivo;
    
- ingresar en una interfaz limitada;
    
- compartir temporalmente en un entorno controlado.
    

Conclusión:

```text
La seguridad no debe ignorar la usabilidad operativa.
```

---

## 12. Lección 9: El modo `check` agrega utilidad sin romper el alcance

Agregar el modo:

```bash
python3 carnada.py check
```

fue una buena decisión porque aumenta el valor práctico de la herramienta sin convertirla en un gestor de contraseñas.

El modo `check` permite analizar:

- longitud;
    
- mayúsculas;
    
- minúsculas;
    
- números;
    
- símbolos;
    
- caracteres ambiguos;
    
- entropía aproximada;
    
- rating.
    

Y lo hace localmente, sin almacenar ni enviar contraseñas.

Conclusión:

```text
Una función adicional es válida cuando refuerza el objetivo principal sin ampliar demasiado el riesgo.
```

---

## 13. Lección 10: La documentación transforma un script en proyecto

Antes del rediseño, CARNADA podía verse como un script personal.

Después de documentar:

```text
README.md
docs/usage.md
docs/security-notes.md
docs/architecture/overview.md
examples/usage-examples.md
diagrama de arquitectura
```

el proyecto ganó contexto, explicación y presentación profesional.

La documentación permite que otra persona entienda:

- qué hace la herramienta;
    
- qué no hace;
    
- cómo se usa;
    
- por qué fue diseñada así;
    
- cuáles son sus límites;
    
- qué decisiones de seguridad se tomaron.
    

Conclusión:

```text
Un proyecto técnico no solo se evalúa por el código, también por la claridad con la que explica su propósito.
```

---

## 14. Lección 11: Un README no debe prometer demasiado

Se decidió no incluir un Roadmap en el README.

Motivo:

- evita compromisos innecesarios;
    
- presenta el proyecto como una herramienta completa en su alcance actual;
    
- reduce expectativas de funciones futuras;
    
- mantiene el README más sobrio.
    

Esto fue coherente con el enfoque actual del proyecto: dejar CARNADA funcional y profesional, mientras el foco principal del autor se mantiene en Threat Hunting.

Conclusión:

```text
Un README debe explicar el alcance real del proyecto, no prometer una evolución que no es prioridad.
```

---

## 15. Lección 12: Eliminar dependencias externas reduce fricción y riesgo

La versión inicial dependía de:

```text
cryptography
cffi
pycparser
```

Al eliminar cifrado, esas dependencias dejaron de ser necesarias.

La versión final usa solo librerías estándar de Python:

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

Esto permite que cualquier usuario pueda ejecutar la herramienta después de clonar el repositorio:

```bash
git clone https://github.com/beathunterzero/carnada.git
cd carnada
python3 carnada.py
```

Sin instalar paquetes adicionales.

Conclusión:

```text
Si una herramienta puede funcionar sin dependencias externas, eso mejora portabilidad, auditoría y experiencia de uso.
```

---

## 16. Lección 13: `.venv` es útil para desarrollo, no siempre para uso

El entorno virtual puede servir localmente durante el desarrollo, pero en este caso no debe presentarse como requisito para el usuario.

Como CARNADA no usa paquetes externos, el usuario no necesita crear un `.venv`.

El `.gitignore` debe excluirlo:

```gitignore
.venv/
```

Conclusión:

```text
El entorno de desarrollo del autor no debe confundirse con los requisitos del usuario final.
```

---

## 17. Lección 14: Una arquitectura simple puede ser suficiente

No todo proyecto necesita una arquitectura modular desde el inicio.

CARNADA funciona correctamente como un solo archivo:

```text
carnada.py
```

Separarlo en múltiples archivos como:

```text
cli.py
profiles.py
generator.py
analyzer.py
formatter.py
```

podría ser útil si el proyecto creciera mucho, pero en el estado actual agregaría complejidad innecesaria.

Conclusión:

```text
La arquitectura debe responder al tamaño y propósito real del proyecto, no a una idea abstracta de sofisticación.
```

---

## 18. Lección 15: El valor de seguridad también está en decir “no”

Durante el rediseño se decidió no agregar o mantener:

```text
vault
almacenamiento
cifrado
hashing
sincronización
red
dependencias externas
roadmap
```

Cada “no” ayudó a proteger el alcance del proyecto.

Conclusión:

```text
En proyectos de seguridad, saber qué no construir es tan importante como saber qué construir.
```

---

## 19. Lección 16: La salida JSON y quiet mode deben documentar sus riesgos

Funciones como:

```bash
python3 carnada.py --quiet
python3 carnada.py --json
```

son útiles, pero pueden causar exposición accidental si el usuario redirige o guarda la salida.

Ejemplos riesgosos:

```bash
python3 carnada.py --json > password.json
python3 carnada.py --quiet > password.txt
PASSWORD=$(python3 carnada.py --quiet)
echo "$PASSWORD"
```

Por eso se documentaron advertencias de uso.

Conclusión:

```text
Una función útil debe ir acompañada de advertencias cuando puede exponer secretos por mal uso.
```

---

## 20. Lección 17: El proyecto ahora comunica mejor el perfil técnico del autor

CARNADA quedó alineado con una narrativa profesional:

- Python;
    
- CLI;
    
- seguridad defensiva;
    
- uso de terminal;
    
- documentación técnica;
    
- reducción de riesgo;
    
- decisiones explícitas;
    
- enfoque local-first.
    

Esto comunica mejor criterio técnico que una herramienta con muchas funciones criptográficas mezcladas sin propósito claro.

Conclusión:

```text
Un proyecto pequeño bien diseñado puede aportar más al perfil profesional que un proyecto grande pero confuso.
```

---

## 21. Estado final del proyecto

El proyecto pasó de:

```text
generador + hashing + cifrado + clave maestra + guardado + salida extensa
```

a:

```text
CLI local para generar y evaluar contraseñas
```

La versión final tiene:

- propósito claro;
    
- salida limpia;
    
- perfiles útiles;
    
- análisis local;
    
- sin almacenamiento;
    
- sin red;
    
- sin dependencias externas;
    
- documentación técnica;
    
- arquitectura visual;
    
- límites explícitos;
    
- mejor presentación en GitHub.
    

---

## 22. Conclusión operativa

La principal lección de CARNADA es que profesionalizar un proyecto no siempre significa agregar más código.

En este caso, profesionalizar significó:

```text
eliminar funciones innecesarias
reducir riesgo
limpiar la salida
documentar decisiones
definir alcance
mejorar usabilidad
hacerlo ejecutable desde shell
```

CARNADA ahora representa mejor una herramienta pequeña, local, auditable y útil para flujos de trabajo defensivos.

Su valor está en la claridad de diseño.

---

### Documentación relacionada

[[01 - Índice del Proyecto CARNADA]]