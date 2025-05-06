# ⚡ Parallel SSSP Update Framework for Dynamic Networks

A scalable, platform-independent implementation of the **Single-Source Shortest Path (SSSP) update algorithm** for large-scale dynamic graphs. This project leverages **MPI, OpenMP, OpenCL, and METIS** to accelerate SSSP updates in evolving networks.

> 📌 Developed as a final project for the **Parallel and Distributed Computing (PDC)** course at **FAST-NUCES, Islamabad**.

---

## 📖 Overview

Modern networks such as social graphs, road networks, and communication systems evolve constantly. Traditional algorithms like **Dijkstra’s** are inefficient for such dynamic environments due to frequent, full recomputation.

This project implements an optimized **two-phase parallel SSSP update algorithm** based on [Khanda et al., 2022]. Our solution supports:
- Distributed memory (via **MPI**)
- Shared memory (via **OpenMP**)
- GPU acceleration (via **OpenCL**)
- Graph partitioning (via **METIS**)

---

## 🚀 Key Features

### ✅ Dynamic SSSP Updates  
Efficiently handles edge insertions and deletions without full recomputation.

### ⚙️ Two-Phase Parallel Algorithm  
1. **Identify Affected Subgraph**  
   Parallel detection of vertices impacted by edge updates.  
2. **Update Phase**  
   Asynchronously relaxes affected vertices using lock-free operations.

### 🧵 Hybrid Parallelization Stack  
| Layer | Technology |
|-------|------------|
| Partitioning | METIS |
| Inter-node | MPI |
| Intra-node | OpenMP |
| GPU Acceleration | OpenCL |

### 📊 Performance Visualization  
Python scripts benchmark and visualize execution times across various process counts.

---

## 👨‍💻 Team Members

- Muhammad Zohaib Raza  
- Muhammad Awais
- Hamid Ali

---

## 🧩 Project Structure

```
.
├── sssp.cpp, graph.cpp, main.cpp, utils.cpp     # Parallel core logic
├── serial_execution.cpp                         # Serial Dijkstra implementation
├── opencl_utils.cpp, relax_edges.cl             # OpenCL support
├── sample_graph.txt, sample_updates.txt         # Input data
├── plotGraph.py, visualizer.py                  # Python scripts
├── hosts                                        # MPI hostfile
```

---

## 🔧 Compilation & Execution

### 1. 📦 Prerequisites

| Component | Version |
|-----------|---------|
| OS | Ubuntu/Linux or WSL2 |
| C++ Compiler | `g++` (C++17 compatible) |
| MPI | OpenMPI or MPICH |
| OpenMP | Bundled with GCC |
| OpenCL | v2.0+ |
| METIS | v5.1.0+ |
| Python | 3.6+ |

Install Python requirements:
```bash
pip install matplotlib
```

---

### 2. ⚙️ Build Instructions

#### 🖥️ Serial SSSP
```bash
g++ -std=c++11 serial_execution.cpp -o serial_sssp
./serial_sssp sample_graph.txt sample_updates.txt 10000 output_serial.txt
```

#### ⚡ Parallel SSSP
```bash
mpicxx -O3 -march=native -funroll-loops -fopenmp -DCL_TARGET_OPENCL_VERSION=200 \
-o sssp main.cpp graph.cpp utils.cpp sssp.cpp opencl_utils.cpp -I. \
-L/usr/local/lib -lOpenCL -lmetis
```

---

### 3. 🚀 Run Instructions

#### ✅ Parallel Execution
```bash
mpirun --allow-run-as-root --hostfile hosts --bind-to core \
-np 4 ./sssp sample_graph.txt sample_updates.txt 10000 output.txt --openmp --opencl
```

> 🔁 Use `--openmp` and `--opencl` flags as needed.

#### 📊 Benchmark Visualization
```bash
python3 plotGraph.py
# Output: sssp_performance.png
```

#### 📈 Serial vs Parallel Comparison
```bash
python3 visualizer.py
```

---

## 📚 Algorithm Workflow

### 🔁 Two-Phase Update Strategy

1. **Phase 1: Affected Subgraph Identification**  
   - Detects affected vertices from dynamic edge changes.  
   - Handles insertions via tentative relaxations.  
   - Handles deletions by propagating disconnects.

2. **Phase 2: Parallel Update**  
   - Iteratively relaxes affected vertices.  
   - Uses **OpenMP** and **OpenCL** for compute.  
   - **MPI** synchronizes partition boundaries.

---

## 📐 Core Data Structures

- **Adjacency List**: Space-efficient graph representation.  
- **SSSP Tree**: Tracks distances, parents, and update flags.  
- **Dynamic Edge Arrays**: `Ins_k`, `Del_k` for updates.

---

## ⚙️ Performance Highlights

- **Speedup**: Up to **8.5× vs Gunrock** (GPU) and **5× vs Galois** (CPU).  
- **Scalability**: Supports graphs with ~16M vertices, 250M edges.  
- **Load Balancing**: Achieved via **METIS + OpenMP dynamic scheduling**.  
- **Heterogeneous Execution**: Unified CPU-GPU processing pipeline.

---

## 🧪 Sample Input Format

### `sample_graph.txt`
```
# Format: u v w
0 1 5
1 2 3
2 3 1
...
```

### `sample_updates.txt`
```
# Format: type u v w
I 4 5 2    # Insertion
D 1 2 -1   # Deletion
```

---

## 🛠️ Future Improvements

- CUDA support for tighter GPU integration  
- Dynamic load balancing across nodes  
- Fault-tolerant update propagation  

---

## 📜 References

- Khanda et al. (2022). *A Parallel Algorithm Template for Updating SSSP in Large-Scale Dynamic Networks*.