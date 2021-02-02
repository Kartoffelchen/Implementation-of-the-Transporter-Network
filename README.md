# Implementation of TransporterNetwork
This is the specific repo for documentation of my syudent thesis, which is based on TransporterNetwork. There is some opinions, confusions and ideas about the paper and codes of the TransporterNetwork.
# Work that I need to do for my student thesis
1. literature review
2. implement the TransporterNetwork both in simulation environment and on real robot
3. xxxx
# Reading TN codes
The link to TN codes: https://github.com/google-research/ravens  
## environment/environment.py  
+ class Environment(gym.Env), inherit from gym.Env  
+ color_tuple/ depth_tuple/ self.observation_space = gym.spaces.Dict()/  self.position_bounds = gym.spaces.Box()/ self.action_space = gym.spaces.Dict()
## demo.py  
+ act = agent.act(obs,info)  
+ obs, reward, done, info = env.step(act)  
+ reward...  
