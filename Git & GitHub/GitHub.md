# ¿Qué es GitHub?

GitHub es una plataforma basada en la nube que permite:
- Alojar repositorios Git
- Colaborar en proyectos
- Gestionar versiones de código y documentación
- Trabajar desde múltiples dispositivos
- Integrar automatizaciones (CI/CD)
- Publicar proyectos públicos o privados

GitHub es el estándar de la industria para Developers, DevOps, Security Specialist, Sysadmins y documentación técnica.

## Conceptos fundamentales

### Repositorio (Repo)

Es un proyecto almacenado en GitHub. Puede contener:
- Código
- Documentación
- Scripts
- Archivos Markdown
- Configuraciones

Puede ser **público** o **privado**.

### Branch (Rama)

Una línea de trabajo independiente dentro del repositorio. La rama principal suele llamarse:

```code
main
```

### Commit

Un “snapshot” o foto del estado del proyecto en un momento específico.

### Push

Envía tus commits locales hacia GitHub.

### Pull

Trae los cambios desde GitHub hacia tu máquina.

### Pull Request (PR)

Propuesta de cambios para revisión antes de integrarlos a la rama principal. Muy usado en equipos.

## Autenticación en GitHub

GitHub ya no permite contraseñas para operaciones Git. Los métodos válidos son:

### Token Personal (PAT)

Funciona como contraseña temporal. Ideal para WSL, scripts o entornos donde no usas GitHub Desktop.

### SSH

Método más seguro y permanente. Ideal para uso diario.

### GitHub CLI (gh)

Permite autenticación desde terminal con comandos más avanzados.

## Tipos de repositorios en GitHub

### Público

- Visible para todos
- Ideal para documentación técnica, proyectos open-source o portafolios
- Se puede clonar sin autenticación

### Privado

- Solo tú (y quienes invites) pueden verlo
- Ideal para proyectos internos o sensibles

## Estructura típica de un repositorio

```code
/carpeta-principal
  ├── README.md
  ├── .gitignore
  ├── /src
  ├── /docs
  └── /scripts
```

## Interacción básica con GitHub desde Git

### Subir cambios:

```bash
git add .
git commit -m "Mensaje"
git push
```

### Descargar cambios:

```bash
git pull
```

### Clonar un repositorio:

```bash
git clone https://github.com/usuario/repositorio.git
```

## GitHub + WSL (puntos clave)

- WSL es un entorno Linux separado
- Necesita su propia configuración de Git
- Necesita su propio token o claves SSH
- No comparte credenciales con Windows
- No usa GitHub Desktop

Por eso es importante documentar:
- Cómo generar un token
- Cómo guardarlo
- Cómo usar SSH
- Cómo configurar Git dentro de WSL

## ¿Para qué uso GitHub en mi día a día?

- Respaldar documentación técnica
- Acceder a mi documentación desde cualquier PC
- Mantener historial de cambios
- Sincronizar mi vault de Obsidian
- Tener un repositorio público como referencia profesional
- Practicar buenas prácticas de control de versiones
