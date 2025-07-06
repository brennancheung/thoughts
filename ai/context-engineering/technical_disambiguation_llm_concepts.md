# Technical Disambiguation: Core Concepts in Transformer-Based LLMs

## Overview

In discussions of Large Language Models, terms like "latent space," "embedding," and "high-dimensional vectors" are often used interchangeably or imprecisely. This document provides technical disambiguation and architectural specificity for these concepts within transformer-based LLMs.

## 1. Embeddings

### Definition
An embedding is a learned mapping from discrete tokens (words, subwords, or characters) to continuous vectors in a fixed-dimensional space.

### In Transformer Architecture
```python
# Conceptual implementation
class TokenEmbedding(nn.Module):
    def __init__(self, vocab_size, d_model):
        super().__init__()
        self.embedding = nn.Embedding(vocab_size, d_model)
        
    def forward(self, token_ids):
        # token_ids: [batch_size, seq_len]
        # output: [batch_size, seq_len, d_model]
        return self.embedding(token_ids)
```

### Key Properties
- **Input embeddings**: The initial transformation from token IDs to vectors
- **Dimensionality**: Typically 768 (BERT), 1024 (GPT-2), or larger
- **Learned parameters**: Part of the model's trainable weights
- **Not contextual**: Same token always maps to same vector initially

### Nuance
Embeddings specifically refer to the **lookup table** that maps discrete symbols to vectors, not the contextual representations that emerge later in the network.

## 2. High-Dimensional Vectors

### Definition
Vectors in ℝⁿ where n is large (typically hundreds to thousands of dimensions).

### In LLMs
Multiple types of high-dimensional vectors exist at different stages:

1. **Token embeddings**: Initial non-contextual vectors
2. **Positional encodings**: Vectors encoding sequence position
3. **Hidden states**: Contextual representations at each layer
4. **Attention weights**: Though often lower dimensional after projection

### Mathematical Properties
```python
# Example dimensions in various models
GPT2_small = {
    'embedding_dim': 768,
    'hidden_dim': 768,
    'attention_head_dim': 64,  # 768 / 12 heads
}

GPT3_175B = {
    'embedding_dim': 12288,
    'hidden_dim': 12288,
    'attention_head_dim': 128,  # 12288 / 96 heads
}
```

### Why High-Dimensional?
- **Expressiveness**: More dimensions allow encoding more nuanced relationships
- **Linear separability**: High dimensions make it easier to linearly separate concepts
- **Curse and blessing**: While suffering from sparsity, also enables rich representations

## 3. Latent Space

### Definition
The continuous vector space where the model performs its computations and where semantic relationships are encoded geometrically.

### Technical Specificity in Transformers
Transformers have **multiple latent spaces**, not just one:

1. **Embedding space**: Where token embeddings live
2. **Hidden state space**: The space at each transformer layer
3. **Attention space**: After Q, K, V projections
4. **Output space**: Before final vocabulary projection

### Key Distinction
"Latent" implies the space is **learned** and **not directly observed**. The model discovers the structure of this space during training.

```python
# Different latent spaces in a transformer
def transformer_forward(input_ids):
    # Embedding space
    x = token_embeddings(input_ids) + positional_embeddings
    
    for layer in transformer_layers:
        # Hidden state space (changes at each layer)
        # Each layer transforms the representation
        x = layer(x)
        
        # Within each layer:
        # - Attention space (after Q,K,V projection)
        # - FFN intermediate space (often 4x hidden dim)
    
    # Output space (before logits)
    return x
```

## 4. Manifolds

### Definition
A manifold is a topological space that locally resembles Euclidean space. In ML context, it's a lower-dimensional surface embedded in high-dimensional space where data concentrates.

### The Manifold Hypothesis in LLMs
The hypothesis suggests that natural language data lies on a lower-dimensional manifold within the high-dimensional embedding space.

### Evidence in LLMs
1. **Semantic directions**: Vector arithmetic (king - man + woman ≈ queen)
2. **Smooth interpolations**: Gradual transitions between concepts
3. **Clustering**: Semantically similar words form neighborhoods

### Technical Implications
```python
# Conceptual: Data doesn't fill the entire space uniformly
# If embeddings are 768-dimensional, the "true" semantic manifold
# might be much lower dimensional (e.g., 50-100 dimensions)

# This is why techniques like PCA/t-SNE can create meaningful 2D/3D visualizations
from sklearn.manifold import TSNE

# Reduce 768D embeddings to 2D for visualization
tsne = TSNE(n_components=2)
embeddings_2d = tsne.fit_transform(embeddings_768d)
```

