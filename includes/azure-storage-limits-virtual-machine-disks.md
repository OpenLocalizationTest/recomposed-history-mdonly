
**Disks in Standard Storage Accounts**

Standard Storage Disk Type | Basic Tier VM | Standard Tier VM
---|---|---
Disk size | 1023 GB | 1023 GB
IOPS per disk<sup>2</sup> | 300 | 500
Throughput per disk | 8 KB per second |  8 KB per second
Max number of highly utilized disks<sup>3</sup> | 66 | 40

<sup>1</sup>See [Virtual machine sizes](../virtual-machines/virtual-machines-size-specs.md) for additional details.

<sup>2</sup>The total request rate limit for a standard storage account is 20,000 IOPS. If a virtual machine utilizes the maximum IOPS per disk, then to avoid possible throttling, ensure that the total IOPS across all of the virtual machines' VHDs does not exceed the standard storage account limit (20,000 IOPS). See [Virtual machine sizes](../virtual-machines/virtual-machines-size-specs.md) for additional details.

<sup>2</sup>You can roughly calculate the number of highly utilized disks supported by a single standard storage account based on the transaction limit. For example, for a Basic Tier VM, the maximum number of highly utilized disks is about 66 (20,000/300 IOPS per disk), and for a Standard Tier VM, it is about 40 (20,000/500 IOPS per disk). However, note that the storage account can support a larger number of disks if they are not all highly utilized at the same time.


**Disks in Premium Storage Accounts**

Premium Storage Disk Type | P10 | P20 | P30
---|---|---|---
Disk size | 128 GiB | 512 GiB | 1024 GiB (1 TB)
IOPS per disk | 500 | 2300 | 5000
Throughput per disk | 100 MB per second | 150 MB per second | 200 MB per second
Max number of highly utilized disks | ? | ? | ?