# Agentic Workflow Language - Panel Discussion

*The four research scientists gather around the conference table with their supervisor. Each has their design documents and critiques spread out before them.*

**Supervisor**: Good morning everyone. Before we dive into the technical details, I want to make sure we're all aligned on what we're trying to build and why. Let's go around the room - please explain in terms we can all understand what you think this Agentic Workflow Language is meant to accomplish. Dr. Conway, why don't you start us off from the concatenative perspective?

**Dr. Conway (Concatenative Expert)**: Thanks. The way I see it, we're trying to solve a fundamental mismatch. When people work with AI today, they're stuck in this back-and-forth chat pattern - ask a question, get an answer, ask another question. But that's not how we actually solve problems. 

When I tackle a complex problem, I explore multiple paths, I combine different approaches, I backtrack and try again. The Agentic Workflow Language should let us express these natural problem-solving patterns directly. In my world, we think of this as data flowing through transformations - like water through pipes. Each operation transforms the data and passes it along.

**Dr. Iverson (Array Expert)**: Exactly! And to add to that - we're often dealing with *many* things at once, not just one conversation. If I want to analyze a topic from 10 different angles, why should I write 10 separate programs? In array thinking, we operate on whole collections as easily as single items. 

The language should make "analyze these 50 documents" as simple as "analyze this one document." No loops, no special handling - the same operations just work at any scale.

**Dr. Thompson (Traditional Expert)**: I appreciate those perspectives, but I'm also thinking about the thousands of developers who will actually use this. They need to debug when things go wrong, estimate costs before running expensive operations, and integrate this with their existing codebases.

The "why" for me is enabling regular developers to orchestrate complex AI workflows without becoming experts in a completely new paradigm. Yes, we want power and elegance, but we also need error messages that make sense and tools that feel familiar.

**Dr. Mac Lane (Category Theory Expert)**: *adjusts glasses* Let me try to synthesize what we're all saying. At its core, we're building a language for *composition* - taking small, understandable pieces and combining them into larger solutions. The mathematical beauty here is that the same patterns appear at every level.

Whether you're combining two operations or two entire workflows, the rules should be consistent. This isn't just about aesthetics - it means users can reason about complex systems by understanding simple rules.

**Supervisor**: Good, so we all agree on the core problem - enabling non-linear, composable AI workflows. But I'm hearing different priorities. Conway, you mentioned data flow through pipes. Can you explain what that means without the jargon?

**Dr. Conway**: Sure. Imagine you're making coffee. You start with beans, grind them, add hot water, filter, and get coffee. Each step transforms what you have into something new. In concatenative programming, we just write: `beans grind add-water filter` - the data flows implicitly from left to right. No need to name intermediate results.

**Dr. Iverson**: And in my approach, if you want to make coffee for 20 people, you don't change the process - the operations automatically scale up. That's the power of array thinking.

**Dr. Thompson**: But what happens when the grinder jams? That's my concern. We need clear error handling, not just elegant notation.

**Dr. Mac Lane**: This is where the mathematical foundation helps. If we define our operations with clear contracts - what they expect and what they promise - we can catch many errors before they happen.

**Supervisor**: I see some tension here between elegance and practicality. How do we resolve this?

**Dr. Thompson**: I actually think we can have both. My design shows progressive complexity - start simple, add power as needed. A beginner writes `askAI("question")`, an expert writes complex pipelines.

**Dr. Conway**: But that risks creating a language that's neither here nor there. In my experience, committing to a paradigm gives you more power, not less.

**Dr. Iverson**: What if we think of it differently? Not as choosing between paradigms, but finding the paradigm that naturally fits the domain. AI workflows *are* arrays of operations on arrays of data. The notation should reflect that.

**Dr. Mac Lane**: I'd like to raise a fundamental question - what does it mean for two workflows to be "the same"? If we can't answer that, how can users reason about their programs?

**Supervisor**: Can you elaborate on why that matters?

**Dr. Mac Lane**: If I write a workflow one way today and slightly differently tomorrow, are they equivalent? Can the system optimize one into the other? This is where formal semantics matter - not for academic purity, but for practical predictability.

**Dr. Conway**: That's actually where stack-based systems shine. Function composition has clear laws - `(f ∘ g) ∘ h = f ∘ (g ∘ h)`. Users can refactor with confidence.

**Dr. Thompson**: But users need to understand those laws first. How many developers know category theory?

