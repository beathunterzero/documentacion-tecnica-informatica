# Buenas prácticas para Docker en general
## No usar Docker Desktop UI

Aunque Docker Desktop es cómodo visualmente, **no es ideal para entornos técnicos avanzados**.

### Problemas principales

- **Consume más RAM** La interfaz gráfica y los servicios adicionales cargan procesos innecesarios.
- **Consume más CPU** Docker Desktop ejecuta servicios en segundo plano que aumentan el uso del procesador.
- **No aporta valor técnico real** Todo lo que hace la UI puede hacerse más rápido y mejor desde CLI.

### Recomendación

Usar **Docker Engine + WSL2** o Docker nativo en Linux para un entorno más limpio y eficiente.

## Usar WSL2 como backend

WSL2 es la forma más eficiente de ejecutar Docker en Windows.

### Ventajas

- **Más rápido** WSL2 usa un kernel Linux real, lo que mejora el rendimiento de contenedores.
- **Más ligero** No requiere máquinas virtuales pesadas como Hyper-V.
- **Mejor integración con Linux** Permite usar herramientas nativas, scripts y entornos reales de Linux.

### Recomendación

Instalar Docker Engine dentro de WSL2 y evitar Docker Desktop cuando sea posible.

## Mantener solo imágenes necesarias

Las imágenes ocupan espacio rápidamente. Mantener solo las necesarias evita desperdicio de almacenamiento.

```bash
docker image prune
```

### Qué hace:

- Elimina imágenes **dangling** (sin etiqueta).
- Libera espacio sin afectar imágenes en uso.

### Consejo:

Ejecutarlo semanalmente o después de pruebas grandes.

## Limpiar contenedores detenidos regularmente

Los contenedores detenidos no aportan nada y ocupan espacio.

```bash
docker container prune
```

### Qué hace:

- Elimina todos los contenedores **detenidos**.
- Mantiene tu entorno limpio y ordenado.

### Consejo:

Ideal después de laboratorios o pruebas rápidas.

## Nombrar contenedores siempre

Nunca dejes que Docker asigne nombres aleatorios como `sleepy_morse`.

```bash
docker run -it --name debianlab debian bash
```

### Beneficios:

- Facilita detener, iniciar o inspeccionar contenedores.
- Mejora la documentación.
- Evita confusiones en entornos con múltiples contenedores.

## Usar `-it` para laboratorios interactivos

Cuando necesitas una terminal dentro del contenedor:

```bash
docker run -it kali-rolling bash
```

### ¿Por qué?

- `-i` mantiene STDIN abierto.
- `-t` asigna una pseudo-terminal.
- Permite trabajar como si fuera una máquina real.

Ideal para:
- Pentesting
- Análisis manual
- Pruebas de herramientas

## Evitar contenedores huérfanos

Un contenedor huérfano es uno que queda ejecutándose sin supervisión.

### Siempre detenerlos:

```bash
docker stop <id>
```

### Riesgos de dejarlos vivos:

- Consumo innecesario de recursos.
- Puertos abiertos sin control.
- Procesos corriendo sin monitoreo.
- Riesgos de seguridad en entornos sensibles.

# Buenas prácticas para CTI/CTH

Estas recomendaciones son clave para entornos de ciberseguridad, análisis de amenazas y simulación de TTPs.

## Para análisis de malware

El objetivo es aislar completamente el entorno.

### Recomendaciones:

- **Usar contenedores aislados** Sin redes externas o con redes controladas.
- **No montar volúmenes del host** Evita que el malware acceda a tu sistema real.
- **No exponer puertos innecesarios** Reduce superficie de ataque.

### Opciones útiles:

```bash
docker run --network none ...
docker run -v /dev/null:/host ...
```

## Para reproducir TTPs (Técnicas, Tácticas y Procedimientos)

Ideal para laboratorios de Red Team, Purple Team o simulaciones MITRE ATT&CK.

### Recomendaciones:

- **Usar imágenes ligeras** `alpine`, `debian`, `ubuntu-minimal` → más rápidas y fáciles de resetear.
- **Crear contenedores efímeros** No guardar estado, siempre recrear desde cero.
- **Documentar comandos** Para reproducibilidad y auditoría.

### Ejemplo de contenedor efímero:

```bash
docker run --rm -it debian bash
```

## Para OSINT

Docker es ideal para aislar herramientas de OSINT.

### Recomendaciones:

- **Usar contenedores con herramientas específicas** Ejemplo: contenedores para Maltego, Spiderfoot, Recon-ng.
- **Evitar instalar herramientas en el host** Mantiene tu sistema limpio y evita conflictos.
- **Crear entornos reproducibles** Facilita compartir configuraciones con equipos.

************
## También puedes ver:
[[Comandos esenciales en Docker]]