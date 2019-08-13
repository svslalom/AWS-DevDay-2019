# slalom-aws-devday-2019

AWS Dev Day (AWS, Slalom, Snowflake, and Tableau)

A high level description will go here. A high level description will go here. A high level description will go here. A high level description will go here. We'll mention some of the components in the architecture

![alt text](https://slalom-aws-workshop-us-west-2.s3-us-west-2.amazonaws.com/content/images/awsdevday/aws-dev-day.png "AWS Dev Day Architecture")

## Overview

We'll mention the Dev Day is based on the `snowflake-on-ecs` open-sourced framework from Slalom Build. We'll talk about how we added in Tableau Desktop for the analytics consumption. We'll also include some bullets about what you will learn today.

- This is something you will learn
- This is something you will learn
- This is something you will learn
- This is something you will learn

## Getting Started

We'll have a link to the snowflake-on-ecs codebase HERE to keep it simple. We'll mention the pre-requisites such as an active Snowflake Trial account. We'll talk about how they will download Tableau. We should probably do all the setup initially to get everyone at the same step before we continue. Maybe at this point we have them log into AWS and change their default password?


## Deploying to AWS

### Build Docker image

Start Docker host and run the following command.

``` bash
docker build -t slalombuild/airflow-ecs .
```

### Deploy Docker image to Amazon ECR

Create the ECR repository in your AWS account using the AWS CLI tool

```bash
aws ecr create-repository --repository-name slalombuild/airflow-ecs --region us-west-2
{
    "repository": {
        "repositoryArn": "arn:aws:ecr:us-west-2:999999999999:repository/slalombuild/airflow-ecs",
        "registryId": "999999999999",
        "repositoryName": "slalombuild/airflow-ecs",
        "repositoryUri": "999999999999.dkr.ecr.us-west-2.amazonaws.com/slalombuild/airflow-ecs",
        "createdAt": 1560650383.0
    }
}
```

Tag your image with the repositoryUri value from the previous step

```bash
docker tag slalombuild/airflow-ecs \
999999999999.dkr.ecr.us-west-2.amazonaws.com/slalombuild/airflow-ecs:1.10.4
```

Get the docker login authentication command string for your registry.

```bash
aws ecr get-login --no-include-email --region us-west-2
```

Run the `docker login` command that was returned in the previous step. This command provides an authorization token that is valid for 12 hours.

```bash
docker login -u AWS -p
abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890abcdef1234567890
...
abcdef1234567890abcdef123 = https://999999999999.dkr.ecr.us-west-2.amazonaws.com
```

Push the image to your ECR repository with the repositoryUri value from the earlier step.

```bash
docker push 999999999999.dkr.ecr.us-west-2.amazonaws.com/slalombuild/airflow-ecs:1.10.4
```

### Set SSM Parameters

#### String Parameters

Set Airflow backend Postgres database name and username

```bash
aws ssm put-parameter --name /airflow-ecs/AirflowDbName --type String \
--value "airflowdb"

aws ssm put-parameter --name /airflow-ecs/AirflowDbUser --type String \
--value "airflow"
```

Set Snowflake username

```bash
aws ssm put-parameter --name /airflow-ecs/SnowflakeUser --type String  \
--value "snowflake_user"
```

Set ECR image url

```bash
aws ssm put-parameter --name /airflow-ecs/ImageUrl \
--type String --value "999999999999.dkr.ecr.us-west-2.amazonaws.com/slalombuild/airflow-ecs:1.10.4"
```

#### Secure String Parameters

Set passwords for Airflow backend Postgres db and Snowflake

```bash
aws ssm put-parameter --name /airflow-ecs/AirflowDbCntl --type SecureString \
--value "xxxxxxxxxxx"

aws ssm put-parameter --name /airflow-ecs/SnowflakeCntl --type SecureString  \
--value "xxxxxxxxxxx"
```

Generate and set Fernet key for Airflow cryptography

```bash
python -c "from cryptography.fernet import Fernet; FERNET_KEY = Fernet.generate_key().decode(); print(FERNET_KEY)"
abcdef1234567890abcdef1234567890abcdef12345=

aws ssm put-parameter --name /airflow-ecs/FernetKey --type SecureString \
--value "abcdef1234567890abcdef1234567890abcdef12345="
```

### Deploy CloudFormation Stacks

See CloudFormation template file for full list of parameters and defaults.

#### Network Stack

Create VPC, Subnets, and ECS cluster

```bash
aws cloudformation deploy --template-file ./cloudformation/private-vpc.yml \
    --stack-name ecs-fargate-network \
    --capabilities CAPABILITY_IAM
```

#### ECS Service Stack

Create ECS Service and Task Definition

```bash
# Hit https://www.whatsmyip.org for AllowWebCidrIp value
aws cloudformation deploy --template-file ./cloudformation/private-subnet-pubilc-service.yml \
    --stack-name ecs-fargate-service \
    --parameter-overrides \
        StackName=ecs-fargate-network \
        AllowWebCidrIp=xxx.xxx.xxx.xxx/32 \
        SnowflakeAccount=ab12345
```

## Running in AWS

### Run Snowflake DAGs

- Navigate to ECS in AWS Console
- Browse to the Service you created
- Get the public IP of the running task
- Browse to the http://yourtaskpublicip:8080 to reach the Airflow web UI
- Enable the schedule for the `snowflake_source` DAG and manually trigger a launch
- Once DAG runs are complete, do the same for the `snowflake_analytics` DAG
- Once complete, query the `analytics` tables you just built in Snowflake

## Upcoming Features
- Airflow Celery executor support for scaling out
- Integration with [dbt](http://getdbt.com) for building data models

