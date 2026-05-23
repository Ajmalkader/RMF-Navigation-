# RMF-Navigation


<img width="3672" height="2026" alt="workshop" src="https://github.com/user-attachments/assets/6af68234-7b44-4e8f-82be-53ae7f3fd374" />


# Overview
The goal of this workshop is to guide participants through the process of setting up an Open-RMF deployment at a facility. This includes:

Importing and annotating a floor plan with artifacts to generate a digital twin of the world and navigation graphs that will be used by the fleets.
Implementing a full control fleet adapter to integrate a fleet of robots with Open-RMF.
Implementing a door adapter to integrate a set of doors with Open-RMF.
Implementing a lift adapter to integrate a set of lifts with Open-RMF.
Configuring and running the rmf-web dashboard for managing operations.
Trying our various scenarios.
At the beginning of the workshop, please ensure you have checked out to workshop branch. The main branch contains the codebase with the final implementations of all the steps above and may be used as reference along the way.

Live instructions will be provided at each step with accompanying slides. The sections below contain relevant launch instructions for each milestone.

# Floor plan annotation
After annotation your floor plan in rmf_traffic_editor might look like this. It is perfectly okay if it looks different. Creativity is welcome! 

<img width="804" height="899" alt="example_annotation" src="https://github.com/user-attachments/assets/408d3802-c7d4-4631-b559-5aca19687966" />


# Build the workspace.

cd ~/roscon_ws
source ~/rmf_ws/install/setup.bash
colcon build
Launch the auto-generated digital twin in gazebo-classic

cd ~/roscon_ws
source install/setup.bash
ros2 launch roscon_gz_classic workshop.launch.xml run_adapters:=false map_name:=workshop_example
Fleet integration with Open-RMF

<img width="836" height="920" alt="test_fleet_adapter" src="https://github.com/user-attachments/assets/71f184b1-8052-416a-b68b-f75df18e563b" />



Launch the demo world. You should now be able to see the schedule and fleet state markers in RViz for each robot.

cd ~/roscon_ws
source install/setup.bash
ros2 launch roscon_gz_classic workshop.launch.xml
Then send a couple of patrolling tasks to Open-RMF.

cd ~/roscon_ws
source install/setup.bash
ros2 run rmf_demos_tasks dispatch_patrol -p p2 p4 p6 -n 3 --use_sim_time
ros2 run rmf_demos_tasks dispatch_patrol -p p4 p5 p8 -n 3 --use_sim_time
Note: You may see some errors from the lift and door adapters but this okay, we will implement them shortly.

ros2 run rmf_demos_tasks dispatch_patrol -p p4 p5 p8 -n 3 --use_sim_time

# Door integration with Open-RMF

<img width="1311" height="917" alt="test_door_adapter" src="https://github.com/user-attachments/assets/55d12b49-261d-40d7-a624-106e0fb4e509" />


Launch the demo world. You should now be able to see the door states in RViz. You can also use the DoorPanel to manually open/close doors to test the adapter.

cd ~/roscon_ws
source install/setup.bash
ros2 launch roscon_gz_classic workshop.launch.xml
Request a robot to go outside. Open-RMF will automatically request the integrated door to open and close as the robot passes through.

cd ~/roscon_ws
source install/setup.bash
ros2 run rmf_demos_tasks dispatch_patrol -p snack_bar --use_sim_time

# Lift integration with Open-RMF

<img width="1432" height="920" alt="test_lift_adapter" src="https://github.com/user-attachments/assets/74eddda6-b604-4203-bed0-37810c8c7e6e" />



Launch the demo world. You should now be able to see the lift states in RViz. You can also use the Lift to manually command the lift.

cd ~/roscon_ws
source install/setup.bash
ros2 launch roscon_gz_classic workshop.launch.xml
Request a robot to go to L2. Open-RMF will automatically request the integrated lift to transport the robot to the second level. Remember to change Map Name in the SchedulePanel to L2 to visualize the second level when the robot transitions.

cd ~/roscon_ws
source install/setup.bash
ros2 run rmf_demos_tasks dispatch_patrol -p balcony_1 --use_sim_time
Launching with rmf-web dashboard


Launch the demo world with the additional server_uri launch argument. This allows the fleet adapter to communicate with the api-server.

cd ~/roscon_ws
source install/setup.bash
ros2 launch roscon_gz_classic workshop.launch.xml server_uri:="ws://localhost:8000/_internal"
The simulation will warn that the client is not connected. Next, we launch the api-server, which will make that warning go away, and an acknowledgement that it has been connected.

source ~/roscon_ws/install/setup.bash
cd ~/rmf-web/packages/api-server
pnpm run start
In another terminal serve the built static dashboard, and proceed to http://localhost:3000.

source ~/roscon_ws/install/setup.bash
cd ~/rmf-web/packages/dashboard
serve -s build
