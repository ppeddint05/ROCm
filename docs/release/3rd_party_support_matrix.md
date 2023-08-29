# 3rd Party Support Matrix

ROCm™ supports various 3rd party libraries and frameworks. Supported versions
are tested and known to work. Non-supported versions of 3rd parties may also
work, but aren't tested.

(ml_framework_compat_matrix)=

## Deep Learning

ROCm releases support the most recent and two prior releases of PyTorch and
TensorFlow

| ROCm  | [PyTorch](https://github.com/pytorch/pytorch/releases/) | [TensorFlow](https://github.com/tensorflow/tensorflow/releases/) | [MAGMA](https://icl.utk.edu/magma/index.html) |
|:------|:--------------------------:|:--------------------:|:-----:|
| 5.0.2 | 1.8,  1.9,  1.10           | 2.6, 2.7, 2.8        |       |
| 5.1.3 | 1.9,  1.10, 1.11           | 2.7, 2.8, 2.9        |       |
| 5.2.x | 1.10, 1.11, 1.12           | 2.8, 2.9, 2.9        |       |
| 5.3.x | 1.10.1, 1.11, 1.12.1, 1.13 | 2.8, 2.9, 2.10       |       |
| 5.4.x | 1.10.1, 1.11, 1.12.1, 1.13 | 2.8, 2.9, 2.10, 2.11 | 2.5.4 |
| 5.5.x | 1.10.1, 1.11, 1.12.1, 1.13 | 2.10, 2.11           | 2.5.4 |
| 5.6   | 1.11, 1.12.1, 1.13.1       | 2.12                 | 2.5.4 |

## Communication libraries

ROCm supports [OpenUCX](https://openucx.org/) an "an open-source,
production-grade communication framework for data-centric and high-performance
applications".

UCX version | ROCm 5.4 and older | ROCm 5.5 and newer |
|:----------|:------------------:|:------------------:|
| -1.14.0   | COMPATIBLE         | INCOMPATIBLE       |
|  1.14.1+  | COMPATIBLE         | COMPATIBLE         |

The Unified Collective Communication Library [UCC](https://https://github.com/openucx/ucc)
also has support for ROCm devices.

UCC version | ROCm 5.5 and older | ROCm 5.6 and newer |
|:----------|:------------------:|:------------------:|
| -1.1.0    | COMPATIBLE         | INCOMPATIBLE       |
|  1.2.0+   | COMPATIBLE         | COMPATIBLE         |

## Algorithm libraries

ROCm releases provide algorithm libraries with interfaces compatible with
contemporary CUDA / NVIDIA HPC SDK alternatives.

- Thrust → rocThrust
- CUB → hipCUB

| ROCm  | Thrust / CUB | HPC SDK |
|:------|:------------:|:-------:|
| 5.0.2 | 1.14         | 21.9       |
| 5.1.3 | 1.15         | 22.1       |
| 5.2.x | 1.15         | 22.2, 22.3 |
| 5.3.x | 1.16         | 22.7       |
| 5.4.x | 1.16         | 22.9       |
| 5.5.x | 1.17         | 22.9       |
| 5.6   | 1.17.2       | 22.9       |

For the latest documentation of these libraries, refer to the
[associated documentation](../reference/gpu_libraries/c%2B%2B_primitives.md).
