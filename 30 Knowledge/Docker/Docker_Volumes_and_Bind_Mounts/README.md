---
title: Docker Volumes and Bind Mounts
tags:
  - Docker
  - Docker_Volumes
  - Bind_Mounts
Source:
  - https://www.youtube.com/watch?v=r_LgmqejAkA
---


For example the container has Countdown the log file gets deleted why because Docker Containers are  **ephemeral** in nature
> _What is the meaning of ephemeral :
> they are very short-lived

container does not have a file system by default  because containers are very lightweight in nature.

### **Problem 1:** Data Persistence Problems
_**Data persistence problems in Docker** occur because containers are ephemeral by design, meaning ==any data created or modified inside a container's writable layer is permanently deleted when the container is removed==_

Containers are short-lived and **ephemeral** by default, meaning they do not permanently store files on the host system. For example, if an Nginx container continuously records user log details (such as login IPs for auditing) and the container goes down, those log files are permanently deleted, making historical auditing impossible

The problem is if the container goes down, so it used all of these resources from the host operating system but they are not a permanent resources for this container that means if the container goes down that will free up the resources from the operating system.
They get killed so now the user details or the log file of the nginx is deleted, **if the log file is deleted now the organization does not have information**


### **Problem 2:** Loss of Shared Historical Data Between Containers
In a multi-container architecture (such as a front-end and back-end container setup), the back-end might dynamically generate JSON, YAML, or HTML files for the front-end to read. If the back-end container goes down without persistent storage, the front-end loses access to historical files (e.g., records from previous days) and can only serve current data

### **Problem 3:** Inability to Read Files Directly from the Host Operating System:
Containers do not have a standard built-in way to access specific directories or files residing on the host system. For instance, if a Cron job running on the host machine periodically generates a data file (JSON, XML, or YAML) that a container needs to read and display, the container cannot natively read that file from the host file system






---
## **Mind Map**


![[NotebookLM Mind Map.png]]