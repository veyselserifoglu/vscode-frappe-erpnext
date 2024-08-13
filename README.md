# A tutorial on how to set your Frappe + ERPnext dev environment like a senior.

# Table of contents

* Motivation.
* Scope. 
* Prerequisites.
* Setting up Docker in linux environment. 
* Write a dockerfile for Python environment.
* Write a dockerfile for Frappe + ERPnext. 
* Write a docker compose file for running DB and Redis. 
* Setting up the VScode Dev container extension. 
* Running altogether. 
* Summary. 
* Resources. 
* License. 
 

 ## Prerequisites
 
 To get started, follow these steps: 
 
 * VScode: Install from [here](https://code.visualstudio.com/download)
 * Dockerhub account: Signup from [here](https://app.docker.com/signup?)
 * Docker: [Follow the official install instructions for Docker](https://docs.docker.com/get-docker/#supported-platforms) 
 * Docker compose: [Follow the official install instructions for docker compose](https://docs.docker.com/compose/install/)
 

After installing the necessary tools, we will need to install the following extensions: 

* Devcontainers: The Dev Containers extension lets you use a Docker container as a full-featured development environment. 

* Python: It is the vscode plugin for python. 

### Install the plugins for Devcontainers and Python

#### steps to install the Dev Containers extension - Figure 1:

![Figur - 1: Step-by-Step Installation Guide](/static/images/install_dev_containers_extension.png)

- Go to the Extensions Marketplace in VS Code by clicking the Extensions icon in the sidebar. 
- In the search bar at the top, type `Dev Containers` to find the Dev Containers extension.  
- Click the `Install` button next to the Dev Containers extension to install the extension. 

The extension starts a development container. Workspace files can be mounted into the container from the local file system, or copied or cloned into it once the container is running. Later in this tutorial, we will see how to install other extension within the dev containers. 

#### steps to install the Python plugin: 

- I assume that you are already in the marketplace. 
- In the search bar, type `Python`, and click on the `install` button. 


## Setting up the Docker on linux
 




