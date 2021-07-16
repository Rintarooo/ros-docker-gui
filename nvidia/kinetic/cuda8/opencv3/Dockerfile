FROM rintarooo/ros-kinetic:cuda8
# FROM base/ros-kinetic:cuda8
# https://qiita.com/kendemu/items/a805b0b9828b6f6031db

MAINTAINER Rintarooo <310rnomeado@gmail.com>
LABEL Description="ROS-Kinetic-Desktop with CUDA 8 support (Ubuntu 16.04) and OpenCV v3.4.1" Vendor="TurluCode" Version="3.0"
LABEL com.turlucode.ros.opencv="3.4.1"

# Get OpenCV
# for gcc > 6.1 use ENABLE_PRECOMPILED_HEADERS=OFF
# for opencv 3.4.1 use BUILD_EXAMPLES=OFF (https://github.com/opencv/opencv/issues/11117) OR detele driver_api_multi.cpp and driver_api_stereo_multi.cpp
# True? Using WITH_OPENMP=ON has no sense if TBB is used (lower priority).
# RUN git clone https://github.com/opencv/opencv.git && cd opencv && git checkout 3.4.1 && cd .. && \
# git clone https://github.com/opencv/opencv_contrib.git && cd opencv_contrib && git checkout 3.4.1 && cd .. && \

RUN git clone -b 3.1.0 https://github.com/opencv/opencv_contrib opencv_contrib && \
git clone -b 3.1.0-with-cuda8 https://github.com/daveselinger/opencv opencv && \
rm opencv/samples/gpu/driver_api_multi.cpp opencv/samples/gpu/driver_api_stereo_multi.cpp
# echo "ls" && \
# ls && \
# echo "ls ros-kinetic-opencv3-3.3.1" && \
# ls ros-kinetic-opencv3-3.3.1 && \




# cp opencv/modules/cudalegacy/src/graphcuts.cpp  ros-kinetic-opencv3-3.1.0/modules/cudalegacy/src/graphcuts.cpp && \
RUN echo "deb-src http://packages.ros.org/ros/ubuntu xenial main" >> /etc/apt/sources.list.d/ros-latest.list && \
      apt-get update && apt-get source ros-kinetic-opencv3 && \
      echo "**pwd**" && \
      pwd && \
      echo "**ls**" && \
      ls && \
      cp opencv/modules/cudalegacy/src/graphcuts.cpp  ros-kinetic-opencv3-3.3.1/modules/cudalegacy/src/graphcuts.cpp && \
      apt-get install -y devscripts build-essential && \
      echo "**ls**" && \
      ls && \
      # echo "find" && \
      # find / -iname "ros-kinetic-opencv" && \
      # cd ros-kinetic-opencv3-3.1.0 && \
      cd ros-kinetic-opencv3-3.3.1 && \
      echo "**ls**" && \
      ls && \
      # apt-get build-dep ros-kinetic-opencv3-3.1.0 && \
      # apt-get build-dep ros-kinetic-opencv3-3.3.1 && \
      dpkg-source --before-build ros-kinetic-opencv3-3.3.1 && \
      dpkg-buildpackage -b -uc && \
      cd ..  && \
      mkdir /usr/src/deb && \
      cp ros-kinetic-opencv3_3.1.0-16xenial_amd64.deb /usr/src/deb/ && \
      cd /usr/src/deb/ && \
      chmod a+wr /usr/src/deb && \
      apt-ftparchive packages . | gzip -c9 > Packages.gz && \
      apt-ftparchive sources . | gzip -c9 > Sources.gz && \
      chmod a+wr /etc/apt/sources.list.d/ros-latest.list && \
      echo "deb file:/usr/src/deb ./" >> /etc/apt/sources.list.d/ros-latest.list && \
      sed -i -e "1,2s/^/#/g" /etc/apt/sources.list.d/ros-latest.list && \
      apt-get update && \
      apt-get remove ros-kinetic-opencv3 && \
      apt-get install -y ros-kinetic-opencv3 && \
      apt-get install -y ros-kinetic-desktop-full && \
      sed -i -e "s/#//g" /etc/apt/sources.list.d/ros-latest.list


RUN cd opencv && mkdir build && cd build && \
cmake -D CMAKE_BUILD_TYPE=RELEASE \
      -D CMAKE_INSTALL_PREFIX=/usr/local \
      -D INSTALL_C_EXAMPLES=OFF \
      -D INSTALL_PYTHON_EXAMPLES=OFF \
      -D WITH_V4L=ON \
      -D WITH_QT=ON \
      -D WITH_OPENGL=ON \
      -D WITH_CUBLAS=1 \
      -D WITH_TBB=ON \
      -D WITH_OPENMP=ON \
      -D WITH_CUDA=ON \
      -D ENABLE_PRECOMPILED_HEADERS=OFF \
      -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules \
      -D BUILD_EXAMPLES=OFF \
      -D CUDA_CUDA_LIBRARY=/usr/local/cuda-8.0/targets/x86_64-linux/lib/stubs/libcuda.so .. && \
make -j8 && make install && cd ../.. && \
rm -rf /opencv && rm -rf /opencv_contrib

RUN sh -c 'echo "/usr/local/lib" >> /etc/ld.so.conf.d/opencv.conf' && ldconfig

# Launch terminator
CMD ["terminator"]