## 1. Identificación

- Proyecto: CARNADA
- Tipo de nota: Índice técnico
- Área: Python / CLI / Ciberseguridad defensiva
- Estado: Documentación base
- Autor: Rhodyn Ildefonso (@beathunterzero)
- Relación con: Proyecto personal en GitHub
- Enfoque: Herramienta local para generación y evaluación de contraseñas

---

## 2. Propósito

Esta nota funciona como índice principal para documentar el proyecto CARNADA dentro del vault de Obsidian.

El objetivo es centralizar la documentación técnica, decisiones de diseño, arquitectura, uso desde shell, consideraciones de seguridad y lecciones aprendidas durante la evolución del proyecto.

---

## 3. Descripción General

CARNADA es una herramienta CLI escrita en Python para generar contraseñas seguras y evaluar contraseñas existentes de forma local.

El proyecto fue rediseñado para mantener una superficie de riesgo reducida. Por esa razón, la herramienta no almacena secretos, no cifra archivos, no genera hashes de contraseñas, no usa dependencias externas y no realiza conexiones de red.

Su valor principal está en ofrecer una utilidad simple, auditable y práctica para flujos de trabajo desde terminal.

---

## 4. Alcance del Proyecto

CARNADA permite:

- generar contraseñas seguras desde la shell;
- usar perfiles de generación según el contexto;
- generar múltiples contraseñas;
- calcular una entropía aproximada;
- evaluar contraseñas localmente;
- usar salida normal, silenciosa o JSON.

CARNADA no busca ser:

- un gestor de contraseñas;
- una bóveda de secretos;
- una herramienta de cifrado;
- un sistema de almacenamiento de credenciales;
- una solución empresarial de gestión de contraseñas.

---

## 5. Componentes Principales

El proyecto se compone de los siguientes elementos:

- `carnada.py`: script principal de la herramienta.
- `README.md`: documentación pública del repositorio.
- `docs/usage.md`: guía de uso.
- `docs/security-notes.md`: notas de seguridad.
- `docs/architecture/overview.md`: descripción de arquitectura.
- `docs/architecture/carnada-architecture.png`: diagrama visual.
- `examples/usage-examples.md`: ejemplos prácticos de ejecución.

---

## 6. Enfoque Técnico

El diseño actual de CARNADA sigue estos principios:

- ejecución local;
- cero almacenamiento de contraseñas;
- cero comunicación de red;
- cero dependencias externas;
- salida limpia para terminal;
- compatibilidad con shell scripting;
- uso de la librería estándar de Python;
- separación lógica entre generación, análisis, entropía y formato de salida.

---

## 7. Valor del Proyecto

El valor de CARNADA no está en competir con gestores de contraseñas maduros, sino en funcionar como una herramienta pequeña, clara y auditable.

Desde una perspectiva de ciberseguridad defensiva, el proyecto demuestra buenas decisiones de diseño:

- reducir funciones innecesarias;
- evitar almacenamiento de secretos;
- eliminar cifrado innecesario cuando no hay persistencia;
- evitar dependencias de terceros;
- documentar límites y riesgos;
- mantener una salida controlada para CLI.

---

## 8. Conclusión Operativa

CARNADA es un proyecto personal pequeño, pero técnicamente bien delimitado.

Su alcance reducido es una decisión de seguridad, no una limitación accidental.

El proyecto sirve como ejemplo de cómo transformar un script inicial de laboratorio en una herramienta CLI más profesional, simple y defendible.

---

### Documentación relacionada

[[02 - Arquitectura de CARNADA]]
[[03 - Uso de CARNADA desde la Shell]]
[[04 - Perfiles de Generación de Contraseñas]]
[[05 - Notas de Seguridad de CARNADA]]
[[06 - Ejemplos Prácticos de Uso]]
[[07 - Decisiones Técnicas del Proyecto]]
[[08 - Lecciones Aprendidas]]
[[09 - Explicación del código de CARNADA]]
[[01 - Python para WSL]]
[[02 - Estructura de proyectos en Python]]
[[05 - Estructura de un repositorio]]
