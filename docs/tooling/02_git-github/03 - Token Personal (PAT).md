## 1. Definición y Necesidad Técnica

Un **Personal Access Token (PAT)** es una credencial alfanumérica generada por GitHub que actúa como sustituto de la contraseña tradicional para la autenticación en operaciones de Git sobre HTTPS.

Desde la deprecación de las contraseñas para operaciones Git, el PAT es obligatorio en entornos donde no se utiliza SSH, permitiendo un control granular sobre qué permisos tiene cada aplicación o terminal sobre tus repositorios.

## 2. Proceso de Generación en GitHub

La creación de un token se realiza desde la interfaz web de GitHub siguiendo esta ruta:

**Settings** > **Developer Settings** > **Personal Access Tokens**.

### 2.1 Tipos de Tokens

|**Tipo**|**Características**|**Recomendación**|
|---|---|---|
|**Fine-grained**|Permisos específicos por repositorio y por acción.|Ideal para máxima seguridad.|
|**Classic**|Permisos generales para toda la cuenta.|Ideal para compatibilidad con herramientas antiguas.|

### 2.2 Configuración de Alcance (Scopes)

Para operaciones estándar de CTH y desarrollo, se deben asignar los siguientes permisos mínimos:

- **Classic:** Seleccionar el scope `repo` (incluye control total de repositorios privados).
    
- **Fine-grained:** * **Repository permissions:** `Contents` (Read & Write) y `Metadata` (Read-only/Write).
    
- **Expiración:** Se recomienda un máximo de 90 días para equilibrar seguridad y comodidad.
    

> **Nota Crítica:** GitHub solo mostrará el token **una vez**. Si se pierde sin haber sido guardado, deberá ser regenerado.

## 3. Autenticación en WSL mediante HTTPS

Al ejecutar un comando que interactúe con el servidor (ej. `git push`), la terminal solicitará credenciales:

1. **Username:** Tu nombre de usuario de GitHub (ej. `beathunterzero`).
    
2. **Password:** Pega el **Token (PAT)** generado (el cursor no se moverá por seguridad).
    

## 4. Persistencia de Credenciales en Linux

Para evitar la introducción manual del token en cada operación, se debe configurar el **Credential Helper**.

### 4.1 Uso del Almacén `store`

Este método guarda tus credenciales en un archivo de texto dentro de tu home en WSL.

Bash

```bash
# 1. Configurar el ayudante de credenciales global
git config --global credential.helper store

# 2. Ejecutar una operación de red para disparar el guardado
git push origin main
```

### 4.2 Anatomía del Archivo de Credenciales

El token se almacena físicamente en: `~/.git-credentials`.

- **Formato interno:** `https://usuario:token@github.com`
    
- **Advertencia de Seguridad:** Este archivo **no está cifrado**. Cualquier persona con acceso a tu terminal de WSL podría leer el token. Mantén tu sesión protegida.
    

## 5. Mantenimiento y Renovación

Los tokens caducan según la fecha elegida. Cuando Git devuelva un error de `Authentication failed`, sigue este flujo:

1. **Generar** un nuevo token en la web de GitHub.
    
2. **Limpiar** la credencial antigua en WSL:
    
    Bash
    
    ```bash
    git credential-cache exit  # Si usas cache
    # O simplemente edita/borra el archivo:
    rm ~/.git-credentials
    ```
    
3. **Re-autenticar:** Ejecuta `git push`, introduce el nuevo PAT y Git lo almacenará automáticamente de nuevo.
    

---

### Referencias Externas

- [GitHub: Creating a personal access token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)
    
- [Git Scm: git-credential-store documentation](https://git-scm.com/docs/git-credential-store)
    
- [GitHub: Token expiration and revocation policy](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/token-expiration-and-revocation)
    

### Documentación Relacionada

[[01 - Git]]
[[02 - GitHub]]
[[04 - Autenticación SSH con GitHub]]
[[08 - Buenas prácticas y manejos avanzado]]