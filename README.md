# Amazon ECS Lab - Detailed README

## 📘 Overview

This README provides a complete and structured explanation of the **Amazon Elastic Container Service (ECS) Lab** you completed. It consolidates the entire lab workflow, important concepts, and key steps into a clear, detailed, and ready-to-use document.

This document covers:

* What ECS is
* What you configured in the lab
* Step-by-step breakdown of tasks
* Architecture overview
* Important concepts and terminology
* Final outcomes of the lab

---

# 🟦 1. Introduction

Your company has a partially configured **Amazon ECS cluster** in a test environment. You are tasked with learning ECS by completing the remaining configuration.

In this lab, you learned how to:

* Create **Task Definitions**
* Create an **ECS Cluster**
* Create and deploy an **ECS Service**
* Deploy an **updated application version**
* **Scale** a running ECS service

Before beginning, you should understand:

* Docker basics
* Web applications
* Load balancers
* Basic DevOps concepts

---

# 🟦 2. Lab Objectives

By the end of the lab, you will be able to:

✔ Create an ECS **Task Definition**
✔ Deploy an application to an **ECS Service**
✔ Deploy an updated version using a **new task definition revision**
✔ Scale up your ECS service **capacity**

---

# 🟦 3. Lab Environment

The lab environment provides pre-configured AWS resources:

### Pre-Provisioned Resources

* **VPC** with two public subnets
* **Network Load Balancer (NLB)**
* **EC2 Auto Scaling Group** for ECS launch type
* **Partially configured ECS Cluster**

### Traffic Flow Architecture

```
User → Internet Gateway → Network Load Balancer → ECS Cluster → EC2 Container Instances
```

---

# 🟦 4. Key ECS Concepts

## 🔹 Task Definition

A JSON blueprint describing:

* Container images
* CPU & Memory allocations
* Ports
* Volumes
* Startup commands

## 🔹 Task

A running instance of a task definition.

## 🔹 Service

Ensures a **desired count** of tasks is always running. Supports:

* Auto-recovery of failed tasks
* Rolling updates
* Integration with load balancers

## 🔹 Cluster

A group of EC2 instances or Fargate capacity where ECS tasks run.

## 🔹 Launch Types

* **EC2:** Tasks run on your own EC2 nodes
* **Fargate:** Serverless container compute

---

# 🟦 5. Task 1: Create a Task Definition

### Steps

1. Open **Elastic Container Service** → Task Definitions
2. Choose **Create new task definition (JSON)**
3. Paste the provided JSON definition
4. Create the task definition

This task definition:

* Runs two containers (`simple-app` and `busybox`)
* Uses **httpd** as the web server
* Uses **busybox** to dynamically write an HTML file
* Shares a volume `my-vol` between both containers
* Exposes port 80

### Outcome

✔ Task Definition created for the sample web application.

---

# 🟦 6. Task 2: Create a Service

### Steps

1. Open the Task Definition → **Deploy → Create Service**
2. Use:

   * **Service name:** `myFirstService`
   * **Launch type:** EC2
   * **Desired tasks:** 1
3. Enable Load Balancing

   * Load Balancer: **MyLoadBalancer**
   * Listener: **TCP:80**
   * Target Group: **myTargetGroup**
4. Create the service

### After creation

* Go to the Load Balancer page
* Copy NLB DNS name
* Open it in a new tab

### Outcome

✔ You see the sample app page served from ECS.

---

# 🟦 7. Task 3: Deploy a New Application Version

You updated the app by creating a new revision of the task definition.

### What changed?

* The HTML message changed from **"Congratulations!"** to **"Thank You!"**

### Steps

1. Create a new **task definition revision** with updated content
2. Go to ECS → Cluster → Service → **Update Service**
3. Select the **latest revision**
4. ECS performs a **rolling deployment**:

   * Starts new task
   * Stops old task
   * Ensures no downtime

### Outcome

✔ Application updated to show **Thank You!** without downtime.

---

# 🟦 8. Task 4: Scale the Service

### Steps

1. Open the service → **Update Service**
2. Set **Desired tasks = 2**
3. ECS launches a second task

### Outcome

✔ Service capacity increased → Now running **2 tasks** for better availability.

---

# 🟦 9. Task 5: Review CloudFormation Template (Optional)

You reviewed how AWS CloudFormation was used to create:

* ECS Cluster
* EC2 Auto Scaling Group
* VPC components
* Load Balancer

### Why this matters:

CloudFormation = **Infrastructure as Code (IaC)**
Helps automate ECS deployments in real environments.

---

# 🟦 10. Final Outcomes

You successfully:

✔ Created an ECS Task Definition
✔ Deployed a service to ECS
✔ Updated your application using a new revision
✔ Performed rolling deployments with zero downtime
✔ Scaled a service from 1 to 2 running tasks
✔ Reviewed automated infrastructure provisioned via CloudFormation

This lab gives a complete introduction to **container orchestration using Amazon ECS**.

---

# 🟦 11. Additional Resources

* Amazon ECS Documentation
* ECS Terminology & Components
* Docker Official Docs
* ECS Task Definition Reference
* ECS Service Scheduler Concepts
* AWS CloudFormation Documentation

---

# ✅ End of README

This README gives a structured, beginner-friendly, and complete understanding of your ECS lab. Let me know if you'd like:

* A shorter summary
* A diagram version
* Interview questions on ECS
* A cheat sheet version
