??? bug "若 Ubuntu 版本 >= 22.04"

    由于精灵屏 sdk 仅支持 openssl1.1.1，而自 Ubuntu 22.04，默认使用的 openssl 版本为 openssl3.0  
    故需要手动安装 openssl1.1.1

    **安装方式：**

    ```sh
    mkdir openssl
    cd openssl

    wget http://archive.ubuntu.com/ubuntu/pool/main/o/openssl/libssl1.1_1.1.1f-1ubuntu2.19_amd64.deb
    wget http://archive.ubuntu.com/ubuntu/pool/main/o/openssl/libssl-dev_1.1.1f-1ubuntu2.19_amd64.deb
    wget http://archive.ubuntu.com/ubuntu/pool/main/o/openssl/openssl_1.1.1f-1ubuntu2.19_amd64.deb

    sudo dpkg --install *
    ```