**Dr. Iverson**: They don't need to know the theory, just see the patterns. When you work with arrays long enough, you internalize that `map f (map g xs) = map (f after g) xs`. It becomes intuitive.

**Supervisor**: Let's get specific. Can each of you show me how you'd express this workflow: "Take a research question, generate 5 hypotheses, test each one, and synthesize the results"?

**Dr. Conway**: 
```
question 
5 hypotheses 
'test each 
synthesize-results
```

**Dr. Iverson**: 
```
synthesize test¨ 5 hypotheses question
```

**Dr. Thompson**: 
```
question 
|> generateHypotheses(5)
|> map(test)
|> synthesizeResults()
```

**Dr. Mac Lane**: 
```
question >>> hypotheses(5) >>> mapM(test) >>> synthesize
```

**Supervisor**: Interesting - you're all expressing the same idea, but with different emphasis. Conway removes all the punctuation, Iverson reads right-to-left with that `¨` symbol, Thompson uses familiar syntax, and Mac Lane shows the monadic structure. What can we learn from this?

**Dr. Thompson**: That we're all solving the same problem! Maybe the question isn't which syntax wins, but how to support multiple syntaxes over a common semantic core.

**Dr. Conway**: Now you're speaking my language. In Forth, we often have different "vocabularies" for different domains, all compiling to the same stack operations.

**Dr. Iverson**: As long as the core supports proper array operations. Syntax is secondary to semantics.

**Dr. Mac Lane**: Precisely. Define the mathematical structure first, then find notations that express it clearly for different audiences.

**Supervisor**: This is productive. What other key issues do we need to address?

**Dr. Thompson**: Cost control. When each operation might cost $0.50 in API calls, users need visibility and control.

**Dr. Conway**: Effect tracking. My design separates pure transformations from effects like AI calls.

**Dr. Iverson**: Parallelism. When I map over 100 items, which run in parallel? Which in sequence?

**Dr. Mac Lane**: Equivalence and optimization. Which transformations preserve meaning?

**Supervisor**: Ok, I see some good progress here. It sounds like we're all agreeing that if we spend some rigor now and get the semantics right then it doesn't really matter what language syntax we use. But the key first step is to really understand that core? One issue I'd like to raise is that of the catching errors before they happen vs surfacing them later. I think we need to address runtime scenarios like what if the API call to the LLM fails, if there is rate limiting, or if it just flat out errors.

*The scientists lean forward, considering this practical concern.*

**Dr. Thompson**: Yes! This is exactly what keeps me up at night. In production, APIs fail all the time. Rate limits, network issues, model overload, malformed responses - these aren't edge cases, they're Tuesday.

**Dr. Conway**: In the concatenative world, we handle this through our effect stack. When you write `question @llm response`, the `@llm` operation doesn't just transform data - it also pushes an effect record. This lets us intercept and handle failures at runtime.

**Dr. Iverson**: But that's reactive. In array languages, we can be more proactive. If I'm mapping an operation over 1000 items and I know the rate limit is 10/second, the runtime should automatically batch and throttle. The user writes `llm¨ items` and the system handles the logistics.

**Dr. Mac Lane**: *drawing on whiteboard* I think we need to distinguish between types of failures. There are recoverable failures - rate limits, temporary network issues - and semantic failures - the LLM returns nonsense or refuses to answer. These need different handling strategies.

**Supervisor**: Can you give me concrete examples?

**Dr. Mac Lane**: Consider this workflow: `question >>> expand(5) >>> evaluate >>> pick-best`. What if the expansion step only generates 3 responses instead of 5 due to content filters? Is that a failure? A warning? Should we proceed with 3?

**Dr. Thompson**: In my design, I use Result types - every operation returns either Success(value) or Failure(error). The pipeline continues only if all operations succeed. But you can also write:
```
question 
|> expand(5).recover(expandSimpler)
|> evaluate
|> pickBest()
```

**Dr. Conway**: That's explicit, which is good, but verbose. In Forth-like systems, we use error handlers on the return stack:
```
: safe-expand
  5 expand
  ['] handle-partial on-error ;
```

**Dr. Iverson**: Both approaches assume errors are exceptional. But with AI, partial results are normal! If I ask for 10 perspectives and get 7, that's usually fine. The language should embrace partiality, not fight it.

**Supervisor**: So how do we handle rate limiting specifically?

