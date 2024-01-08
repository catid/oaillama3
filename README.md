# oaimixtral

Simple setup to self-host Mixtral model with an OpenAI API.  I'd put this in a blog post but I want to attach some files so I guess a github repo makes more sense.

These instructions will work for Ubuntu Linux servers with 1-2 Nvidia GPUs.  Might work for other setups.

Example video of what you can do with this here: [https://x.com/MrCatid/status/1744440953731985433?s=20
](https://x.com/MrCatid/status/1744441977452609857?s=20)

## Setup

Install conda from https://docs.conda.io/projects/miniconda/en/latest/

```bash
# Install git large file support
sudo apt install git git-lfs
git lfs install

git clone https://github.com/theroyallab/tabbyAPI.git
cd tabbyAPI

cd models

# For one 3090/4090 GPU:
#git clone --branch 3.5bpw https://huggingface.co/turboderp/Mixtral-8x7B-instruct-exl2

# For two 4090 GPUs (my setup):
git clone --branch 6.0bpw https://huggingface.co/turboderp/Mixtral-8x7B-instruct-exl2

cd ..

# Install Python dependencies
conda create -n mixtral python=3.10
conda activate mixtral
pip install -r requirements.txt

# Grab some config files from here to save time
cd templates
wget https://raw.githubusercontent.com/catid/oaimixtral/main/mixtral.jinja
cd ..
wget https://raw.githubusercontent.com/catid/oaimixtral/main/config.yml

./start.sh
```

The `mixtral.jinja` file overrides the tokenizer config in the Mixtral model so that it allows system prompts, which are often used with OpenAI API.

It prints the API key to the console and is also available via `api_tokens.yml`.  This replaces your normal OpenAI API key.  To reference the server specify API host = `http://gpu5.lan:5000`, replacing `gpu5.lan` with the name of your Linux server.

To use your server with the OpenAI Python API:

```python
# OpenAI
from openai import OpenAI
client = OpenAI(api_key=api_key_from_api_tokens_yml, base_url="http://gpu5.lan:5000/v1")
```

again replacing the server name and API key with the one from your server.

## References

This uses the mixtral.jinja suggested by TabbyAPI discord: https://github.com/theroyallab/llm-prompt-templates
