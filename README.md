# ecs-codepipeline

### platform pipeline setup
1. Github repo 1 `Platform`: clone or fork from https://github.com/ShehryarAbbasi/dev-blue-nestedstack
    1. open cloudformation stack: `master-stack-pipeline.yaml`
    2. fill in the values for this under `GitHubRepoPlatform` (use an oAuthToken or connect to Github after the pipeline is created)
2. Github repo 2 `ECS`: clone or fork from https://github.com/ShehryarAbbasi/ecs-demo-php-simple-app
    1. update `master.yaml` to point to this repo
3. Github repo 3 `Lambda`: clone or fork from https://github.com/ShehryarAbbasi/lambdaSAM
    1. update `master.yaml` to point to this repo
4. Create a cloudformation stack using `master-stack-pipeline.yaml` (through the aws cli or aws console UI, disable rollbacks)
5. There is a manual approval stage in the pipeline before it executes changes to your infrastructure.

### Result
1. the `Platform pipeline` creates the following:
    1. master-stack
        1. VPC with 2 Public and 2 Private Subnets
        2. Security Groups for the Application LoadBalancer, ECS Cluster
        3. Application LoadBalancer
        4. ECS Cluster
        5. `ECS Deployment Pipeline`
            1. points to your second Github repo
            2. creates an ECR repo
            3. Pushes and tags a new docker image to ECR when you commit code changes
            4. Deploys to your ECS cluster after image is built
            3. The ECS service is setup to scales to 200% to accept new task definitions (new docker images)
        6. `Lambda Deployment Pipeline`
            1. points to your third Github repo
            2. Serverless Application Model template `samTemplate.yaml` contains function and API Gateway definitions
            3. API Gateway definitions can be expanded to use an inline or external Swagger file.
    2. Other resources can be added separately (consuming the Exported values from the Platform pipeline) or within `master.yaml`
