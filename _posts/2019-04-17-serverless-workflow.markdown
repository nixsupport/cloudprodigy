---
title: Serverless ETL Workflow
subtitle: Big Data
layout: default
modal-id: 2
date: 2019-04-17
img: serverless-workflow.png
thumbnail: serverless-workflow-thumbnail.png
alt: Serverless ETL Workflow
project-date: April 2019
client: None
category: Big Data
services: EMR, CloudFormation, Lambda, Step Functions, ECS
description: This solution was designed for ETL workflow. AWS StepFunction service was used to orchestrate the workflow. It gets triggered by a time based CloudWatch event rule. Once triggered, a docker container is launched to execute scripts which further pushes the data to S3 and metadata to SQS. A Lambda function pulls data from SQS. Next Lambda in workflow launches EMR cluster. Once Cluster is ready, EMR Steps are submitted. Appropriate status checks are implemented with "Wait" tasks in Step Functions. 

---
