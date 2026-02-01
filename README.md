# Run Jupyter Notebook

Chinese version: see [README.zh.md](README.zh.md)

This guide explains how to launch Jupyter in this project and run `1/01_mnist.ipynb` step by step, including CUDA acceleration and WSL (Linux) setup with compilation.

## Prerequisites
- Python installed (recommended 3.12)
- JupyterLab installed:

```bash
python -m pip install --upgrade jupyter
```

## Launch Options
- Open browser automatically:
```bash
cd C:\Users\newbd\projects\dev\deeplearning-study
python -m jupyter lab
```

- No browser, fixed port (example: 8889)
```bash
cd C:\Users\newbd\projects\dev\deeplearning-study
python -m jupyter lab --no-browser --port 8889
```
- Use the terminal URL with token (e.g.):
  - http://localhost:8889/lab?token=...
  - If localhost is blocked, use http://127.0.0.1:8889/lab?token=...
- Stop the server: press Ctrl+C in the terminal (twice if needed)

## Run the Notebook
- In the file browser, open `1/01_mnist.ipynb`
- Select Kernel: `Python 3`
- Run cells one by one (Shift+Enter)
- First run will download MNIST dataset (requires network)

## CUDA Acceleration (Optional)
- Check CUDA availability:
```bash
python -c "import torch; print(torch.__version__, 'cuda', torch.version.cuda); print('is_available', __import__('torch').cuda.is_available())"
```
- Install CUDA builds of PyTorch/torchvision/torchaudio (CUDA 12.4 example):
```bash
python -m pip install --upgrade --force-reinstall torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu124
```
- Notes:
  - On native Windows, `torch.compile` with Inductor/Triton is not supported and may fail
  - To keep “compile” semantics, use `backend="eager"`, or switch to Linux/WSL where Triton is available

## Run in WSL (Linux)
- Requirements:
  - Enable WSL2: `wsl --install`
  - Use recent NVIDIA Windows driver (supports WSL CUDA)
- Install and run inside WSL (with compilation):
```bash
# Enter distribution (e.g. Ubuntu)
wsl -d Ubuntu

# Update system and install Python
sudo apt update -y && sudo apt install -y python3.12 python3.12-venv python3-pip

# Create venv and enter project directory (Windows drive under /mnt)
python3.12 -m venv /mnt/c/Users/newbd/projects/dev/deeplearning-study/.venv-wsl
source /mnt/c/Users/newbd/projects/dev/deeplearning-study/.venv-wsl/bin/activate
cd /mnt/c/Users/newbd/projects/dev/deeplearning-study

# Install deps (CUDA 12.4 example)
pip install -U jupyterlab
pip install -U torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu124
pip install -U triton

# Verify CUDA and Triton
python -c "import torch; print(torch.__version__, 'cuda', torch.version.cuda); print('is_available', torch.cuda.is_available())"
python -c "import triton; print('triton ok')"

# Launch JupyterLab in WSL
jupyter lab --no-browser --port 8891
```
- Open the printed URL in your Windows browser (e.g. `http://localhost:8891/lab?token=...`) and follow “Run the Notebook”.
- The “compile model” cell uses `torch.compile` (Inductor) by default. If compilation errors occur, change to `backend="eager"` or skip compilation.

## Compilation Notes
- In WSL (Linux), `torch.compile` uses Inductor which requires Triton; this guide installs Triton.
- On native Windows, Triton is hard to use; prefer WSL for compilation or switch to `backend="eager"`.
- The notebook explicitly asserts Triton presence when using compilation; for universal compatibility, you can detect Triton and fallback to `backend="eager"`.

## Troubleshooting
- Service unavailable / cannot open:
  - Disable proxy/VPN; ensure local URLs are not blocked
  - Change port: `--port 8890`
  - Use the token URL from terminal; or open the helper page:
    - `file:///C:/Users/newbd/AppData/Roaming/jupyter/runtime/jpserver-XXXX-open.html`
- CUDA errors (e.g., `.cuda()` fails):
  - CUDA not enabled/installed; follow the steps above to install and verify
- Restart kernel / rerun:
  - Use Kernel → Restart Kernel, then rerun cells top-to-bottom
- JupyterLab workspace save error (net::ERR_ABORTED):
  - Usually harmless on initial load; try hard refresh (Ctrl+Shift+R)
  - Reset via Settings → Advanced Settings → Workspaces → Reset, or remove default workspace JSON in WSL

## Static Preview
- A non-executed static HTML preview is available for quick reading:
  - `1/01_mnist_static.html`
