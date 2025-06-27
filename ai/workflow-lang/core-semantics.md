# Core Semantics for Agentic Workflow Language

## Consensus Model

After extensive discussion among experts from concatenative, array programming, traditional, and category theory perspectives, we have reached consensus on the core semantic model for the Agentic Workflow Language.

## Fundamental Abstraction

The language models AI workflows as **transformations of information through composable operations**. This abstraction is interpreted differently by each paradigm but maintains consistent semantics:

- **Concatenative view**: Data flowing through a stack with implicit routing
- **Array view**: Operations naturally lifting over collections
- **Traditional view**: Dataflow graph with nodes and edges
- **Categorical view**: Morphisms between information states

## Core Primitives

The minimal set of primitives that all paradigms agree upon:

### 1. Sequential Composition
The ability to chain operations where the output of one becomes the input of the next.

- **Concatenative**: `f g h` (left-to-right flow)
- **Array**: `h g f` (right-to-left composition)
- **Traditional**: `f |> g |> h` (pipeline)
- **Categorical**: `f >>> g >>> h` (morphism composition)

### 2. Branching (One-to-Many)
The ability to take a single input and produce multiple outputs for parallel processing.

- **Concatenative**: `dup` (duplicate stack top)
- **Array**: Natural array creation
- **Traditional**: `map` or `parallel`
- **Categorical**: Product or coproduct construction

### 3. Merging (Many-to-One)
The ability to combine multiple results into a single output.

- **Concatenative**: Stack reduction operations
- **Array**: Reduction along ranks
- **Traditional**: `reduce` or `fold`
- **Categorical**: Universal morphism from product/coproduct

### 4. Transformation
The ability to apply operations that change data from one form to another.

- All paradigms agree this is function application
- Domain-specific operations are built on this primitive

## Computational Model

```
     Transform           Branch              Transform            Merge
Start --------> State A -------> [State B₁, B₂, ..., Bₙ] --------> Final
                          
```

This pattern can be:
- **Nested**: Any state can itself be a workflow
- **Composed**: Workflows can be treated as single operations
- **Parallel**: Independent branches execute concurrently (semantically)

## Domain-Specific Properties

What makes this language special for AI workflows:

1. **Collections are Primary**: Operations naturally work over collections
   - Single items are just collections of size 1
   - No special syntax needed for "map" in many cases

2. **Semantic Operations**: Built-in operations understand meaning
   - `expand`: Creates semantically related variations
   - `synthesize`: Merges with semantic understanding
   - `perspectives`: Generates diverse viewpoints

3. **Non-determinism is Expected**: Same input can produce different outputs
   - Not a bug but a feature
   - Language embraces this rather than fighting it

4. **Resource Awareness**: Operations have cost (time, money, computation)
   - Can be tracked and controlled
   - But not required for basic semantics

## Unifying Principles

1. **Compositionality**: Complex workflows are built from simple operations
2. **Uniformity**: Same operations work at all scales (single item, collection, nested)
3. **Referential Transparency**: In the happy path, operations are pure transformations
4. **Domain Alignment**: Primitives match how humans think about AI tasks

## Mathematical Foundation

The core forms a **symmetric monoidal category** where:
- Objects are information states (topics, conversations, results)
- Morphisms are transformative operations
- Tensor product models parallel composition
- Unit object is the empty/null state

This ensures:
- Associativity of composition
- Identity operations exist
- Parallel operations can be reordered (when independent)
- Strong theoretical foundation for optimization

## Implementation Freedom

This semantic core allows:
- Multiple concrete syntaxes
- Different execution strategies
- Various optimization approaches
- Extension with new operations

As long as implementations respect these core semantics, they can:
- Choose eager or lazy evaluation
- Implement different parallelization strategies
- Provide different user interfaces
- Optimize based on usage patterns

## Next Steps

With this semantic foundation agreed upon, we can:
1. Design concrete syntaxes that appeal to different users
2. Specify execution models
3. Add error handling semantics
4. Build type systems
5. Implement prototypes

The key achievement is that all paradigms can work with this core while maintaining their unique strengths.