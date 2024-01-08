# oaimixtral
Simple setup to self-host Mixtral model with an OpenAI API

These instructions will work for Ubuntu Linux servers with 1-2 Nvidia GPUs.  Might work for other setups.

## Setup

```bash
git clone https://github.com/theroyallab/tabbyAPI.git
cd tabbyAPI

git lfs install

# For one 3090/4090 GPU:
#git clone --branch 3.5bpw https://huggingface.co/turboderp/Mixtral-8x7B-instruct-exl2

# For two 4090 GPUs (my setup):
git clone --branch 6.0bpw https://huggingface.co/turboderp/Mixtral-8x7B-instruct-exl2



```

## References

This uses the mixtral.jinja suggested by TabbyAPI discord: https://github.com/theroyallab/llm-prompt-templates
