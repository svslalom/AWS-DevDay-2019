# slalom-aws-devday-2019

AWS Dev Day (AWS, Slalom, Snowflake, and Tableau)

A high level description will go here. A high level description will go here. A high level description will go here. A high level description will go here. We'll mention some of the components in the architecture

![alt text](https://slalom-aws-workshop-us-west-2.s3-us-west-2.amazonaws.com/content/images/awsdevday/aws-dev-day.png "AWS Dev Day Architecture")

## Overview

We'll mention the Dev Day is based on the `snowflake-on-ecs` open-sourced framework from Slalom Build. We'll talk about how we added in Tableau Desktop for the analytics consumption. We'll also include some bullets about what you will learn today. We'll ask them to think about how any of this applies to use cases they are currently working through. And, that we'll help them figure it out througout the course of the day. Ideally this is something they can take back to work / home and implement on their own.

- This is something you will learn
- This is something you will learn
- This is something you will learn
- This is something you will learn

## Getting Started

### Download the Code

We'll have a link to the snowflake-on-ecs codebase HERE to keep it simple. Download it, unzip it somewhere you can find later. We'll mention the pre-requisites such as an active Snowflake Trial account. We'll talk about how we'll be using Slalom's AWS Workshop account that we use for events like this. 

### Log into AWS

We'll have them log into AWS and change the default password we gave them. This is probably a good time to have them navigate to SSM and retrieve the `snowflake_user` password they will use during the Snowflake steps.

## Setting Up Snowflake

### Log into Your Snowflake Account

Log into your Snowflake account with the username and password you created when you set it up.

### Setup the Snowflake Framework

Open the `setup_framework.sql` script in the Snowflake UI. We'll take a walk through the script and describe what it is doing and why. Then we'll instruct them how to run it in one shot. We'll explain why we are using this account to setup the framework, and that we'll be switching to another Snowflake account for the remainder of the Dev Day.

### Deploy the Source Database Objects

Log into Snowflake with the `snowflake_user` account and the password specified in the script. 
Open the `deploy_source.sql` script in the Snowflake UI. We'll take a walk through the script and describe what it is doing and why. Then we'll instruct them how to run it in one shot.

## Setting up AWS

### Deploy Foundational Components - Instructor Only

Now that they have Snowflake installed, how can they leverage AWS to automatically load data? We'll build the Docker image, and run through the `snowflake-on-ecs` steps up until the ECS Network template is deployed.

### Deploy Airflow Running on ECS Fargate

Talk about what Airflow is and why we're using it. Talk about how we could really use any workflow engine here, and run whatever we like on ECS Fargate

## Running the Analytics Pipeline

### Code Walk Through - SQL and DAGs

RDS/ECS will take about 10 minutes to launch. Use this time to walk through the Snowflake SQL and the Airflow DAGs used to automate it. 

### Run the Pipeline

Launch Airflow on ECS Task public IP port 8080. Run the Source pipeline to load Source tables. Run Analytics pipeline to load Analytics tables. This will take some time to load. Go back to Snowflake and see the History tab, you can see Snowflake running the jobs and loading data. When it's done, Airflow UI will report success. We know it will since we've run this a billion times. Run a quick SQL query to see the data in the tables `query_analytics.sql`. Hey that's cool but we can do so much more, with Tableau!

## Run Tableau Desktop

TABLEAU STUFF GOES HERE
