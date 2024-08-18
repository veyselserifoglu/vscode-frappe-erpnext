# A tutorial on how to set your Frappe + ERPnext dev environment like a senior.

# Table of contents

* [Motivation](#motivation).
* [Scope](#scope). 
* [Prerequisites](#prerequisites).
* [Setting up Docker In Linux Environment](#setting-up-docker-in-linux-environment). 
* [Setting up the Docker Compose In Linux Environment](#setting-up-the-docker-compose-in-linux-environment).
* [Setting up the Docker Hub In Linux Environment](#setting-up-the-docker-hub-in-linux-environment).
* [Writing a docker compose file for running Frappe, DB and Redis](#writing-a-docker-compose-file-for-frappe-db-and-redis). 
* [Accessing the dev containers environment for frappe_web](#accessing-the-dev-containers-environment-for-frappe_web).
* [Developing inside the dev container frappe_web](#developing-inside-the-dev-container-frappe_web).
* [Running Everything Together and Launching the Frappe Site](#running-everything-together-and-launching-the-frappe-site). 
* [Summary](#summary). 
* [Resources](#resources).
 
## Motivation 
Have you ever found yourself saying, "It works on my local," or heard your colleagues say it? I've been there too—more than once! But don't worry, you're not alone. The good news is, there's a way to reduce these common frustrations.

By working in an environment that more closely resembles production, you can minimize the gap between development and deployment, leading to fewer surprises when your code goes live. This tutorial will guide you through setting up a Frappe development environment inside a Docker container, helping you create a more reliable workflow, even if it doesn't perfectly mirror the complexity of production.

#### Why develop with Docker 🐳?

Docker is a CI/CD tool that enables seamless code deployment from development to production environments. By creating OS-level virtualization, it can package an application and its dependencies in a virtual container and ship it between different environments. The main advantages of using Docker within your development environment are:

* Reproducibility - Docker enables you to seamlessly package your code and its dependencies into a single container and execute, test, share, and deploy it with a high level of consistency
* Collaboration - Docker solves the dependencies madness when a team of developers works together on a specific project. Having a unified environment saves a ton of time during the development step. For example, if one developer gets some error, it is easy for other developers to reproduce the error and help debug it
* Deployment - Docker simplify the code shipment from the development environment to the production

#### Why VScode 💻?

VScode (aka Visual Studio Code) is a general-purpose free code editor developed by Microsoft. It can be run locally on Windows, macOS, and Linux OSs or on a cloud environment. The main advantages of using VScode as your IDE are:

* Multi-languages - VScode supports out-of-the-box JavaScript, TypeScript, and Node.js, and has a variety of extensions that enable running other programming languages such as Python, C++, C#, Go, etc.
* Extensions - VScode has a large community support that builds and maintains a variety of extensions that extend the editor's capabilities and functionality. That includes extensions that support programming languages (such as Python, R, and Julia), plugs-in that enables connection with external applications (Docker, Postgres, etc.), and other applications
* Git integration - VScode has built-in integration with Git
* Docker integration - VScode supports natively Docker integration, which we will dive deep into in the coming sections

VScode + Docker + Python = ❤️ ❤️ ❤️

Docker's high reproducibility, coupled with the seamless integration of Visual Studio Code with Docker, makes it a great development tool, particularly for Python developers. This integration enables developers to easily create, test, and deploy their Python applications in a consistent and reproducible environment, ensuring that their code runs as expected.

## Scope
It's not possible to cover everything in one guide, so I've focused on staying on topic. Below are the points that will and won't be covered in this guide, to help you know what to expect.

#### What Won't Be Covered

* In-depth Docker installation and configuration for non-Linux environments.
* Advanced Docker networking and orchestration features.
* Detailed explanation of Frappe app development beyond initial setup and configuration.

#### What Will Be Covered

* Installing necessary tools and extensions for setting up a Frappe development environment inside a dev container.
* Setting up Docker, Docker Compose, and Docker Hub in a Linux environment.
* Writing and configuring a docker-compose file for Frappe, DB, and Redis.
* Accessing and using the dev container environment for the frappe_web app.
* Launching and navigating the Frappe site.

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

#### Figure 1 - steps to install the Dev Containers extension:

![Figur - 1: Step-by-Step Installation Guide](/static/images/install_dev_containers_extension.png)

- Go to the Extensions Marketplace in VS Code by clicking the Extensions icon in the sidebar. 
- In the search bar at the top, type `Dev Containers` to find the Dev Containers extension.  
- Click the `Install` button next to the Dev Containers extension to install the extension. 

The extension starts a development container. Workspace files can be mounted into the container from the local file system, or copied or cloned into it once the container is running. Later in this tutorial, we will see how to install other extension within the dev containers. 

#### steps to install the Python plugin: 

- I assume that you are already in the marketplace. 
- In the search bar, type `Python`, and click on the `install` button. 


## Setting up Docker In Linux Environment
 
If you like to use the Docker Desktop, which I believe is user-friendly especially if you do not have any docker expereience, [follow the docker desktop installation guide](https://www.docker.com/products/docker-desktop/). For this tutorial, we will use the terminal to install Docker on ubuntu. 

    $ sudo apt-get update
    $ sudo snap install docker
    $ docker --version

    # you need to see this output 
    Docker version 24.0.7 (the version could different on your side, that is ok!)

In this tutorial, we won't look into the docker architecture and workflow. So, [Here is a detailed documentation on its architecture and workflow!](https://docs.docker.com/guides/docker-overview/). Also, [Here is a detailed documentation on the CLI-commands!](https://docs.docker.com/reference/cli/docker/)


## Setting up the Docker Compose In Linux Environment

Docker Compose allows you to define and manage multi-container applications in a single YAML file. This simplifies the complex task of orchestrating and coordinating various services, making it easier to manage and replicate your application environment. Later, we will see how we can setup multiple services like Redis, DB, and others in a single YAML file. 

    $ sudo apt-get update
    $ sudo apt-get install docker-compose-plugin
    $ docker compose version

    # you need to see this output 
    Docker Compose version v2.23.0-desktop.1 (the version could different on your side, that is ok!)


## Setting up the Docker Hub In Linux Environment

Docker Hub is a container registry built for developers and open source contributors to find, use, and share their container images. With Hub, developers can host public repos that can be used for free. By public repos, it means that all of your images can be pulled by others since they are public, so DO NOT share any sensitive information within your docker images. 

If you want to run private images, [Find more details on pricing](https://www.docker.com/pricing/)

At this point, I assume that you created a new account for your own usage. For the purpose of this tutorial, we only need to login into the dockerhub account via the terminal. 

Docker hub utilizes a personal access token (PAT) to use as an alternative to the password for Docker CLI authentication. Therefore, to create a PAT [please follow the official instruction guide!](https://docs.docker.com/security/for-developers/access-tokens/)

Now, let's login

    $ docker login --username YOUR_USERNAME

    # Then it will ask you to use the PAT you created above
    password: YOUR_PAT

<b>Test your docker installation with `hello world` image.</b> 

    $ docker run hello-world

    # If you get this output, then congratulations - your setup is siccessful!

    Hello from Docker!
    This message shows that your installation appears to be working correctly. 


## Writing a docker compose file for Frappe, DB and Redis

    version: '3.7'

    x-environment:
        &common_env
        - MYSQL_ROOT_PASSWORD=123
        - MYSQL_DATABASE=frappe_db
        - MYSQL_USER=root
        - MYSQL_PASSWORD=123
        - ERPNEXT_VERSION=version-13-hotfix
        - FRAPPE_ADMIN_PASSWORD=helloworld
        - FRAPPE_VERSION=version-13

    services:
        # Frappe service
        frappe:
            image: frappe_image:custom
        
            volumes: 
                # Inside the volumes, you can mount any file that you
                # wanna use inside the container

                - file - 1
                - file - 2 
                - etc.

            environment:
                # Here we need to add env variables related to frappe
                # internally

                *common_env

            ports:
                # This can be modified according to your preference. 
                - 13000:8000

        # Redis service
        redis:
            image: redis:alpine
            container_name: redis
            ports:
                - 6379:6379

        # Database for frappe, by default, frappe uses MariaDB
        frappe_db:
            image: mariadb:10.5
            container_name: frappe_db
            environment:
                - MYSQL_ROOT_PASSWORD=123
                - MYSQL_DATABASE=frappe_db
            ports:
                - 3306:3306

In brief, this Docker Compose file defines a multi-container application for Frappe/ERPNext, including services for Frappe, Redis, and MariaDB. The frappe service is configured with environment variables for site setup and database connection, while ports are mapped to allow access to the application. Redis and MariaDB are included as supporting services, with their default ports exposed for inter-service communication. 

<b>Note</b>: Volumes can be mounted to persist data or configuration files within the Frappe container. Later, we will see how we can mount files to run inside the container. 

<b>Check if the services / containers are running:</b>

    # run this command
    $ docker ps

Figure 2 - running services 

![running docker containers](/static/images/running_services.png)


## Accessing the dev containers environment for frappe_web

After you run the docker-compose file, you can access it and start developing as if you are working locally, only this time it is inside a dev container. Here are the steps to follow:

Figure 3 - access the dev containers with simple 3 steps

![access frappe dev containers](/static/images/access_frappe_dev_containers.PNG)

1. Click on the Remote Explorer icon on the left sidebar.
2. In the Dev Containers section, find and click on the container frappe_web.
3. Right-click on the container name and select Attach in New Window from the context menu.

<b>Note</b>: It is possible to access other services like the database and Redis in exactly the same way.


## Developing inside the dev container frappe_web
In this section, we'll guide you through configuring Frappe sites, creating a new site, and installing apps. Once everything is set up, we'll launch the Frappe web application to see the configurations in action.

### Step 1: Configure Frappe Sites
    
we need to configure redis urls and database host name according to the containers name. Here is how to do that:

    $ cd ~/frappe-bench
    $ cd sites
    $ nano commont_site_config.json 

    # once you are inside the file, add the following configurations
    {
        "background_workers": 1,
        "db_host": "frappe_db",
        "live_reload": true,
        "redis_cache": "redis://frappe_redis:6379",
        "redis_queue": "redis://frappe_redis:6379",
        "redis_socketio": "redis://frappe_redis:6379",
        "socketio_port": 9000,
        "webserver_port": 8000
    }

1. `background_workers`: Specifies the number of background workers as 1, which handle asynchronous tasks such as sending emails or running scheduled jobs.

2. `db_host`: Defines frappe_db as the hostname for the database server that Frappe connects to, typically corresponding to the MariaDB container.

3. `live_reload`: Enables live_reload, allowing the Frappe web server to automatically reload when code changes are detected, facilitating quicker development.

4. `redis_cachePoints` to redis://frappe_redis:6379 for Redis caching, improving performance by storing frequently accessed data in memory.

5. `redis_queue`: Sets the Redis server address at redis://frappe_redis:6379 for managing task queues, ensuring tasks are processed in an orderly manner.

6. `redis_socketio`: Uses redis://frappe_redis:6379 for Socket.IO communications, which handles real-time updates and notifications.

7. `socketio_port`: Specifies the socketio_port as 9000, the port where Socket.IO listens for real-time communication.

8. `webserver_port`: Defines the webserver_port as 8000, which is the port where the Frappe web server is accessible.

### Step 2: Create a New Site


    bench new-site "dev.localhost" --mariadb-root-password 123 --admin-password 123 --no-mariadb-socket && \
    bench --site "dev.localhost" set-config developer_mode 1 && \
    bench --site "dev.localhost" clear-cache 

* bench new-site "dev.localhost": Creates a new Frappe site with the name dev.localhost.

* --mariadb-root-password 123: Specifies the root password for the MariaDB database.

* --admin-password 123: Sets the admin password for the Frappe site.

* -no-mariadb-socket: Ensures the connection to MariaDB is through TCP/IP rather than a Unix socket.

* bench --site "dev.localhost" set-config developer_mode 1: This enables developer mode for the site dev.localhost. 

<b>Note</b>: Similar to Step 1, where we modified site configurations manually, set-config does the same but via a CLI command. It takes two parameters: a key and a value. For example, `set-config developer_mode 1` sets developer_mode to 1.

### step 3: Get and Install frappe apps on the site
Once the site is created:

<b>OPTIONAL</b>:
In case you need to clone your custom app from `Github`, you can do that as follows:

    bench get-app --branch https://github.com/myusername/myapp

* bench get-app: This command is used to fetch and add a new app to your Frappe or ERPNext instance.

After that, just install the erpnext app on the dev.localhost site. Replace erpnext with the name of any other app if you want to install something different.

    bench --site dev.localhost install-app erpnext

### step 4: Apply migration on the site
Applies any pending database migrations for the dev.localhost site. This command ensures that the database schema is up to date with the latest code changes.

    bench --site dev.localhost migrate


Figure 4 - Steps for lunching a new Frappe site
![frappe site lunching steps](/static/images/frappe_site_lunch_steps.PNG)

### step 5: Start the app

    bench start

<b>Note:</b> In case you forgot the administrator password, here is how you can rewnew it using the bench command:

    bench --site dev.localhost set-admin-password <your password goes here>

## Running Everything Together and Launching the Frappe Site
We are ready! Here's a quick demo on how to launch your Frappe site and explore its key features:

![Watch the video](/static/videos/Settup-frappe-dev-environment.mp4)


## Summary
In this guide, we walked through the process of setting up a Frappe development environment inside a dev container. From configuring sites and creating a new one to installing apps and launching the web application, each step was covered to ensure a smooth development experience. While this tutorial doesn't delve deeply into Dockerization and the intricacies of Docker layers, it does provide insights into docker-compose and how to write and use it effectively.

With everything in place, you're now ready to start building and exploring your Frappe site.

### Feedback and Suggestions

Your feedback is valuable! If you have any questions, suggestions, or tips, feel free to share them in the comments, reach out directly, or open an issue if you found some bugs. Your input will help improve future guides and contribute to the community's growth.

## Resources

* [Docker Documentation](https://docs.docker.com/engine/install/ubuntu/).

* [VScode Documentation](https://code.visualstudio.com/docs).

* [VScode Dev containers Documentation](https://code.visualstudio.com/docs/devcontainers/containers).

* [RamiKrispin vscode-python](https://github.com/RamiKrispin/vscode-python/tree/main#motivation).

## License
This project is licensed under the [MIT License](/LICENSE).