**Dr. Thompson**: Explicit retry logic with exponential backoff:
```
async function callWithRetry(prompt, maxRetries = 3) {
  for (let i = 0; i < maxRetries; i++) {
    try {
      return await llm(prompt);
    } catch (e) {
      if (e.code === 'RATE_LIMIT') {
        await sleep(Math.pow(2, i) * 1000);
      } else throw e;
    }
  }
}
```

**Dr. Conway**: Too much ceremony. The effect system should handle this:
```
question 
@llm.retry(3).backoff
response
```

**Dr. Iverson**: You're both thinking too locally. Rate limits are a global resource constraint. The runtime should manage a token bucket across ALL operations:
```
set-rate-limit 10/second
big-array llm¨  ⍝ automatically throttled
```

**Dr. Mac Lane**: *nods* This touches on something deeper - resource semantics. We need to track not just types but resources. Linear types could help here. Each API call consumes a "token" from a finite pool.

**Supervisor**: What about cascading failures? If step 3 of a 10-step pipeline fails, what happens to the work already done?

**Dr. Conway**: Transaction semantics! In my design, you can mark checkpoints:
```
start-transaction
  expensive-operation-1
  checkpoint
  expensive-operation-2
  might-fail-operation
commit-or-rollback
```

**Dr. Thompson**: Or memoization. Cache successful intermediate results:
```
pipeline = cached(step1) 
  |> cached(step2)
  |> step3  // If this fails, we don't redo step1&2
```

**Dr. Iverson**: This is why I like functional approaches. If operations are pure except for AI calls, we can replay from any point. The array holds all intermediate states naturally.

**Dr. Mac Lane**: We're dancing around the real issue - what are the semantics of partial failure? Is a workflow that completes 7 of 10 branches successful? This isn't a implementation detail, it's a semantic question.

**Supervisor**: Good point. How would each of your approaches handle this scenario: "Generate 10 marketing slogans, but the AI filter blocks 3 as inappropriate"?

**Dr. Conway**: Stack-based approach pushes what it can:
```
10 slogans
'appropriate? filter
count 7 >= assert "Need at least 7"
```

**Dr. Iverson**: Array approach naturally handles variable-size results:
```
slogans ← appropriate? filter 10 generate-slogans topic
→ naturally returns 7 items
```

**Dr. Thompson**: Explicit handling with warnings:
```
result = generateSlogans(10)
  .filter(appropriate)
  .logWarning(r => r.length < 10)
  .assert(r => r.length >= 7, "Too many filtered")
```

**Dr. Mac Lane**: The type system should express this:
```
generateSlogans : Topic → IO (Vector 10 (Maybe Slogan))
filter : (a → Bool) → Vector n (Maybe a) → Σ(m:Nat). Vector m a
-- The output type tracks that we have some number m ≤ n results
```

**Supervisor**: I'm seeing a pattern - you all have solutions, but they're quite different. Is there a unified approach?

**Dr. Thompson**: What if we think in terms of policies? Let users declare how to handle different failure modes:
```
workflow.policy({
  rateLimits: 'exponential-backoff',
  partialResults: 'continue-if-above-threshold',
  errors: 'retry-then-fail'
})
```

**Dr. Conway**: Policies could work, but they should be composable like everything else. Push a policy onto the control stack, pop it when done.

**Dr. Iverson**: Or make them array operations. Policies are just filters on the result space.

**Dr. Mac Lane**: Philosophically, we're trying to model computation in an unreliable distributed system. The π-calculus might be more appropriate than pure functional models here.

**Supervisor**: Let's get practical. What's the minimum viable error handling for version 1?

**Dr. Thompson**: Try-catch for catastrophic failures, timeouts for hung operations, and automatic retry for rate limits. Keep it simple.

**Dr. Conway**: Plus effect tracking so users know what might fail before they run it.

**Dr. Iverson**: And partial result handling. Don't make users write special cases for getting 8 results instead of 10.

**Dr. Mac Lane**: As long as the semantics are clear. What does the program *mean* when operations fail?

**Supervisor**: Ok, sounds like we all have solutions to the problem but I agree with Lane, we haven't really rigorously defined the classes of problems and what each of those classes mean (both from a design perspective) but also more practically.

*The room falls quiet as everyone considers this fundamental question.*

**Dr. Mac Lane**: *starts writing on the whiteboard* Let me propose a taxonomy of failure modes in AI workflows:

