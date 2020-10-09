# AWS ECS Deployment

### Keywords
aws ecs cli hands-on docker elastic container service devops
#

### Configure Cluster
```
ecs-cli configure 
    --cluster <CLUSTER_NAME> --default-launch-type FARGATE --config-name <PROJECT_CONFIG_NAME> --region <REGION>
```

### Deploy Cluster
Let ECS CLI create and configure the VPC

```ecs-cli up --cluster-config <CLUSTER_CONFIG_NAME>```

**OR**

```
ecs-cli up --cluster-config <CLUSTER_CONFIG_NAME> --vpc <VPC_ID> --subnets <YOUR_SUBNET_ID_1>, <YOUR_SUBNET_ID_2>
```

### Setup security group
```
aws ec2 create-security-group --description <DESCRIPTION> --group-name <SECURITY_GROUP_NAME> --vpc-id <VPC_ID>
```
```
aws ec2 authorize-security-group-ingress --group-id <SECURITY_GROUP_ID_CREATED> --protocol tcp --port 80 --cidr 0.0.0.0/0 --region <REGION>
```
### Setup IAM Role
This role contains the access policy to AWS resources for the containers.

- Create file `assume_role_policy.json`

    ```
    {
    "Version": "2012-10-17",
    "Statement": [
            {
                "Sid": "",
                "Effect": "Allow",
                "Principal": {
                    "Service": "ecs-tasks.amazonaws.com"
                },
                "Action": "sts:AssumeRole"
            }
        ]
    }
    ```

- run
    ```
    aws iam --region <REGION> create-role --role-name ecsTaskExecutionRole --assume-role-policy-document file://assume-role_policy.json

    ```

- Run the following to attach the AWS managed policy for ECS Tasks that allow ECS containers to create AWS CloudWatch Log Group.
    ```
    aws iam --region <REGION> attach-role-policy --role-name ecsTaskExecutionRole --policy-arn arn:aws:iam::aws:policy/service-role/AmazonECSTaskExecutionRolePolicy
    ```

### Docker Compose -> ECS

- Change `docker-compose.yml`
    - Before 
        ```
        version: '3'
        services:
        web:
            image: nginx 
            ports:
                - "80:80"
        ```

    - After 
        ```
        version: '3'
        services:
        web:
            image: nginx 
            ports:
                - "80:80"
        ```
        ```
            logging:
                driver: awslogs
                options: 
                    awslogs-group: tutorial
                    awslogs-region: eu-west-1
                    awslogs-stream-prefix: web
        ```

- Create `ecs-params.yml` that contains the configurations of your **ECS Cluster** and **ECS Service**

    It can include:
    - **Networking configuration** with your vpc and subnets.
    - **Permission configuration** with the role that you created in the second step.
    - **Task configuration**: properties like CPU and RAM limits for deploying the service.
    ```
    version: 1
    task_definition:
        task_execution_role: ecsTaskExecutionRole
        ecs_network_mode: awsvpc
        task_size:
            mem_limit: 0.5GB
            cpu_limit: 256
    run_params:
        network_configuration:
            awsvpc_configuration:
                subnets:
                    - "subnet-025a02ea8eeb28be6"
                    - "subnet-02ac58fd8d25836c0"
                security_groups:
                    - "sg-0bbc7a832db6933ea"
                assign_public_ip: ENABLED
    ```

### Deploy the docker-compose

```
ecs-cli compose --project-name <PROJECT_NAME> service up --create-log-groups --cluster-config <PROJECT_CONFIG_NAME>
```

check service status

```
ecs-cli compose --project-name <PROJECT_NAME> service ps --cluster-config <PROJECT_CONFIG_NAME>
```