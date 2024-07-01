# Transformer inference arithmetic

### Example of GPU specs

| FP32                 | 31.2 TF                 |
| -------------------- | ----------------------- |
| TF32 Tensor Core     | 62.5 TF \| 125 TF\*     |
| BFLOAT16 Tensor Core | 125 TF \| 250 TF\*      |
| FP16 Tensor Core     | 125 TF \| 250 TF\*      |
| INT8 Tensor Core     | 250 TOPS \| 500 TOPS\*  |
| INT4 Tensor Core     | 500 TOPS \| 1000 TOPS\* |
| GPU Memory           | 24 GB GDDR6             |
| GPU Memory Bandwidth | 600 GB/s                |
| Max TDP Power        | 150W                    |

* Compute Bandwidth (TFLOPS): 125 TFLOPS
* Memory Bandwidth (GB/s): 600 GB/s

### Arthmetic intensity: operations per byte ratio&#x20;

&#x20;compute\_bw / memory\_bw (ops:byte)

> we have to complete 208.3 floating point operations for every byte of memory access.
>
> * If we find ourselves only able to complete fewer than 208.3 operations per byte, our system performance is _**memory bound**_. This essentially means that the speed and efficiency of our system are constrained by the rate at which we can transfer data or the input-output operations that it can handle.
> * If we want to do more than 208.3 floating point operations per byte, our system is instead _**compute bound**_. In this state, our effectiveness and performance are restrained not by the memory, but rather the number of compute units that our chip possesses.

### Batching

Given A10 24GB SRAM, Llama 7B model, then

```
24 GB - (2 * 7GB) = 10GB

kv_cache_size
    = (2 * 2 * n_layers * d_model) bytes/token
    = (4 * 32 * 4096) bytes/token
    = 524288 bytes/token
    ~ 0.00052 GB/token
    
kv_cache_tokens
    = 10 GB / 0.00052 GB/token
    = 19,230 tokens
    
batches = kv_cache_tokens / context size
        = 19230 tokens / 4096
        = 4 batches
```

### Time per token

{% code overflow="wrap" %}
```
time/token = 
        total number of bytes moved (the model weights) / accelerator memory bandwidth
        
Prefill time = number of tokens * ( number of parameters / accelerator compute bandwidth)

Total generation time = prefill time + number of tokens * time/token
```
{% endcode %}

### Other metrics

```python
def print_details_info(model_config, args, whole_end2end, total_token_num):
    msg: str = ""

    if dist.get_rank() == 0:
        msg += "-------Perf Summary-------\n"
        whole_avg_latency = whole_end2end / (total_token_num)
        num_layers = getattr(model_config, "num_layers", model_config.num_hidden_layers)
        num_parameters = num_layers * model_config.hidden_size * model_config.hidden_size * 12
        if args.dtype in ["fp16", "bf16"]:
            num_bytes = 2
        else:
            num_bytes = 4

        msg += f"Whole batch end2end time: {whole_end2end * 1000:.2f} ms\n"
        msg += f"Whole batch per token latency: {whole_avg_latency * 1000:.2f} ms\n"
        msg += f"Throughput: {total_token_num / whole_end2end:.2f} tokens/s\n"
        msg += f"Flops: {num_parameters * num_bytes / whole_avg_latency / 1e12:.2f} TFLOPS\n"

    if torch.cuda.is_available():
        msg += f"-------Memory Summary Device:{get_accelerator().current_device()}-------\n"
        msg += f"Max memory allocated: {get_accelerator().max_memory_allocated() / GIGABYTE:.2f} GB\n"
        msg += f"Max memory reserved: {get_accelerator().max_memory_reserved() / GIGABYTE:.2f} GB\n"
```

Notice that `num_parameters` is very rough estimation of the model parameters.

Flops (2 for fp16) = num\_params \* 2 / (e2e time / total\_token\_time)

### Reference

{% embed url="https://www.baseten.co/blog/llm-transformer-inference-guide/#3500569-calculating-the-operations-per-byte-opsbyte-ratio" %}

{% embed url="https://kipp.ly/transformer-inference-arithmetic/" %}