```
1. Resource Constraints
   - Rate limiting (temporal)
   - Token limits (quantity)
   - Cost limits (financial)
   
2. Partial Success
   - Requested N, received M < N
   - Some branches fail, others succeed
   - Quality threshold not met
   
3. Semantic Failures  
   - Refusal (content policy)
   - Nonsense output
   - Wrong format/type
   
4. System Failures
   - Network timeout
   - Service unavailable  
   - Authentication errors
```

**Dr. Thompson**: That's helpful. Each category needs different handling. Resource constraints should be handled transparently by the runtime. System failures need retries. But semantic failures - those need user intervention.

**Dr. Conway**: I see it differently. We should classify by recoverability:
```
immediate-retry:    network glitch
delayed-retry:      rate limit  
transform-retry:    refusal → rephrase
partial-accept:     7 of 10 results
hard-fail:          invalid API key
```

**Dr. Iverson**: You're both thinking too imperatively. In array languages, we classify by shape:
- Shape-preserving failures: filter that removes some elements
- Shape-changing failures: operation that can't produce expected dimensions
- Shape-unknown failures: dynamic results we can't predict

**Supervisor**: These are all useful lenses, but how do we unify them? What does a user need to know when writing a workflow?

**Dr. Mac Lane**: The key insight is that we're dealing with *partial functions*. Not every input produces an output. The question is: how do we model partiality?

**Dr. Thompson**: In practical terms, users need to answer: "What should happen when things go wrong?" For each operation, they need escape hatches.

**Dr. Conway**: But that's too much cognitive load. The language should have sensible defaults. Only override when needed.

**Dr. Iverson**: Exactly. The default should be: collect what you can, report what failed, let the user decide if it's enough.

**Supervisor**: Let's make this concrete. Say I have this workflow:
```
analyze-market-research:
  collect-surveys(1000)
  -> filter-valid
  -> extract-insights  
  -> generate-report
```

What happens if we only get 600 valid surveys? Is that a failure?

**Dr. Mac Lane**: It depends on the semantics we assign. We could model this as:
```
collect-surveys : Int → IO (Array Survey)
-- Returns between 0 and n surveys

filter-valid : Array Survey → Array ValidSurvey  
-- Preserves ≤ input size

extract-insights : Array ValidSurvey → Maybe Insights
-- Fails if too few inputs

generate-report : Insights → Report
-- Total function
```

**Dr. Thompson**: So the failure point is `extract-insights` - it needs a minimum viable dataset. The user should specify that threshold:
```
extract-insights.requireMinimum(500)
```

**Dr. Conway**: In a stack language, we'd handle this with predicates:
```
collect-surveys
filter-valid
dup count 500 >= 
  [ extract-insights generate-report ]
  [ "Insufficient data" error ]
if-else
```

**Dr. Iverson**: APL would just flow the data through:
```
report ← generate-report extract-insights filter-valid collect-surveys 1000
⍝ extract-insights returns empty if insufficient data
⍝ generate-report handles empty case
```

**Supervisor**: I'm noticing we're conflating mechanism with policy. Mac Lane, can you separate the semantic categories from the implementation strategies?

**Dr. Mac Lane**: *erases and rewrites*

```
SEMANTIC CATEGORIES:

1. Total Operations
   - Always succeed given valid input
   - Example: string concatenation

2. Partial Operations  
   - May fail for valid input
   - Example: LLM with content filter

3. Constrained Operations
   - Require resources (time/money/tokens)
   - Example: API calls with rate limits

4. Nondeterministic Operations
   - Results vary between runs
   - Example: creative generation

ORTHOGONAL CONCERNS:

A. Cardinality
   - Expected vs actual count
   
B. Quality  
   - Success vs semantic correctness
   
C. Resources
   - Available vs consumed
   
D. Time
   - Sync vs async vs timeout
```

**Dr. Thompson**: That's clearer. So for each operation, we need to declare which categories it falls into?

**Dr. Conway**: That feels like Java's checked exceptions all over again. Too much ceremony.

**Dr. Iverson**: What if we infer it? Operations that touch external services are partial and constrained. Pure transformations are total.

**Dr. Mac Lane**: We could use effect types:
```
expand : Topic -[AI, Partial]-> Array Response
filter : (a -> Bool) -> Array a -[Pure]-> Array a  
```

**Supervisor**: This is good theoretical foundation, but what does this mean practically? If I'm a user writing a workflow, what do I need to think about?

