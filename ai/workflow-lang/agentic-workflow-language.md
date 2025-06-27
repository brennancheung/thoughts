# Agentic Workflow Language

## The Problem

Current AI interfaces trap us in linear conversations. You ask a question, get an answer, ask another question. But real intellectual work doesn't happen linearly—you explore multiple paths, compare alternatives, combine insights, and build understanding through iteration.

Imagine trying to:
- Explore 10 variations of an idea and pick the best 3
- Get multiple perspectives on a topic and find the consensus
- Branch a conversation into parallel explorations then merge the insights
- Transform outputs through multiple stages of refinement

With chat interfaces, this requires endless copy-pasting, manual tracking, and mental juggling. The interface fights against how we actually think.

## The Solution: A Language for AI Orchestration

This language treats AI conversations as data that can be transformed through composable operations. Just like how programming languages let us manipulate data structures, this language lets us manipulate conversations and AI outputs.

Core insight: Every AI interaction produces data that can flow into other operations. By making these flows explicit and composable, we unlock entirely new ways of working with AI.

## The Natural Flow of AI Workflows

When working with AI, you rarely want just one answer. Real intellectual work involves exploring possibilities, comparing alternatives, and synthesizing insights. This language embraces that reality.

### Branching and Merging

Think about how you actually solve problems with AI:
- You start with a question or idea
- You explore multiple angles or approaches
- You compare and evaluate the results
- You synthesize the best insights into a conclusion

This is a branching and merging pattern—and it's the core of this language:

```
"How should I approach this problem?"
    | expand 5           // Branch: explore 5 different approaches
    | evaluate-pros-cons // Process: analyze each approach
    | compare-feasibility // Process: check practical constraints
    / synthesize         // Merge: combine insights into recommendation
```

### The Universal Pattern

This branching and merging appears everywhere:
- **In your workflow**: Research branches into sources, then merges into conclusions
- **In programming**: Operations that map over collections, then reduce to results
- **In thinking**: Divergent exploration followed by convergent synthesis

We call these operations:
- **Expand**: Take one conversation and branch into many (brainstorm, explore, generate)
- **Reduce**: Take many results and merge into one (synthesize, decide, conclude)

### The Same Operations, Different Containers

The power of this language is that the same operations work whether you're dealing with arrays, streams, or parallel AI conversations. They're different containers, but they follow the same pattern:

```
// Different containers, same operations:
"idea" | expand 10      → [idea₁, idea₂, ...]     // Array of ideas
"idea" | expand 10      → stream<ideas>           // Stream of ideas  
"idea" | expand 10      → parallel AI chats       // Multiple conversations

// The operations work on any container:
container | filter "innovative"    // Works on arrays, streams, or chats
container | map improve           // Transform each element
container / pick-best             // Reduce to single result
```

Think of it like this: whether you have a list of items, a stream of events, or multiple AI agents working in parallel, you can:
- **Map**: Transform each element
- **Filter**: Keep only what matches
- **Reduce**: Combine into a single result

The container changes, but the operations remain consistent. This is why you can write `expand 10 | evaluate / pick-best` and it works whether those are 10 items in memory or 10 AI agents running in parallel.

This uniformity is powerful: you express what you want to do, not how to manage the underlying complexity. The language handles whether to spawn parallel processes, manage streams, or coordinate AI agents—you just describe the transformation you need.

### Practical Examples

```
// Expand one topic into many subtopics, then pick the best
"startup idea" 
    | expand 10        // One → Many: Generate 10 variations
    | evaluate         // Many → Many: Score each one
    / pick-best        // Many → One: Select the winner

// Or more concisely:
"startup idea" expand 10 | evaluate / pick-best
```

The `/` operator is our universal reducer—it takes many things and collapses them into one, whether that's picking the best, finding consensus, or merging insights.

### Why This Matters

