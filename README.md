# Comprehensive Technical Analysis: High-Availability K3s Cluster Deployment on AWS

**Candidate Name:** Samantha Makondo
**Student Identifier:** 230667201 
**Academic Program:** Advanced Diploma in IT

---

## 🏗 Detailed Architectural Framework

### Theoretical Foundation: The Evolution of K3s
K3s represents a paradigm shift in container orchestration, specifically engineered to address the localized demands of **Edge Computing** and **Internet of Things (IoT)** deployments. By functioning as a fully compliant, CNCF-certified Kubernetes distribution, K3s provides a sophisticated management layer while maintaining a minimal resource footprint. 

Unlike traditional "upstream" Kubernetes, which includes a vast array of legacy drivers and external cloud-provider dependencies, K3s optimizes the binary by removing non-essential components. This results in a consolidated package under 100MB, significantly reducing the attack surface for security vulnerabilities and simplifying the lifecycle management of clusters deployed in geographically dispersed or resource-constrained environments, such as modern 5G telecommunications hubs.

### Core Architectural Subsystems
* **Primary Control Plane (Server Node):** This serves as the centralized "intelligence" of the cluster. It hosts the **Kubernetes API Server**, which acts as the front-end for the cluster’s control plane, along with the **Scheduler** for resource allocation and the **Controller Manager** for maintaining state consistency.
* **Agent Nodes (Worker Layer):** These instances constitute the execution fabric where containerized microservices and virtualized network functions reside. They communicate directly with the control plane to receive task assignments and report health status.
* **Optimized Container Runtime:** K3s utilizes **containerd** as its default runtime. Containerd is a lightweight, industry-standard daemon that manages the complete container lifecycle on its host system—from image transfer and storage to container execution and supervision.
* **Abstracted Networking (CNI):** To facilitate inter-pod communication, K3s implements **Flannel**. This Container Network Interface (CNI) establishes a Layer 3 VXLAN-based overlay networking fabric, allowing pods across different virtual machines to communicate via secure encapsulated tunnels.
* **High-Efficiency Datastore (Kine):** In a significant departure from standard Kubernetes, K3s utilizes the **Kine** abstraction layer to replace the resource-heavy `etcd` database with **SQLite**. This drastically reduces the memory and CPU overhead required to maintain the cluster's state.
* **Embedded Service Delivery Services:** K3s provides out-of-the-box support for **ServiceLB** (a simple load balancer) and the **Traefik Ingress Controller**, which manages sophisticated external access to the internal services via HTTP and HTTPS routing.

---

## 🖥 Infrastructure & Provisioning Specifications

To ensure a robust High-Availability (HA) environment capable of handling simulated 5G workloads, the following AWS EC2 instances were provisioned within the `us-east-1` region:

| Component | Strategic Functional Role | Instance Type | Hardware Allocation | Operating System |
| :--- | :--- | :--- | :--- | :--- |
| **Master-1** | Initial Cluster Leader / API Gateway | t3.large | 2 vCPU / 8GB RAM | Ubuntu 22.04 LTS |
| **Master-2** | High Availability Peer / Replicated State | t3.large | 2 vCPU / 8GB RAM | Ubuntu 22.04 LTS |
| **Master-3** | High Availability Peer / Consensus Member | t3.large | 2 vCPU / 8GB RAM | Ubuntu 22.04 LTS |

The selection of the **t3.large** instance family was intentional, providing sufficient burstable CPU credits and enough RAM to host both the control plane components and the simulated network functions simultaneously without resource contention.

---

## 🛠 Deployment & Provisioning Workflow

### 1. Network Topology and Security Hardening
A dedicated **AWS Virtual Private Cloud (VPC)** was architected to provide a logically isolated section of the AWS Cloud. Within this VPC, specialized **Security Groups** were meticulously defined to enforce a "least-privilege" security posture, whitelisting only the deterministic traffic required for Kubernetes orchestration:

* **TCP Port 6443:** Dedicated for secure, encrypted communication between the nodes and the Kubernetes API Server.
* **UDP Port 8472:** Essential for the **Flannel VXLAN** overlay, which encapsulates pod-to-pod traffic across different virtual instances.
* **TCP Port 10250:** Utilized for Kubelet-to-Master telemetry, allowing the control plane to monitor node health and resource utilization metrics.

---

## 📸 Empirical Deployment Evidence

### 3.1 Cluster Synchronicity and Node Status
The following evidentiary output validates the cluster's structural integrity. It confirms that all provisioned nodes have successfully negotiated the handshake process with the master node and have reached a stabilized **Ready** state.
![Nodes Status]<img width="952" height="948" alt="Screenshot 2026-03-26 213354" src="https://github.com/user-attachments/assets/6dca855f-f681-488e-8536-d6110d9270a3" />


### 3.2 System-Level Pod Health & Networking Fabric
This report provides a granular view of the `kube-system` namespace. It demonstrates the operational health of the core controllers, including **CoreDNS** for service discovery, **Metrics Server** for resource monitoring, and **Traefik** for ingress management.
![Pods Status]<img width="943" height="947" alt="Screenshot 2026-03-26 213429" src="https://github.com/user-attachments/assets/6dd596b0-f52a-4af7-ba45-74874ad14fce" />


### 3.3 Cloud Resource Management (AWS Console)
The AWS Management Dashboard serves as final confirmation that the underlying infrastructure is active and running within the specified availability zones, ensuring no billing or provisioning errors occurred.
![AWS Console]<img width="1914" height="805" alt="Screenshot 2026-03-26 213519" src="https://github.com/user-attachments/assets/1c995643-a9b0-4792-adf8-5ce3e512cc4f" />


---

## 🔍 Technical Analysis & Critical Reflection

### 4.1 Pedagogical Knowledge and Skill Acquisition
This assignment facilitated a comprehensive exploration into the practical implementation of **Cloud-Native technologies**. Beyond mere command execution, I developed a profound understanding of how AWS networking primitives—specifically VPCs and Security Groups—intersect with container orchestration. Gaining hands-on experience in managing the decoupling of the control plane from the data plane has clarified the mechanics of high availability and the importance of decentralized systems in modern IT infrastructure.

### 4.2 Engineering Challenges & Remediation Strategies
The deployment process was not without significant technical hurdles, which provided valuable debugging experience:
* **GitHub Authentication Conflict (403 Error):** I encountered a persistent "Permission Denied" error during upstream repository synchronization. This was identified as a misconfiguration in the local Git origin mapping. To resolve this, I re-mapped the remote URL and re-authenticated using a Personal Access Token (PAT) with appropriate scopes.
* **Handshake Latency & Connection Timeouts:** Initial attempts to join worker nodes to the master resulted in connection timeouts. A systematic audit of the AWS security layer revealed that the firewall was dropping UDP traffic on port **8472**. Once this port was explicitly whitelisted alongside TCP **6443**, the nodes achieved immediate synchronization.

### 4.3 Strategic Utility of K3s in the 5G Ecosystem
In the context of the **5G Service Based Architecture (SBA)**, K3s is a cornerstone technology for **Multi-access Edge Computing (MEC)**. By replacing the resource-intensive `etcd` with a lightweight **SQLite** database, K3s significantly reduces the hardware requirements for edge nodes. This allows telecommunications providers to deploy **Containerized Network Functions (CNFs)**—such as the **User Plane Function (UPF)**—on low-power hardware located near cell towers, ensuring ultra-low latency for 5G users.

### 4.4 Synergistic Scalability: Virtualization & Containerization
The convergence of **Virtualization (via AWS EC2)** and **Containerization (via K3s)** creates an incredibly elastic foundation for modern network engineering. Virtualization provides the ability to abstract hardware and provision servers in minutes, while containerization ensures that applications remain portable and isolated. Together, these technologies support **Horizontal Auto-scaling**, allowing a cluster to dynamically increase its capacity during traffic surges (such as peak hours in a mobile network) and scale back down to minimize operational expenditure when demand decreases.