**Dr. Thompson**: Three questions:
1. What's the minimum acceptable result?
2. How long am I willing to wait?
3. How much am I willing to spend?

**Dr. Conway**: And one more: What's my fallback plan?

**Dr. Iverson**: We're overcomplicating. Most users want: "Do the best you can with what you get."

**Dr. Mac Lane**: But "best" needs definition. Is 7 results of quality 8/10 better than 10 results of quality 6/10?

**Supervisor**: Maybe we need levels of sophistication. Default behavior for simple cases, fine control when needed?

**Dr. Thompson**: Yes! 
```
// Level 1: Just works
results = expand(10)  // Gets what it can

// Level 2: With requirements  
results = expand(10, minimum: 5)  // Fails if < 5

// Level 3: With full policy
results = expand(10, {
  minimum: 5,
  timeout: 30s,
  budget: $1.00,
  onPartial: (n) => n >= 7 ? proceed : retry
})
```

**Dr. Conway**: That's the right progression. Simple things simple, complex things possible.

**Dr. Iverson**: As long as the simple case is truly simple. No configuration files, no boilerplate.

**Dr. Mac Lane**: And the semantics must be compositional. If operation A can return 5-10 results and operation B needs at least 7, the composition should handle that mismatch cleanly.

**Supervisor**: Good. So we're agreeing on progressive sophistication with compositional semantics. What other architectural decisions do we need to make?

**Supervisor**: Actually, let me pause us there. I want to punt on the error handling for now and return to our core discussion. We need to make sure we can figure out clean semantics and syntax first, then layer in error handling afterwards. I know it's important, but let's table it to vet this idea and see what's possible in theory for the happy path scenario.

*The scientists nod and refocus*

**Dr. Conway**: Good call. Let's assume all operations succeed and focus on the core computational model.

**Dr. Mac Lane**: Yes, we can add partiality later. First, let's establish what these workflows *are* mathematically.

**Dr. Iverson**: Agreed. In the happy path, my array operations flow beautifully without special cases.

**Dr. Thompson**: That works for me. Let's design the ideal developer experience first.

**Supervisor**: So going back to basics - we all showed different syntax for the same workflow earlier. The core pattern seems to be: start with data, transform it through operations, possibly branch into multiple paths, then merge back together. Is that the fundamental model we're working with?

**Dr. Mac Lane**: Precisely. In categorical terms, we have:
- Objects: Information states (topics, conversations, results)
- Morphisms: Transformative operations
- Composition: Sequential application
- Products: Parallel/branching operations  
- Coproducts: Merging/reduction operations

**Dr. Conway**: And in stack terms, it's even simpler - data flows left to right through transformations. The branching is just duplicating the stack, the merging is combining results back onto one stack.

**Dr. Iverson**: For arrays, branching is natural - one item becomes many. Operations apply uniformly. Merging is reduction. It's the same pattern at every level.

**Dr. Thompson**: I see it as a dataflow graph. Nodes are operations, edges carry data. The syntax just serializes the graph.

**Supervisor**: So we're all describing the same computational model with different words. What are the core primitives we absolutely need?

**Dr. Conway**: Three things:
1. Sequential composition: `A B C`
2. Branching: `dup` or `fork`  
3. Merging: `join` or `reduce`

Everything else is library functions.

**Dr. Iverson**: I'd argue we only need two:
1. Function application (which includes composition)
2. Rank polymorphism (handles both branching and merging)

With these, `expand` is just creating an array, `reduce` is rank-0 application.

**Dr. Thompson**: From a practical standpoint:
1. Pipeline operator: `|>` or `|`
2. Parallel map: `map` or `each`
3. Reduce: `reduce` or `/`
4. Branch: `branch { ... }`

**Dr. Mac Lane**: Let me try to find the minimal categorical foundation:
1. Composition: `f >>> g`  
2. Product: `f &&& g` (run both, keep both results)
3. Coproduct: `f ||| g` (choose one path)
4. Functorial map: `map f`
5. Fold: `fold f`

**Supervisor**: These seem related but different. Can we unify them?

**Dr. Mac Lane**: *draws on board* They're all views of the same structure:

```
Sequential: A --f--> B --g--> C

Parallel:   A --f--> B
            |        |
            +--g--> C

Choice:     A --f--> B
              \    /
               -g->
```

**Dr. Conway**: Right, and in concatenative style, these are just:
- Sequential: `f g`
- Parallel: `dup f swap g swap`  
- Choice: `[f] [g] if`