This pattern is powerful because it matches how we actually think:
1. **Diverge**: Explore multiple possibilities
2. **Transform**: Work with each possibility
3. **Converge**: Synthesize back to actionable insights

By making this pattern explicit and easy to express, complex workflows become natural to write and understand.

### For Programmers and Mathematicians

If you're coming from a programming background:
- `expand` = `flatMap` or list comprehension
- `/` = `reduce` or `fold`
- `|` = `map` or pipeline operator
- The whole system = functional reactive streams

If you're coming from mathematics:
- `expand` = anamorphism (unfold)
- `/` = catamorphism (fold)
- Full pipeline = hylomorphism
- Operations = morphisms between categories

But you don't need to know any of this—the operations are intuitive because they match how we naturally think about problems.

## Language Primitives

### Core Data Types

- **Chat**: A single conversation thread
- **ChatArray**: Multiple parallel conversations
- **Topic**: A semantic starting point
- **Result**: Output of any operation

### Expand Operations (One → Many)

- `expand N` - Generate N variations (general purpose)
- `variations N` - Generate N variations (alias)
- `perspectives N` - Get N different viewpoints  
- `sub-topics N` - Create N subtopics
- `examples N` - Generate N examples
- `brainstorm N` - Generate N ideas
- `alternatives N` - Create N alternatives

### Transform Operations (Array → Array)

- `map [operation]` - Apply operation to each element
- `filter [criteria]` - Keep only matching elements
- `rank [criteria]` - Order by criteria
- `select N` - Take first N elements

### Reduce Operations (Many → One)

- `/ pick-best` - Select highest quality
- `/ consensus` - Find common ground
- `/ merge` - Combine coherently
- `/ synthesize` - Create unified output

### Transform Operations (One → One)

- `deep-dive` - Elaborate extensively
- `summarize` - Condense to key points
- `critique` - Analyze critically
- `refine` - Polish and improve

## Syntax

### Basic Pipeline
```
input | operation1 | operation2 | operation3
```

### Expand and Reduce
```
input operation N / reduction
```

### Complex Example
```
"AI safety" 
    sub-topics 5 
    | perspectives 3
    | filter "contrarian"
    / consensus
    | critique
    | refine
```

This generates 5 subtopics, gets 3 perspectives on each, filters for contrarian views, finds consensus among them, critiques the result, and refines it.

## Dynamic Interaction

The language supports dynamic exploration where you can reference and manipulate specific conversations:

```
// Reference specific outputs
{{#3}} {{#7}} merge summarize

// Branch from existing conversation
{{#3}} branch {
    technical: deep-dive | examples 3
    practical: applications 5 | rank "feasibility"
} compare
```

## Real-World Workflows

### Research Analysis
```
paper-topic
    research-questions 10
    | literature-search
    | group "methodology"
    / identify-gaps
    | propose-studies 3
    | feasibility-check
    / rank "impact"
```

### Creative Development  
```
story-premise
    plot-variations 7
    | develop-characters
    | conflict-analysis
    / strongest-narrative
    | scene-breakdown 10
    | parallel-drafts 3
    / reader-test
```

### Problem Solving
```
problem-statement
    root-causes 5
    | solution-space 4
    | evaluate-tradeoffs
    / optimal-approach
    | implementation-plan
    | risk-assessment
    | mitigation-strategies
```

## Implementation Modes

### 1. Direct Syntax
Write operations directly using the language:
```
topic variations 10 | evaluate / pick-best 3
```

### 2. Natural Language
AI interprets intent and generates operations:
```
"Give me 10 ways to approach this problem and help me pick the best 3"
→ topic variations 10 | evaluate / pick-best 3
```

### 3. Interactive
Build workflows step-by-step with visible results at each stage.

## Why This Matters

1. **Cognitive Alignment**: Works how we think—exploring multiple paths, comparing options, building understanding iteratively

2. **Composability**: Small operations combine into powerful workflows

3. **Visibility**: See your entire thought process, not just the final result

