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

1. Copy the contents of the `setup_framework.sql` script (located in the `snowflake` directory) and paste in the Snowflake query editor window.
![alt text](images/image-02.png)
2. Highlight the query text and click on the blue Run button at the top left of the query editor window to run the script. This will create several objects in the database, including a few new databases, and a new user account that we'll use next.
3. Log out of your account by selecting the drop down menu on the top right of the window.
![alt text](images/image-04.png)

### Deploy the Source Database Objects

Log into Snowflake with the `snowflake_user` account and the password specified in the script. 
Open the `deploy_source.sql` script in the Snowflake UI. We'll take a walk through the script and describe what it is doing and why. Then we'll instruct them how to run it in one shot.

1. Log back in to Snowflake with the following credentials:
    - User Name: `snowflake_user`
    - Password: `__CHANGE__`
2. You will immediately be promted to change your password. Enter a secure password and click Finish. You are now logged in to the same Snowflake account as a different user.
3. Copy the contents of the `deploy_source.sql` script (located in the `snowflake` directory) and paste in the Snowflake query editor window.
4. Highlight the query text and click on the blue Run button at the top left of the query editor window to run the script. This will create two stage tables, a file format, and two tables that we'll be loading data from S3 into.
![alt text](images/image-08.png)
5. We're now done with the initial Snowflake framework setup. Next, we'll be provisioning infrastructure in AWS that will allow us to run Airflow jobs to load these Snowflake tables with data.

#

## Setting up AWS

### Deploy Foundational Components - Instructor Only

Now that they have Snowflake installed, how can they leverage AWS to automatically load data? We'll build the Docker image, and run through the `snowflake-on-ecs` steps up until the ECS Network template is deployed.

### Deploy Airflow Running on ECS Fargate

Talk about what Airflow is and why we're using it. Talk about how we could really use any workflow engine here, and run whatever we like on ECS Fargate

1. Log into AWS and navigate to Cloud Formation.
![alt text](images/image-09.png)
2. On the main Cloud Formation page, you'll see that a stack called `ecs-fargate-network` has already been created. This stack contains the core networking infrastucture, including the VPC, subnets, security groups, and ECS cluster, that we'll use as a foundation for our next deployment.
![alt text](images/image-10.png)
3. Click the Create Stack button at the top right of the page. In the "Specify template" section, select "Upload a template file". Click "Choose file" and navigate to the location where the project repository is cloned. Select the `cloudformation/private-subnet-pubilc-service.yml` file. Click Next.
![alt text](images/image-12.png)
4. On the next page, you'll see a list of parameters that we need to set. These will be injected into the Cloud Formation stack creation and will enable you to connect to Airflow from your computer once the ECS task is up and running. Set the following parameters then click Next:
    - Stack name: `ecs-airflow`
    - AllowWebCidrIp: `<your IP address>`
    - Snowflake account: this can be derived from the URL of your Snowlake account. For example: if the URL for your account is `https://ms72259.us-east-1.snowflakecomputing.com/`, then the account ID is `ms72259.us-east-1`
    - SnowflakeRegion - the region of your Snowflake account (corresponds to an AWS region)
  ![alt text](images/image-13.png)
  ![alt text](images/image-14.png)
5. On the next page, leave all of the default options and click Next. Scroll to the bottom of the next page and click Create Stack.
6. You'll be routed to a page that contains details on your stack. Click on the Events tab to see the progress of the stack creation. This process will take between 10 and 20 minutes. Once it's complete, you'll see an event indicating that the creation is complete.
![alt text](images/image-17.png)
7. Now that the stack has been created, navigate to ECS and select the single cluster that is running. 
![alt text](images/image-19.png)
8. On the cluster details page, select the Tasks tab and find the task with a task definition name that corresponds to the name you gave your Cloud Formation stack.
![alt text](images/image-20.png)
9. Select the task to view the task details. In the Network section, copy the Public IP address and paste it in your browser address bar. Append `:8080` to the IP address and navigate to the page. You should see the Airflow user interface.
![alt text](images/image-21.png)
![alt text](images/image-22.png)

## Running the Analytics Pipeline

### Code Walk Through - SQL and DAGs

RDS/ECS will take about 10 minutes to launch. Use this time to walk through the Snowflake SQL and the Airflow DAGs used to automate it. 

### Run the Pipeline

Launch Airflow on ECS Task public IP port 8080. Run the Source pipeline to load Source tables. Run Analytics pipeline to load Analytics tables. This will take some time to load. Go back to Snowflake and see the History tab, you can see Snowflake running the jobs and loading data. When it's done, Airflow UI will report success. We know it will since we've run this a billion times. Run a quick SQL query to see the data in the tables `query_analytics.sql`. Hey that's cool but we can do so much more, with Tableau!

1. In the Airflow UI, you should see two DAGs, `snowflake_analytics` and `snowflake_source`. Toggle the `snowflake_source` switch to On and select the Trigger Dag button in the Links section of the DAG row. When prompted to run the DAG, click OK.
![alt text](images/image-23.png)
2. The `snowflake_source` DAG is now running and loading data into Snowflake. Navigate back to Snowflake and click on the History button. You should see the progress of the queries that are being executed by Airflow.
![alt text](images/image-24.png)
3. After a few minutes, the DAG should be complete. Back in Snowflake, run a quick query on the `public.airline_raw` table to confirm that data was loaded successfully.
![alt text](images/image-26.png) 
![alt text](images/image-27.png)
4. In Airflow, toggle the `snowflake_analytics` switch to On and select the Trigger Dag button in the Links section of the DAG row. When prompted to run the DAG, click OK. This DAG will take data loaded into the stage tables and load it into the final destination tables that can be used for analytical queries.
![alt text](images/image-28.png)
5. Once the DAG execution is complete, navigate back to Snowflake. Copy the contents of the `query_analytics.sql` script (located in the `snowflake` directory) and paste it in the Snowflake query window. Run the query.
![alt text](images/image-31.png)

## Run Tableau Desktop

Please click [here](https://snowflake-lab.s3-us-west-2.amazonaws.com/public/docs/AWS-Slalom-Snowflake-Tableau-DevDay-TableauDesktop-08.20.2019.pdf) to download the Tableau Desktop instructions, then please follow the instructions outlined in the document.
