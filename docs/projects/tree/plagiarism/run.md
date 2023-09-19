修改配置见 `Rocket.toml`。

1. 直接下载

    在 release 中，找到 `linux.zip`。解压，在文件夹内：
    ```sh
    ./plagiarism-detector-rust
    ```

2. 手动编译

    **生产：**

    ```sh
    cargo run --release
    ```

    端口: `9999`

    **测试：**

    ```sh
    cargo run
    ```

    端口: `8000`