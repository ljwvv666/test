# NLP_LOCAL Docker 环境配置说明

- 本项目通过 Docker 进行环境隔离和部署，适用于 Windows 系统。请严格按照以下步骤操作。
- 总共分为**两个阶段**：
  - 第一阶段：在第一台主机上从零配置docker环境
  - 第二阶段：在其他电脑上快速搭建 Docker 环境
---

## 一、主机上从零开始配置 Docker 环境

### 1. 启用 WSL（Windows Subsystem for Linux）

以管理员身份打开 PowerShell，依次执行：

```powershell
Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
netsh winsock reset
```

执行完毕后，**重启电脑**。

### 2. 安装 Docker Desktop

- 访问 [Docker 官网](https://www.docker.com/products/docker-desktop/) 下载并安装 Docker Desktop。
- 安装完成后，启动 Docker Desktop。

### 3. 构建 NLP_LOCAL 项目的 Docker 镜像

进入 config 目录：

```powershell
cd 路径\到\nlp_local\config
docker build -t nlp_local:v1.0 .
```

### 4. 运行容器并挂载本地代码

假设你的代码和依赖文件都在 `D:/pz` 文件夹下：

```powershell
docker run -it --rm -v D:/pz:/app nlp_local:v1.0 python /app/main.py
```

- 注意：代码中涉及的文件路径需改为 `/app/xxx` 形式，例如 `dict_path = "/app/frequency_dictionary_en_82_765.txt"`。

如需进入容器交互式终端：

```powershell
docker run -it --rm -v D:/pz:/app nlp_local:v1.0 /bin/bash
ls /app
exit
```

---

## 二、在其他电脑上快速搭建 Docker 环境

### 1. 重复 WSL 和 Docker Desktop 安装步骤

同上，先启用 WSL 并安装 Docker Desktop。

### 2. 导入镜像（两种方式）
#### 方式一：通过镜像仓库拉取

1. **将镜像推送到远程仓库（如 Docker Hub）**

   在第一台电脑上执行：

   ```sh
   docker tag nlp_local:latest your_dockerhub_username/nlp_local:latest
   docker push your_dockerhub_username/nlp_local:latest
   ```

2. **在其他电脑上拉取镜像并运行**

   ```sh
   docker pull your_dockerhub_username/nlp_local:latest
   docker run -it --rm -v /path/to/nlp_local:/app your_dockerhub_username/nlp_local:latest /bin/bash
   ```
#### 方式二：手动拷贝镜像文件（.tar）
- 将第一台电脑导出的镜像文件（如 `nlp_local-v1.0.tar`）拷贝到目标电脑。

    在 PowerShell 中执行：

    ```powershell
    docker load -i 路径\nlp_local-v1.0.tar
    docker images  # 检查镜像是否加载成功
    ```

### 3. 运行容器

```powershell
docker run -it --rm -v D:/pz:/app nlp_local:v1.0 python /app/main.py
```

或进入容器终端：

```powershell
docker run -it --rm -v D:/pz:/app nlp_local:v1.0 /bin/bash
```

---

## 常见问题

- **挂载目录建议**：请将所有代码和依赖文件（如 main.py、`frequency_dictionary_en_82_765.txt` 等）放在同一文件夹下，挂载到 `/app`。
- **路径问题**：容器内访问路径统一为 `/app/xxx`。
- **依赖安装慢**：已在 Dockerfile 中使用清华源加速。

---

## 参考命令

- 构建镜像：  
  `docker build -t nlp_local:v1.0 .`
- 导出镜像：  
  `docker save -o nlp_local-v1.0.tar nlp_local:v1.0`
- 导入镜像：  
  `docker load -i nlp_local-v1.0.tar`
- 运行主程序：  
  `docker run -it --rm -v D:/pz:/app nlp_local:v1.0 python /app/main.py`
- 进入容器终端：  
  `docker run -it --rm -v D:/pz:/app nlp_local:v1.0 /bin/bash`

---

如有疑问请联系项目维护者。