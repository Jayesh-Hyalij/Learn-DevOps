# Kubernetes Documentation

## 1. What is Kubernetes?
Kubernetes (often called K8s) is an open-source platform that helps you manage and run containerized applications easily. It automates the deployment, scaling, and management of applications in containers.

## 2. Why Use Kubernetes?
Kubernetes helps you run applications reliably and efficiently. It manages your containers so you don’t have to do it manually. It can automatically restart containers if they fail, scale them up or down based on demand, and roll out updates without downtime.

## 3. Kubernetes vs Docker
Docker is a tool to create and run containers. Kubernetes is a system to manage many containers across multiple machines. While Docker handles individual containers, Kubernetes helps you orchestrate and manage containers at scale.

## 4. Orchestration Meaning
Orchestration means managing and coordinating multiple tasks automatically. In the context of containers, orchestration means managing many containers, making sure they run properly, communicate with each other, and scale as needed.

## 5. What is K8s?
K8s is a short form for Kubernetes. The "8" represents the eight letters between "K" and "s" in the word Kubernetes. It is widely used as a nickname for Kubernetes.

## 6. Key Features and Benefits of Kubernetes
- **Automatic bin packing:** Efficiently places containers based on resource requirements.
- **Self-healing:** Restarts or replaces containers that fail.
- **Horizontal scaling:** Automatically scales applications up or down.
- **Service discovery and load balancing:** Distributes network traffic to containers.
- **Automated rollouts and rollbacks:** Updates applications without downtime.
- **Secret and configuration management:** Manages sensitive information securely.

## 7. History of Kubernetes
Kubernetes was originally designed by Google but is now managed by the Cloud Native Computing Foundation since September 2014. It was created to solve problems Google faced with managing their internal workloads.

## 8. Programming Language
Kubernetes is written in the Go programming language.

## 9. Why Kubernetes Was Designed
Before Kubernetes, Google used an internal cluster manager called Borg (around 2003-2004) to manage workloads inside the company. Borg was a powerful system that helped Google run thousands of applications efficiently on their servers. However, this system was internal to Google and not available to others.

Many other companies faced similar challenges in managing their applications and infrastructure at scale. They needed a system that could automate deployment, scaling, and management of containerized applications across many machines.

Google studied these common problems and decided to create an open-source system based on their experience with Borg. This new system was named Kubernetes and was released in 2014-2015 to help organizations manage their containerized applications easily and reliably.

The name "Kubernetes" comes from a Greek word meaning captain, helmsman, or pilot.

## 10. Kubernetes Services or Components
- **API Server:** The front end of the Kubernetes control plane.
- **Scheduler:** Assigns work to nodes.
- **Controller Manager:** Manages controllers that regulate the state of the cluster.
- **etcd:** A key-value store for all cluster data.
- **Kubelet:** Agent running on each node to manage containers.
- **Kube-proxy:** Manages network rules on nodes.

## 11. Cluster and Node
- **Cluster:** A set of machines (nodes) that run containerized applications managed by Kubernetes.
- **Node:** A single machine (physical or virtual) in the cluster that runs containers.

## 12. Architecture: Master-Slave Architecture
Kubernetes follows a master-slave architecture where the master node controls and manages the cluster, and the slave nodes (worker nodes) run the containerized applications.

![Kubernetes Architecture](/Assets/kubernetes-architecture.jpg)

## 13. YAML Language
Kubernetes uses YAML files to define configurations such as deployments, services, and pods. YAML is a human-readable language used to describe the desired state of the system.

## 14. Workflow of Kubernetes
1. You define your application and its requirements in YAML files.
2. You submit these files to the Kubernetes API server.
3. The scheduler assigns the work to nodes.
4. Kubelet on each node ensures containers are running as expected.
5. Kubernetes monitors the cluster and makes adjustments automatically.

---

This documentation provides a simple introduction to Kubernetes and its key concepts to help you get started with learning Kubernetes.
