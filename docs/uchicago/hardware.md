# Hardware available at University of Chicago Analysis Facility (UC AF)

## Cluster Summary

| Metric          | Value   |
| --------------- | ------- |
| Total nodes     | 142     |
| Total CPU cores | 10,372  |
| Total memory    | ~39 TiB |
| Total GPUs      | 82      |

## Interactive Nodes

| Access Protocol  | Number of Nodes |
| ---------------- | --------------- |
| ssh              | 4               |
| Jupyter Notebook | 4               |

## Batch Compute (HTCondor)

| Queues | Capacity (cores) | Walltime Limit | Notes                                                                                    |
| ------ | ---------------- | -------------- | ---------------------------------------------------------------------------------------- |
| long   | 1520             | 168 Hours      | The long queue jobs run on the hyperconverged nodes                                      |
| short  | 1280             | 4 Hours        | The short queue jobs can run on both the fast compute nodes and the hyperconverged nodes |

## Storage Spaces

| Storage    | Type                 | Capacity                                                       | Default Quota | Notes                                                                                                                                                                                                                        |
| ---------- | -------------------- | -------------------------------------------------------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `/data`    | Ceph Filesystem      | 4.5PB                                                          | 5TB           | Storage provided by the batch worker nodes. Two types of disks: spinning disks from hyperconverged nodes form the regular pool (in production), NVMe disks from the fast compute nodes form the fast pool (to be configured) |
| `/cold`    | Ceph Filesystem      | 4.5PB                                                          | 0TB           | Cold storage for parking data that needs to be kept around that cannot be placed in an RSE                                                                                                                                   |
| `/home`    | NFS filesystem       | 19TB                                                           | 100GB         |                                                                                                                                                                                                                              |
| `/scratch` | SSD Local Filesystem | 2TB on the hyperconverged nodes, 6TB on the fast compute nodes | N/A           |                                                                                                                                                                                                                              |

## Hardware Specifications

### Node Breakdown

| Node Type             | Number of Nodes | Total Cores | Total Memory |
| --------------------- | --------------- | ----------- | ------------ |
| Compute workers       | 114             | 8,644       | 33.2 TiB     |
| GPU workers           | 8               | 568         | 2.5 TiB      |
| Login nodes           | 8               | 672         | 2.0 TiB      |
| Service/storage nodes | 7               | 320         | 0.8 TiB      |
| Head/management nodes | 4               | 120         | 0.3 TiB      |
| XCache node           | 1               | 48          | 187 GiB      |

### XCache Node

| Node Type    | Number of Nodes | Processor Per Node                              | Cores Per Node | Memory Per Node   | Storage Per Node        | Notes                     |
| ------------ | --------------- | ----------------------------------------------- | -------------- | ----------------- | ----------------------- | ------------------------- |
| XCache Nodes | 1               | Two Intel(R) Xeon(R) Silver 4214 CPU @ 2.20 GHz | 24C/48T        | 192 GB DDR4 SDRAM | Twenty four 1.5 TB NVMe | Two 25 Gbps network links |

### GPU Workers

| Node | GPU Model                  | Schedulable GPUs | Notes                                       |
| ---- | -------------------------- | ---------------- | ------------------------------------------- |
| g001 | NVIDIA A100 SXM4 40GB      | 28 MIG slices    | 4 physical A100s partitioned as 1g.5gb each |
| g002 | NVIDIA A100 SXM4 40GB      | 4                | Full (non-MIG) GPUs                         |
| g003 | NVIDIA GeForce RTX 2080 Ti | 8                |                                             |
| g004 | NVIDIA GeForce GTX 1080 Ti | 8                |                                             |
| g005 | Tesla V100 PCIE 16GB       | 4                |                                             |
| g006 | NVIDIA GeForce RTX 2080 Ti | 8                |                                             |
| g007 | NVIDIA GeForce RTX 2080 Ti | 8                |                                             |
| g008 | NVIDIA H200 NVL            | 14 MIG slices    | Partitioned as 1g.16gb each                 |

#### GPU Summary by Model

| GPU Model                          | Count | Notes                     |
| ---------------------------------- | ----- | ------------------------- |
| NVIDIA A100 SXM4 40GB (MIG slices) | 28    | g001 — 1g.5gb MIG slices  |
| NVIDIA H200 NVL (MIG slices)       | 14    | g008 — 1g.16gb MIG slices |
| NVIDIA GeForce RTX 2080 Ti         | 24    | g003, g006, g007          |
| NVIDIA GeForce GTX 1080 Ti         | 8     | g004                      |
| NVIDIA A100 SXM4 40GB (full)       | 4     | g002                      |
| Tesla V100 PCIE 16GB               | 4     | g005                      |
