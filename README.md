# REUDE_KIIF_GPS_Denied_OAK_D_Project

## Overview

This repository provides an end-to-end **GPS-denied drone navigation stack** using an **OAK-D stereo camera**, **Visual-Inertial Odometry (VIO)**, and **MAVLink communication**.
The system is designed for **non-GPS environments**, enabling real-time perception and state estimation for autonomous aerial platforms.

The stack integrates:

* **VINS-Fusion** for visual–inertial odometry
* **DepthAI (OAK-D)** for stereo vision and feature tracking
* **Ceres Solver** for nonlinear optimization
* **MAVLink UDP Proxy** for flight controller communication

This repository is structured as a **meta-repo using Git submodules**, ensuring exact version locking and reproducibility.

---

## Repository Structure

```
nongps_pro/
├── VINS-Fusion/        (submodule)
├── ceres-solver/       (submodule)
├── depthai-core/       (submodule)
├── oak_d_vins_cpp/     (submodule)
├── mavlink-udp-proxy/  (submodule)
└── README.md
```

⚠️ **Important**
Do NOT manually clone any of the above folders.
They are managed exclusively via **git submodules**.

---

## Clone Instructions (MANDATORY)

This repository **uses submodules**.
You **must** clone it as follows:

```bash
git clone --recurse-submodules https://github.com/REUDE-Technologies/REUDE_KIIF_GPS_Denied_OAK_D_Project.git nongps_pro
cd nongps_pro
```

If the repository was already cloned without submodules:

```bash
git submodule update --init --recursive
```

---

## System Requirements

* Ubuntu 22.04 (tested)
* CMake ≥ 3.16
* GCC ≥ 9
* OAK-D camera
* Internet connection (for first-time dependency installation)

---

## System Preparation

### Update & Upgrade

```bash
sudo apt update
sudo apt upgrade -y
```

### Install Essential Build Tools

```bash
sudo apt install -y build-essential cmake git wget curl
```

---

## Install & Build Dependencies

### 1. Ceres Solver (v2.1.0)

```bash
sudo apt install -y \
  libeigen3-dev \
  libgoogle-glog-dev \
  libgflags-dev \
  libsuitesparse-dev \
  libopencv-dev

cd ceres-solver
mkdir build && cd build

cmake .. \
  -DCMAKE_BUILD_TYPE=Release \
  -DCMAKE_CXX_STANDARD=17 \
  -DBUILD_TESTING=OFF \
  -DBUILD_EXAMPLES=OFF

make -j$(nproc)
sudo make install
sudo ldconfig
```

---

### 2. VINS-Fusion (apm_wiki branch)

```bash
cd ~/nongps_pro/VINS-Fusion/vins_estimator
cmake .
make -j$(nproc)
```

---

### 3. DepthAI Core (v2.25.0)

```bash
sudo apt install -y \
  libusb-1.0-0-dev \
  libopencv-dev \
  libspdlog-dev \
  libglfw3-dev

cd ~/nongps_pro/depthai-core
git submodule update --init --recursive

cmake -S . -B build
cmake --build build
sudo cmake --install build
sudo ldconfig
```

---

### 4. OAK-D VINS Interface

```bash
cd ~/nongps_pro/oak_d_vins_cpp

cmake -Ddepthai_DIR=../depthai-core/build/install/lib/cmake/depthai .
make -j$(nproc)
```

---

### 5. MAVLink UDP Proxy

```bash
cd ~/nongps_pro/mavlink-udp-proxy
git submodule update --init --recursive
./build_it
```

---

## Running the System

Open **three terminals**.

### Terminal 1 – OAK-D Feature Tracker

```bash
cd ~/nongps_pro/oak_d_vins_cpp
./feature_tracker
```

### Terminal 2 – VINS-Fusion

```bash
cd ~/nongps_pro/VINS-Fusion/vins_estimator
./vins_fusion oak_d.yaml
```

### Terminal 3 – MAVLink UDP Proxy

```bash
cd ~/nongps_pro/mavlink-udp-proxy
./mavlink_udp
```

---

## Notes & Warnings

* ❌ Do **NOT** run `git clone` inside this repository
* ❌ Do **NOT** commit inside submodules unless you own them
* ✅ All dependency versions are **pinned via submodules**
* ✅ Always clone using `--recurse-submodules`

## Intended Audience

* Robotics researchers
* Drone engineers
* Students working on GPS-denied navigation
* Developers experimenting with OAK-D–based VIO pipelines

## License & Attribution

This project integrates multiple open-source projects.
Refer to individual submodule repositories for their respective licenses.
