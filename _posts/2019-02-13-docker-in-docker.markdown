---
title: Docker-In-Docker
subtitle: DevOps
layout: default
modal-id: 7
date: 2019-02-13
img: docker-in-docker.png
thumbnail: docker-in-docker-thumbnail.png
alt: Docker-In-Docker
project-date: February 2019
client: None
category: DevOps
services: Amazon EKS, Docker, Azure DevOps
description: This solution consolidates the build agents required for Azure DevOps Build and Release pipelines. Agents run inside EKS with docker installed in order to communicate with ECR. Build Agents running in EKS have docker container running as a side-car in same pod so that instead of mounting EKS host volume for docker client, it is actually referred to side container over TCP.

---
