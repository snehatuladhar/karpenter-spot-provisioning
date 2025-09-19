# Karpenter Spot Node Provisioning Workflow

This workflow provisions **Karpenter** with a **Spot NodePool** on an existing EKS cluster. It automatically creates the necessary namespaces, installs Karpenter CRDs, the Helm chart, and applies the Spot EC2NodeClass and NodePool configuration.

---

## Prerequisites

All underlying infrastructure must already be provisioned before running this workflow:

- VPC
- EKS Cluster
- IAM roles
- Terraform state files
- Node groups

---

## Workflow Overview

**Name:** Karpenter Provisioning  
**Trigger:** `workflow_dispatch`  

**Inputs:**

| Input       | Description                                    |
|------------|-----------------------------------------------|
| `commitsha` | Commit hash to use for manifest rollback (optional) |
| `environment` | Target environment (`dev`, `stage`, `prod`) |

**Key Steps:**

1. Checkout the repository and set up environment variables based on the selected environment.  
2. Configure AWS credentials using OIDC for the target account.  
3. Retrieve existing Terraform outputs (cluster name, NodeGroup name).  
4. Update kubeconfig for the EKS cluster.  
5. Create required Kubernetes namespaces using Helm.  
6. Substitute the cluster name and nodegroup in Karpenter manifests.  
7. Install Karpenter CRDs for NodePool, NodeClass, and NodeClaims.  
8. Install Karpenter Helm chart with Spot configuration.  
9. Apply Spot EC2NodeClass and NodePool manifests.
8. You can add values according to the environment currently provisoning for dev environment


---

## How It Works

- **Namespaces:** Ensures required namespaces exist before installing Karpenter.  
- **Karpenter CRDs:** NodePool, NodeClass, and NodeClaims resources are required for dynamic provisioning.  
- **Spot NodePool:** Configured to launch Spot instances with the desired instance families, zones, and other constraints.  
- **Cost Optimization:** Karpenter dynamically provisions nodes only when needed and consolidates underutilized nodes to reduce costs.

---

## Usage

1. Ensure all infrastructure is provisioned and Terraform state files exist.  
2. Trigger the workflow from GitHub Actions:  

