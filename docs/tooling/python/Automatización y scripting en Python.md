## Scripts simples

```python
print("Hola mundo")
```

## Uso de argparse

```python
import argparse

parser = argparse.ArgumentParser()
parser.add_argument("--name")
args = parser.parse_args()

print(f"Hola {args.name}")
```

## Automatización de tareas

Ejemplos:
- Limpieza de logs
- Procesamiento de archivos
- Scraping
- Parsers de IoCs
- Scripts para CTI/CTH

## Integración con PowerShell

Ejecutar script:

```powershell
python .\src\main.py --name Rhodyn
```

## Integración con WSL

Ejecutar script desde Windows:

```powershell
wsl python3 /home/rhodyn/proyecto/src/main.py
```

## Buenas prácticas

- Mantener scripts en `src/scripts/`
- Usar argparse siempre
- Evitar rutas absolutas
- Documentar comandos en README

*************************
## También puedes ver:
[[Mantenimiento de distribuciones]]