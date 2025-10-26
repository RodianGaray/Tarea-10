# Repositorio: Dockerización-PyBullet

Este repositorio agrupa tres simulaciones desarrolladas con **PyBullet** (Carro, Brazo y Bípedo), cada una ejecutable en su propio contenedor Docker. Incluye la estructura, el código base y los archivos necesarios para dockerizar y ejecutar las simulaciones.

---
# Proyecto PyBullet — Robots de ejemplo

Repositorio con tres ejemplos en PyBullet: **Bípedo**, **Brazo** y **Carro**.

## Estructura del repositorio
- /bipedo/
  - biped2d.urdf
  - bipedo.py
- /brazo/
  - brazo.py
- /carro/
  - carro.py
- Dockerfile
- run_examples.sh
- .gitignore

## Requisitos
- Docker instalado (alternativa: Python 3.9+ y `pip install pybullet numpy`)

## Construir la imagen Docker
Desde la raíz del proyecto:
docker build -t robots_pybullet .

## Ejecutar el contenedor e interactuar
Ejemplo (interactivo, elimina el contenedor al salir):
docker run -it --rm robots_pybullet

Dentro del contenedor puedes ejecutar:
python3 bipedo/bipedo.py
python3 brazo/brazo.py
python3 carro/carro.py

O ejecutar los tres ejemplos en modo secuencial (headless):
./run_examples.sh

### Nota sobre visualización (GUI)
Los scripts por defecto usan `DIRECT` (sin ventana). Si quieres ver la GUI (windowed):
- En tu host Linux con X11: permite conexiones X (`xhost +local:root`) y ejecuta Docker montando `/tmp/.X11-unix` y pasando `-e DISPLAY`:
  docker run -it --rm -e DISPLAY=$DISPLAY -v /tmp/.X11-unix:/tmp/.X11-unix robots_pybullet
- Dentro del contenedor, cambia `p.connect(p.DIRECT)` por `p.connect(p.GUI)` en los scripts o ejecuta la misma versión que ya tiene la opción de GUI si la detecta.

## Qué hace cada ejemplo (resumen)
- **Bípedo**: carga un URDF simple (`biped2d.urdf`), aplica gravedad y avanza la simulación; muestra posición del robot.
- **Brazo**: crea un brazo simplificado (3 links) programáticamente y aplica una secuencia sencilla de movimientos (articular).
- **Carro**: crea un cuerpo con "ruedas" (cuerpos enlazados) y aplica una fuerza/velocidad en X; muestra posición.

## Archivos importantes
- Dockerfile: imagen base y pip install de dependencias.
- run_examples.sh: ejecuta bipedo, brazo y carro (modo headless).
- Añade tus imágenes en `images/` y enlázalas en README si deseas.

## Mejoras sugeridas
- Añadir URDFs más completos y controladores PID.
- Añadir script `docker-compose` o CI para construir imagen y tests.

## Créditos

Proyecto desarrollado por:

* **Samuel Parra (@samuel12148)**
* **Miguel Caro (@MiguelCaro06)**

© 2025 GitHub, Inc.
