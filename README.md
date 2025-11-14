# Amazon ECS Lab

This README is a **fully detailed**, structured, and beginner-friendly explanation of the complete Amazon ECS Lab you performed. It includes **all code**, explanations, architecture, and steps from start to finish.

---

# 📘 1. Introduction

In this lab, your company has a **partially configured ECS cluster**, and your goal is to learn Amazon ECS by completing the missing pieces.

You will configure:

* **Task Definition** → describes the containers
* **Cluster** → group of EC2 instances running ECS agent
* **Service** → runs & maintains your containers continuously

---

# 🎯 2. Lab Objectives

By the end of this lab, you will:

* ✔ Create an **ECS Task Definition**
* ✔ Deploy an application to an **ECS Service**
* ✔ Update the application by creating a **new task definition revision**
* ✔ Scale up the application using **service scaling**

---

# 🧠 3. Prerequisites

You should know:

* Basics of **Docker containers**
* Basic **DevOps** terms
* Understanding of **web applications**, **TCP ports**, **load balancers**

---

# 🏗 4. Lab Environment Overview

AWS pre-provisioned the following resources:

### ✔ VPC with 2 public subnets

### ✔ Network Load Balancer

### ✔ Auto Scaling Group (launching ECS-ready EC2 instances)

### ✔ ECS Cluster (partial)

### 🖥 Architecture Flow

```
User → Internet Gateway → Network Load Balancer → ECS Cluster → EC2 Instances → Containers
```

These components allow you to deploy containers using the **EC2 launch type**.

---

# 📦 5. Key ECS Concepts

### ▶ Task Definition

A JSON template describing:

* Container images
* CPU/memory
* Network ports
* Volumes
* Commands to run

### ▶ Task

A running instance of a task definition.

### ▶ Service

Manages how many tasks run and attaches them to load balancers.

### ▶ Cluster

Group of EC2 or Fargate compute resources.

### ▶ Launch Types

* **EC2** → You manage EC2 instances
* **Fargate** → Serverless containers

---

# 🧩 6. Task 1 — Create a Task Definition

Open **ECS Console → Task Definitions → Create new task definition with JSON**.

Delete everything and paste this JSON:

---

# 📜 **Task Definition (Original Version)**

```json
{
  "family": "yourApp-demo",
  "containerDefinitions": [
    {
      "volumesFrom": [],
      "portMappings": [
        {
          "hostPort": 80,
          "containerPort": 80
        }
      ],
      "command": null,
      "environment": [],
      "essential": true,
      "entryPoint": null,
      "links": [],
      "mountPoints": [
        {
          "containerPath": "/usr/local/apache2/htdocs",
          "sourceVolume": "my-vol",
          "readOnly": null
        }
      ],
      "memory": 300,
      "name": "simple-app",
      "cpu": 10,
      "image": "httpd:2.4"
    },
    {
      "volumesFrom": [
        {
          "readOnly": null,
          "sourceContainer": "simple-app"
        }
      ],
      "portMappings": [],
      "command": [
        "/bin/sh -c \"while true; do echo '<html> <head> <title>Amazon ECS Sample App</title> <style>body {margin-top: 40px; background-color: #333;} </style> </head><body> <div style=color:white;text-align:center> <h1>Amazon ECS Sample App</h1> <h2>Congratulations!</h2> <p>Your application is now running on a container in Amazon ECS.</p>' > top; /bin/date > date ; echo '</div></body></html>' > bottom; cat top date bottom > /usr/local/apache2/htdocs/index.html ; sleep 1; done\""
      ],
      "environment": [],
      "essential": false,
      "entryPoint": [
        "sh",
        "-c"
      ],
      "links": [],
      "mountPoints": [],
      "memory": 200,
      "name": "busybox",
      "cpu": 10,
      "image": "busybox"
    }
  ],
  "volumes": [
    {
      "host": {
        "sourcePath": null
      },
      "name": "my-vol"
    }
  ]
}
```

✔ This creates two containers:

* **simple-app (httpd)** → serves webpage
* **busybox** → continuously regenerates HTML

