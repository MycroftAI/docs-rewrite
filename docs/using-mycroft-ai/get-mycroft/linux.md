---
description: >-
  Install Mycroft on Linux, learn how to start and stop services, configure
  proxies, or remove Mycroft from your system.
---

# Linux

## Prerequisites

This section of documentation assumes the following:

* That you already have Linux installed on your computer
* That your computer is already connected to the internet
* That you are comfortable issuing basic Linux commands from a **terminal** or **shell prompt**
* That your device has a built-in microphone and speakers, or, you have successfully connected microphone and speakers to your device.
* That your device already has `git` installed and working. If you don't already have `git` installed, [here is a great set of instructions](https://gist.github.com/derhuerst/1b15ff4652a867391f03).

### System Requirements

Whilst Mycroft runs on a Raspberry Pi 3B or above, this is achieved through a custom release of Raspbian Lite significantly reducing the system overhead by not running a desktop environment and other unnecessary processes. Mycroft will run on older hardware however your experience may vary significantly.

Our [Precise wake word engine](https://github.com/MycroftAI/mycroft-precise#mycroft-precise) also relies upon TensorFlow. For x86 Intel processors this requires the AVX \(Advanced Vector Extensions\) instruction set. To ensure your system supports AVX open a terminal and run: `grep avx /proc/cpuinfo`. AVX should be listed under the flags for each CPU core. If nothing is returned it is most likely that your system does not support AVX. Technical users may be able to build an older version of TensorFlow \(1.13\) from source using the [instructions provided on their website](https://www.tensorflow.org/install/source). Alternatively you may use Mycroft with the PocketSphinx wake word engine; see \(Switching Wake Word Listeners\)\[../customizations/wake-word\#switching-wake-word-listeners\].

The ARM architecture has a similar requirement called SIMD \(Single Instruction, Multiple Data\). This has been available since ARMv7 which includes the Cortex A53 used by the RaspberryPi and the Cortex A7 from the OrangePi.

## Getting Started

There are multiple ways to install Mycroft for Linux.

### Installing via git clone

The simplest way to install Mycroft for Linux is to clone the `mycroft-core` repo to your system and using Pip, which will install all dependencies, and [Mycroft components](http://mycroft.ai/documentation/mycroft-software-hardware/).

The `mycroft-core` repo is at [https://github.com/MycroftAI/mycroft-core](https://github.com/MycroftAI/mycroft-core).

The instructions below will install Mycroft in your HOME directory.

```text
cd ~/
git clone https://github.com/MycroftAI/mycroft-core.git
cd mycroft-core
pip install .
```

## Running Mycroft for Linux

The Mycroft for Linux installation includes two scripts that you use to control Mycroft services.

### mycroft-start

`mycroft-start` is used to start one, or all, Mycroft services.

The usage of `mycroft-start` is:

```text
usage: mycroft-start [COMMAND] [restart] [params]

Services:
  all                      runs core services: bus, audio, skills, voice
  debug                    runs core services, then starts the CLI
  audio                    the audio playback service
  bus                      the messagebus service
  skills                   the skill service
  voice                    voice capture service
  enclosure                mark_1 enclosure service

Tools:
  cli                      the Command Line Interface

Options:
  restart                  (optional) Force the service to restart if running

Examples:
  mycroft-start all
  mycroft-start all restart
  mycroft-start bus
  mycroft-start voice
```

#### To start all Mycroft services at once

```text
$ mycroft-start all
Starting all mycroft-core services
Initializing...
Starting background service bus
Starting background service skills
Starting background service audio
Starting background service voice
```

#### To start individual Mycroft services

Services can also be started individually.

```text
$ mycroft-start audio
Initializing...
Starting background service audio
```

### Stopping Mycroft services

```text
$ mycroft-stop
Stopping all mycroft-core services
```

## Start Mycroft on boot

You could create a mycroft service to get Mycroft automatic started on boot.

For this create a file named `/etc/systemd/system/mycroft.service` with the following content:

```text
[Unit]
Description=Mycroft AI
After=pulseaudio.service

[Service]
User=pi
ExecStart=mycroft-start all
ExecStop=mycroft-stop
Type=forking
Restart=no

[Install]
WantedBy=multi-user.target
```

Please modify `WorkingDirectory` and `User` to your needs. Reload the unit files with `sudo systemctl daemon-reload`and then, enable the new created service with `sudo systemctl enable mycroft.service` . You could start Mycroft by running `sudo systemctl start mycroft.service` stop it by `sudo systemctl stop mycroft.service` and get the status by typing `sudo systemctl status mycroft.service`.

## Pairing Mycroft for Linux

Once successfully installed, you will need to **pair** your Mycroft for Linux **Device** with your [home.mycroft.ai](https://home.mycroft.ai) account.

Speak

> Hey Mycroft, pair my device

Mycroft will Speak `"I am connected to the internet and need to be paired. Your 6-digit Registration Code is XXXXXX"`

Use the **Registration Code** to pair your Mycroft for Linux **Device** with home.mycroft.ai.

[View the home.mycroft.ai documentation to learn how to add your Device to home.mycroft.ai](http://mycroft.ai/documentation/home-mycroft-ai-pairing/).

Once paired, you can then use [basic Skills](http://mycroft.ai/documentation/basic-commands/).

## Using Mycroft behind a proxy

Many schools, universities and workplaces run a `proxy` on their network. If you need to type in a username and password to access the external internet, then you are likely behind a `proxy`.

If you plan to use Mycroft behind a proxy, then you will need to do an additional configuration step.

_NOTE: In order to complete this step, you will need to know the `hostname` and `port` for the proxy server. Your network administrator will be able to provide these details. Your network administrator may want information on what type of traffic Mycroft will be using. We use `https` traffic on port `443`, primarily for accessing ReST-based APIs._

## Using Mycroft behind a proxy without authentication

If you are using Mycroft behind a proxy without authentication, add the following environment variables, changing the `proxy_hostname.com` and `proxy_port` for the values for your network. These commands are executed from the Linux command line interface \(CLI\).

```bash
$ export http_proxy=http://proxy_hostname.com:proxy_port
$ export https_port=http://proxy_hostname.com:proxy_port
$ export no_proxy="localhost,127.0.0.1,localaddress,.localdomain.com,0.0.0.0,::1"
```

## Using Mycroft behind an authenticated proxy

If you are behind a proxy which requires authentication, add the following environment variables, changing the `proxy_hostname.com` and `proxy_port` for the values for your network. These commands are executed from the Linux command line interface \(CLI\).

```bash
$ export http_proxy=http://user:password@proxy_hostname.com:proxy_port
$ export https_port=http://user:password@proxy_hostname.com:proxy_port
$  export no_proxy="localhost,127.0.0.1,localaddress,.localdomain.com,0.0.0.0,::1"
```

### Keeping Mycroft for Linux updated

Keeping your `mycroft-core` installation up to date is simple.

1. Change to the directory where your `mycroft-core` installation is. This is most likely at `~/mycroft-core`
2. Type `git stash` - this preserves your Mycroft configuration. `git` may prompt you to [set up an identity](https://git-scm.com/book/en/v2/Getting-Started-First-Time-Git-Setup).
3. Type `git pull` to get the latest code. By default, using a `git` installation will bring down the `dev` branch of the repo. If you want to pull down another branch - for instance to test it - use `git pull origin BRANCH_NAME`.
4. Type `git stash pop` to return the configuration that was stashed with `git stash`
5. Type `pip install .` to update your `virtualenv` - it's a good idea to do this if you update your `mycroft-core` installation.
6. Type `mycroft-start all` to restart the services

## Removing Mycroft for Linux from your system

If you have installed `mycroft-core` using the `git-clone` method, you can remove the installation with Pip:

```bash
pip uninstall mycroft-core
```

This does not remove the cloned `mycroft-core` project directory. If cloned directly into the home directory, this can be removed with:

```text
rm -rf ~/mycroft-core
```

{% hint style="danger" %}
**Warning: always be very careful when running `rm -rf` commands. Running this command on the wrong directory can delete your entire filesystem. Run `rm --help` for more details.**
{% endhint %}

Lastly you can manually remove files created by Mycroft using the following commands:

```bash
sudo rm -rf /var/log/mycroft # Log files
rm -f /var/tmp/mycroft_web_cache.json # Configuration from Home.mycroft.ai
rm -rf "${TMPDIR:-/tmp}/mycroft" # Temp files
rm -rf "$HOME/.mycroft" # User level configuration
sudo rm -rf /opt/mycroft # Mycroft Skills directory
rm -rf "$HOME/mycroft-core" # Mycroft-core installation
```

{% hint style="info" %}
Depending on your system, you may need to run the commands with `sudo`
{% endhint %}

## Snap Package

A pre-Alpha Snap package is available, with the aim to provide a simple and secure means of installing Mycroft on a broad range of Linux distributions.

The Snap package currently has a number of major usability bugs that need to be addressed before it will be promoted to an installable channel. For now we recommend the git clone method above, but we welcome any help in improving Mycroft as a Snap:

{% page-ref page="snap.md" %}

## Troubleshooting

{% page-ref page="../troubleshooting/" %}

