# ecs-codepipeline

### platform pipeline setup
1. create an s3 bucket with a subfolder (this will be used to store pulled stack files)
2. create a github repo and commit all files from package: CICD-platform.zip
3. create a second github repo and commit all files from package: ECS-application.zip
4. run the cloudformation stack: master-stack-pipeline.yaml (through the console or aws cli, disable rollback)
    1. fill in the values for your github repo (use an oAuthToken or connect to Github after the pipeline is created)
    2. To allow connection to github, go to Codepipeline UI and click on 'EDIT', select the 'SOURCE' and click on 'Connect to Github', there will be a prompt to allow access.
5. Whenever you save changes to Codepipeline or commit to the Github repo, the pipeline will kickoff
6. There is a manual approval stage in the pipeline before it executes changes to your infrastructure.

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
