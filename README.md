# oaillama3

Simple setup to self-host full quality LLaMA3-70B model at 4.65 bpw quantization with an OpenAI API, on 2x3090/4090 GPUs.

To clarify, it is fairly easy to get these models to run.. for a while.  Some additional tweaks are needed to avoid the inference engine running out of memory and dying.  vLLM keeps crashing with AutoAWQ quantized versions for example.  The exllamav2 configuration shared here works around the issue.

Using my TextWorld reasoning benchmark, it scores a 4.12/5, which is similar to GPT-4 and better than GPT-3.5 and all other models.  The quality should improve as better quantizations are released and the context length is improved.

## Setup

Install conda from https://docs.conda.io/projects/miniconda/en/latest/

Then you can literally copy/paste this into a terminal to provision a server:

```bash
# Install git large file support
sudo apt install git git-lfs
git lfs install

git clone https://github.com/theroyallab/tabbyAPI.git
cd tabbyAPI

# Config file to make exllamav2 work
rm -f config.yml
wget https://raw.githubusercontent.com/catid/oaillama3/main/config.yml

cd models

# For two 4090 GPUs (my setup):
git clone https://huggingface.co/LoneStriker/Meta-Llama-3-70B-Instruct-4.65bpw-h6-exl2

cd ..

# Install Python dependencies
conda create -n oai python=3.10 -y && conda activate oai

./start.sh
```

It prints the API key to the console and is also available via `api_tokens.yml`.  This replaces your normal OpenAI API key.  To reference the server specify API host = `http://gpu5.lan:5000`, replacing `gpu5.lan` with the name of your Linux server.

To use your server from the older OpenAI Python completions API:

```
openai.api_base = "http://devnuc.lan:5000/v1"
openai.api_key = api_key_from_api_tokens_yml
```

If you're editing someone else's code search for "openai.api_key" and if you find it then use the above method.  This is the most common way to adapt a script.

To use your server from the newer OpenAI Python API:

```python
# OpenAI
from openai import OpenAI
client = OpenAI(api_key=api_key_from_api_tokens_yml, base_url="http://gpu5.lan:5000/v1")
```

again replacing the server name and API key with the one from your server.

If you have multiple GPU servers you can put a HAProxy in front of the cluster if the API keys are the same: Just copy one `api_tokens.yml` to all the other machines, and restart all the `start.sh` scripts.  To set up HAProxy just ask your Mixtral model for instructions. :)


## HAProxy tuning

Adding maxconn helped spread the load better: `sudo vi /etc/haproxy/haproxy.cfg`

```
frontend http_front
        bind *:5000
        default_backend http_back

backend http_back
        balance roundrobin
        server gpu1 gpu1.lan:5000 check maxconn 6
        server gpu2 gpu2.lan:5000 check maxconn 6
        server gpu3 gpu3.lan:5000 check maxconn 6
        server gpu4 gpu4.lan:5000 check maxconn 6
        server gpu5 gpu5.lan:5000 check maxconn 6
        server gpu6 gpu6.lan:5000 check maxconn 6
```
