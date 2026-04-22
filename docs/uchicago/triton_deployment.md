# Triton Inference Server - Deployment Documentation

## Overview

This document outlines the technical deployment of the NVIDIA Triton Inference
Server on the UChicago Analysis Facility Kubernetes cluster. The deployment is
based on the ManiacLab Helm chart with several custom configurations to improve
performance, fault tolerance, and integration with existing monitoring and
scaling systems.

!!! note "Audience"

    This documentation is intended for system administrators and infrastructure
    engineers. Users looking for information on how to use Triton should refer to
    the [Triton User Guide](triton.md).

## Deployment Method

Triton is deployed via Helm using the ManiacLab on-premises Helm chart:

**FluxCD Configuration:**
[https://github.com/maniaclab/flux_apps/tree/main/af/triton](https://github.com/maniaclab/flux_apps/tree/main/af/triton)

**Chart Repository:**
[https://github.com/maniaclab/triton-inference-server/tree/main/deploy/k8s-onprem](https://github.com/maniaclab/triton-inference-server/tree/main/deploy/k8s-onprem)

The chart has been modified to meet specific operational requirements for the
UChicago Analysis Facility infrastructure.

## Key Customizations

The following customizations have been applied to the standard Helm chart
deployment:

### 1. GPU Node Affinity

**Configuration:** Node affinity rules ensure Triton pods are scheduled
exclusively on GPU-enabled nodes.

**Purpose:** Guarantees optimal performance by ensuring inference workloads run
on nodes with GPU hardware acceleration.

### 2. Fault-Tolerant Model Loading

**Configuration:** Triton is configured to allow failed model loads without
impacting service availability.

**Purpose:** Prevents a single faulty model from bringing down the entire Triton
service. If one model fails to load, other models remain accessible and the
service continues operating normally.

### 3. Model Repository Polling

**Configuration:** Model repository polling enabled with 60-second interval.

**Purpose:** Automatically detects and loads new models added to the S3
repository without requiring manual service restarts. When users upload new
models (after admin approval), Triton discovers them within one minute.

## Monitoring Integration

Triton integrates with the UChicago AF system-wide monitoring infrastructure for
metrics collection and observability.

### Prometheus Adapter

**Integration:** Triton metrics are collected via the Prometheus Adapter.

**Custom Metrics:** A custom metric rule, `avg_time_queue_us`, is defined in the
adapter chart to expose Triton-specific latency data.

**Usage:** Metrics are used for:

- Performance monitoring and alerting
- Autoscaling decisions (see Service Configuration below)
- Capacity planning and optimization

## Service Configuration

### Service Type

**Type:** ClusterIP service

**Access:** Internal to Kubernetes cluster only (not externally exposed)

**Endpoint:** `triton-traefik.triton.svc.cluster.local:8000`

This configuration ensures Triton is accessible only to workloads running within
the UChicago AF Kubernetes cluster, maintaining security and access control.

### Horizontal Pod Autoscaler (HPA)

**Configuration:**

- **Minimum replicas:** 1
- **Maximum replicas:** 3
- **Scaling metric:** `avg_time_queue_us` (average request queue time)

**Behavior:** The HPA dynamically adjusts the number of Triton server instances
based on request latency. When queue times increase (indicating high load),
additional instances are spawned. When load decreases, instances are scaled down
to conserve resources.

## Validation

To validate the Triton deployment, administrators should perform the following
checks:

### 1. Verify Pod Scheduling

Confirm that Triton pods are running on GPU-enabled nodes:

```bash
kubectl get pods -o wide
```

Look for Triton pods and verify they're scheduled on nodes with GPU resources.

### 2. Check Model Repository Synchronization

Verify model repository polling is working by examining Triton logs:

```bash
kubectl logs <triton-pod-name> | grep -i "model"
```

You should see periodic polling messages and successful model loads.

### 3. Verify Prometheus Metrics

Confirm that the `avg_time_queue_us` metric is exposed and visible to
Prometheus:

```bash
# Query Prometheus for Triton metrics
curl http://<prometheus-endpoint>/api/v1/query?query=avg_time_queue_us
```

The metric should return current queue time values.

### 4. Confirm HPA Scaling

Verify that the Horizontal Pod Autoscaler is active and monitoring the
deployment:

```bash
kubectl get hpa
```

Check that the HPA shows current metrics and is within the configured replica
range (1-3).

## Future Improvements

The following enhancements are planned or under consideration for future
deployment iterations:

### 1. External Access

**Current:** ClusterIP service (internal only) **Planned:** Expose service via
Ingress or LoadBalancer for external requests

This would enable access to Triton from outside the Kubernetes cluster,
supporting remote inference requests from batch jobs or external analysis
workflows.

### 2. Enhanced Autoscaling

**Current:** Scaling based on request latency (`avg_time_queue_us`) **Planned:**
Incorporate GPU utilization metrics into autoscaling logic

This would provide more sophisticated autoscaling that considers both request
queue depth and actual GPU resource utilization.

### 3. Model-Type Segregation

**Current:** Single Triton deployment serving all model types **Planned:**
Deploy multiple specialized Triton servers for different model categories

Examples:

- **NLP Server:** Natural Language Processing models
- **CV Server:** Computer Vision models
- **GNN Server:** Graph Neural Network models

Benefits:

- Optimized resource allocation per model type
- Isolated failure domains
- Specialized GPU configurations per workload type

---

**Related Documentation:**

- [Triton User Guide](triton.md) - User-facing documentation for accessing and
  using Triton
- [NVIDIA Triton Documentation](https://docs.nvidia.com/deeplearning/triton-inference-server/user-guide/docs/) -
  Official Triton documentation