4. **Reusability**: Save and replay successful workflow patterns

5. **Scale**: Handle complexity that would overwhelm linear chat

## Advanced Patterns

### Tree Search
```
hypothesis
    experiments 5
    | predict-outcomes
    | run-simulations
    / most-promising 2
    | variations 3
    | deep-analysis
    / optimal-path
```

### Ensemble Intelligence
```
question agents ["analyst", "creative", "critic"] 
    | deliberate
    / synthesize
```

### Iterative Refinement
```
draft iterate 5 [
    critique | address-issues | improve
]
```

## Getting Started

Start simple:
```
"your topic" variations 5 / pick-best
```

Then build complexity:
```
"your topic" 
    variations 5 
    | evaluate
    | rank "novelty"
    | select 3
    | deep-dive
    / synthesize
```

The language grows with your needs, from simple explorations to complex multi-agent orchestrations.

## Full Example: YouTube Creator Workflow

Let's walk through a complete real-world example of creating a YouTube video using the agentic workflow language.

### Defining Custom Operations

First, we define reusable operations with their prompts:

```
let trending-analysis niche =
    "Analyze current trends in {niche} on YouTube. Focus on:
    - Videos getting high engagement in the last 7 days
    - Emerging topics not yet saturated
    - Viewer pain points in comments
    Return a structured analysis with specific video opportunities."

let title-optimizer topic =
    "Create a compelling YouTube title for: {topic}
    Requirements:
    - Under 60 characters
    - Include emotional hook
    - Clear value proposition
    - SEO-friendly keywords
    Consider successful patterns in this niche."

let hook-generator topic =
    "Write a 15-second hook for a video about: {topic}
    Requirements:
    - Create curiosity gap
    - Promise clear value
    - Address viewer's problem
    - Set up the payoff"

let script-section section topic previous_sections =
    "Write the {section} section for a video about: {topic}
    Context: {previous_sections}
    Requirements:
    - Conversational tone
    - One key idea per paragraph
    - Include examples
    - Transition smoothly from previous section"
```

### Main Workflow Definition

```
let youtube-video-creation niche video-style target-duration =
    // Phase 1: Ideation and Research
    let potential-topics = 
        niche 
        | trending-analysis
        | extract-opportunities 10
        | branch {
            audience-fit: score-alignment target-audience
            competition: analyze-saturation
            effort: estimate-production-complexity
        }
        / weighted-score [0.5, 0.3, 0.2]
        | select 5
    
    // Phase 2: Concept Development
    let refined-concepts = 
        potential-topics
        | for-each [
            angles 3
            | unique-value-props
            | test-hooks 5
            / best-performing
        ]
        | rank "viral-potential"
        | select 3
    
    // Phase 3: Title and Thumbnail Ideation
    let winning-concept = 
        refined-concepts
        | parallel {
            titles: title-optimizer expand 10 | a-b-test-predictor
            thumbnails: thumbnail-concepts 5 | visual-impact-score
        }
        / best-combination
    
    // Phase 4: Script Creation
    let full-script = 
        winning-concept
        | outline-structure target-duration
        | expand-sections [
            "hook" hook-generator expand 5 / most-engaging
            "intro" script-section
            "problem" script-section | pain-amplifier
            "solution" script-section | value-stacker
            "examples" generate-examples 3 | relevance-filter
            "conclusion" script-section | cta-optimizer
        ]
        | flow-check
        | reading-time-adjust target-duration
    
    // Phase 5: Production Planning
    let production-plan = 
        full-script
        | parallel {
            visuals: extract-visual-cues | shot-list-generator
            graphics: identify-data-points | infographic-concepts
            b-roll: suggest-footage-needs | stock-footage-finder
        }
    
    // Phase 6: Optimization and Variations
    let content-variations = 
        full-script production-plan
        | branch {
            short-form: extract-clips 5 | format-for-shorts
            long-form: expand-deep-dives 3
            series: split-into-series 3
        }
    
    {
        main-video: full-script,
        production: production-plan,
        variations: content-variations,
        metadata: {
            title: winning-concept.title,
            thumbnail: winning-concept.thumbnail,
            tags: extract-tags full-script,
            description: generate-description full-script
        }
    }
```

