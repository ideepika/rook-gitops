Based on your repository structure and the GitOps setup you've been working on, here's a comprehensive README for your `rook-gitops` repository:

# Rook GitOps

**GitOps-based deployment of Rook Ceph storage on Kubernetes using ArgoCD**

[![ArgoCD](https://img.shields.io/badge/GitOps-ArgoCD-blueimg.shields.io/badge/Storage## Overview

This repository contains GitOps configurations for deploying and managing **Rook Ceph** distributed storage on Kubernetes clusters using **ArgoCD**. It provides production-ready configurations with enhanced security, monitoring, and multi-environment support.

## Features

-  **Network encryption** enabled for secure Ceph communications
-  **Monitoring integration** with Prometheus and Grafana
-  **Debug toolbox** for operational maintenance
-  **Multi-environment support** (dev/staging/prod)
-  **Automated GitOps** deployment with ArgoCD
-  **Multiple storage classes** (Block, File System)
-  **High-performance** OSD configuration (8GB memory)
-  **Production-ready** configurations

## Repository Structure

```
infrastructure/
├── dev/
│   └── argocd/
│       ├── applications/           # ArgoCD Application manifests
│       │   ├── rook-operator.yaml
│       │   └── rook-cluster.yaml
│       └── values/                 # Helm values files
│           ├── rook-operator.yaml
│           └── rook-cluster.yaml
├── staging/                        # Staging environment configs
└── prod/                          # Production environment configs
```

## Quick Start

### Prerequisites

- Kubernetes cluster (v1.25+)
- ArgoCD installed and configured
- Storage nodes labeled with `storage-node=true`
- At least 8GB RAM per storage node (for OSD configuration)

### Deploy Rook Operator

```bash
# Apply ArgoCD application for Rook Operator
kubectl apply -f dev/argocd/applications/rook-operator.yaml

# Verify operator deployment
kubectl get pods -n rook-ceph -l app=rook-ceph-operator
```

### Deploy Rook Cluster

```bash
# Apply ArgoCD application for Rook Cluster
kubectl apply -f dev/argocd/applications/rook-cluster.yaml

# Monitor cluster deployment
kubectl get cephcluster -n rook-ceph
kubectl get pods -n rook-ceph --watch
```

### Verify Deployment

```bash
# Access Ceph toolbox
kubectl -n rook-ceph exec -it deploy/rook-ceph-tools -- ceph status

# Check storage classes
kubectl get storageclass
```

## Configuration Highlights

### Network Security
- **Encryption**: Wire-level encryption for all Ceph communications
- **msgr2 protocol**: Modern Ceph messenger protocol required
- **SSL dashboard**: Secure HTTPS access to Ceph dashboard

### Resource Allocation
- **OSD Memory**: 8GB per OSD for optimal performance
- **Priority Classes**: System-critical scheduling for core components
- **Resource Requests/Limits**: Defined for all components

### Storage Configuration
- **Replication**: 3-way replication with minimum 2 replicas
- **Failure Domain**: Host-level fault tolerance
- **Block Storage**: RBD with layering features
- **File System**: CephFS with active-standby MDS

### Operational Features
- **Log Collection**: Daily log collection with 500MB rotation
- **Health Checks**: Comprehensive daemon and liveness probes
- **Disruption Management**: Pod disruption budgets enabled
- **Monitoring**: Prometheus metrics and custom rules

## Storage Classes

This deployment creates the following storage classes:

| Name | Type | Default | Features |
|------|------|---------|----------|
| `ceph-block` | RBD Block | ✅ | Volume expansion, snapshots |

## Accessing Ceph Dashboard

```bash
# Port forward to dashboard
kubectl port-forward svc/rook-ceph-mgr-dashboard -n rook-ceph 8443:8443

# Get admin password
kubectl -n rook-ceph get secret rook-ceph-dashboard-password -o jsonpath="{['data']['password']}" | base64 --decode

# Access: https://localhost:8443
```

## Monitoring

The deployment includes comprehensive monitoring:

- **Ceph metrics** exported to Prometheus
- **Performance counters** with configurable priority limits
- **Custom alert rules** for Ceph-specific monitoring
- **Service monitors** for metrics collection

## Troubleshooting

### Common Commands

```bash
# Check cluster health
kubectl -n rook-ceph exec -it deploy/rook-ceph-tools -- ceph health detail

# View OSD status
kubectl -n rook-ceph exec -it deploy/rook-ceph-tools -- ceph osd status

# Check placement groups
kubectl -n rook-ceph exec -it deploy/rook-ceph-tools -- ceph pg stat

# View logs
kubectl logs -n rook-ceph deployment/rook-ceph-operator
```

### Node Preparation

Ensure storage nodes are properly labeled:

```bash
# Label storage nodes
kubectl label nodes <node-name> storage-node=true

# Verify node labels
kubectl get nodes -l storage-node=true
```

## Environment Management

### Development
- Uses `useAllNodes` and `useAllDevices` for flexibility
- Automated sync policies enabled
- Debug toolbox included

### Production
- Specific device filters and node selectors
- Manual sync policies for controlled deployments
- Enhanced monitoring and alerting

## Security Considerations

- **Network encryption** prevents data interception
- **Container security contexts** with non-root users
- **RBAC** with minimal required permissions
- **Pod security policies** (where supported)
- **Device encryption** for data at rest

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/enhancement`)
3. Test changes in development environment
4. Submit a pull request with detailed description

## Version Compatibility

| Component | Version | Notes |
|-----------|---------|-------|
| Rook | v1.17.8 | Operator and cluster version |
| Ceph | v18.2.4 | Pacific release |
| CSI | v3.15.0 | Container Storage Interface |
| Kubernetes | v1.25+ | Minimum supported version |

## Support

- **Documentation**: [Rook Ceph Documentation](https://rook.io/docs/rook/latest/)
- **Community**: [Rook Slack](https://rook-io.slack.com/)
- **Issues**: [GitHub Issues](https://github.com/rook/rook/issues)

## License

This project follows the same license as Rook Ceph (Apache 2.0).

---

**Built with ❤️ for cloud-native storage**

*This repository demonstrates GitOps best practices for deploying enterprise storage solutions on Kubernetes*.[1][2][3]

[1](https://argo-cd.readthedocs.io/en/stable/user-guide/best_practices/)
[2](https://akuity.io/blog/gitops-best-practices-whitepaper)
[3](https://spacelift.io/blog/gitops-kubernetes)
[4](https://github.com/ideepika/rook-gitops)
[5](https://argo-cd.readthedocs.io)
[6](https://argo-cd.readthedocs.io/en/stable/operator-manual/applicationset/Template/)
[7](https://argo-cd.readthedocs.io/en/stable/user-guide/status-badge/)
[8](https://argo-cd.readthedocs.io/en/stable/operator-manual/notifications/templates/)
[9](https://argo-cd.readthedocs.io/en/release-2.8/operator-manual/applicationset/Template/)
[10](https://platform9.com/blog/gitops-for-kubernetes/)
[11](https://argo-cd.readthedocs.io/en/stable/getting_started/)
[12](https://github.com/andredesousa/kubernetes-best-practices)
[13](https://cloud.google.com/kubernetes-engine/enterprise/config-sync/docs/concepts/gitops-best-practices)
[14](https://argo-cd.readthedocs.io/en/release-2.4/operator-manual/notifications/templates/)
[15](https://www.youtube.com/watch?v=ZgJQG475oME)
[16](https://aws.amazon.com/blogs/containers/kubernetes-right-sizing-with-metrics-driven-gitops-automation/)
[17](https://kubeaid.io/docs/argocd-helm-charts/argo-cd/charts/argo-cd/README)
[18](https://nsddd.top/posts/gitops-practice-theory-part/)
[19](https://www.cncf.io/blog/2024/09/09/deploy-your-first-app-on-kubernetes-with-gitops/)
[20](https://www.alibabacloud.com/blog/practices-for-building-gitops-delivery-based-on-ack-one-and-acr_600067)
