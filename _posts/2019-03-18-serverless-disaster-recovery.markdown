---
title: Disaster Recovery - I
subtitle: Cloud Architect
layout: default
modal-id: 1
date: 2019-03-18
img: serverless-disaster-recovery.png
thumbnail: serverless-disaster-recovery-thumbnail.png
alt: Serverless Disaster Recovery
project-date: December 2018
client: None
services: DLM, Lambda, Step Functions
category: Cloud Architect
description: This solution was implemented to automated the DR process. Daily EBS snapshots being taken everyday using DLM service. All EBS volumes are tagged with mount point and instance id (by DLM service). In the event of failure, a new instance is launched, all the snapshots for original instance are pulled based on DescribeSnapshots API call, volumes are created and attached with the new instance based on the mount point tags.

---
