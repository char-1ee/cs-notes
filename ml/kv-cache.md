---
description: Why we dont have Q cache?
---

# KV Cache

<figure><img src="../.gitbook/assets/image.png" alt=""><figcaption><p>GPT2 architecture</p></figcaption></figure>

Notes about the GPT 2 architecture except Attention layer (yellow stands for new joining token):

1. `wte` and `wqe` are 2 embeddings that map input\_ids and position\_ids to hidden\_states
2. `c_attn` is a linear transformation module which triple hidden\_states, and then divide tripled hidden\_states into q, k, v.
3. `lm_head` module generates logits. Transform the hidden\_states from `d_model` into `vocab_size`.

<figure><img src="../.gitbook/assets/image (1).png" alt=""><figcaption><p>Attentin computation</p></figcaption></figure>

Notice that Attention logits are decided by last row of attention score, but with the whole `V` and `K`. This tells that only the last row of `Q` participate the computation. That is the reason why we only need KV cache than QKV cache.

Sp we got this finally:&#x20;

<figure><img src="../.gitbook/assets/image (2).png" alt=""><figcaption><p>GPT2 with KV cache</p></figcaption></figure>



### Reference

{% embed url="https://medium.com/@fenriar/how-kv-cache-works-in-gpt-like-architecture-models-70f682396376" %}
