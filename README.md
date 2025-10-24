# Repositorio: Dockerización-PyBullet

Este repositorio agrupa tres simulaciones desarrolladas con **PyBullet** (Carro, Brazo y Bípedo), cada una ejecutable en su propio contenedor Docker. Incluye la estructura, el código base y los archivos necesarios para dockerizar y ejecutar las simulaciones.

---

## Estructura del repositorio

```
Dockerizacion-PyBullet/
├── README.md
├── .gitignore
├── carro/
│   ├── carro.py
│   └── Dockerfile
├── brazo/
│   ├── brazo.py
│   └── Dockerfile
├── bipedo/
│   ├── biped.py
│   ├── biped2d.urdf
│   └── Dockerfile
└── docker-compose.yml  # opcional
```

---

## README.md

````markdown
# Dockerización del Carro, Brazo y Bípedo (PyBullet)

Este proyecto contiene tres simulaciones diferentes desarrolladas con **PyBullet**, cada una dockerizada de forma independiente.

## Simulaciones incluidas

- **Carro**: Simula el movimiento de un vehículo básico con PyBullet.
- **Brazo**: Control de articulaciones y movimiento de un brazo robótico.
- **Bípedo**: Modelo 2D de un robot bípedo con su archivo URDF.

## Cómo ejecutar

1. Clonar el repositorio
```bash
git clone <URL_DEL_REPO>
cd Dockerizacion-PyBullet
````

2. Construir las imágenes Docker

```bash
docker build -t pybullet-carro ./carro
docker build -t pybullet-brazo ./brazo
docker build -t pybullet-bipedo ./bipedo
```

3. Ejecutar cada contenedor

```bash
# Carro
docker run --rm pybullet-carro

# Brazo
docker run --rm pybullet-brazo

# Bípedo
docker run --rm pybullet-bipedo
```

## Cómo funciona la dockerización

Dockerizar una simulación significa encapsular todas las dependencias del entorno dentro de un contenedor. En este caso, cada simulación utiliza una imagen base de Python y la librería **PyBullet** para la simulación física.

El flujo de trabajo es:

1. Crear un `Dockerfile` que instale las dependencias necesarias.
2. Copiar el código de la simulación al contenedor.
3. Construir la imagen con `docker build`.
4. Ejecutar el contenedor con `docker run`, obteniendo un entorno aislado y reproducible.

## Ejemplo de visualización

Cuando se ejecuta cada simulación, se abre una ventana de PyBullet mostrando el entorno 3D del modelo correspondiente:

* El **carro** se desplaza sobre una superficie plana.
* El **brazo** realiza movimientos de articulación básicos.
* El **bípedo** muestra la estructura física definida por su archivo URDF.

---

## .gitignore

```
__pycache__/
*.pyc
*.pyo
.vscode/
.DS_Store
```

---

## Código base de las simulaciones

### Archivo: carro/carro.py

```python
import pybullet as p
import pybullet_data
import time

physicsClient = p.connect(p.GUI)
p.setAdditionalSearchPath(pybullet_data.getDataPath())
p.setGravity(0, 0, -9.8)
plane = p.loadURDF("plane.urdf")
car = p.loadURDF("racecar/racecar.urdf", basePosition=[0, 0, 0.2])

for i in range(1000):
    p.stepSimulation()
    time.sleep(1./240.)

p.disconnect()
```

### Archivo: brazo/brazo.py

```python
import pybullet as p
import pybullet_data
import time

physicsClient = p.connect(p.GUI)
p.setAdditionalSearchPath(pybullet_data.getDataPath())
p.setGravity(0, 0, -9.8)
plane = p.loadURDF("plane.urdf")
arm = p.loadURDF("kuka_iiwa/model.urdf", basePosition=[0, 0, 0])

for i in range(5000):
    p.setJointMotorControl2(arm, 2, p.POSITION_CONTROL, targetPosition=0.5)
    p.stepSimulation()
    time.sleep(1./240.)

p.disconnect()
```

### Archivo: bipedo/biped.py

```python
import pybullet as p
import pybullet_data
import time

physicsClient = p.connect(p.GUI)
p.setAdditionalSearchPath(pybullet_data.getDataPath())
p.setGravity(0, 0, -9.8)
plane = p.loadURDF("plane.urdf")
biped = p.loadURDF("biped2d.urdf", basePosition=[0, 0, 1])

for i in range(3000):
    p.stepSimulation()
    time.sleep(1./240.)

p.disconnect()
```

### Archivo: bipedo/biped2d.urdf

```xml
<?xml version="1.0" ?>
<robot name="biped2d">
  <link name="base_link">
    <visual>
      <geometry>
        <box size="0.2 0.05 0.4"/>
      </geometry>
    </visual>
    <collision>
      <geometry>
        <box size="0.2 0.05 0.4"/>
      </geometry>
    </collision>
    <inertial>
      <mass value="1"/>
    </inertial>
  </link>
</robot>
```

---

## Dockerfiles

### Archivo: carro/Dockerfile

```dockerfile
FROM python:3.11-slim

RUN apt-get update && apt-get install -y --no-install-recommends \
    libgl1-mesa-glx \
    libosmesa6-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app
COPY carro.py ./
RUN pip install --no-cache-dir pybullet
CMD ["python", "carro.py"]
```

### Archivo: brazo/Dockerfile

```dockerfile
FROM python:3.11-slim

RUN apt-get update && apt-get install -y --no-install-recommends \
    libgl1-mesa-glx \
    libosmesa6-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app
COPY brazo.py ./
RUN pip install --no-cache-dir pybullet
CMD ["python", "brazo.py"]
```

### Archivo: bipedo/Dockerfile

```dockerfile
FROM python:3.11-slim

RUN apt-get update && apt-get install -y --no-install-recommends \
    libgl1-mesa-glx \
    libosmesa6-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app
COPY biped.py ./
COPY biped2d.urdf ./
RUN pip install --no-cache-dir pybullet
CMD ["python", "biped.py"]
```

---

## docker-compose.yml (opcional)

```yaml
version: '3.8'
services:
  carro:
    build: ./carro
    container_name: carro_sim
    environment:
      - DISPLAY=${DISPLAY}
    volumes:
      - /tmp/.X11-unix:/tmp/.X11-unix

  brazo:
    build: ./brazo
    container_name: brazo_sim
    environment:
      - DISPLAY=${DISPLAY}
    volumes:
      - /tmp/.X11-unix:/tmp/.X11-unix

  bipedo:
    build: ./bipedo
    container_name: bipedo_sim
    environment:
      - DISPLAY=${DISPLAY}
    volumes:
      - /tmp/.X11-unix:/tmp/.X11-unix
```

---

## Créditos

Proyecto desarrollado por:

* **Samuel Parra (@samuel12148)**
* **Miguel Caro (@MiguelCaro06)**

© 2025 GitHub, Inc.
