# Hardware available at University of Chicago Analysis Facility (UC AF)

## Interactive Nodes

| Access Protocol  | Number of Nodes |
| ---------------- | --------------- |
| ssh              | 4               |
| Jupyter Notebook | 4               |

## Batch Compute (HTCondor)

| Queues | Capacity (cores) | Walltime Limit | Notes                                                                                    |
| ------ | ---------------- | -------------- | ---------------------------------------------------------------------------------------- |
| long   | 1520             | 72 Hours       | The long queue jobs run on the hyperconverged nodes                                      |
| short  | 1280             | 4 Hours        | The short queue jobs can run on both the fast compute nodes and the hyperconverged nodes |

## Storage Spaces

| Storage    | Type                 | Capacity                                                       | Default Quota | Notes                                                                                                                                                                                                                        |
| ---------- | -------------------- | -------------------------------------------------------------- | ------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `/data`    | Ceph Filesystem      | 4.5PB                                                          | 5TB           | Storage provided by the batch worker nodes. Two types of disks: spinning disks from hyperconverged nodes form the regular pool (in production), NVMe disks from the fast compute nodes form the fast pool (to be configured) |
| `/cold`    | Ceph Filesystem      | 4.5PB                                                          | 0TB           | Cold storage for parking data that needs to be kept around that cannot be placed in an RSE                                                                                                                                   |
| `/home`    | NFS filesystem       | 19TB                                                           | 100GB         |                                                                                                                                                                                                                              |
| `/scratch` | SSD Local Filesystem | 2TB on the hyperconverged nodes, 6TB on the fast compute nodes | N/A           |                                                                                                                                                                                                                              |

## Hardware Specifications

| Node Type         | Number of Nodes | Processor Per Node                             | Cores Per Node | Memory Per Node   | Storage Per Node                                              | Notes                                              |
| ----------------- | --------------- | ---------------------------------------------- | -------------- | ----------------- | ------------------------------------------------------------- | -------------------------------------------------- |
| Hyperconverged    | 19              | Two AMD EPYC 7402 CPUs at 2.8 GHz              | 48C/96T        | 512 GB DDR4 SDRAM | Two 960 GB SSD, two 2TB NVMe and twelve 16 TB spinning disks. | 3 nodes provided by, and for, UChicago ATLAS group |
| Fast Compute      | 16              | Two Intel(R) Xeon(R) Gold 6348 CPU at 2.60 GHz | 56C/112T       | 384 GB DDR4 SDRAM | Ten 3.2 TB NVMe                                               | Provided by, and for, IRIS-HEP SSL                 |
| Interactive Nodes | 8               | Two AMD EPYC 7402 CPUs at 2.8 GHz              | 48C/96T        | 256 GB DDR4 SDRAM | Two 960 GB SSD                                                | 2 nodes provided by, and for, UChicago ATLAS group |
| XCache Nodes      | 1               | Two Intel(R) Xeon(R) Silver 4214 CPU @ 2.20GHz | 24C/48T        | 192 GB DDR4 SDRAM | Twenty four 1.5 TB NVMe                                       | Two 25 Gbps network links                          |

| Node Type | Number of Nodes | Processor Per Node                             | Cores Per Node | Memory Per Node   | GPUs Per Node (Mem)                    | Storage Per Node |
| --------- | --------------- | ---------------------------------------------- | -------------- | ----------------- | -------------------------------------- | ---------------- |
| GPU A     | 2               | Two AMD EPYC 7543 32-Core Processor            | 64C/128T       | 512 GB DDR4 SDRAM | Four NVIDIA A100 (40G)                 | One 1.5TB NVMe   |
| GPU B     | 1               | Two Intel(R) Xeon(R) Silver 4116 CPU @ 2.10GHz | 24C/48T        | 96 GB DDR4 SDRAM  | Four Tesla V100 (16G)                  | Three 220GB SSD  |
| GPU C     | 3               | Two Intel(R) Xeon(R) Gold 6146 CPU @ 3.20GHz   | 24C/48T        | 192 GB DDR4 SDRAM | Eight NVIDIA GeForce RTX 2080 Ti (12G) | Six 450GB SSD    |
| GPU D     | 1               | Two Intel(R) Xeon(R) CPU E5-2687W v4 @ 3.00GHz | 24C            | 128 GB DDR4 SDRAM | Eight NVIDIA GeForce GTX 1080 Ti (12G) | Six 450GB SSD    |
