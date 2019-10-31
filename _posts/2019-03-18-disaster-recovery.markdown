---
title: Disaster Recovery - II
subtitle: Cloud Architect
layout: default
modal-id: 9
date: 2019-03-18
img: disaster-recovery.png
thumbnail: disaster-recovery-thumbnail.png
alt: Disaster Recovery
project-date: December 2018
client: None
services: AWS Backup, DynamoDB, Lambda, Step Functions
category: Cloud Architect
description: This solution was implemented to automated the DR process. Daily EBS snapshots being taken everyday using AWS Backup service. All EBS volumes are tagged with mount point and instance id. In the event of failure, a new instance is launched, all the snapshots for original instance are pulled from Backup service, volumes are created and attached with the new instance based on the mount point tags.

---
