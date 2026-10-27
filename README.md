# Gen3 SensONE Description

ROS 2 Jazzy URDF/Xacro description for a Kinova Gen3 (7-DoF) with a Bota SensONE force/torque sensor and Robotiq 2F-85 gripper.

## Build

Requires the Kinova `kortex_description` package from `ros2_kortex` and `xacro`, `robot_state_publisher`, `joint_state_publisher_gui`, and `rviz2`.

```bash
cd ~/ros2_ws
colcon build --packages-select gen3_sensone_description --symlink-install
source install/setup.bash
```

## Generate the URDF

```bash
ros2 run xacro xacro \
  $(ros2 pkg prefix --share gen3_sensone_description)/urdf/gen3_sensone_robot.urdf.xacro \
  -o /tmp/gen3_sensone.urdf

check_urdf /tmp/gen3_sensone.urdf
```

The saved defaults should use `sensor_mesh_yaw=0.785398` and `adapter_mesh_roll=-1.964543`. To override them temporarily, add `sensor_mesh_yaw:=0.785398 adapter_mesh_roll:=-1.964543` before `-o`.

## Visualize and publish TF

In separate terminals (source `install/setup.bash` in each):

```bash
ros2 run robot_state_publisher robot_state_publisher \
  --ros-args -p robot_description:="$(cat /tmp/gen3_sensone.urdf)"
```

```bash
ros2 run joint_state_publisher_gui joint_state_publisher_gui
```

```bash
rviz2
```

In RViz, set **Fixed Frame** to `base_link`, add **RobotModel** (description topic `/robot_description`) and **TF**. If another node already publishes joint states, do not start `joint_state_publisher_gui`.

## Check transforms

```bash
ros2 run tf2_ros tf2_echo end_effector_link sensone_link
ros2 run tf2_ros tf2_echo sensone_link sensone_ft_frame
```

The current URDF defines `end_effector_link` → `sensone_link` as `(0, 0, 0.049)` m with identity rotation, and `sensone_link` → `sensone_ft_frame` as identity. The F/T measurement origin and sensor readings still require physical validation.

After changing Xacro files, regenerate `/tmp/gen3_sensone.urdf` and restart `robot_state_publisher` to see the changes.
