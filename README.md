# ECS Service on AWS Lab

This repository contains all the resources and configuration files you need for a hands-on lab to learn the basics of Amazon Elastic Container Service (Amazon ECS) using EC2 launch type and a Network Load Balancer. This guide closely follows a structured lab environment that teaches you major ECS concepts by completing a partially configured setup.

## Objectives

By following this lab, you will learn how to:

- Create an ECS Task Definition
- Deploy an application to an ECS Service
- Deploy an application update by changing the task definition
- Scale up an application

## Prerequisites

- Familiarity with Docker containers
- Basic knowledge of server administration and DevOps concepts
- Basic understanding of web applications, ports, HTTP, and load balancing

---

## Lab Architecture Overview

The lab environment consists of the following pre-provisioned AWS resources:

- **Amazon VPC**: Two public subnets in separate Availability Zones (AZs)
- **Network Load Balancer (NLB)**: One node in each public subnet
- **EC2 Auto Scaling Group**: Used as the ECS launch type
- **Amazon ECS cluster**: Partially configured for this lab

**Data flow:**  
External user → Internet Gateway → NLB (Public Subnet) → ECS Cluster (EC2 Instances)

---

## Step-by-step Guide

### 1. Register an ECS Task Definition

A Task Definition is a JSON configuration that describes which Docker images to run and how.

**Example Task Definition:**

```json
{
  "family": "yourApp-demo",
  "containerDefinitions": [
    {
      "name": "simple-app",
      "image": "httpd:2.4",
      "cpu": 10,
      "memory": 300,
      "portMappings": [{ "containerPort": 80, "hostPort": 80 }],
      "mountPoints": [
        {
          "containerPath": "/usr/local/apache2/htdocs",
          "sourceVolume": "my-vol"
        }
      ],
      "essential": true
    },
    {
      "name": "busybox",
      "image": "busybox",
      "cpu": 10,
      "memory": 200,
      "volumesFrom": [
        { "sourceContainer": "simple-app" }
      ],
      "entryPoint": ["sh", "-c"],
      "command": [
        "/bin/sh -c \"while true; do echo '<html> <head> <title>Amazon ECS Sample App</title> <style>body {margin-top: 40px; background-color: #333;} </style> </head><body> <div style=color:white;text-align:center> <h1>Amazon ECS Sample App</h1> <h2>Congratulations!</h2> <p>Your application is now running on a container in Amazon ECS.</p>' > top; /bin/date > date ; echo '</div></body></html>' > bottom; cat top date bottom > /usr/local/apache2/htdocs/index.html ; sleep 1; done\""
      ],
      "essential": false
    }
  ],
  "volumes": [
    { "name": "my-vol" }
  ]
}
```

#### To register a task definition in the AWS Console:

1. Navigate to ECS service.
2. Go to **Task Definitions** → **Create new task definition** → *Create with JSON*.
3. Paste in the JSON above.
4. Click **Create**.

---

### 2. Create an ECS Service

The ECS Service ensures a specified number of task instances are running at all times.

#### Steps:

1. Open your Task Definition (`yourApp-demo`), click **Deploy** → **Create service**.
2. **Service name**: `myFirstService`
3. **Launch type**: EC2
4. **Desired tasks**: `1`
5. **Load balancing**:  
    - Check **Use load balancing**
    - **VPC**: Lab VPC  
    - **Load balancer type**: Network Load Balancer  
    - **Load balancer**: `MyLoadBalancer`
    - **Listener**: `TCP:80`
    - **Target group**: `myTargetGroup`
6. Leave remaining options as default; click **Create**.

*Wait for service creation to complete. The app will be accessible via the load balancer’s DNS name!*

---

### 3. Deploy an Updated Application

#### 3.1. Register a New Task Definition Revision

Change the `<h2>` heading from "Congratulations!" to "Thank You!" in the shell command of the `busybox` container.

1. In **Task Definitions**, select `yourApp-demo` → **Create new revision** → *with JSON*
2. Paste the updated Task Definition JSON (see lab guide).
3. Click **Create**.

#### 3.2. Update the ECS Service

1. Go to **Clusters** → `ECSCluster` → **Services** → `myFirstService`
2. Click **Update Service**
3. Select the latest task definition revision
4. Click **Update**

Monitor the deployment. Refresh your app in the browser to see the message update to **Thank You!**

---

### 4. Scale the Application

Easily increase (or decrease) the number of running tasks.

1. In **Services**, select `myFirstService` → **Update Service**
2. Change **Desired tasks** to `2`
3. Click **Update**
4. Monitor the events until scaling completes
5. The running tasks count should update accordingly

---

### Optional: Review CloudFormation Stack

1. Go to **CloudFormation** in the AWS console.
2. Find the stack containing `myFirstService`, click its link.
3. Review **Template** to see infrastructure-as-code in action.

---

## Additional Resources

- [Amazon ECS Documentation](https://docs.aws.amazon.com/ecs/latest/developerguide/what-is-ecs.html)
- [Amazon ECS Task Definitions](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/task_definitions.html)
- [Service Scheduler Concepts](https://docs.aws.amazon.com/AmazonECS/latest/developerguide/ecs_service_scheduler.html)
- [AWS CloudFormation Getting Started](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/GettingStarted.Walkthrough.html)
- [Docker Documentation](https://docs.docker.com/)

---

## Conclusion

By following this lab, you learned how to:

- Register an ECS task definition
- Deploy and update an ECS service
- Perform a rolling application update
- Scale an ECS service
- Review automation via CloudFormation

Happy containerizing!
