---
description: Why we dont have Q cache?
---

# KV Cache

<figure><img src="../.gitbook/assets/image (6).png" alt=""><figcaption><p>GPT2 architecture</p></figcaption></figure>

Notes about the GPT 2 architecture except Attention layer:

1. `wte` and `wqe` are 2 embeddings that map input\_ids and position\_ids to hidden\_states
2. `c_attn` is a linear transformation module which triple hidden\_states, and then divide tripled hidden\_states into q, k, v.
3. `lm_head` module generates logits. Transform the hidden\_states from `d_model` into `vocab_size`.

The yellow strip in the tensor initially represents the last token of the input sequence. As the forward computation proceeds, it gradually becomes the probability distribution of the next token, which is the last row of `logits`. The rows except for last one in`logits`are meaningless in the inference phase.

<figure><img src="../.gitbook/assets/image (1) (1) (1) (1).png" alt=""><figcaption><p>Attentin computation</p></figcaption></figure>

Notice that Attention logits are decided by last row of attention score, but with the whole `V` and `K`. This tells that only the last row of `Q` participate the computation. That is the reason why we only need KV cache than QKV cache.

So we got this finally:&#x20;

<figure><img src="../.gitbook/assets/image (2) (1) (1) (1).png" alt=""><figcaption><p>GPT2 with KV cache</p></figcaption></figure>

### Why there is no Q cache in inference but KV cache?

Math explanation (with Causal Mask)

$$
Attention(K, Q, V) = softmax(\frac{QK^\top}{\sqrt{d_k}})V,
K, Q \in \mathbb{R}^{T \times d_k}, V \in \mathbb{R}^{T \times d_v}
$$

$$
\mathcal{S}(QK^\top)V = \begin{bmatrix}
\mathcal{S}_1(q_1 \cdot k_1) & 0 & \cdots & 0 \\
\mathcal{S}_2(q_2 \cdot k_1) & \mathcal{S}_2(q_2 \cdot k_2) & \cdots & 0 \\
\vdots & \vdots & \ddots & \vdots \\
\mathcal{S}_T(q_T \cdot k_1) & \mathcal{S}_T(q_T \cdot k_2) & \cdots & \mathcal{S}_T(q_T \cdot k_T)
\end{bmatrix}
\begin{bmatrix}
v_1 \\
v_2 \\
\vdots \\
v_T
\end{bmatrix} =
\begin{bmatrix}
\mathcal{S}_1(q_1 \cdot k_1)v_1 \\
\mathcal{S}_2(q_2 \cdot k_1)v_1 + \mathcal{S}_2(q_2 \cdot k_2)v_2 \\
\vdots \\
\mathcal{S}_T(q_T \cdot k_1)v_1 + \mathcal{S}_T(q_T \cdot k_2)v_2 + \cdots + \mathcal{S}_T(q_T \cdot k_T)v_T
\end{bmatrix}
$$



$$
[\mathcal{S}(QK^\top)V]_t = \sum_{j=1}^{t} \mathcal{S}_t(q_t \cdot k_j)v_j
$$

Note that, Q only provides `q_t` of current position in Attention computation. While KV provide elements from all positions into computation.&#x20;

### Example

```python
class SimplifiedTransformerLayer(nn.Module):
    def __init__(self, d_model, num_heads):
        super().__init__()
        self.qkv = nn.Linear(d_model, 3 * d_model)
        self.out = nn.Linear(d_model, d_model)
        self.d_model = d_model
        self.num_heads = num_heads

    def forward(self, x, kv_cache=None):
        qkv = self.qkv(x)
        q, k, v = qkv.chunk(3, dim=-1)
        
        if kv_cache is not None:
            k_cache, v_cache = kv_cache
            k = torch.cat([k_cache, k], dim=1)
            v = torch.cat([v_cache, v], dim=1)
        
        # Compute attention
        attn_output = self.attention(q, k, v)
        
        return self.out(attn_output), (k, v)

    def attention(self, q, k, v):
        # Simplified attention mechanism
        scores = torch.matmul(q, k.transpose(-2, -1)) / (self.d_model ** 0.5)
        attn_weights = torch.softmax(scores, dim=-1)
        return torch.matmul(attn_weights, v)

# Usage example
layer = SimplifiedTransformerLayer(d_model=512, num_heads=8)
input_seq = torch.randn(1, 10, 512)  # Batch size 1, sequence length 10
output, kv_cache = layer(input_seq)

# Next token prediction
next_token = torch.randn(1, 1, 512)
output, kv_cache = layer(next_token, kv_cache)
```

### Reference

{% embed url="https://medium.com/@fenriar/how-kv-cache-works-in-gpt-like-architecture-models-70f682396376" %}

{% embed url="https://www.zhihu.com/question/653658936/answer/3545520807" %}
