# Research Skills FSS 2026: *Openness in and with AI*

This README provides installation instructions for running the jupyter notebook `openness-and-ai.ipynb`. The notebook runs local LLM experiments with [Ollama](https://ollama.com/) via the python package [litellm](https://github.com/BerriAI/litellm).

## Prerequisites:

- Python==3.13
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


| Platform    | Command / Instruction                                                                             |
| ----------- | ------------------------------------------------------------------------------------------------- |
| **macOS**   | `brew install ollama` or download the app from [ollama.com/download](https://ollama.com/download) |
| **Linux**   | `curl -fsSL https://ollama.com/install.sh | sh`                                                   |
| **Windows** | Download the installer from [ollama.com/download](https://ollama.com/download)                    |


After installation, start the Ollama server from the CLI:

```bash
ollama serve  # starts the local API on http://localhost:11434
```

### Pulling the models

We work with 3 open LLMs in the notebook:

| Model                  | OSAI Model Page                                                            | HuggingFace                                                                                                    | Ollama Model Library                                                           |
| ---------------------- | -------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------ |
| smollm2:1.7b           | [https://osai-index.eu/model/smollm/](https://osai-index.eu/model/smollm/) | [https://huggingface.co/HuggingFaceTB/SmolLM2-1.7B](https://huggingface.co/HuggingFaceTB/SmolLM2-1.7B)         | [https://ollama.com/library/smollm2](https://ollama.com/library/smollm2) |
| olmo-3:7b-instruct     | [https://osai-index.eu/model/OLMo](https://osai-index.eu/model/OLMo)       | [https://huggingface.co/allenai/Olmo-3-7B-Instruct](https://huggingface.co/allenai/Olmo-3-7B-Instruct)         | [https://ollama.com/library/olmo-3](https://ollama.com/library/olmo-3)   |
| yulan-mini-instruct-v1 | [https://osai-index.eu/model/YuLan](https://osai-index.eu/model/YuLan)     | [https://huggingface.co/yulan-team/YuLan-Mini-Instruct](https://huggingface.co/yulan-team/YuLan-Mini-Instruct) | -                                                                        |

Running the notebook expects that your Ollama instance is active (`ollama serve`) and that you have installed the models with `ollama pull model-name`:

```bash
ollama pull smollm2:1.7b
ollama pull olmo-3:7b-instruct
```

(→ Browse the full model library at [ollama.com/library](https://ollama.com/library).)

#### Installation instructions for `yulan-mini-instruct-v1:latest`

`yulan-mini-instruct-v1:latest` is not available in the Ollama model library but you can download it from HuggingFace. Follow these instructions to convert the HuggingFace model so that Ollama can run it:

1. Download the model to a folder with your CLI

```bash
curl -L -o yulan-mini-instruct-v1.gguf "https://huggingface.co/mradermacher/YuLan-Mini-Instruct-V1-GGUF/resolve/main/YuLan-Mini-Instruct-V1.IQ4_XS.gguf"
```

1. Run the following command in the same folder:

```bash
echo "FROM ./yulan-mini-instruct-v1.gguf" > Modelfile
```

1. Convert the model

```bash
ollama create yulan-mini-instruct-v1 -f Modelfile
```

1. All 3 models should now appear when running `ollama list` in your CLI:

```bash
smollm2:1.7b                     cef4a1e09247    1.8 GB    3 hours ago
yulan-mini-instruct-v1:latest    31f77b7419bc    1.5 GB    3 hours ago
olmo-3:7b-instruct               ea72df8c85d7    4.5 GB    5 hours ago
```

#### LiteLLM on top of Ollama

LLM providers (OpenAI, Anthropic, Hugging Face, Ollama, …) each expose their own SDK and API format. [LiteLLM](https://docs.litellm.ai/) acts as a unified abstraction layer that translates a single `completion()` call into the provider-specific format under the hood.

---

## 2) Installation for JupyterLab users

If you already have a working JupyterLab setup:

Start Ollama:

```bash
# Start local Ollama API (leave running)
ollama serve
```

Open `openness-and-ai.ipynb` in JupyterLab and run the cells to install additional dependencies (`litellm`, `json_repair`).

---

## 3) Installation for IDE users

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

### Step C: Run from your IDE

1. Open `openness-and-ai.ipynb` in your IDE.
2. Select the `Python (openness-ai)` kernel (or your active `.venv` interpreter).
3. Run the cells to install additional dependencies (`litellm`, `json_repair`).

---

## Ollama model verification

Before running the notebook, check:

```bash
ollama list
```

You should see something like this:

```bash
smollm2:1.7b                     cef4a1e09247    1.8 GB    3 hours ago
yulan-mini-instruct-v1:latest    31f77b7419bc    1.5 GB    3 hours ago
olmo-3:7b-instruct               ea72df8c85d7    4.5 GB    5 hours ago
```

---

## Notes

- `ollama serve` must be running while executing the notebook, or calls to `completion(..., api_base="http://localhost:11434")` will fail.