✔ Both containers share the same **volume** `/usr/local/apache2/htdocs`

✔ Result: A web page that updates the time every second

---

# 🛠 7. Task 2 — Create an ECS Service

Steps:

1. Go to your Task Definition → **Deploy → Create Service**
2. Set:

   * **Service name:** `myFirstService`
   * **Launch type:** EC2
   * **Desired count:** 1
3. Enable Load Balancer:

   * NLB: **MyLoadBalancer**
   * Listener: **TCP:80**
   * Target Group: `myTargetGroup`
4. Create the service

Open the load balancer DNS:

```
MyLoadBalancer-xxxx.elb.us-east-1.amazonaws.com
```

You should see the running sample application.

---

# 🔄 8. Task 3 — Deploy a New Application Version

You create a **new revision** of the task definition.

### Change: "Congratulations!" → "Thank You!"

---

# 📜 **Task Definition (Updated Version)**

```json
{
  "family": "yourApp-demo",
  "containerDefinitions": [
    {
      "volumesFrom": [],
      "portMappings": [
        {
          "hostPort": 80,
          "containerPort": 80
        }
      ],
      "command": null,
      "environment": [],
      "essential": true,
      "entryPoint": null,
      "links": [],
      "mountPoints": [
        {
          "containerPath": "/usr/local/apache2/htdocs",
          "sourceVolume": "my-vol",
          "readOnly": null
        }
      ],
      "memory": 300,
      "name": "simple-app",
      "cpu": 10,
      "image": "httpd:2.4"
    },
    {
      "volumesFrom": [
        {
          "readOnly": null,
          "sourceContainer": "simple-app"
        }
      ],
      "portMappings": [],
      "command": [
        "/bin/sh -c \"while true; do echo '<html> <head> <title>Amazon ECS Sample App</title> <style>body {margin-top: 40px; background-color: #333;} </style> </head><body> <div style=color:white;text-align:center> <h1>Amazon ECS Sample App</h1> <h2>Thank You!</h2> <p>Your application is now running on a container in Amazon ECS.</p>' > top; /bin/date > date ; echo '</div></body></html>' > bottom; cat top date bottom > /usr/local/apache2/htdocs/index.html ; sleep 1; done\""
      ],
      "environment": [],
      "essential": false,
      "entryPoint": [
        "sh",
        "-c"
      ],
      "links": [],
      "mountPoints": [],
      "memory": 200,
      "name": "busybox",
      "cpu": 10,
      "image": "busybox"
    }
  ],
  "volumes": [
    {
      "host": {
        "sourcePath": null
      },
      "name": "my-vol"
    }
  ]
}
```

### Update Service Steps:

1. ECS → Cluster → `myFirstService`
2. Click **Update Service**
3. Select the **latest revision**
4. ECS performs a **rolling update**:

   * Runs new tasks
   * Stops old tasks
   * Ensures no downtime

Refresh the load balancer → You now see **Thank You!**

---

# 📈 9. Task 4 — Scale the Service

Steps:

1. ECS → Clusters → **myFirstService**
2. Update Service → **Desired tasks = 2**

ECS scheduler:

* Starts a second container
* Balances traffic via NLB

✔ Service now runs **2 tasks** for higher availability

---

# 📄 10. Task 5 — Review CloudFormation Template (Optional)

CloudFormation created:

* ECS Cluster
* EC2 Auto Scaling Group
* Load Balancer
* IAM roles
* Networking

This demonstrates **Infrastructure as Code (IaC)** best practices.

---

# 🏁 11. Final Results

You successfully learned:

* ✔ Creating ECS task definitions
* ✔ Deploying ECS services
* ✔ Exposing containers via NLB
* ✔ Updating apps using new revisions
* ✔ Rolling deployments
* ✔ Scaling ECS services
* ✔ CloudFormation infrastructure review

This completes your ECS foundational learning.

---

# 📚 12. Additional Resources

* Amazon ECS Docs
* ECS Task Definition Reference
* ECS Service Scheduler
* Docker Docs
* CloudFormation Docs

---

