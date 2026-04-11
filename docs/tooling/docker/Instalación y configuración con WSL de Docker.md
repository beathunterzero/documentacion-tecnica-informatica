## Objetivo del entorno

Usar Docker desde Ubuntu WSL2 **sin abrir Docker Desktop**, con consumo mínimo de recursos y sin procesos innecesarios en Windows.

## Instalación de Docker Desktop (Windows)

### Descargar

Desde la página oficial de Docker.

### Instalar con estas opciones activadas:

|Opción|Estado|Motivo|
|---|---|---|
|Use WSL 2 instead of Hyper‑V|✔️|Requerido para integración con WSL|
|Enable WSL Integration|✔️|Permite usar Docker desde Ubuntu|
|Add docker.internal names to hosts|✔️|Networking|
|Use containerd for pulling/storing images|✔️|Más eficiente|
|Start Docker Desktop when you sign in|❌|Evita procesos en segundo plano|
|Open Docker Dashboard when Docker starts|❌|No queremos UI|
|Enable Scout / SBOM|❌|Ahorra recursos|
|Send usage statistics|Opcional|No afecta|

### Reiniciar Windows

Para que WSL2 y Docker Desktop se integren correctamente.

## Configuración de WSL Integration

En Docker Desktop → Settings → Resources → WSL Integration:
- Activar **Ubuntu**
- Desactivar otras distros (opcional)
- Enable integration with default distro

Cerrar Docker Desktop.
## Verificar Docker desde Ubuntu WSL

```bash
docker --version
docker info
```

## Probar instalación

```bash
docker run hello-world
```

Salida esperada:

```code
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

## Troubleshooting inicial

### Docker no responde en WSL

```bash
dockerstop
wsl --shutdown
dockerstart --background
```

### Error: Cannot connect to the Docker daemon

```bash
sudo usermod -aG docker $USER
```

Cerrar y abrir WSL.

### Error de permisos en `/var/run/docker.sock`

```bash
sudo chmod 666 /var/run/docker.sock
```

*********
## Puedes ver también:
[[Automatización para Docker en PowerShell]]

