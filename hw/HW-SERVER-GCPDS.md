# Target System Hardware Context for AI Proofreading Tasks

The following hardware specifications dictate the computing constraints for local AI deployment. Please use this context to understand processing limits, memory boundaries, and potential execution environments.

## System Overview
* **OS:** Ubuntu 26.04 LTS (Resolute Raccoon) x86_64
* **Host Device:** Dell Precision 7520
* **Kernel:** Linux 7.0.0-22-generic

## Compute Specifications
* **CPU:** Intel(R) Xeon(R) CPU E3-1535M v6 @ 3.10GHz (Boost up to 4.20 GHz)
  * **Cores/Threads:** 4 Cores / 8 Threads
* **System Memory (RAM):** 16 GB Total (~15 GiB usable, 14 GiB currently free)
* **Storage:** 468 GB Total (436 GB Available, ext4 filesystem)

## Graphics & Acceleration
* **Discrete GPU:** NVIDIA Quadro M1200 Mobile
  * *Note:* Typically equipped with 4GB GDDR5 VRAM.
  * *Driver Status:* `nvidia-smi` failed to detect the driver. CUDA/NVIDIA drivers need to be installed/configured for hardware acceleration.
* **Integrated GPU:** Intel HD Graphics P630

## Constraints Summary
The system relies primarily on a robust 4-core CPU and 16GB of system RAM. Discrete GPU acceleration is currently bottlenecked by missing drivers and a limited VRAM pool (4GB). Local LLM inference will heavily depend on CPU execution (via RAM) or partial GPU layer offloading once drivers are resolved.