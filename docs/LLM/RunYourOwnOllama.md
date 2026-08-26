# How to Run LLM Models via Ollama at SLAC AF

[Ollama](https://ollama.com/) makes it easy to run LLM models by yourself,
locally, in a client/server mode. The server (where the LLM models are actually
run) can be at a remote location. Downloading models can be done with a simple
command. The following instructions assume that the Ollama client (or your
python codes) will be run on your desktop, while resource demanding models will
be run at the SLAC AF.

## Getting Start with Ollama on a Mac Desktop

1. Use `brew` to install Ollama.
    - If installed via the Ollama.app from Ollama web site, you may want disable
      the `Ollama Application` from **Open at Login** in **System Setting ->
      General -> Login Items and Extensions**.
2. Start the Ollama server:
    - If you want to run LLM models via Ollama on your desktop, type
      `ollama serve`.
    - If your plan is to run the LLM models at SLAC AF, see instruction below.
3. Start the Ollama client and run a model: (assume the server is running), type
   `ollama run llama3.2`. Now you can start typing your questions.
    - This command actually tell the server to pull a model named `llama3.2`
      from Ollama model library, and run it at the server side.

## Interact with Ollama

By default, the Ollma server listens to port 11434 for incoming request. You can
interact with Ollama using `curl` or `python`, in addition the the above
`ollama run ...` command.

- Using `curl` (This is only good to demonstrate how things work under the hook)

```sh
curl --silent http://localhost:11434/api/chat -d '{
  "model": "llama3.2",
  "stream": false,
  "messages": [
    {"role": "system",
     "content": "you are good assistant with knowldege on Chemistry."},
    {"role": "user",
     "content": "Balance this chemical equation: ch4 + o2 -> co2 + h2o"}
  ]
}' | jq
```

- Use in Python

```python
from ollama import Client

msg_sys = {'role': 'system',
           'content': 'You are a good assistant with knowledge on Chemistry'}
msg_usr = {'role': 'user',
           'content': 'Please balance chemical equation c2h6 + o2 -> co2 + h2o'}

client = Client()
stream = client.chat(model='llama3.2',
                     messages=[msg_sys, msg_usr],
                     options={'temperature': 0.3,
                              'num_predict': -1},
                     stream=True)

for chunk in stream:
    print(chunk['message']['content'], end='', flush=True)
```

## Running LLM Models via Ollama at SLAC AF

First, complete the ssh key login setup so that you can use
[ssh in between SLAC nodes](../slac/accessing.md) without using a password. Then
verify that you can submit Slurm batch jobs at SLAC. You can use the following
command to verify that:

```sh
ssh -J s3dflogin.slac.stanford.edu iana srun -A atlas -p ampere hostname
```

(**`iana`** is a cluster of interactive nodes, where Slurm jobs are submitted).

You can use the following two commands to run Ollama at SLAC, using only CPUs or
requesting GPUs.

- CPU only: This is intended for mananing models, such as pulling a new model

```sh
ssh -t -L 11434:localhost:11435 -J s3dflogin iana CALLBACKPORT=11435 srun -A atlas -p milano --pty --nodes 1 --cpus-per-task 1 --time=30:00               sh /sdf/group/atlas/op/ollama/ollama.sh serve
```

- with GPU: This is good for running a model

```sh
ssh -t -L 11434:localhost:11435 -J s3dflogin iana CALLBACKPORT=11435 srun -A atlas -p ampere --pty --nodes 1 --cpus-per-task 4 --time=30:00 --gpus a100:1 sh /sdf/group/atlas/op/ollama/ollama.sh serve
```

These two commands above listen to the default Ollama port (11434) on your
desktop, and forward to the Ollama server running at the SLAC AF's batch node.
The number `11435` is a randomly chosen port. If someone else is already
listening to this port on the **`iana`** node, then you will need to choose a
different port

### Where does Ollama Pickup Model Files

When `/sdf/group/atlas/op/ollama/ollama.sh` starts, it will check the following
places for model files:

- Environment variable `$OLLAMA_MODELS` (You can set this environment variable
  in your `.bashrc` file)
- Directory `$HOME/.ollama/models`
- Default ATLAS wide location at `/sdf/group/atlas/op/ollama/models`.

Only one of the above directories will be used by the script to look for model
files.

### (Optional) How does This Port Forwarding Work.

There are two forwarding going on

- `ssh -t -L 11434:localhost:11435 -J s3dflogin iana` forwards the local port to
  `iana:11435`
- Batch node that runs `/sdf/group/atlas/op/ollama/ollama.sh serve` will run
  another ssh back to the `iana` node, and listen to port 11435 and forward
  traffic to the Ollama server.

## Reference Links

- [Ollama API doc for running and managing LLMs](https://github.com/ollama/ollama/blob/main/docs/api.md)
- [OpenAI compatibility with Ollama](https://ollama.com/blog/openai-compatibility)
