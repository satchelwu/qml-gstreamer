# qml-gstreamer

Ubuntu
sudo apt install qt5-default qtdeclarative5-dev qttools5-dev -y
git clone  https://github.com/satchelwu/qml-gstreamer.git
cd qml-gstreamer && mkdir build && cd build && cmake .. -DCMAKE_INSTALL_PREFIX=/usr &&  make -j$(nproc) && sudo make install
