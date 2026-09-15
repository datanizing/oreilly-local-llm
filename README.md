# Working Locally with Open Weight LLMs

In this course, we will concentrate on *decoder models*, also
called GPT-like models. These models can be used for
completing text, or answering questions (in the style
of ChatGPT). As this use case is much more popular,
we will focus on this.

## Presentation
The [slides](<2026-07-22-Local LLMs.pdf>) contain additional
background and theroretical information.

## Notebooks

You can either try to run the notebooks directly
or try to follow how I run them and use it as a 
documentation (or run it later).

### Transformers

We will start with the `transformers` library as this
is straightforward. You will see later, that this is
not the optimal choice, though.

* [11-qwen3.5-9b.ipynb: Running an small Qwen3.5 model](11-qwen3.5-9b.ipynb)
* [12-gemma-4-12b.ipynb: Running Google's gemma-4-12B](12-gemma-4-12b.ipynb)


### vLLM

A better choice is often to use `vllm` as an execution
engine and put this into a separate process. The access
is then accomplished via the Open AI API.

* [21-vllm-gemma-4-12b.ipynb: gemma-4-12B runs in `vllm`](21-vllm-gemma-4-12b.ipynb)
* [22-vllm-gemma-4-12b-qat-w4a16-ct.ipynb: a quantized version of gemma-4-12B runs in `vllm`](22-vllm-gemma-4-12b-qat-w4a16-ct.ipynb)


### SGlang

`sglang` is another server which also allows running
LLMs in a separate process. You will see that `sglang`
can be quite a bite faster than `vllm`.

* [31-sglang-gemma-4-12b.ipynb: gemma-4-12B runs in `sglang`](31-sglang-gemma-4-12b.ipynb)
* [32-sglang-gemma-4-12B-it-qat-w4a16-ct.ipynb: quantization speeds things up in `sglang` much faster](32-sglang-gemma-4-12B-it-qat-w4a16-ct.ipynb)
* [33-sglang-gemma-4-12B-it-qat-w4a16-ct-mtp.ipynb: multi-token prediction adds even more speed](33-sglang-gemma-4-12B-it-qat-w4a16-ct-mtp.ipynb)


### tabbyAPI

`tabbyapi` is yet another execution environment which
offers to run models in the very efficient `exl3`
quantization. Take a look at the speeds and the
efficient memory usage!

* [41-tabbyapi-gemma-4-12B-it-exl3.ipynb: gemma-4-12B in exl3 4-bit quantization](41-tabbyapi-gemma-4-12B-it-exl3.ipynb)
* [42-qwen3.8-27B-exl3.ipynb: a much larger qwen3.8-27B in exl3 4-bit quantization](42-qwen3.8-27B-exl3.ipynb)


### llama.cpp

`llama.cpp` was originally conceived as a CPU-only
enviroment for LLMs. However, it has gained very broad
hardware support and is the *speed king* for single
users.

* [51-llama.cpp-gemma-4-12B-it-q4.ipynb: gemma-4-12b in 4-bit quantization with MTP](51-llama.cpp-gemma-4-12B-it-q4.ipynb)
* [52-llama.cpp-qwen3.8-27B-q4.ipynb: same for qwen3.8-27B](52-llama.cpp-qwen3.8-27B-q4.ipynb)
* [53-llama.cpp-qwen3.6-35B-A3B-q4.ipynb: even more speed with MoE qwen3.6-35B-A3B](53-llama.cpp-qwen3.6-35B-A3B-q4.ipynb)


## Running LLMs on the CPU

For this, a good starting point is [LM Studio](https://lmstudio.ai/) which is available for all major platforms. However, it is not Open Source Software.

An alternative (but also not completely open) is [ollama](https://ollama.com/).

If you want to run free software, [llama.cpp](https://github.com/ggml-org/llama.cpp) is recommended. It is a very active project with multiple releases per day and supports many current models. `llama.cpp` can also run as server and is compatible to the OpenAI API.

### Creating your own GGUFs

The easiest way is to download GGUFs, which are widely available on [Hugging Face](https://huggingface.co).

If a GGUF is not available, you can also [create it there](https://huggingface.co/spaces/ggml-org/gguf-my-repo).

Using `llama.cpp`, you can convert Hugging Face repositories (download first via `huggingface-cli download --local-dir Qwen3.5-9B Qwen/Qwen3.5-9B`) to GGUF:

```bash
python convert_hf_to_gguf.py --outfile Qwen3.5-9B.gguf Qwen3.5-9B
```

Afterwards, you can quantize them:

```bash
$ build/bin/llama-quantize Qwen3.5-9B.gguf Qwen3.5-9B-Q4_K_M.gguf q4_k_m
```

Finally, run a `llama-server` to access the frontend:

```bash
$ build/bin/llama-server -m Qwen3.5-9B-Q4_K_M.gguf --port 8080
```

## Trying models without local hardware

On [Hugging Face](https://huggingface.co), some models
are available for inference.

You can also try [OpenRouter](https://openrouter.ai/models),
some of the models are free, for some you have to pay (very
little). Many new models are available there.


