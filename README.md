# launch_unifier_ws

This is a meta workspace repository for the launch_unifier package.

## Usage

```bash
cd launch_unifier_ws
vcs import src < launch_unifier.repos

source /opt/ros/humble/setup.bash

colcon build --symlink-install --cmake-args -DCMAKE_BUILD_TYPE=RelWithDebInfo -DCMAKE_EXPORT_COMPILE_COMMANDS=1 --allow-overriding launch_ros launch_testing_ros ros2launch

source install/setup.bash

# Confirm that launch_ros points to the local package
ros2 pkg prefix launch_ros
# Expected: .../launch_unifier_ws/install/launch_ros
```

