## 1. Introducción al Proyecto

`carnada.py` es una herramienta táctica de generación de contraseñas, hashing y cifrado simétrico escrita en Python. Su objetivo es proporcionar una utilidad de línea de comandos robusta para la gestión segura de credenciales, utilizando algoritmos criptográficos modernos (AES-GCM y ChaCha20-Poly1305) y derivación de claves mediante PBKDF2.

---

## 2. Desglose del Código Línea por Línea

### 2.1 Importaciones y Configuración Inicial

```python
import argparse
import hashlib
import secrets
import string
import sys
from datetime import datetime
from getpass import getpass

from cryptography.hazmat.primitives.kdf.pbkdf2 import PBKDF2HMAC
from cryptography.hazmat.primitives import hashes
from cryptography.hazmat.primitives.ciphers.aead import AESGCM, ChaCha20Poly1305
from cryptography.hazmat.backends import default_backend
import base64
import os
```

- `import argparse`: Permite definir y leer argumentos desde la línea de comandos.
- `import hashlib`: Proporciona funciones de hashing (SHA-256, SHA-512, etc.).
- `import secrets`: Genera valores aleatorios criptográficamente seguros (ideal para contraseñas).
- `import string`: Trae conjuntos de caracteres (mayúsculas, minúsculas, dígitos, etc.).
- `import sys`: Permite interactuar con el sistema (por ejemplo, `sys.exit`).
- `from datetime import datetime`: Para obtener fecha y hora actuales.
- `from getpass import getpass`: Permite pedir contraseñas sin mostrarlas en pantalla.
- **Líneas con** `cryptography...`:
    - `PBKDF2HMAC`: KDF para derivar una clave desde una passphrase.
    - `hashes`: Algoritmos de hash para el KDF.
    - `AESGCM`**,** `ChaCha20Poly1305`: Algoritmos de cifrado autenticado.
    - `default_backend`: Backend criptográfico por defecto.
- `import base64`: Para codificar bytes en texto base64 (legible y almacenable).
- `import os`: Para generar bytes aleatorios (`os.urandom`) y otras funciones del sistema.

### 2.2 Constantes de Configuración

```python
CARACTERES_SEGUROS = (
    string.ascii_uppercase.replace('O', '').replace('I', '') +
    string.ascii_lowercase.replace('l', '').replace('o', '') +
    string.digits.replace('0', '').replace('1', '') +
    "!@#$%^&*()-_=+[]{};:,.<>/?"
)
```

- `CARACTERES_SEGUROS`:
    - Toma mayúsculas y elimina `O` e `I` (para evitar confusión visual).
    - Toma minúsculas y elimina `l` y `o`.
    - Toma dígitos y elimina `0` y `1`.
    - Añade símbolos especiales.
    - Resultado: un conjunto de caracteres “seguros” y poco ambiguos.

```python
DEFAULT_LENGTH = 18
KDF_ITERATIONS = 100_000
SALT_SIZE = 16
NONCE_SIZE = 12  # recomendado para AESGCM y ChaCha20Poly1305
```

- `DEFAULT_LENGTH`: Longitud por defecto de la contraseña (18).
- `KDF_ITERATIONS`: Número de iteraciones para PBKDF2 (100 000, valor robusto).
- `SALT_SIZE`: Tamaño del salt en bytes (16).
- `NONCE_SIZE`: Tamaño del nonce (12 bytes), recomendado para AES-GCM y ChaCha20-Poly1305.

### 2.3 Generación de contraseñas y hashes

```python
def generar_contrasena(longitud: int, incluir_mayus: bool, incluir_minus: bool,
                       incluir_numeros: bool, incluir_simbolos: bool) -> str:
    if longitud < 8:
        longitud = 8
```

- **Función** `generar_contrasena`:
    - Recibe longitud y flags para incluir mayúsculas, minúsculas, números y símbolos.
    - Si la longitud es menor que 8, la fuerza a 8 (mínimo recomendado).

```python
    chars = ""
    if incluir_mayus:
        chars += string.ascii_uppercase.replace('O', '').replace('I', '')
    if incluir_minus:
        chars += string.ascii_lowercase.replace('l', '').replace('o', '')
    if incluir_numeros:
        chars += string.digits.replace('0', '').replace('1', '')
    if incluir_simbolos:
        chars += "!@#$%^&*()-_=+[]{};:,.<>/?"
```

