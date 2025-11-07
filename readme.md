# ForestSegmentationInterfaces

Paquete **ROS 2** de *interfaces* (acciones/mensajes) para la captura sincronizada de datos en proyectos de
segmentación forestal y prevención de incendios.  
Provee la acción **`TakeSnapshot`** que utilizan otros nodos (p. ej., servidor/cliente de *snapshots*)
para guardar de forma coordinada Imagen + LiDAR + GPS y metadatos.

---

## 📦 Contenido del paquete

```

forest_segmentation_interfaces/
├── action/
│   └── TakeSnapshot.action
├── CMakeLists.txt
└── package.xml

```

### Acción: `TakeSnapshot`

**Nombre de tipo completo**: `forest_segmentation_interfaces/action/TakeSnapshot`

**Goal**
- `float32 sync_slop_sec` — Tolerancia de sincronización (segundos) para los *message_filters*.
- `bool require_caminfo` — Si `true`, intenta guardar también `camera_info.json` en ese *snapshot*.

**Feedback**
- `string state` — Estado de progreso (`"syncing"`, `"saving"`, `"done"`, etc.).

**Result**
- `bool success` — `true` si el *snapshot* se guardó correctamente.
- `string error` — Mensaje de error cuando `success == false`.
- `string output_dir` — Carpeta raíz donde se guardan los archivos.
- `string basename` — Prefijo temporal común de los archivos del *snapshot*.
- `string image_path` — Ruta del PNG guardado.
- `string points_path` — Ruta del NPY/PCD de puntos guardado.
- `string gps_path` — Ruta del JSON con datos GPS.
- `string tf_path` — Ruta del JSON con TF/frames usados.

> Esta acción **no** define los *topics* ni los *frames*; eso lo hacen los nodos que la consumen.
> El objetivo del paquete es solo declarar la interfaz.

---

## 🚀 Requisitos

- ROS 2 (Humble/Iron/Jazzy; otras versiones podrían funcionar).
- Herramientas de *build*: `colcon`, `rosdep`.

---

## 🛠️ Instalación y compilación

Clona el paquete dentro de tu *workspace*:

```bash
cd ~/ros2_ws/src
git clone git@github.com:quique-munoz/ForestSegmentationInterfaces.git
# o vía HTTPS: https://github.com/quique-munoz/ForestSegmentationInterfaces.git
cd ..
rosdep install --from-paths src --ignore-src -y
colcon build --packages-select forest_segmentation_interfaces
source install/setup.bash
````

Comprueba que la acción está registrada:

```bash
ros2 interface show forest_segmentation_interfaces/action/TakeSnapshot
```

---

## 🔧 Uso básico (pruebas)

> Necesitas tener corriendo un **Action Server** que implemente `TakeSnapshot`
> (por ejemplo, tu nodo `snapshot_saver_action` del paquete de aplicación).

Enviar un *goal* desde la CLI:

```bash
ros2 action send_goal /take_snapshot \
  forest_segmentation_interfaces/action/TakeSnapshot \
  "{sync_slop_sec: 0.05, require_caminfo: true}"
```

Listar acciones disponibles:

```bash
ros2 action list
```

---

## 🧩 Integración en código (Python)

```python
from rclpy.action import ActionClient
from forest_segmentation_interfaces.action import TakeSnapshot

client = ActionClient(node, TakeSnapshot, '/take_snapshot')

goal = TakeSnapshot.Goal()
goal.sync_slop_sec = 0.08
goal.require_caminfo = False

# ... enviar goal y gestionar feedback/result como de costumbre
```

## 👤 Autoría y contacto

**Maintainer**: Enrique Muñoz (quique-munoz)
