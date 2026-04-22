## 1\. Definición y Fundamentos

La autenticación mediante **SSH (Secure Shell)** utiliza criptografía asimétrica para establecer una conexión segura entre tu instancia de WSL y los servidores de GitHub. A diferencia de los tokens (PAT), las claves SSH permiten una comunicación persistente, más segura y sin necesidad de interactuar con contraseñas en cada operación de Git.

## 2\. Fase de Preparación: Verificación de Existencia

Antes de generar nuevos artefactos, es imperativo auditar el estado actual del directorio de configuración SSH para evitar sobrescribir claves activas.

### 2.1 Auditoría del Directorio

Ejecuta el siguiente comando en tu terminal de Ubuntu:

```bash
ls -la ~/.ssh
```

  * **Escenario A (Sin claves):** Si recibes `ls: cannot access '/home/rhodyn/.ssh': No such file or directory`, el entorno está limpio para comenzar.
  * **Escenario B (Claves existentes):** Si visualizas archivos con extensión `.pub` (pública) y archivos sin extensión (privada) como `id_ed25519` o `id_rsa`, ya posees credenciales.

## 3\. Generación de Claves (Algoritmo Ed25519)

GitHub recomienda el uso de **Ed25519** por su alta resistencia criptográfica y menor latencia en comparación con RSA.

### 3.1 Comando de Generación

```bash
ssh-keygen -t ed25519 -C "tu_correo@ejemplo.com"
```

  * **Ruta de almacenamiento:** Al solicitar `Enter file in which to save the key`, presiona **ENTER** para aceptar la ubicación estándar.
  * **Manejo de Passphrase:** \* **Opción Sin Contraseña (Recomendada para WSL):** Presiona **ENTER** dos veces. Esto permite que Git realice operaciones (push/pull) de forma totalmente automatizada.
      * **Opción Con Contraseña:** Introduce una clave. Git la solicitará cada vez que intentes conectar, a menos que uses el agente SSH de forma persistente.

## 4\. Gestión del Agente SSH

El agente es el proceso encargado de mantener tus claves privadas cargadas en memoria y listas para su uso.

### 4.1 Inicialización y Carga

```bash
# Iniciar el daemon del agente
eval "$(ssh-agent -s)"

# Vincular tu clave privada al agente
ssh-add ~/.ssh/id_ed25519
```

  * **Verificación:** Al finalizar, deberías ver el mensaje: `Identity added: /home/usuario/.ssh/id_ed25519`.

## 5\. Registro de la Clave Pública en GitHub

La **clave pública** es la que compartes con el servidor; la privada **jamás** debe salir de tu máquina.

1.  **Obtener contenido:** `cat ~/.ssh/id_ed25519.pub`
2.  **Copiar:** Selecciona toda la cadena que inicia con `ssh-ed25519` y termina con tu correo.
3.  **Configurar en Web:** \* Ve a **GitHub \> Settings \> SSH and GPG Keys**.
      * Click en **New SSH Key**.
      * **Título:** Ej. "Ubuntu WSL - beathunterzero".
      * **Key Type:** Authentication Key.
      * **Key:** Pega el contenido copiado y guarda.

## 6\. Validación Técnica de la Conexión

Para confirmar que GitHub reconoce tu identidad:

```bash
ssh -T git@github.com
```

  * **Advertencia inicial:** Verás un mensaje sobre la autenticidad del host. Escribe **`yes`** y presiona ENTER.
  * **Respuesta exitosa:** `Hi [Nombre]! You've successfully authenticated, but GitHub does not provide shell access.`

## 7\. Migración de Repositorios (HTTPS a SSH)

Si ya tienes proyectos trabajando con HTTPS (pidiendo tokens), debes "migrarlos" a SSH.

### 7.1 Requisito Crítico: Ubicación

Debes estar **dentro de la carpeta del proyecto** que contiene el directorio oculto `.git`.

```bash
cd ~/ruta/a/tu/proyecto
```

  * Si ejecutas comandos de Git fuera, obtendrás: `fatal: not a git repository`.

### 7.2 Cambio de URL Remota

```bash
# 1. Verificar URL actual (debería mostrar https://...)
git remote -v

# 2. Cambiar URL a formato SSH
git remote set-url origin git@github.com:usuario/nombre-repo.git

# 3. Validar cambio (debería mostrar git@github.com:...)
git remote -v
```

## 8\. Resumen de Buenas Prácticas y Troubleshooting

  * **Error de Permisos:** Si el socket falla, usa `sudo chmod 666 /var/run/docker.sock` (si aplica) o revisa los permisos de `~/.ssh` (deben ser `700` para la carpeta y `600` para la clave privada).
  * **Backup:** Si formateas tu PC, perderás el acceso SSH. Guarda una copia de `~/.ssh` en un lugar seguro.
  * **Unicidad:** No uses la misma clave para múltiples dispositivos; genera una nueva por cada equipo.

-----

### Referencias Externas

  * [GitHub Documentation: Connecting with SSH](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)
  * [SSH Academy: The Ed25519 Algorithm](https://www.google.com/search?q=https://www.ssh.com/academy/ssh/keygen%23ed25519-keys)
  * [Arch Wiki: SSH Keys Security](https://wiki.archlinux.org/title/SSH_keys)

### Documentación Relacionada

[[01 - Git]]
[[02 - GitHub]]
[[03 - Token Personal (PAT)]]
[[08 - Buenas prácticas y manejos avanzado]]