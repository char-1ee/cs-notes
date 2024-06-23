---
description: This page focus on the System side
---

# Survey on efficient LLM inference

LLM inference optimization methods

* Low-bit quantization
* Parallel computing
* Memory management
* Request scheduling
* Kernel optimization

## Low-bit quantization

* Quantization-Aware Training (QAT)
* Post-Training Quantization (PTQ)

### PTQ

Int8/int4 quant on model weight and activation.

[A Comprehensive Study on Post-Training Quantization for Large Language Models](https://link.zhihu.com/?target=https%3A//arxiv.org/pdf/2303.08302v2.pdf)

### QAT

* [QLoRA: Efficient finetuning of quantized llms](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2305.14314)
* [LLM-QAT: Data-Free Quantization Aware Training for Large Language Models](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2305.17888)
* [Memory-Efficient Fine-Tuning of Compressed Large Language Models via sub-4-bit Integer Quantization](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2305.14152)

Low bit quant -> better infernce latency

[LLM.int8(): 8-bit Matrix Multiplication for Transformers at Scale](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2208.07339)

## Parallel Computing

### Model parallelism

Tensor parallelism, Pipeline parallelism, Sequence parallelism

PaLM inference, AlpaServe, FlexFlow-Serve, SpotServe

### Distributed inference

Inference with edge computing resources (isolated and connected via Internet)

[Petals: Collaborative Inference and Fine-tuning of Large Models](https://arxiv.org/abs/2209.01188)

## Memory Management

* [vLLM](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2309.06180): PageAttention
* [SpecInfer](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2305.09781): TreeAttention
* [LightLLM](https://link.zhihu.com/?target=https%3A//github.com/ModelTC/lightllm): TokenAttention

KV Cache is now a standard fact for inference. These optimizations enhance throughput while delays the request response (trade-off).

## Request Scheduling

Early-stage request-level scheduling:

* FastTransformers
* [ORCA](https://www.usenix.org/conference/osdi22/presentation/yu): iteration-level scheduling + selective batching

Continuous batching -> vLLM, TensorRT-LLM (flight batching), RayLLM

[S3](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2306.06000): output sequence length predictor -> schedule more concurrent requests with limited GPU memory -> higher inference throughput.

## Kernel Optimization

* Kernel fusion
* Custom attention
* Sampling
* Varing sequence length
* Auto compilation

### Kernel fusion

Fusion of operators -> reduce kernel launch cost + memory access cost

FasterTransformer, LightSeq, ByteTransformer

* GEMM
* addBias and non-GEMM layers (residual, LayerNorm, ReLu)
* GEMM and activation layer with CUTLASS

### Custom attention

#### Prefill

[xFormers](https://link.zhihu.com/?target=https%3A//github.com/facebookresearch/xformers) oneline softmax idea: computes attention with CUTLASS

#### Decode

[FlashDecoding](https://link.zhihu.com/?target=https%3A//pytorch.org/blog/flash-decoding/) and [FlashDecoding++](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2311.01282)

### Sampling

BeamSearch, TopK, TopP, temperature -> good, but sampling efficiency.

[LightSeq](https://link.zhihu.com/?target=https%3A//arxiv.org/abs/2010.13887): layering sampling that groups the vocab table, and then indexing candidates within each group using multiple GPU instructions. After that ranks these candidates to get first-K tokens.

### Varing sequence length

Padding -> waste computing resources and memory

[Packing](https://link.zhihu.com/?target=https%3A//github.com/bytedance/effective\_transformer): stores sequence in continuous memory space -> no padding. But attention computation cannot happen in contiunous memory space, so there needs unpacking before attention computing.

[ByteTransformer](https://arxiv.org/abs/2210.03052) provides a detailed application of packing and unpacking.

### Auto compilation

TVM, MLIR, JAX, OpenAI Triton, TASO, TorchInductor.

## Inference frameworks

<figure><img src="../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>

## Reference

Towards Efficient Generative Large Language Model Serving: A Survey from Algorithms to Systems

{% embed url="https://zhuanlan.zhihu.com/p/680635901" %}
