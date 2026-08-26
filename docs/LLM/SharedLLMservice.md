# Shared LLM Service at SLAC AF

SLAC AF provides a shared Large Language Model (LLM) service that allows the AF
users to access various open source LLMs models. This service is based on
[Ollama](https://ollama.com/), and is currently an experimental service.

## How to Access

The service provides an HTTP API interface, It supports accessing via the Ollama
API, and is also
[compatible the OpenAI API](https://ollama.com/blog/openai-compatibility). The
service runs on host `sdfiana005.sdf.slac.stanford.edu` at port `11434`. If you
are from outside of SLAC, you will need to use the following SSH port forwarding
to access the service.

```sh
ssh -L 11434:sdfiana005:11434 s3dflogin.slac.stanford.edu
```

1. The OpenAI API accessing URL is `http://localhost:11434/v1/` (set
   `OPENAI_API_KEY` to a non-empty string)
2. The Ollama API accessing URL is `http://localhost:11434/`

To check the available models, use the following command:

```sh
curl -s http://localhost:11434/api/tags | jq '.["models"][]["name"]'
```

To check the current running models, use the following command:

```sh
curl -s http://localhost:11434/api/ps | jq '.["models"][]["name"]'
```

(We may require a real API KEY in the future).

## Limitations

The service is experimental and is an on-demand service: The frontend of this
service is a http proxy that will start a Slurm batch job to run LLM models via
Ollama. To work in an environment that has limited GPU resources, the service is
designed to:

- Submit a Slurm batch job to run the LLM model when the first request comes in.
  When the job run time limit has reached, and there are still active users, the
  frontend service will submit another Slurm batch job to continue providing the
  service. There will be a **gap** between the time the job is submitted and the
  time the job actually starts running. Use the following command to check the
  status of the Slurm job:
    ```sh
    curl -s http://localhost:11434/backend/state
    ```
- Allow multiple users to share the same LLM service. The service allows a
  limited concurrency usage. Currently the service uses a single Slurm job with
  one Nvidia A100-SXM4-40GB GPU. This limits how many different LLM models can
  be run at the same time, and how many users can use the service at the same
  time. Your request will be queued if it hits those limits.

## Models and Size

Most of the available models are quantized to 4-bit (`Q4_0` or `Q4_K_M`), which
means the model sizes are relatively small, but still perform well with high
quality (accuracy). In our experience:

- The `gemma3:27b` (`Q4`) model is the best performing model for most of the
  tasks. It uses about 20GB of the GPU memory. This leaves rooms for
  multiple-user activities, and/or running smaller models on the same GPU at the
  same time.
- The `gemma3` models do not natively support OpenAI's three-role (`system`,
  `user`, `assistant`) in the message to the models (Ollama API also used these
  three roles). They only support two roles (`user`, `model`). The `gemma3`
  models from Ollama added a template to seamlessly convert a three-role message
  to a two-role message.
- The `gemma3` models do not support tool usage (AI agent) when using OpenAI API
  or Ollama API. Models like `gemma3-tools` from Ollama support tool usage. They
  are the same models as `gemma3`, but with additional template to support tool
  usage.
- `llama3.3:70b` models are too large to run on a single A100 GPU with 40GB of
  memory. Even a `Q4` model requires slight above 40GB of memory (and therefore
  need two A100-SXM4-40GB GPU). So avoid using the `llama3.3:70b` models.
- `all-minilm` models are embedding models. They are very small and fast. They
  are not for inference.

If for some reason you need a model that is not available in the service, or the
service is too busy, you can start our own Ollama service by following the
instructions in [this](./RunYourOwnOllama.md) document.
