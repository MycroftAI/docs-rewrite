---
description: >-
  Learn how to install Mycroft for Docker either from a Docker Hub install, or
  by building the image. Many thanks to Brian Hopkins (@btotharye) for this
  documentation.
---

# Docker

Mycroft is available for `Docker`. You have two options for a `Docker` installation:

* Build Image from source  
* Pull Image from Docker Hub

## Prerequisites

This documentation assumes the following:

* You have already installed `Docker` on your machine based on the operating system you are running.

## Getting Started

### Installing from Docker Hub

The Mycroft for Docker image is updated on [dockerhub](https://hub.docker.com/r/mycroftai/docker-mycroft/) and you can install it by running the command below:

```bash
docker pull mycroftai/docker-mycroft
```

Then follow the instructions below for running Mycroft for Docker.

### Installing via building the Docker image

it pull this repository.

```bash
git clone https://github.com/MycroftAI/docker-mycroft.git
```

Build the `Docker` image in the directory that you have checked out.

```bash
docker build -t mycroft .
```

Follow the instructions for running Mycroft for Docker below to continue.

## Running Mycroft for Docker

To prevent having to register your instance with home.mycroft.ai every time the container is started, and to have persistent data, you can map a local directory into the container. Just replace the `directory_on_local_machine` with the path you want the container mapped to on your local machine \(eg. `/home/user/mycroft`\).

Sounds can be played in the `Docker` container using `pulseaudio`, without modifying any config files

* Set [PULSE\_SERVER](https://www.freedesktop.org/wiki/Software/PulseAudio/Documentation/User/Network/#directconnection) `env` variable  
* Share pulseaudio's [cookie](https://www.freedesktop.org/wiki/Software/PulseAudio/Documentation/User/Network/#authorization)

Run the following to start up Mycroft for Docker:

\_NOTE: You don't need the -e PULSE\_SERVER or any of the other pulse related variables if you only want to use text via a websocket to mycroft for example using this container.

```bash
docker run -d  \
-v directory_on_local_machine:/root/.mycroft  \
--device /dev/snd  \
-e PULSE_SERVER=unix:${XDG_RUNTIME_DIR}/pulse/native  \
-v ${XDG_RUNTIME_DIR}/pulse/native:${XDG_RUNTIME_DIR}/pulse/native  \
-v ~/.config/pulse/cookie:/root/.config/pulse/cookie  \
-p 8181:8181  \
--name mycroft mycroftai/docker-mycroft
```

Confirm via `docker ps` that your container is up and serving port 8181:

```bash
docker ps  
CONTAINER ID IMAGE COMMAND CREATED STATUS PORTS NAMES  
692219e23bf2 mycroft "/mycroft/ai/mycro..." 3 seconds ago Up 1 second 0.0.0.0:8181->8181/tcp mycroft
```

You should now have a running instance of Mycroft for Docker that you can interact with via the command line.

### Pairing Mycroft for Docker

After the container has been started you can watch the logs and look for the line that says "Pairing Code" and use this to pair at [https://home.mycroft.ai](https://home.mycroft.ai).

You can view the logs with:

```bash
docker logs -f mycroft
```

## Interacting With Mycroft on Docker

### Accessing Mycroft Logs

At any time you can watch the logs simply by running the bellow command:

```bash
docker logs -f mycroft
```

You can exit out of this `docker log` command by hitting Ctrl + C. The `--follow` turns it into a real `tail` instead of a `cat` of the log.

## CLI Access

### Accessing Mycroft CLI

You can interact with the CLI of the container by running the following command. This will connect you to the running container via `bash`:

```bash
docker exec -it mycroft /bin/bash
```

Once in the container you can do `./start-mycroft.sh cli` to get a interactive CLI to interact with Mycroft for Docker if needed.

You can type Ctrl + C to exit the cli.

### Installing Skills on Docker Mycroft

You can install **Skills** into the container by running the following:

```bash
docker exec -it mycroft /opt/mycroft/msm/msm install github_url
```

So to install the Hello World Skill:

```bash
docker exec -it mycroft /opt/mycroft/msm/msm install https://github.com/MycroftAI/skill-hello-world
```

### Removing Skills on Docker Mycroft

You can also uninstall a **Skill** using MSM with:

```bash
docker exec -it mycroft /opt/mycroft/msm/msm remove skill-hello-world
```

## Troubleshooting

### Text-to-speech not working

There have been reports that `mimic` may not be properly added to the PATH. Restarting Mycroft within the Docker container can resolve this.

### Getting help

Mycroft for Docker is community-supported. You are welcome to join the [Mycroft Chat Docker channel](https://chat.mycroft.ai/community/channels/docker).