**Dr. Iverson**: In array notation:
- Sequential: `g f`
- Parallel: `f,g`
- Choice: `f⍣condition⊢g`

**Dr. Thompson**: I think we're converging. The core operations are:
1. **Transform**: Apply function to data
2. **Branch**: One becomes many
3. **Merge**: Many become one
4. **Compose**: Chain operations

**Supervisor**: Good. Now, what makes our domain special? What's different about AI workflows versus general computation?

**Dr. Iverson**: The natural "array-ness" of it. You rarely want just one AI response - you want alternatives, perspectives, variations.

**Dr. Conway**: The expense. Each operation might cost real money, so the execution strategy matters.

**Dr. Thompson**: The non-determinism. Running the same workflow twice gives different results.

**Dr. Mac Lane**: The semantic richness. We're not just transforming bits, we're transforming meaning.

**Supervisor**: So our language should make these special properties first-class. How?

**Dr. Thompson**: Built-in operations for common patterns:
```
expand(n)     // Generate n variations
perspectives(n) // Get n viewpoints  
synthesize()   // Merge intelligently
```

**Dr. Conway**: But keep them compositional:
```
5 variations
'evaluate each
best pick
```

**Dr. Iverson**: Make array operations the default:
```
expand    ⍝ naturally produces arrays
evaluate  ⍝ naturally maps over arrays
/best     ⍝ naturally reduces arrays
```

**Dr. Mac Lane**: Ensure the semantics respect the domain:
```
expand : Topic -> Array<Topic>  -- not just any array, semantically related topics
synthesize : Array<Result> -> Result  -- not just concat, semantic merging
```

**Supervisor**: I'm hearing consensus that the language should:
1. Make branching/merging natural and concise
2. Treat collections as the default, not special cases
3. Provide domain-specific operations that compose well

What about the syntax itself? How do we make this readable?

**Dr. Thompson**: I advocate for familiarity:
```
topic
  |> generateVariations(5)
  |> map(analyze)
  |> pickBest()
```

**Dr. Conway**: I prefer minimal:
```
topic
5 variations
analyze each
pick-best
```

**Dr. Iverson**: Dense and powerful:
```
pick-best analyze¨ 5 variations topic
```

**Dr. Mac Lane**: Explicit about structure:
```
topic >>> variations(5) >>> map(analyze) >>> best
```

**Supervisor**: What if we support multiple syntaxes over the same semantic core? Like how math can be written in different notations?

**Dr. Conway**: That's exactly what Forth does with vocabularies. Same stack operations, different words for different domains.

**Dr. Iverson**: APL has similar ideas - you can define your own glyphs that expand to primitives.

**Dr. Thompson**: Many languages compile to a common IR. We could do the same.

**Dr. Mac Lane**: As long as the semantics are fixed, notation is flexible. The key is ensuring all notations have the same meaning.

**Supervisor**: So we're proposing:
1. A semantic core based on branching/transforming/merging
2. Multiple syntax styles that compile to this core
3. Domain-specific operations as first-class citizens
4. Collections/arrays as the natural data structure

What's still unclear to me is the execution model. When I write a workflow, what actually happens?

**Supervisor**: Actually, let's hold off on going into the language syntax specifics. Are we all agreed on the core? To clarify, this core should allow all of you to work within your paradigms. If so, let's document it in core-semantics.md

*The scientists look at each other and nod*

**Dr. Conway**: Yes, I can work with this core. Sequential composition, branching, and merging are the essence of concatenative programming.

**Dr. Iverson**: Absolutely. Arrays and rank polymorphism fit naturally into this model.

**Dr. Thompson**: The dataflow interpretation works perfectly for practical implementation.

**Dr. Mac Lane**: The categorical structure is sound. We have a proper foundation.

**Supervisor**: Excellent. Let's document this consensus.

*[Core semantics documented in core-semantics.md]*

**Supervisor**: Ok, let's drill down to the concrete so we can vet our ideas and understand them in the "real world". Let's come up with a non-trivial example. I like the YouTube creator scenario of thinking of a new video concept idea, seeing if it be a good video that will grow the channel, then figuring out the concept, scripting it, and then producing it. Let's elaborate on that workflow for that and expand it out as far as we can. This will help us test our hypotheses and compare how our syntaxes look when applied to real world scenarios.

Let's create scenario.md and see if we all agree upon the example.