# qml-gstreamer

Ubuntu</br>
sudo apt install qt5-default qtdeclarative5-dev qttools5-dev -y</br>
git clone  https://github.com/satchelwu/qml-gstreamer.git</br>
cd qml-gstreamer && mkdir build && cd build && cmake .. -DCMAKE_INSTALL_PREFIX=/usr &&  make -j$(nproc) && sudo make install
