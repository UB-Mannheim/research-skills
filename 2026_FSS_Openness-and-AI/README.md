# Research Skills FSS 2026: *Openness in and with AI*

This README provides installation instructions for running the jupyter notebook `openness-and-ai.ipynb`. The notebook runs local LLM experiments with [Ollama](https://ollama.com/) via the python package [litellm](https://github.com/BerriAI/litellm).

## Prerequisites:

- Python>=3.11
- Python packages: `litellm`, `json_repair`
- A running Ollama server on `http://localhost:11434`
- Ollama models:
  - `smollm2:1.7b`
  - `olmo-3:7b-instruct`
  - `yulan-mini-instruct-v1:latest` (custom local model)

---

## 1) Ollama Setup

[Ollama](https://ollama.com/) lets you run open-weight LLMs locally on your machine – no API key, no cloud dependency, full control over your data.

### Installation

| Platform | Command / Instruction |
|---|---|
| **macOS** | `brew install ollama` or download the app from [ollama.com/download](https://ollama.com/download) |
| **Linux** | `curl -fsSL https://ollama.com/install.sh \| sh` |
| **Windows** | Download the installer from [ollama.com/download](https://ollama.com/download) |

After installation, start the Ollama server:

```bash
ollama serve  # starts the local API on http://localhost:11434
```

#### Download & run a model

```bash
ollama pull smollm2:1.7b  # download the model (~1 GB)
ollama run  smollm2:1.7b  # interactive chat (optional, just to test)
```

Browse the full model library at [ollama.com/library](https://ollama.com/library).

#### LiteLLM on top of Ollama

LLM providers (OpenAI, Anthropic, Hugging Face, Ollama, …) each expose their own SDK and API format. [LiteLLM](https://docs.litellm.ai/) acts as a unified abstraction layer that translates a single `completion()` call into the provider-specific format under the hood.

## Models used in this noteboook

We work with 3 SLMs/LLMs in this notebook. Running the code expects that your Ollama instance is running (`ollama serve`) and that you have installed these models:

- `smollm2:1.7b` → `ollama pull smollm2:1.7b`
- `olmo-3:7b-instruct` → `ollama pull olmo-3:7b-instruct`
- `yulan-mini-instruct-v1:latest` → `yulan-mini-instruct-v1:latest`

### Installation instructions for `yulan-mini-instruct-v1:latest`

`yulan-mini-instruct-v1:latest` is not available in the Ollama model library but you can download it from HuggingFace. Follow these instructions to convert the HuggingFace model so that Ollama can run it:

1. Download the model to a folder with your CLI

```bash
curl -L -o yulan-mini-instruct-v1.gguf "https://huggingface.co/mradermacher/YuLan-Mini-Instruct-V1-GGUF/resolve/main/YuLan-Mini-Instruct-V1.IQ4_XS.gguf"
```

2. Run the following command in the same folder:

```bash
echo "FROM ./yulan-mini-instruct-v1.gguf" > Modelfile
```

3. Convert the model

```bash
ollama create yulan-mini-instruct-v1 -f Modelfile
```

4. The model should now appear when running `ollama list` in your CLI:

```bash
smollm2:1.7b                     cef4a1e09247    1.8 GB    3 hours ago
yulan-mini-instruct-v1:latest    31f77b7419bc    1.5 GB    3 hours ago
olmo-3:7b-instruct               ea72df8c85d7    4.5 GB    5 hours ago
```

---

## 2) JupyterLab users

If you already have a working JupyterLab setup, install the missing dependencies in your active environment:

Start Ollama and prepare models:

```bash
# Start local Ollama API (leave running)
ollama serve
```

Pull the models:

```bash
ollama pull smollm2:1.7b
ollama pull olmo-3:7b-instruct
```

For `yulan-mini-instruct-v1:latest`:

```bash
# from the project folder
curl -L -o yulan-mini-instruct-v1.gguf "https://huggingface.co/mradermacher/YuLan-Mini-Instruct-V1-GGUF/resolve/main/YuLan-Mini-Instruct-V1.IQ4_XS.gguf"
echo "FROM ./yulan-mini-instruct-v1.gguf" > Modelfile
ollama create yulan-mini-instruct-v1 -f Modelfile
```

Open `openness-and-ai.ipynb` in JupyterLab and run the cells.

---

## 3) IDE users

This notebook can run in IDE notebook support (for example VS Code or PyCharm) without launching JupyterLab UI.

### Step A: Create and activate a virtual environment

```bash
python3 -m venv .venv
source .venv/bin/activate
```

On Windows PowerShell:

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
```

### Step B: Install Python dependencies and kernel support

```bash
pip install --upgrade pip
python -m ipykernel install --user --name openness-ai --display-name "Python (openness-ai)"
```

### Step C: Install and start Ollama

Install Ollama for your OS: [https://ollama.com/download](https://ollama.com/download)

Then:

```bash
ollama serve
```

In a second terminal:

```bash
ollama pull smollm2:1.7b
ollama pull olmo-3:7b-instruct
```

Create `yulan-mini-instruct-v1:latest` as described above.

### Step D: Run from your IDE

1. Open `openness-and-ai.ipynb` in your IDE.
2. Select the `Python (openness-ai)` kernel (or your active `.venv` interpreter).
3. Run the cells.

---

## Ollama model verification

Before running the notebook, check:

```bash
ollama list
```

You should see somethin like this:
```bash
smollm2:1.7b                     cef4a1e09247    1.8 GB    3 hours ago
yulan-mini-instruct-v1:latest    31f77b7419bc    1.5 GB    3 hours ago
olmo-3:7b-instruct               ea72df8c85d7    4.5 GB    5 hours ago
```

---

## Notes

- `ollama serve` must be running while executing the notebook, or calls to `completion(..., api_base="http://localhost:11434")` will fail.
