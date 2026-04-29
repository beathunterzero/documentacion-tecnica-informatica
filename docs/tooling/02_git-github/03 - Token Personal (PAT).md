## 1. Definición y necesidad técnica

Un Personal Access Token (PAT) es una credencial generada en GitHub que reemplaza el uso de contraseñas para autenticación en operaciones Git sobre HTTPS.

Tras la eliminación del soporte de contraseñas en GitHub, el uso de PAT es obligatorio cuando no se emplea autenticación mediante SSH.

Permite además definir permisos específicos sobre repositorios y operaciones, lo que mejora el control de acceso.

---

## 2. Proceso de generación en GitHub

Ruta de creación:  
Settings > Developer Settings > Personal Access Tokens

---

### 2.1 Tipos de tokens

Fine-grained

- Permisos específicos por repositorio y acción
    
- Recomendado para mayor seguridad
    

Classic

- Permisos globales para la cuenta
    
- Útil para compatibilidad con herramientas antiguas
    

---

### 2.2 Configuración de permisos

Para uso estándar en desarrollo y seguridad:

Classic

- Scope: repo
    

Fine-grained

- Repository permissions:
    
    - Contents (Read and Write)
        
    - Metadata (Read)
        

Expiración

- Se recomienda un máximo de 90 días
    

Nota crítica

- El token solo se muestra una vez al crearlo
    

---

## 3. Autenticación en WSL mediante HTTPS

Al ejecutar comandos como git push, se solicitarán credenciales:

Username

- Usuario de GitHub
    

Password

- Token generado (no se mostrará en pantalla al pegarlo)
    

---

## 4. Persistencia de credenciales en Linux

Para evitar introducir el token manualmente en cada operación, se utiliza un credential helper.

---

### 4.1 Uso del método store

```bash
# Configurar almacenamiento de credenciales
git config --global credential.helper store

# Ejecutar operación para guardar credenciales
git push origin main
```

---

### 4.2 Archivo de credenciales

Ubicación:  
~/.git-credentials

Formato:  
[https://usuario:token@github.com](https://usuario:token@github.com/)

Advertencia

- El archivo no está cifrado
    
- Debe protegerse el acceso al entorno
    

---

## 5. Mantenimiento y renovación

Cuando el token expira o se invalida, Git mostrará errores de autenticación.

Flujo de renovación:

1. Generar un nuevo token en GitHub
    
2. Eliminar credenciales antiguas
    

```bash
# Si se usa cache
git credential-cache exit

# Eliminar archivo de credenciales
rm ~/.git-credentials
```

3. Re-autenticación
    

- Ejecutar git push e ingresar el nuevo token
    

---

### Referencias externas

[https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)  
[https://git-scm.com/docs/git-credential-store](https://git-scm.com/docs/git-credential-store)  
[https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/token-expiration-and-revocation](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/token-expiration-and-revocation)

---

### Documentación relacionada

[[01 - Git]]  
[[02 - GitHub]]  
[[04 - Autenticación SSH con GitHub]]  
[[09 - Buenas prácticas y manejos avanzado]]