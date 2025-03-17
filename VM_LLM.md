# LLM Serving

VM_LLM.md

Login as pocadmin

Make directory and download scripts

```
$ mkdir git
$ cd git

$ git clone https://github.com/NthLabs/POC2025-llm.git

$ cd POC2025-llm
```

Edit poc-env (Add NGC API Key)

Download and launch each container, one at a time. (approx 15-20 minutes total)

```
$ sh ./llm-llama31-8b-instruct-interactive.sh

$ sh ./llm-mistral-7b-interactive.sh

$ sh ./embed-nv-embedqa-interactive.sh
```

Once individual funtionality is confirmed, run all containers as detached.

It should take about 5 minutes before all models are serving

```
$ sh llm-llama31-8b-instruct-nim.sh
$ sh llm-mistral-7b-nim.sh 
$ sh embed-nv-embedqa-nim.sh
```
