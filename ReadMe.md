
# Drone-Based 3D Scene Reconstruction

## Authors
1. Mohammed Munawwar
2. Swaraj Mundruppady Rao

This project provides a pipeline for reconstructing 3D scenes using aerial imagery captured by drones. It leverages tools like COLMAP and Open3D to process images and generate 3D models.

## Prerequisites

- **ROS 2**
- **Python 3.8+**
- **COLMAP**: For structure-from-motion and multi-view stereo.
- **Open3D**: For 3D data processing and visualization.
- **cv_bridge**: To convert between ROS image messages and OpenCV images.
- **OpenCV** and **NumPy**

Install dependencies:

```bash
sudo apt update
sudo apt install ros-${ROS_DISTRO}-cv-bridge
pip3 install opencv-python numpy open3d
```

## Installation

1. **Clone the Repository**

   Navigate to your ROS 2 workspace's `src` directory and clone the repository:

   ```bash
   cd ~/ros2_ws/src
   git clone https://github.com/SwarajMundruppadyRao/Drone-Based-3D-Scene-Reconstruction.git
   ```

2. **Build the Package**

   Build the workspace using `colcon`:

   ```bash
   cd ~/ros2_ws
   colcon build --packages-select best_frames
   ```

3. **Source the Workspace**

   Source the setup script to overlay this workspace:

   ```bash
   source install/setup.bash
   ```

   For convenience, you can add this line to your `.bashrc` file.

## Usage

Run the `best_frames` node:

```bash
ros2 run best_frames best_frames
```

This will execute the `best_frames.py` script, which processes the drone-captured images for 3D reconstruction.

# COLMAP Installation Guide (CUDA 12.4 Compatible)

This guide walks you through installing and compiling COLMAP with support for **CUDA 12.4** and **NVIDIA Driver 550+** on Ubuntu.

---

## 1. Clone the COLMAP Repository

Start by cloning the official COLMAP repository:

```bash
git clone https://github.com/colmap/colmap.git
cd colmap
```

## 2. Install Required Dependencies

Make sure the following packages are installed on your system:

```bash
sudo apt update
sudo apt install \
    build-essential cmake ninja-build \
    libboost-all-dev libglew-dev \
    freeglut3-dev libxmu-dev libxi-dev \
    libpng-dev libjpeg-dev libtiff-dev \
    qt5-default libqt5opengl5-dev \
    libsqlite3-dev libgoogle-glog-dev libgflags-dev \
    libatlas-base-dev libsuitesparse-dev \
    libcgal-dev libcgal-qt5-dev
```

## 3. Patch for CUDA 12.4 Compatibility

CUDA 12.x has dropped support for `compute_native`. To avoid build errors, we must patch the CMake helper file.

### Edit `CMakeHelper.cmake`

Open the file:

```bash
nano cmake/CMakeHelper.cmake
```

Inside the macro `COLMAP_ADD_LIBRARY`, add the following **after** the `add_library(...)` line:

```cmake
# Force correct CUDA architecture
if(CUDA_ENABLED)
    set_target_properties(${COLMAP_ADD_LIBRARY_NAME} PROPERTIES
        CUDA_ARCHITECTURES 86)
endif()
```


---

## 4. Configure the Build

Create a `build/` directory and enter it:

```bash
mkdir build
cd build
```

Run CMake with Ninja:

```bash
cmake .. -GNinja -DCMAKE_BUILD_TYPE=Release
```

---

## 5. Compile COLMAP

Use Ninja to compile:

```bash
ninja
```

## 6. (Optional) Make COLMAP Available Globally

You can symlink the executable to use `colmap` from anywhere:

```bash
sudo ln -s ~/colmap/build/src/colmap/exe/colmap /usr/local/bin/colmap
```

## 7. Test Your Installation

Run the following command:

```bash
colmap
```

Expected output:

```
COLMAP 3.12.0.dev0 -- Structure-from-Motion and Multi-View Stereo
(Commit xxxxxxxx with CUDA)
```

Indicates successful build and CUDA acceleration.

## 8. Launch COLMAP GUI

To start the graphical interface:

```bash
colmap gui
```

## References

- [COLMAP GitHub](https://github.com/colmap/colmap)
- [CUDA Toolkit Documentation](https://docs.nvidia.com/cuda/)