### Executing the Workflow

```
// Simple execution
youtube-video-creation "Tech Tutorial" "programming" "10min"

// With named parameters
youtube-video-creation 
    ~niche:"AI productivity tools"
    ~video-style:"hands-on demo" 
    ~target-duration:"8-12 minutes"
    ~target-audience:"busy professionals"

// Or apply it in a pipeline
"Tech Tutorial" | youtube-video-creation "programming" "10min"
```

### Sub-workflow Example: A/B Test Predictor

```
let a-b-test-predictor title-variations =
    title-variations
    | parallel {
        emotion: emotional-impact-score
        curiosity: curiosity-gap-score
        keyword: seo-strength
        length: optimal-length-check
    }
    | normalize-scores
    / weighted-average [0.3, 0.4, 0.2, 0.1]
    | rank-results
    | annotate-strengths
```

### Dynamic Exploration During Execution

During execution, you can interactively explore branches:

```
// After Phase 1 completes, explore specific topics
potential-topics[3] 
    | deep-dive 
    | controversy-check
    | branch {
        safe: family-friendly-angle
        edgy: controversial-takes 3 / most-balanced
    }

// Grab multiple concepts and merge insights
refined-concepts[1] refined-concepts[3] 
    | compare-approaches
    | extract-best-elements
    | synthesize-hybrid
```

### Conditional Flows

```
full-script
    | complexity-check
    | if "too-complex" then
        simplify-language | add-analogies 3
      else if "too-simple" then
        add-depth | include-advanced-tips
      else
        continue
```

### Iterative Refinement Loop

```
full-script
    | iterate 3 [
        viewer-simulator
        | identify-confusion-points
        | clarify-sections
        | flow-check
        | if "ready" then break
    ]
```

### Parallel Agent Execution

```
winning-concept
    | agents [
        "scriptwriter": write-script ~style:"educational",
        "comedian": add-humor-elements,
        "researcher": fact-check | add-citations,
        "editor": improve-flow | tighten-language
    ]
    / merge-contributions
    | final-polish
```

### Usage Examples

1. **Quick video idea generation:**
```
"Python tutorials" | trending-analysis | extract-opportunities 5 / pick-best
```

2. **Full production pipeline:**
```
youtube-video-creation "AI Tools" "review" "15min"
    | production-scheduler
    | resource-allocator
```

3. **Series planning:**
```
"Machine Learning" 
    | curriculum-outline 10
    | for-each (youtube-video-creation "educational" "tutorial" "20min")
    | sequence-optimizer
    | release-calendar
```

## Workflow Definition Syntax

### Basic Definition
```
let workflow-name param1 param2 =
    // workflow steps
    result
```

### Operation Definition
```
let operation-name param1 param2 =
    "prompt template with {param1} and {param2}"

// With optional parameters
let operation-name param1 param2 ~temperature:0.7 ~model:"gpt-4" =
    "prompt template"
```

### Conditional Logic
```
if condition then
    operation
else if condition then
    operation
else
    operation
```

### Loops
```
iterate N [operations]
while condition [operations]
for-each [operations]
```

### Pattern Matching
```
match value {
    pattern1: operation1
    pattern2: operation2
    default: operation3
}
```

## Future Directions

- **Type System**: Ensure operations match data types
- **Visual Interface**: See workflows as interactive graphs
- **Debugging Tools**: Step through workflows with breakpoints
- **Performance Optimization**: Parallel execution strategies
- **Workflow Marketplace**: Share and reuse community workflows

This language transforms AI from a chat partner into a computational medium for thought—where complex intellectual work becomes as natural as writing code.