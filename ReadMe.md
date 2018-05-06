# Continuous Preintegration Theory for Graph-based Visual-Inertial Navigation

This package contains the code implementation of the continuous time preintegration (CPI) methods [[1]](http://udel.edu/~ghuang/papers/tr_cpi.pdf).
We provide a complete system for visual-inertial navigation using synthetically generated vision and inertial measurements.
This code was used for the comparision against the current state-of-the-art discrete method in GTSAM by Forster et al. [[2]](http://www.roboticsproceedings.org/rss11/p06.pdf).
All preintegration methods are implemented in the GTSAM optimization framework.
For comparision, the methods are solved side-by-side using the GTSAM batch fix-lag smoother allowing for near constant memory requirements and easy marginalization.

We also include the MATLAB scripts for generating the Monte-Carlo simulation for comparision, and the trajectories collected from GAZEBO for evaluation and comparision.
We use the GAZEBO simulator to get the groundtruth pose and IMU readings from a MAV trajectory, to which we add random noise when generating our Monte-Carlo scenarios (see the cpi_simulation directory for more details).
We provide the scripts used to generate the results in the paper, and a step by step guide on how to generate these results yourself.




## Building GTSAM

Ensure that you have Intel MKL and Intel TBB installed as this will allow for GTSAM multithreaded performance ([link](https://software.intel.com/en-us/articles/installing-intel-free-libs-and-python-apt-repo
)):
```cmd
wget https://apt.repos.intel.com/intel-gpg-keys/GPG-PUB-KEY-INTEL-SW-PRODUCTS-2019.PUB
sudo apt-key add GPG-PUB-KEY-INTEL-SW-PRODUCTS-2019.PUB
sudo sh -c 'echo deb https://apt.repos.intel.com/mkl all main > /etc/apt/sources.list.d/intel-mkl.list'
sudo sh -c 'echo deb https://apt.repos.intel.com/tbb all main > /etc/apt/sources.list.d/intel-tbb.list'
sudo apt-get update
sudo apt-get install intel-mkl-2018.1-038
sudo apt-get install intel-tbb-2018.0-033
```

Make sure you build with the manifold version of the GTSAM preintegration, you will get errors otherwise.
This version is the original Forster et al. [[2]](http://www.roboticsproceedings.org/rss11/p06.pdf) RSS version.
GTSAM defaults to a tangent space preintegration method.
Note that the GTSAM discrete method was tested using the master branch at commit c21186c6212798e665da6b5015296713ddfe8c1d.
```cmd
git clone https://bitbucket.org/gtborg/gtsam/
cd gtsam
mkdir build
cd build
cmake -DGTSAM_TANGENT_PREINTEGRATION=OFF ..
sudo make -j6 install
```



## Using CPI in your own project

Because we use a JPL quaternion navigation state, factors that are not included in this repository will need to be derived into respect of this state.
Take a look at the cpi_compare directory for more detail on key files and their functions.
Here are the recommended steps for using this in your own system:

1. Copy the `/cpi/`,`/gtsam/`, and `/utils/quat_ops.h` folders/file into your own project
2. Include all .cpp files in your CMakelists.txt
3. Add any new factors into this GTSAM folder, ensuring they are functions of the JPLNavState
4. Copy-paste the desired functions from `GraphSolver_IMU.cpp` file depending on which integration you want to use
5. Construct your GTSAM graph as normal (use the `GraphSolver.cpp` as an example)






