---
author: "li_mingxie"
title: "【docker】docker基本的概念"
date: 2099-07-01T07:28:49+08:00
tags: [
    "docker",
]
categories: [
    "docker"
]
---
### **1.基本概念**

**◆ Kubernetes 是什么？**  
引用官方的解释：  

>
Kubernetes 是一个可移植的、可扩展的开源平台，用于管理容器化的工作负载和服务，可促进声明式配置和自动化。  
Kubernetes 拥有一个庞大且快速增长的生态系统。Kubernetes 的服务、支持和工具广泛可用。<!--more-->  

以我的理解：  
**Kubernetes(简称k8s)是一个可以操作容器和自动化部署的开源平台。**  
简单的说手动管理docker container太累，用Kubernetes管理吧~

### **2.集群结构的简介**

**◆ Node**  
在 Kubernetes 中，节点（Node）是执行工作的机器，以前叫做 minion。  
根据你的集群环境，节点可以是一个虚拟机或者物理机器。每个节点都包含用于运行 pods 的必要服务，并由主控组件管理。  
节点上的服务包括 容器运行时、kubelet 和 kube-proxy。查阅架构设计文档中 Kubernetes 节点 一节获取更多细节。

**◆ Pod**   


**◆ Lable**   


**◆ Service**   


**◆ Replication Controller**  


**◆ Kubernetes Master**  


**◆ Volumes**  


----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`