### Important Nuance
The manifold is **implicit** and **emergent**, not explicitly designed. Different layers may organize information on different manifolds.

## 5. Distributions

### Definition
Probability distributions over tokens, hidden states, or attention patterns.

### Multiple Distributions in LLMs

1. **Token probability distribution** (output)
```python
# After final layer
logits = hidden_states @ embedding_matrix.T  # [batch, seq_len, vocab_size]
probs = softmax(logits / temperature)  # Probability distribution over vocabulary
```

2. **Attention distributions**
```python
# In each attention head
attention_scores = (Q @ K.T) / sqrt(d_k)
attention_probs = softmax(attention_scores)  # Distribution over sequence positions
```

3. **Hidden state distributions**
- Not explicit probability distributions
- But can analyze statistical properties (mean, variance, etc.)

### Distribution Shift Through Layers
Research shows that representations become increasingly "anisotropic" (not uniformly distributed) in deeper layers.

## 6. Representation Space vs. Parameter Space

### Representation Space
Where activations/hidden states live during forward pass:
- Changes with input
- Has semantic structure
- This is what we usually mean by "latent space"

### Parameter Space
Where model weights live:
- Fixed after training
- Much higher dimensional (billions of parameters)
- Not directly interpretable

## 7. Architectural Flow and Transformations

### Complete Picture
```python
def transformer_flow(tokens):
    # 1. Discrete tokens → Embedding space (via lookup)
    embeddings = embedding_layer(tokens)  # [batch, seq, d_model]
    
    # 2. Add positional information
    x = embeddings + positional_encoding
    
    # 3. Through transformer layers (repeated L times)
    for layer in range(n_layers):
        # 3a. Multi-head attention (operating in attention space)
        # Projects to Q,K,V spaces, computes attention, projects back
        attended = multi_head_attention(x)
        x = layer_norm(x + attended)
        
        # 3b. Feed-forward network (expanding to higher dim, then back)
        # Often expands to 4*d_model internally
        fed_forward = ffn(x)
        x = layer_norm(x + fed_forward)
    
    # 4. Final hidden states → Output distribution
    logits = x @ embedding_matrix.T  # Tie weights usually
    probs = softmax(logits)
    
    return probs
```

## 8. Critical Distinctions

### Embedding vs. Hidden State
- **Embedding**: Non-contextual, from lookup table
- **Hidden State**: Contextual, after transformer processing

### Latent Space vs. Embedding Space
- **Embedding Space**: Specific space where token embeddings live
- **Latent Space**: General term for any learned representational space

### Vector vs. Distribution
- **Vector**: A point in space (deterministic)
- **Distribution**: Probability over possible values (stochastic)

### Manifold vs. Space
- **Space**: The full high-dimensional ambient space
- **Manifold**: Lower-dimensional surface where data concentrates

## 9. Common Misconceptions

### "The" Latent Space
LLMs don't have one latent space but many:
- Each layer has its own representational space
- Different heads attend to different subspaces
- The geometry changes through the network

### "Embeddings" as Any Vector
Technically, embeddings are specifically the learned lookup table vectors. Contextual representations are "hidden states" or "representations," not embeddings.

### "High-Dimensional" as Mysterious
High dimensions are simply many numbers. A 768D vector is just 768 floating-point numbers. The mystery is in the learned structure, not the dimensionality itself.

## 10. Practical Implications

### For Analysis
- When analyzing "embeddings," specify which layer/stage
- Distinguish between type embeddings and token embeddings
- Consider anisotropy when measuring distances

### For Intervention
- Early layers: More syntactic, local patterns
- Middle layers: Semantic relationships emerge
- Final layers: Task-specific representations

### For Understanding
- Geometry matters: Cosine similarity often more meaningful than Euclidean
- Layerwise analysis reveals progressive abstraction
- Attention patterns provide interpretable structure

## Conclusion

Precision in terminology is crucial for both theoretical understanding and practical application. These concepts—embeddings, vectors, spaces, manifolds, and distributions—form a hierarchical structure where:

1. **Tokens** are discrete symbols
2. **Embeddings** map tokens to vectors
3. **Vectors** live in high-dimensional spaces
4. **Spaces** contain manifolds where data concentrates
5. **Transformations** move representations through these spaces
6. **Distributions** emerge over possible outputs

Understanding these distinctions enables clearer thinking about model behavior, more precise experimental design, and more effective communication about LLM internals.