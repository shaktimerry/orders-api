# orders-api
AKS is already created.
Azure CNI Overlay is used.
ACR stores the image.
Azure Database for PostgreSQL is the database.
Azure Key Vault stores DB credentials.
AKS Workload Identity is used.
Secrets Store CSI Driver mounts Key Vault secrets.
Ingress is managed NGINX/Application Routing.
Network policies restrict east-west traffic.
HPA + PDB + topology spreading are used.
Azure Monitor/Prometheus can observe the workload.
Azure DevOps deploys the manifests.

                    Git / Azure Repo
                           │
              ┌────────────┴────────────┐
              │                         │
        infrastructure/              k8s/
              │                         │
              ▼                         ▼
       Terraform/Bicep             Kubernetes YAML
              │                         │
              ▼                         ▼
     Azure infrastructure          AKS workloads

                             Internet
                           │
                           ▼
                    Azure Front Door
                       + WAF
                           │
                           ▼
                  AKS Ingress / NGINX
                           │
                           ▼
                    orders-api Service
                           │
                 ┌─────────┼─────────┐
                 ▼         ▼         ▼
              Pod-1      Pod-2      Pod-3
                 │         │         │
                 └─────────┼─────────┘
                           │
                     Azure Database
                       PostgreSQL

                           ▲
                           │
                     Private Endpoint
                           │

Pod ──Workload Identity──► Key Vault
                           │
                           ├── DB_USERNAME
                           ├── DB_PASSWORD
                           └── API_KEY

Important: NetworkPolicy support depends on your AKS networking/policy implementation. Microsoft currently recommends Cilium Network Policy for new deployments rather than Azure Network Policy Manager, which is scheduled for retirement on September 30, 2028.
