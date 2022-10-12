# PGR-A
PGR-A: Parallel Grasp Region Affordance-based 6DOF Novel  Objects Grasping in Clutter

**In piled 5 objects grasping task:**

![image](with_PGR-A.gif)

---
# Experimental environment installation and steps
## Install
* Clone pointnet++: https://github.com/erikwijmans/Pointnet2_PyTorch.git
```
cd Pointnet2_PyTorch && pip3 install -r requirements.txt
```

* install pytorch_6dof-graspnet
```
cd pytorch_6dof-graspnet
pip3 install -r requirements.txt
```

* install UnseenObjectClustering 
```
pip3 install -r requirement.txt
```

* install OMPL RRT
* Download the OMPL installation script
```
chmod u+x install-ompl-ubuntu.sh
./install-ompl-ubuntu.sh --python #will install the latest release of OMPL with Python bindings
```
ref:https://ompl.kavrakilab.org/installation.html

## Steps
### ARM driver&control
* bring up TM5_900
* sophia_test

**TM5_900 driver**
```
roslaunch tm_driver tm5_900_bringup.launch 
```
**send robot path to controller**
```
surgery

rosrun sophia_test tm_move2target_line
```

---

### Unseen segmentation
* realsense
* experiment
* rviz

**realsense**
```
roslaunch realsense2_camera rs_aligned_depth.launch tf_prefix:=measured/camera enable_pointcloud:=true
```

**experiment**
```
cd UnseenObjectClustering/

./experiments/scripts/ros_seg_rgbd_add_test_segmentation_realsense.sh 0
```

**rviz**
```
cd UnseenObjectClustering/

rosrun rviz rviz -d ./ros/segmentation.rviz
```
---

### TF
```
rosrun tf2_ros static_transform_publisher -0.0384158789508 0.0966652170148 0.0191344123493 -0.0204705916742 -0.00662923347538 0.00412111005824 0.999759983492 /tool0 /measured/camera_depth_optical_frame
```
```
rosrun tf2_ros static_transform_publisher 0 0 0 3.14 0 0 /measured/camera_depth_optical_frame /measured/camera_grasp_frame
```
---

### Gripper
```
new

sudo chmod 777 /dev/ttyUSB1

rosrun robotiq_2f_gripper_control Robotiq2FGripperRtuNode.py /dev/ttyUSB1
```
```
picking

rosrun tm_reflexxes2 tm_demo_picking_pose

start

gopen0/gopen/gclose
```
---

### 6dof
* 6dof_graspnet
```
cd pytorch_6dof-graspnet

PYTHONPATH=/home/po/TM5_900/pytorch_6dof-graspnet python3 demo/main_npy_socket.py
```

---

### affordance
* affordance
```
cd Pointnet_Pointnet2_pytorch

python3 affordance.py 
or 
python3 affordance_ten.py
```
---

### demo
```
python3 demo.py --npy_name box_v1
or
python3 demo.py --npy_name ch_v1 --choose_closest True --task knn_mask_fast
or
python3 demo.py --npy_name test1 --choose_closest True --task knn_mask_fast
```