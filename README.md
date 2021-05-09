# Ansible Role: Freqtrade-Docker

Pulls and/or builds [Freqtrade](https://www.freqtrade.io) Docker images on Debian/Ubuntu based systems.

## Information

This Ansible Role is designed to pull or build Docker images for Freqtrade - a crypto-currency algorithmic trading software developed in Python. It also allows to build a custom image based on the official Freqtrade sources, to enhance or customize it with own Python packages and/or commands.

Ansible is an open-source software provisioning, configuration management, and application-deployment tool enabling infrastructure as code. If you are new to Ansible and/or Freqtrade, it is highly recommended to get familiar with the concept of configuration management and infrastructure as code (IaaC). Please see the latest [Ansible documentation](https://docs.ansible.com/ansible/latest/index.html) for further information.

## Requirements

Currently only compatible with Debian/Ubuntu based systems. Tested on Ubuntu 20.04.  **At the moment it is not possible to deploy it on Rasberry Pi's (armv7l) architecture!**

*If you want to see other OS flavors supported, you can very much submit a pull request with the desired additions.*

Note that this role requires root access, so either run it in a playbook with a global `become: yes`, or invoke the role in your playbook like:

    - hosts: tradingbots
      roles:
        - role: nightshift2k.freqtrade-docker
          become: yes

## Installation

### Via Github
    
    ansible-galaxy install git+https://github.com/nightshift2k/ansible-role-freqtrade-docker.git
    
### From Ansible Galaxy

    ansible-galaxy install nightshift2k.freqtrade_docker

## Role Variables

Available variables are listed below, along with default values (see `defaults/main.yml`):


    freqtrade_branch: "stable"
    
The branch in GitHub for the checkout of source files, and also the tag used when pulling the Docker image from registry.

    docker_build_directory: "/tmp/build/docker"
    
Temporary directory for the Docker images. GitHub checkout will happen into subfolder, also if custom image will be build, a Dockerfile will be created there.

    build_freqtrade: false

When set to true, will build the image from GitHub sources, instead of pulling it from Docker Registry.

    build_freqtrade_custom: false
    
Will build an additional image with custom Dockerfile and Prefix (see below).

    freqtrade_custom_prefix : "custom"
    
Needs to be defined, when `build_freqtrade_custom` equals `true` which will define the docker image name prefix (repository), so the custom image will be created as `custom/freqtrade:stable`on the target. 

    freqtrade_custom_commands:
      - "RUN pip install --upgrade pip"
      
Commands that will be placed into the Dockerfile, anything that is supported in Dockerfiles can be placed here as a list.

    freqtrade_custom_pips:
      - finta
      - ta
      - technical

Will be placed after `freqtrade_custom_commands` to install custom Python packages via pip, see `templates/Dockerfile.j2` inside of the role for details.


## Dependencies

This role is dependent on `geeringguy.docker` and also the collection `community.docker` please use `ansible-galaxy` to install the role and collection beforehand.

    ansible-galaxy collection install community.docker
    ansible-galaxy install geerlingguy.docker

## Example Playbook

    - hosts: tradingbots
      vars_files:
        - vars/freqtrade-docker.yml
      roles:
        - role: nightshift2k.freqtrade-docker
          become: yes

*Inside `vars/freqtrade-docker.yml`*:

    freqtrade_branch: "develop"
    docker_build_directory: "/tmp/build/docker"
    build_freqtrade: true
    build_freqtrade_custom: true
    freqtrade_custom_prefix : "nightshift2k"
    freqtrade_custom_commands:
      - "RUN pip install --upgrade pip"
    freqtrade_custom_pips:
      - finta
      - ta
      - technical
      - mergedeep
      ...

## License

MIT / BSD