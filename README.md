# ecs-codepipeline

### platform pipeline setup
1. create an s3 bucket with a subfolder (this will be used to store pulled stack files)
2. Github repo 1: clone or fork from https://github.com/ShehryarAbbasi/dev-blue-nestedstack
    1. open cloudformation stack: master-stack-pipeline.yaml
    2. fill in the values for your github repo (use an oAuthToken or connect to Github after the pipeline is created)
3. Github repo 2: clone or fork from https://github.com/ShehryarAbbasi/ecs-demo-php-simple-app
    1. update the master.yaml to point to this repo

4. run master-stack-pipeline.yaml (through the aws cli or aws console UI, disable rollbacks)
5. There is a manual approval stage in the pipeline before it executes changes to your infrastructure.

### stacks
1. the platform pipeline creates the following:
    1. master-stack
        1. VPC with 2 Public and 2 Private Subnets
        2. Security Groups for the Application LoadBalancer, ECS Cluster
        3. Application LoadBalancer
        4. ECS Cluster
        5. ECS Deployment Pipeline
            1. points to your second Github repo
            2. creates an ECR repo
            3. Pushes a new docker image to ECR when you commit code changes
            4. Deploys to your ECS cluster after image is built
            3. The ECS service is setup to scales to 200% to accept new task definitions (new docker images)
