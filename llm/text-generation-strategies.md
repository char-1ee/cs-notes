# Text generation strategies

## Decoding strategies:

* Greedy search
* Contrastive search
* Multinomial sampling
* Beam-search sampling
* Speculative decoding
* etc.

### Greedy search

Select the word with highest probability as next token.

Model quickly starts repeating itself. Multinomial sampling randomly selects the next token based on the probability distribution over entrie vocab, to reduce repetition.

### Beam search

Unlike greedy search, beam-search decoding keeps several hypotheses at each time step and eventually chooses the hypothesis that has the overall highest probability for the entire sequence.

While the result is arguably more fluent, the output still includes repetitions of the same word sequences. One of the available remedies is to introduce _n-grams_ (_a.k.a_ word sequences of n words) penalties. The most common _n-grams_ penalty makes sure that no _n-gram_ appears twice by manually setting the probability of next words that could create an already seen _n-gram_ to 0.

### Sampling

Randomly picks the next token according to its conditional probability distribution.

#### Top-K sampling

Sampling from the top-K probability tokens.

Combine with Temperature Sampling, Repetition Penalty, Length Penalty for better generation. For example, we adopt a generation strategy of top-k + top-p + temperature. It has a execution order of top-k -> top-p -> temperature.

#### Top-P sampling (Nucleus sampling)

Sampling from the collection of tokens whose probabilities sum up to certain threshold value p.

### Speculative decoding

[https://arxiv.org/pdf/2211.17192.pdf](https://arxiv.org/pdf/2211.17192.pdf)

Uses an draft model with same tokenizer to generate a few candidate tokens. The main model validates the candidate tokens in a single forward pass.

## Reference

{% embed url="https://huggingface.co/blog/how-to-generate" %}

{% embed url="https://huggingface.co/docs/transformers/generation_strategies#decoding-strategies" %}