- **Construcción del conjunto de caracteres**:
    - Empieza con `chars` vacío.
    - Según cada flag, añade el conjunto correspondiente (con los caracteres ambiguos removidos).

```python
    if not chars:
        chars = CARACTERES_SEGUROS
```

- Si el usuario desactiva todo (no mayúsculas, no minúsculas, etc.), se usa el conjunto por defecto `CARACTERES_SEGUROS`.

```python
    return ''.join(secrets.choice(chars) for _ in range(longitud))
```

- Genera la contraseña eligiendo caracteres al azar (criptográficamente seguros) de `chars` hasta alcanzar la longitud.

```python
def generar_hash_sha256(texto: str) -> str:
    return hashlib.sha256(texto.encode('utf-8')).hexdigest()
```

- `generar_hash_sha256`:
    - Codifica el texto en UTF-8.
    - Calcula SHA-256.
    - Devuelve el hash en hexadecimal.

```python
def generar_hash_sha512(texto: str) -> str:
    return hashlib.sha512(texto.encode('utf-8')).hexdigest()
```

- `generar_hash_sha512`:
    - Igual que la anterior, pero usando SHA-512.

### 2.4 KDF y cifrado

```python
def derivar_clave_desde_passphrase(passphrase: str, salt: bytes) -> bytes:
    """
    Deriva una clave de 32 bytes (256 bits) desde una passphrase usando PBKDF2-HMAC-SHA256.
    """
    kdf = PBKDF2HMAC(
        algorithm=hashes.SHA256(),
        length=32,
        salt=salt,
        iterations=KDF_ITERATIONS,
        backend=default_backend()
    )
    return kdf.derive(passphrase.encode('utf-8'))
```

- `derivar_clave_desde_passphrase`:
    - Crea un objeto PBKDF2HMAC con:
        - SHA-256 como algoritmo.
        - Longitud de clave 32 bytes (256 bits).
        - Salt recibido por parámetro.
        - Iteraciones definidas en `KDF_ITERATIONS`.
    - Deriva la clave a partir de la passphrase (codificada en UTF-8).
    - Devuelve la clave en bytes.

```python
def cifrar_con_aes256(plaintext: str, key: bytes, salt: bytes) -> dict:
    aesgcm = AESGCM(key)
    nonce = os.urandom(NONCE_SIZE)
    ciphertext = aesgcm.encrypt(nonce, plaintext.encode('utf-8'), None)
```

- `cifrar_con_aes256`:
    - Crea un objeto `AESGCM` con la clave derivada.
    - Genera un `nonce` aleatorio de tamaño `NONCE_SIZE`.
    - Cifra el `plaintext` (codificado en UTF-8) con AES-GCM, sin datos asociados (`None`).

```python
    return {
        "algoritmo": "AES-256-GCM",
        "salt": base64.b64encode(salt).decode('utf-8'),
        "nonce": base64.b64encode(nonce).decode('utf-8'),
        "ciphertext": base64.b64encode(ciphertext).decode('utf-8'),
    }
```

- Devuelve un diccionario con:
    - Nombre del algoritmo.
    - Salt, nonce y ciphertext codificados en base64 para poder guardarlos como texto.

```python
def cifrar_con_chacha20(plaintext: str, key: bytes, salt: bytes) -> dict:
    chacha = ChaCha20Poly1305(key)
    nonce = os.urandom(NONCE_SIZE)
    ciphertext = chacha.encrypt(nonce, plaintext.encode('utf-8'), None)
```

- `cifrar_con_chacha20`:
    - Igual idea que AES-GCM, pero usando ChaCha20-Poly1305.
    - Genera nonce aleatorio.
    - Cifra el texto.

```python
    return {
        "algoritmo": "ChaCha20-Poly1305",
        "salt": base64.b64encode(salt).decode('utf-8'),
        "nonce": base64.b64encode(nonce).decode('utf-8'),
        "ciphertext": base64.b64encode(ciphertext).decode('utf-8'),
    }
```

- Devuelve la misma estructura, pero indicando el algoritmo ChaCha20-Poly1305.

