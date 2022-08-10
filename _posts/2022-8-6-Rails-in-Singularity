---
layout: post
title: Rails with PostgreSQL within a singularity container
---
No matter if you are a starter or an experienced user of singularity container, you'll always be amazed by the flexibilty it has to offer. One unsung advantage of using singularity is that, it will keep your host clean and free from installations. Who wouldn't want it? 

In this article, we will go-through the step-by-step process on how to set-up rails app within the singularity container. 


## Build Ruby container
After installing [singularity](https://docs.sylabs.io/guides/3.5/admin-guide/installation.html), create a definition file with .def extension.

> A Singularity Definition File (or “def file” for short) is like a set of blueprints explaining how to build a custom container. It includes specifics about the base OS to build or the base container to start from, software to install, environment variables to set at runtime, files to add from the host system, and container metadata. [More](https://docs.sylabs.io/guides/3.3/user-guide/definition_files.html)

Let's name our first definition file as ruby.def. In the header of the definition file, we will mention to pull ruby image from the docker hub. In the sections, we will use ```%post``` to install new libraries in the container. You can learn know more about the definition files and the different types of sections available from [here](https://docs.sylabs.io/guides/3.3/user-guide/definition_files.html). 

``` 
    Bootstrap: docker
    From: ruby:slim-buster

    %post
    apt update
    apt install -y ruby-dev 
```


It's time to create a Singularity Image Format (SIF) file.
> The Singularity Image Format is a compressed squashfs filesystem that has a block organizational structure to include metadata and content for the container definition file, the header, partition, signatures (if present), and of course, the container binary itself. [More](https://singularityhub.github.io/sif/)

To build a SIF file, run the below command in the host terminal,

```sh 
singularity build ruby.sif ruby.def
```

## Build Rails container

Let's create another definition file and name it as rails.def. In the header of the definition file, we will mention the path of ruby.sif file to pull from the local. As we did for ruby.def, we will mention the necessary libraries and gems to be installed in the ```%post``` section.


```
    Bootstrap: localimage
    From: ruby.sif

    %post
        apt install -y build-essential
        gem install rails
        apt install -y libpq-dev
```

Ok, now we know the obvious step which is to build the SIF!

```sh
singularity build sifname.sif path-of-def-file/defname.def
```
    
## Create new Rails app
Once the SIF is created, let shell into the container with the below command.

```sh
singularity shell  path-of-sif-file/sifname.sif
```

Now, we have got a singularity terminal! Yayy!


## Yet to be completed
