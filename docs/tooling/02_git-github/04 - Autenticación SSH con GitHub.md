## 1. Definición y fundamentos

La autenticación mediante SSH (Secure Shell) utiliza criptografía asimétrica para establecer una conexión segura entre el entorno local (por ejemplo, WSL) y los servidores de GitHub.

A diferencia del uso de tokens (PAT), SSH permite una autenticación persistente sin necesidad de ingresar credenciales en cada operación, lo que mejora la seguridad y la eficiencia operativa.

---

## 2. Fase de preparación: verificación de existencia

Antes de generar nuevas claves, es necesario verificar si ya existen claves SSH en el sistema para evitar sobrescritura.

---

### 2.1 Auditoría del directorio

```bash
ls -la ~/.ssh
```

Escenario sin claves

- No existe el directorio .ssh
    

Escenario con claves existentes

- Archivos como id_ed25519, id_rsa y sus correspondientes .pub
    

---

## 3. Generación de claves (Ed25519)

El algoritmo Ed25519 es el recomendado por su seguridad y rendimiento.

```bash
ssh-keygen -t ed25519 -C "tu_correo@ejemplo.com"
```

Ruta de almacenamiento

- Presionar ENTER para usar la ubicación por defecto
    

Passphrase

Sin contraseña (recomendado en entornos personales)

- Permite automatización sin intervención
    

Con contraseña

- Mayor seguridad, pero requiere interacción o uso de ssh-agent
    

---

## 4. Gestión del agente SSH

El agente SSH mantiene las claves privadas cargadas en memoria.

---

### 4.1 Inicialización y carga

```bash
# Iniciar agente
eval "$(ssh-agent -s)"

# Agregar clave privada
ssh-add ~/.ssh/id_ed25519
```

Resultado esperado

- Confirmación de identidad cargada
    

---

## 5. Registro de la clave pública en GitHub

La clave pública se registra en GitHub para permitir la autenticación.

1. Obtener contenido  
    cat ~/.ssh/id_ed25519.pub
    
2. Copiar la clave completa
    
3. Configuración en GitHub
    
    - Settings > SSH and GPG Keys
        
    - New SSH Key
        
    - Tipo: Authentication Key
        
    - Pegar clave y guardar
        

---

## 6. Validación de conexión

```bash
ssh -T git@github.com
```

Primera conexión

- Confirmar autenticidad del host con yes
    

Respuesta esperada

- Mensaje de autenticación exitosa
    

---

## 7. Migración de repositorios (HTTPS a SSH)

---

### 7.1 Ubicación

Ubicarse dentro del repositorio (directorio que contiene .git):

```bash
cd ~/ruta/a/tu/proyecto
```

---

### 7.2 Cambio de URL remota

```bash
# Verificar URL actual
git remote -v

# Cambiar a SSH
git remote set-url origin git@github.com:usuario/nombre-repo.git

# Validar cambio
git remote -v
```

---

## 8. Buenas prácticas y troubleshooting

Permisos

- Directorio ~/.ssh con permisos 700
    
- Clave privada con permisos 600
    

Backup

- Guardar copia de ~/.ssh en un entorno seguro
    

Unicidad

- Generar una clave distinta por dispositivo
    

Notas

- Revisar configuración si existen problemas de conexión o autenticación
    

---

### Referencias externas

[https://docs.github.com/en/authentication/connecting-to-github-with-ssh](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)  
[https://www.ssh.com/academy/ssh/keygen#ed25519-keys](https://www.ssh.com/academy/ssh/keygen#ed25519-keys)  
[https://wiki.archlinux.org/title/SSH_keys](https://wiki.archlinux.org/title/SSH_keys)

---

### Documentación relacionada

[[01 - Git]]  
[[02 - GitHub]]  
[[03 - Token Personal (PAT)]]  
[[09 - Buenas prácticas y manejos avanzado]]