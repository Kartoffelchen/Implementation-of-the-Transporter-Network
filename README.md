# Implementation of TransporterNetwork
This is the specific repo for documentation of my syudent thesis, which is based on TransporterNetwork. There are some opinions, confusions and ideas about the paper and codes of the TransporterNetwork.
# Tasks for my student thesis
1. literature review
2. implement the TransporterNetwork both in simulation environment and on real robot  
(plan is to acomplish the align-corner and the place-red-in-green. Maybe a practical pick-and-place task like food-sortng will also be implemented.)  
3. Hierarchical discretization and infenrence. Change the discretization and infenrence times to obtain a more precise end effector pose.  
4. Comparison of the results (efficiency and accuracy) of different hierarchies of discretization.  
# Reading TN codes
The link to TN codes: https://github.com/google-research/ravens  
## one important thing to mention  
The instruction to install miniconda from the link above (Step 1) doesnt work. I didnt find the reason. But i did find another way to install miniconda.  
*#Setup Ubuntu*  
sudo apt update --yes  
sudo apt upgrade --yes  

*#Get Miniconda and make it the main Python interpreter*  
wget https://repo.continuum.io/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda.sh  
bash ~/miniconda.sh -b -p ~/miniconda  
echo "PATH=$PATH:$HOME/miniconda/bin" >> .bashrc  
sourch /.bashrc  
rm ~/miniconda.sh  

*#Directory related should be dependent upon your own settings, change it when necessary accordingly.*  

After the installation of miniconda, the next steps from the link above(ravens) work well (from Step 2 on).  
In Step 2, *sudo apt-get install -y gcc libgl1-mesa-dev*, this command doesnt work. It seems that this command must be done with root authority.  

## Input and output of TN_Network(or input and output of the agent.act)
In test.py line 107: *act = agent.act(obs,info,goal)*.  
### obs
obs = {'color':(),'depth':()}  

#environment.py line 270~292  
_,_,color,depth,segm = p.getCameraImage(...)  

color_image_size = (config['image_size'][0], config['image_size'][1], 4)  
color = np.array(color, dtype=np.uint8).reshape(color_image_size)  
color = color[:, :, :3]  # remove alpha channel  

depth_image_size = (config['image_size'][0], config['image_size'][1])  
zbuffer = np.array(depth).reshape(depth_image_size)  
depth = (zfar + znear - (2. * zbuffer - 1.) * (zfar - znear))  
depth = (2. * znear * zfar) / depth  

And that intrinsic and extrinsic parameters of the camera also matter a lot!  

### pick_pose and place pose  
The form of them is like ((x,x,x),(x,x,x,x)).
First element is (x,y,z),the position the end effector. And the second element is (x,y,z,w), quaternion form to represent rotation angle, the orientation angel of the end effector. 


## environment/environment.py  
+ class Environment(gym.Env), inherit from gym.Env  
+ color_tuple/ depth_tuple/ self.observation_space = gym.spaces.Dict()/  self.position_bounds = gym.spaces.Box()/ self.action_space = gym.spaces.Dict()
## demo.py  
+ act = agent.act(obs,info)  
+ obs, reward, done, info = env.step(act)  
+ reward...  
## transporter.py
+ line 179: *p0_theta = argmax[2] * (2 * np.pi / pick_conf.shape[2]*. What does this shape[2] mean???  
+ line 173: the shape of the *pick_config* needs to be determined!  
+ line 40: *self.in_shape=(320,160,6)*. Why here is a 6??? xyz + rgb???  
## attention.py
+ line64: *rvecs = self.vec(self.n_rotations, pivot)* What is this self.vec  
# Implement TN on UR5 with Photoneo camera  
## Main structure of the project  
### Part 1: image capture  
To drive Photoneo camera for collecting RGB image and point cloud  
### Part 2: image preprocess  
To preprocess RGB image and point cloud data before inference of Transporter Network  
### Part 3: pick and place poses inference
To inference the pick and place poses of the end effector of the real robot UR5  
### Part 4: trajectory calculation and robot movement control  
To convert the obtained pick and place poses into the trajectory of the robot, control the robot to do the pich-and-place task.  
### Part 5: collect testing data for thesis
To recording captured images and point cloud data, inference results, robot behaves for thesis.  