### 2.5 Guardado en archivo

```python
def guardar_en_archivo_modo_todo(usuario: str, contrasena: str,
                                 hash256: str, hash512: str,
                                 aes_info: dict, chacha_info: dict,
                                 passphrase: str):
    fecha = datetime.now().strftime("%Y-%m-%d %H:%M:%S")
    with open("contrasenas_generadas.txt", "a", encoding="utf-8") as f:
```

- `guardar_en_archivo_modo_todo`:
    - Obtiene la fecha y hora actual como string.
    - Abre (o crea) el archivo `contrasenas_generadas.txt` en modo append (`"a"`).

```python
        f.write(f"[{fecha}]\\n")
        f.write(f"Usuario        : {usuario}\\n")
        f.write(f"Contraseña     : {contrasena}\\n")
        f.write(f"Clave maestra  : {passphrase}\\n")
        f.write(f"Hash SHA-256   : {hash256}\\n")
        f.write(f"Hash SHA-512   : {hash512}\\n")
```

- Escribe:
    - Fecha.
    - Usuario.
    - Contraseña generada.
    - Clave maestra.
    - Hashes SHA-256 y SHA-512.

```python
        f.write("AES-256-GCM:\\n")
        f.write(f"  Salt (base64)  : {aes_info['salt']}\\n")
        f.write(f"  Nonce (base64) : {aes_info['nonce']}\\n")
        f.write(f"  Ciphertext     : {aes_info['ciphertext']}\\n")
        f.write("ChaCha20-Poly1305:\\n")
        f.write(f"  Salt (base64)  : {chacha_info['salt']}\\n")
        f.write(f"  Nonce (base64) : {chacha_info['nonce']}\\n")
        f.write(f"  Ciphertext     : {chacha_info['ciphertext']}\\n")
        f.write("-" * 70 + "\\n")
```

- Escribe la información de cifrado AES y ChaCha (salt, nonce, ciphertext).
- Añade una línea de separación de 70 guiones.

Las otras dos funciones de guardado son versiones reducidas:

```python
def guardar_en_archivo_solo_aes(usuario: str, aes_info: dict, passphrase: str):
    ...
```

- Guarda solo usuario, clave maestra y datos de AES.

```python
def guardar_en_archivo_solo_chacha(usuario: str, chacha_info: dict, passphrase: str):
    ...
```

- Guarda solo usuario, clave maestra y datos de ChaCha.

### 2.6 Argumentos de línea de comandos

```python
def configurar_argumentos():
    parser = argparse.ArgumentParser(
        description="🎣 carnada — Generador de contraseñas seguras con hashing y cifrado",
        formatter_class=argparse.RawDescriptionHelpFormatter,
        epilog=(...)
    )
```

- `configurar_argumentos`:
    - Crea un parser de argumentos con descripción y ejemplos.

```python
    parser.add_argument("--user", "-u", type=str, default=None,
                        help="Nombre de usuario (si no se proporciona, se pedirá)")
```

- `-user` **/** `u`: Nombre de usuario. Si no se pasa, se pedirá por input.

```python
    parser.add_argument("--length", "-l", type=int, default=DEFAULT_LENGTH,
                        help=f"Longitud de la contraseña (default: {DEFAULT_LENGTH})")
```

- `-length` **/** `l`: Longitud de la contraseña (por defecto 18).

```python
    parser.add_argument("--no-mayus", action="store_true", help="No incluir mayúsculas")
    parser.add_argument("--no-minus", action="store_true", help="No incluir minúsculas")
    parser.add_argument("--no-numbers", action="store_true", help="No incluir números")
    parser.add_argument("--no-symbols", action="store_true", help="No incluir símbolos")
```

- Flags para excluir tipos de caracteres. Si se usan, se ponen a `True`.

```python
    parser.add_argument("--save", "-s", action="store_true",
                        help="Guardar en archivo contrasenas_generadas.txt")
```

