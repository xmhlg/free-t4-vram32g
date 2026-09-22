Kaggle Ollama → Cloudflare Tunnel → VS Code AI Agent

VS Code AI Extension / External Agent
                |
                | OpenAI-compatible HTTPS API
                v
        Cloudflare Quick Tunnel
                |
                v
          Kaggle Notebook
                |
                v
             Ollama
                |
                v
           qwen3:8b ( any model that you prefer)
                |
                v
           Kaggle GPU
1. 打开 Kaggle GPU
In your Kaggle Notebook:

Settings → Accelerator → GPU → T4*2

Then verify the GPU:

!nvidia-smi

2. 安装 Ollama

Run:
!sudo apt-get install zstd

!curl -fsSL https://ollama.com/install.sh | sh

3. 运行 Ollama
Kaggle/Jupyter does not reliably support shell background processes using &, so start Ollama with Python:

import subprocess
import time

subprocess.Popen(
    ["ollama", "serve"],
    stdout=subprocess.DEVNULL,
    stderr=subprocess.DEVNULL
)

time.sleep(5)

print("Ollama started")

4. 下载  Ollama 模型


!ollama pull qwen3.8:27b


7. 安装 cloudflared


!wget -q https://github.com/cloudflare/cloudflared/releases/latest/download/cloudflared-linux-amd64.deb
!dpkg -i cloudflared-linux-amd64.deb

8. 创建 Cloudflare 频道


import subprocess
import time

cloudflared = subprocess.Popen(
    [
        "cloudflared",
        "tunnel",
        "--url", "http://127.0.0.1:11434",
        "--http-host-header", "localhost:11434"
    ],
    stdout=subprocess.PIPE,
    stderr=subprocess.STDOUT,
    text=True
)

time.sleep(8)

for _ in range(30):
    line = cloudflared.stdout.readline()
    if line:
        print(line, end="")


9. 得到模型地址

https://example-name.trycloudflare.com
your OpenAI-compatible base URL is:

https://example-name.trycloudflare.com/v1



12. 模型地址参数.

If the extension supports an OpenAI-compatible provider/custom OpenAI endpoint, use:

Provider:
OpenAI Compatible

Base URL:
https://YOUR-TUNNEL.trycloudflare.com/v1

API Key:
ollama

Model:
qwen3.8:27b
