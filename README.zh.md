# MNIST 深度学习练习
（English version: see [README.md](README.md)）

本文档介绍如何在本项目中启动 Jupyter 网页，并一步一步运行文件夹 `1` 里的 `01_mnist.ipynb`。

## 关于 MNIST 数据集
- MNIST 是一个经典的手写体数字数据集（0–9），常被用作深度学习的“Hello World”练习。
- 每张图像为 28×28 的灰度图；数据集包含 60,000 张训练图与 10,000 张测试图。
- 常用于训练与评估将图像映射到数字标签的分类模型。
- 官方页面与资料：
  - MNIST 原始页面（Yann LeCun）：http://yann.lecun.com/exdb/mnist/
  - Torchvision MNIST 文档：https://pytorch.org/vision/stable/generated/torchvision.datasets.MNIST.html

## 前置条件
- 已安装 Python（建议 3.12）
- 已安装 JupyterLab：
  
```bash
python -m pip install --upgrade jupyter
```

## 启动方式
- 方式一：自动打开浏览器
```bash
cd C:\Users\newbd\projects\dev\deeplearning-study
python -m jupyter lab
```

- 方式二：不自动打开，固定端口（示例 8889）
```bash
cd C:\Users\newbd\projects\dev\deeplearning-study
python -m jupyter lab --no-browser --port 8889
```
- 打开终端提示中的链接（包含 token），如：
  - http://localhost:8889/lab?token=...
  - 若 localhost 不通，改用 http://127.0.0.1:8889/lab?token=...
- 停止服务：在运行的终端按 Ctrl+C（必要时按两次）

## 运行 Notebook
- 在左侧文件树打开 `1/01_mnist.ipynb`
- 顶部 Kernel 选择为 `Python 3`
- 逐单元执行（Shift+Enter）
- 首次运行会自动下载 MNIST 数据集（需联网）

## CUDA 加速（可选）
- 检查 CUDA 是否可用：
```bash
python -c "import torch; print(torch.__version__, 'cuda', torch.version.cuda); print('is_available', __import__('torch').cuda.is_available())"
```
- 若要使用 GPU，请安装 CUDA 版 PyTorch/torchvision/torchaudio（以 CUDA 12.4 为例）：
```bash
python -m pip install --upgrade --force-reinstall torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu124
```
- 说明：
  - Windows 环境下 `torch.compile` 的 Inductor/Triton 后端不受支持，可能导致编译出错
  - 如需保留“编译”语义，可改为 `backend="eager"`，或在支持 Triton 的 Linux 环境中使用真正的编译优化

## 常见问题
- Service unavailable / 无法打开页面：
  - 关闭代理或 VPN，确保本地地址不被拦截
  - 端口被占用时换端口：`--port 8890`
  - 使用终端提示的带 token 链接；或打开提示中的本地文件链接以唤起浏览器会话：
    - `file:///C:/Users/newbd/AppData/Roaming/jupyter/runtime/jpserver-XXXX-open.html`
- CUDA 报错（如 `.cuda()` 失败）：
  - 说明当前未启用或未正确安装 CUDA，请按上文方式安装并验证
- 重新执行或内核重启：
  - 用 Kernel 菜单选择 Restart Kernel，然后从上到下重新运行
- JupyterLab workspace 保存报错（net::ERR_ABORTED）：
  - 多为页面初次加载或导航触发的前端请求中断，通常不影响使用；可按 Ctrl+Shift+R 强制刷新
  - 如需重置：Settings → Advanced Settings → Workspaces → Reset，或在 WSL 内删除默认 workspace JSON

## 在 WSL 中运行（可选）
- 前置：
  - Windows 开启 WSL2：`wsl --install`
  - 保持 NVIDIA 显卡驱动为较新版本（支持 WSL CUDA）
- 在 WSL 内安装与运行（含编译加速）：
```bash
# 进入发行版（例如 Ubuntu）
wsl -d Ubuntu

# 更新系统并安装 Python
sudo apt update -y && sudo apt install -y python3.12 python3.12-venv python3-pip

# 创建虚拟环境并进入项目目录（Windows 盘在 /mnt 下）
python3.12 -m venv /mnt/c/Users/newbd/projects/dev/deeplearning-study/.venv-wsl
source /mnt/c/Users/newbd/projects/dev/deeplearning-study/.venv-wsl/bin/activate
cd /mnt/c/Users/newbd/projects/dev/deeplearning-study

# 安装依赖（CUDA 12.4 示例）
pip install -U jupyterlab
pip install -U torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu124
pip install -U triton

# 验证 CUDA 与 Triton
python -c "import torch; print(torch.__version__, 'cuda', torch.version.cuda); print('is_available', torch.cuda.is_available())"
python -c "import triton; print('triton ok')"

# 启动 JupyterLab（WSL 内）
jupyter lab --no-browser --port 8891
```
- 在 Windows 浏览器打开输出链接（如 `http://localhost:8891/lab?token=...`），然后按“运行 Notebook”章节执行。
- Notebook 的“编译模型”单元默认使用 `torch.compile`（Inductor）。如遇编译错误，可改为 `backend="eager"` 或直接不编译。

## 编译加速说明
- 在 WSL（Linux）环境，`torch.compile` 默认走 Inductor 后端，需要 Triton；我们已在指南中安装了 Triton。
- Windows 原生环境下 Triton 不易可用，建议使用 WSL 进行编译加速；或改为 `backend="eager"`。
- Notebook 内已显式断言 Triton 存在（在 WSL 运行时可用）；若要在任意环境自动兼容，可改为检测后回退到 `backend="eager"`。

## 附：静态预览
- 已生成未执行的静态 HTML 版本，便于预览文档内容：
  - `1/01_mnist_static.html`
