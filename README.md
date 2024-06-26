# Ansible Labs Documentation

This documentation provides step-by-step guides and examples for working with Ansible, covering various topics such as setting up the environment, practicing inventory management, configuration, playbooks, tasks, handlers, roles, and more.

## Table of Contents

1. [Setup Environment](#setup-environment)
2. [Practicing Inventory](#practicing-inventory)
3. [Practicing ansible.cfg](#practicing-ansiblecfg)
4. [Practicing Command Escalation (become)](#practicing-command-escalation-become)
5. [Practicing Playbook](#practicing-playbook)
6. [Install Nginx Task](#install-nginx-task)
7. [Practicing Tags](#practicing-tags)
8. [Always Tag](#always-tag)
9. [Practicing Variables](#practicing-variables)
10. [Practicing Loops](#practicing-loops)
11. [Practicing When](#practicing-when)
12. [Practicing Register](#practicing-register)
13. [Practicing Register & When](#practicing-register--when)
14. [Practicing Handler](#practicing-handler)
15. [Practicing Template](#practicing-template)
16. [Roles Project](#roles-project)


## Setup Environment

1. **Install Ansible on your machine.**

    ```bash
    $ sudo apt install ansible
    
    # Install using pip to ensure all required Python packages are installed
    $ pip install ansible
    
    # Setup Docker environment
    ```
    
2. **Create a key to be associated with the container to SSH into it, in the /.ssh/**
    
    ```bash
    $ ssh-keygen -t rsa -b 2048
    ```
    
3. **Create a Dockerfile for the image**

   <p align="center">
       <img src="images/Dockerfile.png" alt="Dockerfile" width="400"/>
   </p>

    
5. **Build the image**
    
    ```bash
    $ docker build -t ansible-ubuntu-image .
    ```
    
6. **Create a container using the image**
    
    ```bash
    $ docker run -itd --name "lab1-server01" ansible-ubuntu-image
    ```
    
7. **Get the container IP to connect to it**
    
    ```bash
    $ docker inspect lab1-server01
    ```
    
    The IP is typically **`172.17.0.2`**.
    
8. **Move the public key to the container to activate it for SSH**
    
    ```bash
    $ ssh-copy-id -i /home/user/.ssh/ansible-key.pub ansible@172.17.0.2
    ```
    <p align="center">
       <img src="images/ssh-copy-id.png" alt="ssh-copy-id" width="400"/>
   </p>
    
9. **Now connect to the container using SSH**
    
    ```bash
    $ ssh ansible@172.17.0.2
    ```
    <p align="center">
       <img src="images/ssh-result.png" alt="ssh-result" width="400"/>
   </p>

## Practicing Inventory

1. **Create an inventory file that contains all the IPs in your environment.**
    
    <p align="center">
       <img src="images/inventory.png" alt="inventory" width="300"/>
   </p>
    
2. **Ping all the servers using the following command.**
    
    ```bash
    $ ansible all -i ./inventory --private-key /home/h-test/.ssh/ansible-key -u ansible -m ping
    ```
    <p align="center">
       <img src="images/ping-result-01.png" alt="ping-result-01" width="400"/>
   </p>

## Practicing ansible.cfg

1. **Write your config file**

    <p align="center">
       <img src="images/ansible-cfg.png" alt="ansible-cfg" width="400"/>
   </p>
    
2. **Now ping the web servers only for example.**
    
    ```bash
    $ ansible webservers -m ping
    ```
    <p align="center">
       <img src="images/ping-result-02.png" alt="ping-result-02" width="400"/>
   </p>

## Practicing Command Escalation (become)

If we tried to execute a sudo command that requires a password to has it’s execution privilege. 
Ansible will response with an error.

```bash
$ ansible webservers -m command -a "sudo touch /etc/shadow"
```

It will cause a runtime error.

So we need to tell ansible that you are going to need the root password, and this gets done using **`become`** 

**To do so, we need to activate the become and password asking either in terminal.**

```bash
$ ansible webservers -m command --ask-become-pass -b -a "sudo touch /etc/shadow"
```

or in **`ansible.cfg`**
<p align="center">
    <img src="images/ansible-cfg-become.png" alt="ansible-cfg-become" width="400"/>
</p>

## Practicing Playbook

1. **Write your playbook file in .yml**

   <p align="center">
       <img src="images/playbook.png" alt="playbook" width="300"/>
   </p>
    
2. **Execute the following command**
    
    ```bash
    $ ansible-playbook pinging-playbook.yml
    ```
    <p align="center">
       <img src="images/playbook-result-01.png" alt="playbook-result-01" width="500"/>
   </p>

## Install Nginx Task
📢 **Task**

    - Update cache
    - Install latest nginx
    - Copy index.html from controller to server01
    - Restart nginx service
    - Can you see your index.html file when you hit server01 on port 80 ?
    

1. **Write a simple index.html file**
    
    <p align="center">
       <img src="images/index-html.png" alt="index-html" width="400"/>
   </p>
    
2. **Write your playbook file**
    
   <p align="center">
       <img src="images/nginx-task-playbook.png" alt="nginx-task-playbook" width="400"/>
   </p>

3. **Execute the following command**
        
    ```bash
    $ ansible-playbook pinging-playbook.yml
    ```
        
4. **Hit nginx**
        
    Expose nginx port on the host by restarting the container and modifying the container networking.

## Practicing Tags

1. **Write your tags-playbook file**
    
   <p align="center">
       <img src="images/tags.png" alt="tags" width="300"/>
   </p>

    
2. **Execute the following command**
    
    ```bash
    $ ansible-playbook tags-playbook.yml --tags install
    ```

## Always Tag

Using `always` tag ensures the associated task is executed regardless.

<p align="center">
    <img src="images/always-tags.png" alt="always-tags" width="300"/>
</p>

## Practicing Variables


1. **you can specify your variables in the playbook file directly, as following..** 

    <p align="center">
        <img src="images/vairables.png" alt="vairables" width="300"/>
    </p>
    
2. **Or write them in a separate variables.yml file. and refer to them in the playbook file.**

   <div style="display: flex; justify-content: center;">
        <img src="images/variables(2).png" alt="variables(2)" width="250"/>
        <img src="images/variables(3).png" alt="variables(3)" width="200"/>
    </div>


 3. **Execute the following command**
    
    ```bash
    $ ansible-playbook variables-playbook.yml
    ```

## Practicing Loops

1. **Write your loops-playbook file**

    <p align="center">
        <img src="images/loops.png" alt="loops" width="300"/>
    </p>

    You also can add multiple attributes to the loop item, as following..
    <p align="center">
        <img src="images/loops(2).png" alt="loops(2)" width="300"/>
    </p>

    Also you can store the item values as a variables, as following..
    <p align="center">
        <img src="images/loops(3).png" alt="loops(3)" width="300"/>
    </p>

2. **Execute the following command**
    
    ```bash
    $ ansible-playbook loops-playbook.yml
    ```

## Practicing When

1. **Write your when-playbook file**

    <p align="center">
        <img src="images/when.png" alt="when" width="300"/>
    </p>

    Execute using:
    
    ```bash
    $ ansible-playbook when-playbook.yml
    ```
    <p align="center">
        <img src="images/when-result.png" alt="when-result" width="400"/>
    </p>

## Practicing Register

1. **Write your register-playbook file**
    
    !<p align="center">
        <img src="images/register.png" alt="register" width="300"/>
    </p>

    Execute using:
    
    ```bash
    $ ansible-playbook register-playbook.yml
    ```
    <p align="center">
        <img src="images/register-result.png" alt="register-result" width="600"/>
    </p>

## Practicing Register & When

![Register & When Playbook](images/register-when-playbook.png)

## Practicing Handler

![Handler Playbook](images/handler-playbook.png)

## Practicing Template

![Template Playbook](images/template-playbook.png)

---

## Roles Project

### Project Directory Files

- **vars/main.yml**
    
    ![Vars Main](images/vars-main.png)
    
- **files/index01.html**
    
    ![Index01 HTML](images/index01-html.png)
    
- **templates/index02.html**
    
    ![Index02 HTML](images/index02-html.png)
    
- **tasks/main.yml**
    
    ![Tasks Main](images/tasks-main.png)
    
- **handlers/main.yml**
    
    ![Handlers Main](images/handlers-main.png)
    
- **./playbook.yml**
    
    ![Playbook](images/playbook-yml.png)

### Run the following command

```bash
$ ansible-playbook playbook.yml
```

### Result

![Roles Project Result](images/roles-project-result.png)

---

This documentation is a comprehensive guide to using Ansible for various tasks and projects. Each section provides detailed instructions and examples for practical implementation.

For more information and detailed usage, refer to the respective sections in this document.

---