- `-save` **/** `s`: Si se pasa, se guardan los resultados en el archivo.

```python
    parser.add_argument(
        "--cipher",
        choices=["none", "aes", "chacha"],
        default="none",
        help="Modo de salida: none = todo, aes = solo AES, chacha = solo ChaCha20"
    )
```

- `-cipher`:
    - `none`: muestra y guarda todo (hashes + AES + ChaCha).
    - `aes`: solo AES.
    - `chacha`: solo ChaCha.

```python
    return parser.parse_args()
```

- Devuelve el objeto `args` con todos los argumentos parseados.

### 2.7 Función principal `main()`

```python
def main():
    args = configurar_argumentos()
```

- Llama a `configurar_argumentos` y obtiene los parámetros de la CLI.

```python
    # Usuario
    if not args.user:
        while True:
            usuario = input("Ingrese el nombre de usuario: ").strip()
            if usuario:
                break
            print("❌ El nombre de usuario no puede estar vacío.")
    else:
        usuario = args.user
```

- Si no se pasó `-user`, pide el usuario por consola hasta que no esté vacío.
- Si se pasó, lo usa directamente.

```python
    if args.length < 8:
        print("⚠️ Longitud mínima recomendada: 8. Se ajustará automáticamente.")
```

- Si la longitud es menor que 8, avisa que se ajustará (la corrección real se hace en `generar_contrasena`).

```python
    # Clave maestra (siempre)
    print("\\nSe requiere una clave maestra para derivar la clave de cifrado.")
    passphrase = getpass("Ingrese clave maestra: ")
    passphrase_confirm = getpass("Confirme clave maestra: ")
```

- Informa que se necesita una clave maestra.
- Pide la clave maestra dos veces, sin mostrarla en pantalla.

```python
    if passphrase != passphrase_confirm:
        print("❌ Las claves maestras no coinciden. Abortando.")
        sys.exit(1)
```

- Si las dos entradas no coinciden, muestra error y termina el programa con código 1.

```python
    # Generar contraseña
    contrasena = generar_contrasena(
        longitud=args.length,
        incluir_mayus=not args.no_mayus,
        incluir_minus=not args.no_minus,
        incluir_numeros=not args.no_numbers,
        incluir_simbolos=not args.no_symbols
    )
```

- Llama a `generar_contrasena`:
    - Usa la longitud pedida.
    - Incluye cada tipo de carácter si el flag `no_*` es `False` (por eso el `not`).

```python
    # Hashes
    hash256 = generar_hash_sha256(contrasena)
    hash512 = generar_hash_sha512(contrasena)
```

- Calcula los hashes SHA-256 y SHA-512 de la contraseña generada.

```python
    # Derivar UNA sola clave para AES y ChaCha
    salt = os.urandom(SALT_SIZE)
    key = derivar_clave_desde_passphrase(passphrase, salt)
```

- Genera un salt aleatorio de tamaño `SALT_SIZE`.
- Deriva una única clave desde la passphrase y el salt, que se usará tanto para AES como para ChaCha.

```python
    aes_info = None
    chacha_info = None
```

- Inicializa variables para guardar la info de cifrado.

```python
    # Cifrado según modo
    if args.cipher == "none":
        aes_info = cifrar_con_aes256(contrasena, key, salt)
        chacha_info = cifrar_con_chacha20(contrasena, key, salt)
    elif args.cipher == "aes":
        aes_info = cifrar_con_aes256(contrasena, key, salt)
    elif args.cipher == "chacha":
        chacha_info = cifrar_con_chacha20(contrasena, key, salt)
```

- Según el modo:
    - `none`: cifra con ambos algoritmos.
    - `aes`: solo AES.
    - `chacha`: solo ChaCha.

### 2.8 Salida en pantalla

```python
    print("\\n" + "="*70)
    print("                 🎣 CARNADA — PASSWORD TOOL")
    print("="*70)
    print(f"Usuario               : {usuario}")
    print(f"Contraseña            : {contrasena}")
    print(f"Longitud              : {len(contrasena)} caracteres")
    print(f"Clave maestra         : {passphrase}")
```

- Imprime cabecera y datos básicos: usuario, contraseña, longitud y clave maestra.

Luego, según `args.cipher`, imprime distintos bloques:

### 2.9 Caso `cipher == "none"`

```python
    if args.cipher == "none":
        print("-" * 70)
        print("SHA-256 — Hash criptográfico usado en blockchain, firmas digitales y verificación de integridad.")
        print(f"Hash SHA-256          : {hash256}")
        print()
        print("SHA-512 — Variante más robusta de SHA-2, usada en sistemas de alta seguridad.")
        print(f"Hash SHA-512          : {hash512}")
        print("-" * 70)
        print("AES-256-GCM — Cifrado autenticado usado en HTTPS, VPNs y almacenamiento seguro.")
        print(f"Salt (base64)         : {aes_info['salt']}")
        print(f"Nonce (base64)        : {aes_info['nonce']}")
        print(f"Ciphertext (base64)   : {aes_info['ciphertext']}")
        print("-" * 70)
        print("ChaCha20-Poly1305 — Cifrado moderno usado en TLS 1.3, WireGuard y dispositivos móviles.")
        print(f"Salt (base64)         : {chacha_info['salt']}")
        print(f"Nonce (base64)        : {chacha_info['nonce']}")
        print(f"Ciphertext (base64)   : {chacha_info['ciphertext']}")
```

- Muestra:
    - Explicación breve de SHA-256 y SHA-512 y sus valores.
    - Explicación de AES-256-GCM y sus parámetros (salt, nonce, ciphertext).
    - Explicación de ChaCha20-Poly1305 y sus parámetros.

### 2.10 Caso `cipher == "aes"`

```python
    elif args.cipher == "aes":
        print("-" * 70)
        print("AES-256-GCM — Cifrado autenticado usado en HTTPS, VPNs y almacenamiento seguro.")
        print(f"Salt (base64)         : {aes_info['salt']}")
        print(f"Nonce (base64)        : {aes_info['nonce']}")
        print(f"Ciphertext (base64)   : {aes_info['ciphertext']}")
```

- Solo muestra la información de AES.

### 2.11 Caso `cipher == "chacha"`

```python
    elif args.cipher == "chacha":
        print("-" * 70)
        print("ChaCha20-Poly1305 — Cifrado moderno usado en TLS 1.3, WireGuard y dispositivos móviles.")
        print(f"Salt (base64)         : {chacha_info['salt']}")
        print(f"Nonce (base64)        : {chacha_info['nonce']}")
        print(f"Ciphertext (base64)   : {chacha_info['ciphertext']}")
```

- Solo muestra la información de ChaCha.

```python
    print("="*70)
```

- Línea final de cierre visual.

### 2.12 Guardado según modo

```python
    if args.save:
        if args.cipher == "none":
            guardar_en_archivo_modo_todo(
                usuario, contrasena, hash256, hash512,
                aes_info, chacha_info, passphrase
            )
        elif args.cipher == "aes":
            guardar_en_archivo_solo_aes(usuario, aes_info, passphrase)
        elif args.cipher == "chacha":
            guardar_en_archivo_solo_chacha(usuario, chacha_info, passphrase)
        print("✅ Resultado guardado correctamente en 'contrasenas_generadas.txt'")
```

- Si se pasó `-save`:
    - Llama a la función de guardado correspondiente al modo de cifrado.
    - Informa que se guardó correctamente.

```python
    print("¡Mantén esta información en un lugar seguro!\\n")
```

- Mensaje final de advertencia.

### 2.13 Bloque `if __name__ == "__main__":`

```python
if __name__ == "__main__":
    try:
        main()
    except KeyboardInterrupt:
        print("\\n\\n⚠️  Programa interrumpido por el usuario.")
        sys.exit(0)
    except Exception as e:
        print(f"\\n❌ Error inesperado: {e}")
        sys.exit(1)
```

- Si el archivo se ejecuta directamente:
    - Llama a `main()` dentro de un `try`.
    - Si el usuario interrumpe con Ctrl+C (`KeyboardInterrupt`), muestra mensaje y sale con código 0.
    - Si ocurre cualquier otra excepción, muestra el error y sale con código 1.

*****************
### Referencias Externas

- [Python Cryptography: Fernet vs AEAD](https://www.google.com/search?q=https://cryptography.io/en/latest/hazmat/primitives/ciphers/aead/)
    
- [NIST: Recommendation for Key Derivation (PBKDF2)](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-132.pdf)
    

### Documentación Relacionada

[[01 - Python para WSL]]
[[01 - Git]]
[[01 - WSL]]