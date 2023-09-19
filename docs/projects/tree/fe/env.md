# 开发环境配置

1. 下载并安装 [Node.js](https://nodejs.org/zh-cn/download) 。

2. 克隆整个[仓库](https://github.com/SMS-COSMO/SMS-Tree)到本地计算机。

    ```bash
    git clone https://github.com/SMS-COSMO/SMS-Tree
    ```

3. 命令行工作目录切到*仓库根文件夹*，使用 `npm` 安装前端仓库的相关依赖：

    ```bash
    npm install
    ```

    > 提示：若安装期间命令行假死，可考虑更换镜像源：
    >
    > ```bash
    > npm config set registry https://registry.npmmirror.com/
    > ```

4. 此仓库包含了项目后端的代码。如需配置项目后端的开发环境，[参见此处](SMS-Tree-BE/README.md)。
