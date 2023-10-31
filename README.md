# launch_unifier_ws

This is a meta workspace repository for the launch_unifier package.

## Credits

This was forked from https://github.com/kenji-miyake/ros_component_diagram_generator

Thanks for the efforts of Kenji Miyake @kenji-miyake

## Usage

```bash
cd launch_unifier_ws
vcs import src < launch_unifier.repos

source /opt/ros/humble/setup.bash

colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=RelWithDebInfo -DCMAKE_EXPORT_COMPILE_COMMANDS=1 --allow-overriding launch_ros launch_testing_ros ros2launch

# source the workspace where the launch files are located
# example:
source ~/projects/autoware/install/setup.bash

# source the launch_unifier_ws workspace
source install/setup.bash

# Confirm that launch_ros points to the local package
ros2 pkg prefix launch_ros
# Expected: .../launch_unifier_ws/install/launch_ros

# run the launch_unifier node with the launch_command containing the full launch command
# example:
ros2 run launch_unifier launch_unifier --ros-args \
-p launch_command:="ros2 launch autoware_launch e2e_simulator.launch.xml vehicle_model:=sample_vehicle sensor_model:=awsim_sensor_kit map_path:=/home/mfc/autoware_map/nishishinjuku_autoware_map"
```

This will generate:

- `output/generated.launch.xml`
- `output/output/entity_tree.pu`

If the `output/generated.launch.xml` contains a xacro command, follow the instructions in the [xacro support](#xacro-support) section.

And you can run the generated launch file in another terminal with following:

```bash
# source the workspace where the launch files are located
# example:
source ~/projects/autoware/install/setup.bash

ros2 launch output/generated.launch.xml
```

And if all goes well, this should act same as the original launch file.

## Known Bugs

It might not support all the launch action types.
It has been tested to work with the Autoware Universe launch files.

### xacro support

It doesn't support the `xacro` command in the launch files.
This happens because it resolves everything to the final state. And requires to have `XML` string inside `XML` file.

Looking into fixing this issue. You need to search for `xml` and replace that manually.

Example:

generated launch xml:
```xml
  <!-- /robot_state_publisher -->
  <node pkg="robot_state_publisher"
        exec="robot_state_publisher"
        name="robot_state_publisher"
        namespace=""
        >

    <!-- parameter tuples global -->
    <param name="use_sim_time" value="True" />
    <param name="wheel_radius" value="0.3830" />
    <param name="wheel_width" value="0.2350" />
    <param name="wheel_base" value="2.790" />
    <param name="wheel_tread" value="1.640" />
    <param name="front_overhang" value="1.0" />
    <param name="rear_overhang" value="1.10" />
    <param name="left_overhang" value="0.1280" />
    <param name="right_overhang" value="0.1280" />
    <param name="vehicle_height" value="2.50" />
    <param name="max_steer_angle" value="0.70" />

    <!-- parameter dictionary -->
    <param name="robot_description" value="/home/mfc/projects/launch_unifier_ws/output/xml_file.xml" />

  </node>
```

replace that with:
```xml
  <node pkg="robot_state_publisher"
        exec="robot_state_publisher"
        name="robot_state_publisher"
        namespace=""
        >

    <!-- parameter tuples global -->
    <param name="use_sim_time" value="True" />
    <param name="wheel_radius" value="0.3830" />
    <param name="wheel_width" value="0.2350" />
    <param name="wheel_base" value="2.790" />
    <param name="wheel_tread" value="1.640" />
    <param name="front_overhang" value="1.0" />
    <param name="rear_overhang" value="1.10" />
    <param name="left_overhang" value="0.1280" />
    <param name="right_overhang" value="0.1280" />
    <param name="vehicle_height" value="2.50" />
    <param name="max_steer_angle" value="0.70" />

    <!-- parameter dictionary -->
    <param name="robot_description" value="$(command 'xacro $(find-pkg-share tier4_vehicle_launch)/urdf/vehicle.xacro vehicle_model:=sample_vehicle sensor_model:=awsim_sensor_kit config_dir:=$(find-pkg-share individual_params)/config/default/awsim_sensor_kit' 'warn')"/>
  </node>
```