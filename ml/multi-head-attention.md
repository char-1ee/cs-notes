# Multi-Head Attention

```python
import torch
import torch.nn as nn
import torch.nn.functional as F
import math

class MultiHeadAttention(nn.Module):
    def __init__(self, d_model, n_heads):
        super(MultiHeadAttention, self).__init__()
        assert d_model % n_head == 0,  "d_model must be divisable by num_heads"
        
        self.d_model = d_model
        self.n_heads = n_heads
        self.head_dim = d_model // n_heads
        
        self.W_q = nn.Linear(d_model, d_model)
        self.W_k = nn.Linear(d_model, d_model)
        self.W_v = nn.Linear(d_model, d_model)
        self.W_o = nn.Linear(d_model, d_model)
        
    def attention(self, Q, K, V, mask=None):
        attn_score = torch.matmul(Q, K.transpose(-2, -1)) / math.sqrt(self.head_dim)
        if mask is not None:
            attn_score = attn_score.masked_fill(mask == 0, -1e9)
        attn_probs = F.softmax(attn_score, dim=-1)
        output = torch.matmul(attn_probs, V)
        return output
        
    def split_heads(self, x):
        batch_size, seq_len, d_model = x.size()
        return x.view(batch_size, seq_len, self.n_heads, self.head_dim).transpose(1, 2)

    def combine_heads(self, x):
        batch_size, _, seq_len, _, _ = x.size()
        return x.transpose(1, 2).contiguous().view(batch_size, seq_len, self.d_model)

    def forward(self, q, k, v, mask=None):
        Q = self.split_heads(self.W_q(q))
        K = self.split_heads(self.W_k(k))
        V = self.split_heads(self.W_v(v))
        
        attn_output = self.attention(Q, K, V, mask)
        output = self.W_o(self.combine_heads(attn_output))
        return output
        
# Example use   
d_model = 512
num_heads = 8
seq_len = 10
batch_size = 2 

attn = MultiHeadAttention(d_model, num_heads)
x = torch.randn(batch_size, seq_len, d_model)
output = attn(x, x, x)
print(output.shape)
```

### How scaled dot product attention layer changes shape?

```bash
Shape changes visualization: (seq_len same for qkv)

Q: (batch_size, num_heads, seq_length_q, d_k)
K: (batch_size, num_heads, seq_length_k, d_k)
V: (batch_size, num_heads, seq_length_v, d_k)
                   |
                   v
attn_scores: (batch_size, num_heads, seq_length_q, seq_length_k)
                   |
                   v
attn_probs: (batch_size, num_heads, seq_length_q, seq_length_k)
                   |
                   v
output: (batch_size, num_heads, seq_length_q, d_k)
```

Note that softmax `dim=-1` is to make the query (row) sum up to 1.

### How the attention heads are splited?

Input shape: (batch\_size, seq\_length, d\_model)

Reshape to: (batch\_size, seq\_length, num\_heads, d\_head)

Transpose to: (batch\_size, num\_heads, seq\_length, d\_head).&#x20;

1. Input tensor:
   * Shape: (2, 3, 12)
   * This represents 2 batches, each with 3 tokens, and each token has a 12-dimensional embedding
2. Split\_heads function:
   * It reshapes the input from (batch\_size, seq\_length, d\_model) to (batch\_size, seq\_length, num\_heads, d\_k)
   * Then it transposes to (batch\_size, num\_heads, seq\_length, d\_k)
3. Result after split\_heads:
   * The new shape is (2, 3, 3, 4)
   * This represents 2 batches, 3 heads, 3 tokens per sequence, and 4 dimensions per head

Let's look at what happens to the first sequence in the first batch:

* Before splitting: It's a 3x12 matrix (3 tokens, each with 12 dimensions)
* After splitting: It becomes 3 matrices of size 3x4 (3 heads, each looking at 3 tokens with 4 dimensions)

### Why we transpose to swap \`seq\_len\` and \`num\_heads\`?

The transpose operation brings the num\_heads dimension to the second position. This is crucial because PyTorch's batch operations work on the first few dimensions. By having (batch\_size, num\_heads) as the first two dimensions, we can easily perform batch matrix multiplications for all heads simultaneously.

### How is heads are combined?

The `combine_heads` function does inverse operation of `split_heads`. `.contiguous()`: This ensures that the tensor is stored in a contiguous block of memory, which is necessary for the subsequent view operation.
