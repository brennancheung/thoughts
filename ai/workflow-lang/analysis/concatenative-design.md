# Concatenative Language Design for AI Workflows

## Overview

This document presents the design of **Flow**, a pure concatenative programming language specifically crafted for composable AI workflows. Flow leverages the fundamental insight that AI workflows are essentially data transformation pipelines, and concatenative languages naturally express such pipelines through implicit data flow and function composition.

## 1. Core Philosophy

### The Concatenative Advantage

In traditional languages, workflow composition requires explicit plumbing:
```python
result1 = step1(input)
result2 = step2(result1)
result3 = step3(result2)
```

In Flow, composition is concatenation:
```
input step1 step2 step3
```

This isn't just syntactic sugar—it's a fundamental shift in how we think about workflows. In Flow:

1. **Data flows implicitly** through the stack, eliminating naming ceremony
2. **Functions compose by juxtaposition**, making pipelines natural
3. **Point-free style** focuses on transformations, not intermediate values
4. **Quotations enable higher-order workflows** without syntax overhead
5. **Stack effects make data dependencies explicit** in the program structure

### Why Concatenative for AI Workflows?

AI workflows are inherently about transformation pipelines:
- Text → Embedding → Similarity Search → Context
- Image → Feature Extraction → Classification → Decision
- Data → Model → Prediction → Action

Concatenative languages make these transformations first-class citizens. The stack *is* the pipeline.

## 2. Operational Semantics

### Stack-Based Execution Model

Flow uses multiple stacks for different concerns:

1. **Data Stack (DS)**: Primary stack for data values
2. **Return Stack (RS)**: For control flow and temporary storage
3. **Effect Stack (ES)**: For tracking computational effects (AI calls, I/O)

### Execution Rules

Program execution proceeds left-to-right, with each word (operation) consuming and producing values on the stacks:

```
Initial: DS = [x, y, z] (top is z)
Word 'f' with stack effect (a b -- c):
  - Pops z and y from DS
  - Applies f(y, z) 
  - Pushes result c to DS
Final: DS = [x, c]
```

### Effect Tracking

AI operations are effects that get recorded on the Effect Stack:

```
"prompt" llm-complete
```

This pushes an LLM effect onto ES while the result goes to DS, enabling:
- Effect analysis before execution
- Retry logic and error handling
- Cost estimation
- Execution planning

## 3. Denotational Semantics

### Programs as Function Composition

Every Flow program denotes a function from stacks to stacks:

```
⟦P⟧ : Stack* → Stack*
```

For concatenation:
```
⟦P Q⟧ = ⟦Q⟧ ∘ ⟦P⟧
```

This gives us the fundamental law: **program concatenation is function composition**.

### Quotations as First-Class Functions

Quotations represent unevaluated programs:

```
⟦[P]⟧ : Stack* → Stack*
⟦[P]⟧(s) = push(s, P)
```

With combinators to manipulate them:
```
⟦call⟧(push(s, P)) = ⟦P⟧(s)
```

### Effect Semantics

Effects are modeled as monadic computations:

```
⟦llm-complete⟧ : String → Effect String
```

Where Effect tracks computational effects separately from pure transformations.

## 4. Syntax Design

### Minimal, Elegant Notation

Flow uses a minimal syntax optimized for readability and composability:

```flow
# Literals
"hello"           # String
42               # Number  
true             # Boolean
{ key: "value" } # Record

# Quotations (deferred execution)
[ 2 * ]          # Function that doubles

# Word definition
: double ( n -- n ) 2 * ;

# Pipeline operators
| branch         # Parallel execution
& merge          # Synchronization
? if-else        # Conditional

# AI primitives
@llm             # LLM invocation
@embed           # Embedding generation
@search          # Vector search
```

### Syntactic Sugar for Common Patterns

While maintaining concatenative purity, we provide sugar for readability:

```flow
# Method chaining syntax (still concatenative)
data 
  .preprocess
  .embed
  .search(k: 5)
  
# Expands to:
data preprocess embed 5 :k search
```

## 5. Core Primitives

### Stack Manipulation
```flow
dup    ( a -- a a )          # Duplicate top
drop   ( a -- )              # Remove top  
swap   ( a b -- b a )        # Exchange top two
over   ( a b -- a b a )      # Copy second to top
rot    ( a b c -- b c a )    # Rotate top three
```

### Data Structures
```flow
cons   ( a list -- list' )   # Prepend to list
uncons ( list -- a list' )   # Deconstruct list
get    ( key record -- value ) # Record access
set    ( value key record -- record' ) # Record update
```

### Control Flow
```flow
call   ( quot -- )           # Execute quotation
if     ( ? t f -- )          # Conditional execution
map    ( list quot -- list' ) # Map over list
fold   ( list init quot -- result ) # Fold/reduce
```

### AI Operations
```flow
@llm        ( prompt -- response )       # LLM completion
@embed      ( text -- vector )           # Generate embedding
@search     ( vector n -- results )      # Vector similarity search
@classify   ( input model -- class )     # Classification
@extract    ( text schema -- data )      # Structured extraction
```

### Workflow Combinators
```flow
|>     ( a [p] [q] -- b c )   # Parallel execution
&>     ( a b -- c )           # Merge results
try    ( [p] [handler] -- )   # Error handling
retry  ( n [p] -- )           # Retry n times
cache  ( key [p] -- result )   # Memoization
```

## 6. Type System

### Optional Stack Effect Annotations

Flow uses optional stack effect annotations for documentation and verification:

```flow
: process-text ( text -- summary )
  @embed              ( text -- vector )
  5 :k @search       ( vector -- results )
  concatenate        ( results -- text' )
  "Summarize:" swap concat  ( text' -- prompt )
  @llm ;             ( prompt -- summary )
```

### Gradual Typing

Types can be inferred or explicitly specified:

```flow
: typed-double ( n:Num -- n:Num ) 2 * ;

# With constraints
: safe-div ( a:Num b:Num{!=0} -- c:Num ) / ;
```

### Effect Types

AI operations carry effect annotations:

```flow
: pure-fn ( a -- b ) ;                    # Pure
: effectful ( a -- b ) <llm> ;           # Uses LLM
: multi-effect ( a -- b ) <llm,search> ; # Multiple effects
```

## 7. Examples

### Basic Text Processing Pipeline
```flow
: analyze-sentiment ( text -- sentiment )
  # Preprocess
  lowercase trim
  
  # Create prompt
  "Analyze sentiment (positive/negative/neutral): " swap concat
  
  # Get LLM response
  @llm
  
  # Parse result
  ["positive" "negative" "neutral"] swap in? 
  ["neutral"] if not ;
```

### Parallel Document Processing
```flow
: process-documents ( docs -- summaries )
  # Define processing for single doc
  [ extract-text 
    500 chunk-words
    [ summarize-chunk ] map
    " " join ]
    
  # Process all docs in parallel
  map-parallel ;

: summarize-chunk ( text -- summary )
  "Summarize in one sentence: " swap concat @llm ;
```

### Conditional Workflow with Retry
```flow
: smart-query ( query -- response )
  # First try direct search
  @embed 10 :k @search
  
  # Check if we have good results
  dup quality-score 0.7 >
  
  # If good, format results; otherwise, use LLM
  [ format-results ]
  [ drop query "Please help with: " swap concat @llm ]
  if ;

: reliable-query ( query -- response )
  3 [ smart-query ] retry ;
```

### Complex RAG Pipeline
```flow
: rag-pipeline ( question -- answer )
  # Parallel paths: embed question and extract keywords  
  dup [ @embed ] [ extract-keywords ] fork
  
  # Search with both strategies
  swap 5 :k @search             # Vector search
  swap keyword-search           # Keyword search
  
  # Merge and rank results
  concat unique rank-by-relevance
  5 take                        # Top 5 results
  
  # Build context and generate
  format-context
  question swap build-prompt
  @llm 
  
  # Post-process
  fact-check
  add-citations ;
```

### Workflow Composition
```flow
# Define reusable components
: ->embedding ( text -- vector )
  preprocess @embed normalize ;

: ->context ( vector -- context )
  10 :k @search 
  [ extract-content ] map
  "\n---\n" join ;

: ->response ( context question -- answer )
  prompt-template @llm ;

# Compose into workflows
: simple-rag ( question -- answer )
  dup ->embedding ->context swap ->response ;

: advanced-rag ( question -- answer )
  dup [ ->embedding ->context ]     # Path 1: vector search
      [ keywords web-search ]       # Path 2: web search  
  fork
  merge-contexts                    # Combine results
  swap ->response                   # Generate answer
  verify-facts ;                    # Post-process
```

## 8. Why This Works

### Natural Dataflow Expression

Concatenative languages make dataflow explicit through the stack. In AI workflows, this maps perfectly to the pipeline nature of data transformations:

```flow
input | preprocess | model | postprocess | output
```

Becomes simply:
```flow
input preprocess model postprocess output
```

### Composability Through Simplicity

Because every operation has a uniform interface (stack → stack), composition is trivial:

```flow
: pipeline1 ( -- ) op1 op2 op3 ;
: pipeline2 ( -- ) op4 op5 ;
: combined ( -- ) pipeline1 pipeline2 ;  # Just concatenate!
```

### Higher-Order Workflows

Quotations enable sophisticated workflow patterns without special syntax:

```flow
# Conditional execution
condition [ path1 ] [ path2 ] if

# Mapping over data
data [ process ] map

# Complex control flow
[ try-primary ] [ use-fallback ] recover
```

### Effect Tracking and Optimization

By tracking effects separately, we can:
1. Analyze workflows before execution
2. Optimize AI operations (batching, caching)
3. Estimate costs
4. Handle failures gracefully

```flow
: workflow ( -- ) <llm,embed,search>
  phase1    # Pure transformations
  phase2    # AI operations (batched)
  phase3 ;  # Final processing
```

### Debugging and Introspection

Stack-based execution makes debugging intuitive:

```flow
: debug-workflow ( -- )
  step1 .s  # Print stack
  step2 .s
  step3 .s ;
```

### Parallel by Default

Concatenative style makes parallelism natural:

```flow
# Sequential
a b c

# Parallel
a [b] [c] fork join

# The syntax clearly shows the fork point
```

## Conclusion

Flow demonstrates that concatenative programming is not just viable but *ideal* for AI workflow composition. By embracing the stack as the primary abstraction, we achieve:

1. **Simplicity**: No variable naming, explicit plumbing, or complex syntax
2. **Composability**: Functions compose through mere juxtaposition  
3. **Expressiveness**: Higher-order patterns through quotations
4. **Performance**: Static analysis enables optimization
5. **Clarity**: Dataflow is visible in the program text

The concatenative paradigm transforms AI workflow programming from orchestration (coordinating named components) to composition (building pipelines through concatenation). This fundamental shift produces code that is simultaneously more concise, more composable, and more comprehensible than traditional approaches.

In Flow, the program structure *is* the workflow structure. There is no gap between design and implementation—they are one and the same.