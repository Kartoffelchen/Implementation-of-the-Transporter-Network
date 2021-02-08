# Implementation of TransporterNetwork
This is the specific repo for documentation of my syudent thesis, which is based on TransporterNetwork. There are some opinions, confusions and ideas about the paper and codes of the TransporterNetwork.

The link to TN codes: https://github.com/google-research/ravens   
The link to TN paper: https://arxiv.org/pdf/2010.14406.pdf  

# Tasks for my student thesis
1. literature review
(Researching papers related to robot manipulation based on vision, reinfoecement learning in robotics,pick-and-place...)  
(Researching latest methods to manipulate robot and grasp, having an overview of this filed...)  
(Generally comparing the pose-based grasping methods and vision-based manipulation mathods...)  
(Briefly introducing the core idea and method of the Transporter Networks...)

2. implement the TransporterNetwork on real robots  
(plan is to acomplish the align-corner and the place-red-in-green. Maybe a practical pick-and-place task like food-sortng will also be implemented.)  
(The specific task needs to be determined!)  
(Main challenge will be the difference between the synthetic image from simulation environment and the real RGB image from practical environment captured by a real camera Photoneo, which will definitely cause errors of inference...)    

3. Hierarchical discretization and infenrence. Change the discretization and infenrence times to obtain a more precise end effector pose. 
(In the TN paper the 360 degrees orientation angle is discretized into 36 bins, 10 degrees for each. Which means that the control error will be about 10 degrees. And calculating the network forwardly for 36 times is too inefficient...)  
(My idea is hierarchical discretization. Firstly dicretizing 360 degrees into 6 bins, 60 degrees for each, and get the best orientation angle theta1. Then taking the (theta1-30,theta1+30) as the new discretization domain, discreting this domain1 into 6 bins, 10 degrees for each, and get the best orientation angle theta2. Then taking the (theta2-5,theta2+5) as the new discretization domain, discreting this domain2 into 10 bins, 1 degree for each, and get the best orientation angle theta3, which is finally the true best orientation angle.)  
(In the original paper, 36 times discreyization and 10 degrees accuracy. But with the method above, 22 times discretizaion and 1 degree accuracy theoratically...)  
(Practical accuracy and efficiency need to be tested, and the parameters also need to be optimized...)    

4. Comparison of the results (efficiency and accuracy) of different hierarchies of discretization.  
(Collecting pick-and-place results of the real robot based on the original TN and the uptimized TN...)  
(Comparing the accuracies and efficiencies of two kinds of implementation methods...)  

# Implement TN on UR5 with Photoneo camera  
## Main structure of the project  
### Part 1: image capture  
To drive Photoneo camera for collecting RGB image and point cloud.  
### Part 2: image preprocess  
To preprocess RGB image and point cloud data before inference of Transporter Network.  
### Part 3: pick and place poses inference
To inference the pick and place poses of the end effector of the real robot UR5.  
### Part 4: trajectory calculation and robot movement control  
To convert the obtained pick and place poses into the trajectory of the robot, control the robot to do the pich-and-place task.  
### Part 5: collect testing data for thesis
To recording captured images and point cloud data, inference results, robot behaves for thesis.  

## Levels of different hierarchies of discretization:  
1. original discretization in the paper, 36 bins in total, accuracy is 10 degrees.    
   [36]  
   ->36 bins,10d  
2.[9,4]  
   ->9 bins,40d  
   ->4 bins,10d  
3.[9,10]  
   ->9 bins,40d  
   ->10 bins,4d  
4.[6,6,10]  
   ->6 bins,60 d  
   ->6 bins,10 d  
   ->10 bins,1 d  
   ->22 bins in total, acc is 1 degree  
5.[4,9,10]  
   ->4 bins, 90 d  
   ->9 bins, 10 d  
   ->10 bins, 1 d  
   ->23 bins in total, acc is 1d  
6.[4,3,3,5]  
   ->4 bins, 90 d  
   ->3 bins, 30 d  
   ->3 bins, 10 d  
   ->5 bins, 2 d  
   ->15 bins in total, acc is 2 d  
7.[4,3,3,10]     
   ->4 bins, 90 d  
   ->3 bins, 30 d  
   ->3 bins, 10 d   
   ->10 bins, 1 d 
   ->20 bins in total, acc is 1 d


# Reading and analyzing TN codes
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


