# Implementation of TransporterNetwork
This is the specific repo for documentation of my syudent thesis, which is based on TransporterNetwork. There is some opinions, confusions and ideas about the paper and codes of the TransporterNetwork.
# Work that I need to do for my student thesis
1. literature review
2. implement the TransporterNetwork both in simulation environment and on real robot
3. xxxx
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
 It see
In Step 2, *sudo apt-get install -y gcc libgl1-mesa-dev*, this command doesnt work. It seems that this command must be done with root authority.

## environment/environment.py  
+ class Environment(gym.Env), inherit from gym.Env  
+ color_tuple/ depth_tuple/ self.observation_space = gym.spaces.Dict()/  self.position_bounds = gym.spaces.Box()/ self.action_space = gym.spaces.Dict()
## demo.py  
+ act = agent.act(obs,info)  
+ obs, reward, done, info = env.step(act)  
+ reward...  
