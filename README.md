# CICD Pipeline with GitHub integration for a container based web app on AWS 

## Description
This sample project is designed to show how to build a CICD pipeline for deploying a containerized application to an ECS cluster. 

This project utilizes the following AWS services:
- CloudFormation
- CodePipeline
- CodeBuild
- IAM
- ECR
- ECS (Fargate)
- API Gateway
- Cloud Map
- S3

The application architecture:
1. **Api Gateway** accepts http requests
2. uses **Cloud Map** (service registry) to get a list of **ECS task** addresses of the **ECS service** configured for the only route
3. proxies the request to the one randomly selected **ECS task** using **VPC Link** 

The CodePipeline workflow steps:
1. Starts pipeline execution when the code changes
2. The GitHub action pulls the last commit
3. The CodeBuild action runs tests 
4. Another CodeBuild action builds a docker image and push it to the ECR repository
5. The ECS action executes a new rolling deployment that updates the ECS service for the new image version from the ECR repository

## Prerequisites
- Installed **aws cli** tool and preconfigured aws user profile in case of using cli. Or just use AWS Console.
- GitHub account w/ a repository that hosts the project source code

## Installation
- Navigate to **CloudFormation** console
- Create a new stack for ECR repository that will host a simple http server by uploading _ecs_cicd-ecr_httpserver.json_ template file 
- On the next page fill in all the required parameters
- Repeat all the above steps w/ the CF template files in the following order:
    - _ecs_cicd-cluster.json_ - ECS cluster based on Fargate compute engine
    - _ecs_cicd-http_service.json_ - ECS service for the sample http app
    - _ecs_cicd-api_gateway.json_ - API Gateway of type HTTP
    - _ecs_cicd-codepipeline.json_ - CI/CD pipeline
- Navigate to CodePipeline > Settings > Connections and find the connection created by the codepipline CF template. Open the connection and finish setting up the GitHub connection.

## How to use
The CodePipeline project is configured to be started every time the source code changes.

