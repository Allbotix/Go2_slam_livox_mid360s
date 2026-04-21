# Go2_slam_livox_mid360s
> Unitree Go2 | Ubuntu 20.04 | ROS2 Foxy

---

## Prerequisites

Make sure these are sourced before running anything:

```bash
source /opt/ros/foxy/setup.bash
source ~/ws_livox/install/setup.bash
source ~/ros2_ws/install/setup.bash
```

---

## Running the Mapping

Open **3 separate terminals** and run the following:

### Terminal 1 — Livox Mid360 LiDAR Driver
```bash
source ~/ws_livox/install/setup.bash
ros2 launch livox_ros_driver2 msg_MID360s_launch.py
```

### Terminal 2 — FAST-LIO Mapping Node
```bash
source ~/ws_livox/install/setup.bash
source ~/ros2_ws/install/setup.bash
ros2 launch fast_lio mapping.launch.py config_file:=mid360.yaml
```

### Terminal 3 — RViz Visualization (Optional)
```bash
source ~/ros2_ws/install/setup.bash
rviz2 -d ~/ros2_ws/src/FAST_LIO_ROS2/rviz_cfg/loam_livox.rviz
```

### Terminal 3 — Verify LiDAR data is flowing
```bash
source ~/ws_livox/install/setup.bash
ros2 topic hz /livox/lidar
```
> You should see ~10 Hz. Once data flows, FAST-LIO will start printing mapping output.

---

## Saving the Map

### Option A — Save while node is running (recommended)
In a new terminal:
```bash
source ~/ros2_ws/install/setup.bash
ros2 service call /map_save std_srvs/srv/Trigger
```
Expected response:
```
response: std_srvs.srv.Trigger_Response(success=True, message='Map saved.')
```

### Option B — Auto-save on shutdown
Simply press `Ctrl+C` in Terminal 2. The map saves automatically on exit (requires `pcd_save_en: true` in config).

---

## Map Output Location

```bash
ls ~/ros2_ws/src/FAST_LIO_ROS2/PCD/
# File: scans.pcd
```

### View the map
```bash
pcl_viewer ~/ros2_ws/src/FAST_LIO_ROS2/PCD/scans.pcd
```

### Save with a custom name
```bash
ros2 service call /map_save std_srvs/srv/Trigger && \
cp /Home/test.pcd ~/maps/my_map_name.pcd
```

---

## Config File Location

```
~/ros2_ws/src/FAST_LIO_ROS2/config/mid360.yaml
```

### Key settings to check
| Parameter | Value | Description |
|---|---|---|
| `pcd_save_en` | `true` | Must be true to save maps |
| `map_file_path` | `/home/unitree/maps/my_map.pcd` | Custom output path |
| `common.lid_topic` | `/livox/lidar` | LiDAR topic |
| `common.imu_topic` | `/livox/imu` | IMU topic |
