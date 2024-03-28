## Preparing a Docker image to run the code

We need the TF2.12 Docker image
```
# add the current user to the docker group
sudo usermod -a -G docker $USER
# close the ssh/bash and reopen it

# prepare and install the necessary packages to get nvidia support in docker
curl -s -L https://nvidia.github.io/nvidia-docker/gpgkey | \
  sudo apt-key add -distribution=$(. /etc/os-release;echo $ID$VERSION_ID)

curl -s -L https://nvidia.github.io/nvidia-docker/$distribution/nvidia-docker.list | \
  sudo tee /etc/apt/sources.list.d/nvidia-docker.list

sudo apt-get update
sudo apt-get install nvidia-docker2

# get the image
docker pull tensorflow/tensorflow:2.12.0-gpu
```

After the Docker image is downloaded, we need to start it and to prepare it with the necessary dependencies
```
docker run -it --gpus all --runtime nvidia --rm tensorflow/tensorflow:2.12.0-gpu bash
```

Within the Docker image that is started:
```
# install git and python3.10
add-apt-repository ppa:deadsnakes/ppa
apt update
apt install git
apt install python3.10
curl -sS https://bootstrap.pypa.io/get-pip.py | python3.10

#clone the repo and install the requirements
git clone https://github.com/alexandrupaler/zxreinforce_small.git
cd zxreinforce_small
pip3.10 install -r requirements.txt
```

After installing everything in the container
* `exit` the image
* `docker commit <container_id> dreamy_heisenberg` where the container_id is from `docker ps -a`

Finally, whenever the image is needed
```
docker run --gpus all --runtime=nvidia -it dreamy_heisenberg bash
```


# ZXreinforce
## This is a repository that does not include the saved data

This project contains the code used to produce the results in "Optimizing ZX-Diagrams with Deep Reinforcement Learning".
* Main code of the algorithm is in zxreinforce
* A script showing how to train an agent is at experiments/train_rl_agent/runner_final.py
* The agent's training progress can be monitored with experiments/evaluation_rl_agent/evaluation_training_logger.ipynb
* An example notebook showing how to simplify a diagram with the trained agent is at experiments/evaluation_rl_agent/simplify_example_traj.ipynb
* Scripts to compare the performance of the RL agent to a greedy strategy and simulated annealing are in experiments/evaluation_performance
* The evaluation of the Copy action is done in experiments/eval_copy_action
* The evaluation of the action dependence on the local environment is done in experiments/prob_vs_layer
* The network weights of the agents trained for the ablation studies can be found in saved_agents
