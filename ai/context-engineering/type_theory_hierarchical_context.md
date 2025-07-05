# Type Theory for Hierarchical Context Engineering

*Building on our understanding of context as nested execution environments with lexical scoping, this document develops a comprehensive type system with formal typing rules that captures the hierarchical nature of context and ensures safe composition.*

## 1. Type System Foundations for Hierarchical Context

### 1.1 Base Types and Context Hierarchy

```haskell
-- Universe of context levels
data Level = Creator | Channel | Video | Task

-- Indexed context types
data Context :: Level -> Type where
  CreatorCtx :: CreatorData -> Context Creator
  ChannelCtx :: Context Creator -> ChannelData -> Context Channel
  VideoCtx   :: Context Channel -> VideoData -> Context Video
  TaskCtx    :: Context Video -> TaskData -> Context Task

-- Subtyping relation: lower levels can access higher levels
(≤) :: Level -> Level -> Bool
Creator ≤ _ = True
Channel ≤ Channel = True
Channel ≤ Video = True
Channel ≤ Task = True
Video ≤ Video = True
Video ≤ Task = True
Task ≤ Task = True
_ ≤ _ = False
```

### 1.2 Dependent Context Types

```haskell
-- Context depends on its parent
ContextWithParent : (l : Level) -> (parent : Context (Parent l)) -> Type

-- Example: Video context depends on specific channel
VideoInChannel : (ch : Context Channel) -> Type
VideoInChannel ch = {
  topic : String,
  style : StyleGuide,
  -- Constraint: style must be compatible with channel
  proof : Compatible ch.style style
}
```

## 2. Formal Typing Rules

### 2.1 Context Formation Rules

```
Γ ⊢ CreatorData type
─────────────────────────── (Creator-Form)
Γ ⊢ Context Creator type

Γ ⊢ c : Context Creator   Γ ⊢ ChannelData type
───────────────────────────────────────────────── (Channel-Form)
Γ ⊢ Context Channel type

Γ ⊢ ch : Context Channel   Γ ⊢ VideoData type
─────────────────────────────────────────────── (Video-Form)
Γ ⊢ Context Video type

Γ ⊢ v : Context Video   Γ ⊢ TaskData type
───────────────────────────────────────────── (Task-Form)
Γ ⊢ Context Task type
```

### 2.2 Context Construction Rules

```
Γ ⊢ data : CreatorData
────────────────────────────── (Creator-Intro)
Γ ⊢ CreatorCtx data : Context Creator

Γ ⊢ parent : Context Creator   Γ ⊢ data : ChannelData
──────────────────────────────────────────────────────── (Channel-Intro)
Γ ⊢ ChannelCtx parent data : Context Channel

Γ ⊢ parent : Context Channel   Γ ⊢ data : VideoData
────────────────────────────────────────────────────── (Video-Intro)
Γ ⊢ VideoCtx parent data : Context Video

Γ ⊢ parent : Context Video   Γ ⊢ data : TaskData
──────────────────────────────────────────────────── (Task-Intro)
Γ ⊢ TaskCtx parent data : Context Task
```

### 2.3 Context Access Rules (Lexical Scoping)

```
Γ ⊢ ctx : Context l   l' ≤ l
─────────────────────────────── (Context-Access)
Γ ⊢ access ctx l' : Context l'

Γ ⊢ ctx : Context Task
───────────────────────────────────────── (Task-Can-Access-All)
Γ ⊢ getCreator ctx : Context Creator
Γ ⊢ getChannel ctx : Context Channel
Γ ⊢ getVideo ctx : Context Video

Γ ⊢ ctx : Context l   field ∈ Fields(l)
──────────────────────────────────────── (Field-Access)
Γ ⊢ ctx.field : FieldType(l, field)
```

### 2.4 Context Closure Rules

```
Γ, x : Context l ⊢ e : τ
────────────────────────────────── (Context-Closure)
Γ ⊢ λctx. e : Context l → τ

Γ ⊢ f : Context l → τ   Γ ⊢ ctx : Context l
───────────────────────────────────────────── (Context-App)
Γ ⊢ f ctx : τ

Γ, parent : Context l ⊢ child : Context (Child l)
─────────────────────────────────────────────────── (Nested-Closure)
Γ ⊢ λparent. child : Context l → Context (Child l)
```

## 3. Linear and Graded Types for Context Resources

### 3.1 Linear Context for One-Time Use

```
────────────────────────── (Lin-Context-Form)
Γ ⊢ Context⊸ l type

Γ ⊢ ctx : Context⊸ l   Γ, x : Context l ⊢ e : τ
─────────────────────────────────────────────────── (Lin-Context-Elim)
Γ ⊢ use ctx as x in e : τ

Γ ⊢ ctx : Context l
────────────────────────── (Lin-Context-Intro)
Γ ⊢ linear ctx : Context⊸ l
```

### 3.2 Graded Context for Bounded Use

```
Γ ⊢ n : Nat
──────────────────────────── (Graded-Context-Form)
Γ ⊢ Context[n] l type

Γ ⊢ ctx : Context[n+1] l
───────────────────────────────────────── (Graded-Use)
Γ ⊢ useOnce ctx : (Context l, Context[n] l)

Γ ⊢ ctx : Context[0] l
───────────────────────── (Graded-Exhausted)
Γ ⊢ exhausted ctx : ⊥
```

## 4. Session Types for Hierarchical Context Protocols

### 4.1 Context Negotiation Protocol

```
ContextProtocol :: SessionType
ContextProtocol = 
  !Level.                    -- Send required level
  ?ContextAtLevel.           -- Receive context at that level
  μX. &{                     -- Choice
    request_parent: !Level. ?ContextAtLevel. X,
    request_child: !Level. !ContextData. ?ContextAtLevel. X,
    done: End
  }
```

### 4.2 Typing Rules for Context Sessions

```
Γ ⊢ c : Channel ContextProtocol
────────────────────────────────────── (Send-Level)
Γ ⊢ send c Creator : Channel (? Context Creator. ...)

Γ ⊢ c : Channel (?Context l. P)
──────────────────────────────────────── (Receive-Context)
Γ ⊢ receive c : (Context l, Channel P)

Γ ⊢ c : Channel (&{lᵢ : Pᵢ})   j ∈ {i}
──────────────────────────────────────── (Context-Choice)
Γ ⊢ select c lⱼ : Channel Pⱼ
```

## 5. Effect Types for Context Operations

### 5.1 Context Effects

```haskell
data ContextEff :: Level -> Effect where
  ReadCtx  :: ContextEff l (Context l)
  WriteCtx :: Context l -> ContextEff l ()
  -- Can only read from parents
  ReadParent :: (l' ≤ l) => Level l' -> ContextEff l (Context l')
```

### 5.2 Effect Typing Rules

```
Γ ⊢ e : Eff [ContextEff l] τ
──────────────────────────────────── (Context-Effect)
Γ ⊢ runContext e : Context l → τ

l' ≤ l
─────────────────────────────────────────────── (Read-Parent-Effect)
Γ ⊢ readParent l' : Eff [ContextEff l] (Context l')

Γ ⊢ ctx : Context l
──────────────────────────────────────────── (Write-Context-Effect)
Γ ⊢ writeContext ctx : Eff [ContextEff l] ()
```

## 6. Path-Dependent Types for Context

### 6.1 Context Paths

```scala
// Path-dependent types for context access
trait ContextPath {
  type Level
  type Data
  val parent: Option[ContextPath]
  val data: Data
}

// Refined types based on path
type CreatorPath = ContextPath { type Level = Creator.type }
type ChannelPath = ContextPath { 
  type Level = Channel.type
  val parent: Some[CreatorPath] 
}
```

### 6.2 Path Typing Rules

```
Γ ⊢ p : Path(l)   Γ ⊢ fieldName : String
────────────────────────────────────────── (Path-Select)
Γ ⊢ p.fieldName : p.Data.fieldName.type

Γ ⊢ p : Path(l)   l' ≤ l
─────────────────────────── (Path-Parent)
Γ ⊢ p.parent : Path(l')
```

## 7. Polymorphic Context Operations

### 7.1 Level-Polymorphic Functions

```haskell
-- Functions generic over context level
map : ∀ (l : Level). (Context l -> Context l) -> Context l -> Context l

-- Level-preserving transformations
transform : ∀ (l : Level) (a : Type). 
            (∀ (l' : Level). l' ≤ l => Context l' -> a) -> 
            Context l -> a
```

### 7.2 Polymorphic Typing Rules

```
Γ, l : Level ⊢ e : τ
──────────────────────── (Level-Gen)
Γ ⊢ Λl. e : ∀l. τ

Γ ⊢ e : ∀l. τ   Γ ⊢ l' : Level
────────────────────────────── (Level-App)
Γ ⊢ e[l'] : τ[l'/l]
```

## 8. Recursive Context Types

### 8.1 Recursive Context Definitions

```haskell
-- Recursive context for series/playlists
data RecursiveContext :: Level -> Type where
  Base : Context l -> RecursiveContext l
  Cons : Context l -> RecursiveContext l -> RecursiveContext l

-- Induction principle
recursiveInduction : ∀ (P : RecursiveContext l -> Type).
                    (∀ ctx. P (Base ctx)) ->
                    (∀ ctx rec. P rec -> P (Cons ctx rec)) ->
                    ∀ rec. P rec
```

### 8.2 Recursive Typing Rules

```
Γ ⊢ ctx : Context l
─────────────────────────────────── (Rec-Base)
Γ ⊢ Base ctx : RecursiveContext l

Γ ⊢ ctx : Context l   Γ ⊢ rec : RecursiveContext l
──────────────────────────────────────────────────── (Rec-Cons)
Γ ⊢ Cons ctx rec : RecursiveContext l

Γ ⊢ rec : RecursiveContext l   
Γ, ctx : Context l ⊢ base : τ
Γ, ctx : Context l, rec' : RecursiveContext l, ih : τ ⊢ step : τ
────────────────────────────────────────────────────────────────── (Rec-Elim)
Γ ⊢ recurse base step rec : τ
```

## 9. Intersection and Union Types for Mixed Contexts

### 9.1 Context Intersections

```
Γ ⊢ ctx : Context l₁   Γ ⊢ ctx : Context l₂
──────────────────────────────────────────── (Context-Inter)
Γ ⊢ ctx : Context l₁ ∩ Context l₂

Γ ⊢ ctx : Context l₁ ∩ Context l₂
────────────────────────────────── (Inter-Elim-L)
Γ ⊢ ctx : Context l₁

Γ ⊢ ctx : Context l₁ ∩ Context l₂
────────────────────────────────── (Inter-Elim-R)
Γ ⊢ ctx : Context l₂
```

### 9.2 Context Unions

```
Γ ⊢ ctx : Context l₁
──────────────────────────────────── (Union-Intro-L)
Γ ⊢ inl ctx : Context l₁ ∪ Context l₂

Γ ⊢ ctx : Context l₂
──────────────────────────────────── (Union-Intro-R)
Γ ⊢ inr ctx : Context l₁ ∪ Context l₂

Γ ⊢ ctx : Context l₁ ∪ Context l₂
Γ, x₁ : Context l₁ ⊢ e₁ : τ
Γ, x₂ : Context l₂ ⊢ e₂ : τ
────────────────────────────────────────── (Union-Elim)
Γ ⊢ case ctx of {inl x₁ => e₁; inr x₂ => e₂} : τ
```

## 10. Substructural Rules for Context

### 10.1 Weakening (Adding Unused Context)

```
Γ ⊢ e : τ
───────────────────────── (Context-Weakening)
Γ, x : Context l ⊢ e : τ
```

### 10.2 Exchange (Reordering Context)

```
Γ, x : Context l₁, y : Context l₂, Δ ⊢ e : τ
──────────────────────────────────────────────── (Context-Exchange)
Γ, y : Context l₂, x : Context l₁, Δ ⊢ e : τ
```

### 10.3 Contraction (Duplicating Context)

```
Γ, x : Context l, y : Context l ⊢ e : τ   x ≡ y
─────────────────────────────────────────────────── (Context-Contraction)
Γ, x : Context l ⊢ e[x/y] : τ
```

## 11. Operational Semantics with Types

### 11.1 Type-Preserving Reduction

```
         ctx ↦ ctx'
───────────────────────────── (Context-Step)
E[ctx] ↦ E[ctx'] : Context l

(λx:Context l. e) ctx ↦ e[ctx/x] : τ
─────────────────────────────────────── (Beta-Context)
```

### 11.2 Type Safety Theorems

**Progress**: If `⊢ e : τ` then either `e` is a value or `∃e'. e ↦ e'`

**Preservation**: If `⊢ e : τ` and `e ↦ e'` then `⊢ e' : τ`

**Context Safety**: If `⊢ ctx : Context l` then `size(ctx) ≤ WINDOW_LIMIT`

## 12. Dependent Types for Context Constraints

### 12.1 Size-Indexed Contexts

```haskell
-- Context with size in the type
data SizedContext : Nat -> Level -> Type where
  MkSized : (ctx : Context l) -> 
            (prf : size ctx = n) -> 
            SizedContext n l

-- Window-safe contexts
WindowSafe : Level -> Type
WindowSafe l = Σ(n : Nat). (n ≤ WINDOW_LIMIT) × SizedContext n l
```

### 12.2 Constraint Typing Rules

```
Γ ⊢ ctx : Context l   Γ ⊢ size ctx = n : Type
───────────────────────────────────────────────── (Sized-Intro)
Γ ⊢ MkSized ctx refl : SizedContext n l

Γ ⊢ n ≤ WINDOW_LIMIT   Γ ⊢ ctx : SizedContext n l
──────────────────────────────────────────────────── (Window-Safe)
Γ ⊢ (n, proof, ctx) : WindowSafe l
```

## 13. Practical Type System Design

### 13.1 Gradual Typing for Context

```typescript
// Mix static and dynamic typing
type Context<L extends Level> = 
  | StaticContext<L>      // Fully typed
  | DynamicContext        // Runtime checked
  | GradualContext<L>     // Partially typed

// Gradual typing rules allow mixing
function processContext<L extends Level>(
  ctx: Context<L> | DynamicContext
): Result
```

### 13.2 Type Inference for Context

```haskell
-- Bidirectional type checking
infer : Expr -> Maybe (Context l, Type)
check : Expr -> Type -> Maybe (Context l)

-- Inference rules
inferContext (Access path) = 
  do l <- inferLevel path
     return (Context l, ContextType l)

checkContext expr (Context l) =
  do (ctx, ty) <- infer expr
     unify ty (ContextType l)
     return ctx
```

## 14. Advanced Type Features

### 14.1 Higher-Kinded Types for Context Transformers

```haskell
-- Context transformers
type ContextT :: (Level -> Type) -> Level -> Type
type ContextT m l = Context l -> m (Context l)

-- Monad transformer for context
instance MonadTrans (ContextT m) where
  lift :: m a -> ContextT m l a
  lift ma = \ctx -> fmap (const ctx) ma
```

### 14.2 Row Types for Extensible Context

```purescript
-- Extensible context records
type BaseContext r = {
  creator :: CreatorData,
  channel :: ChannelData
  | r
}

type VideoContext = BaseContext (video :: VideoData)
type TaskContext = BaseContext (video :: VideoData, task :: TaskData)

-- Row polymorphism
withExtraData :: forall r. BaseContext r -> BaseContext (extra :: Data | r)
```

## Conclusion

This type system for hierarchical context engineering provides:

1. **Formal typing rules** that enforce context hierarchy
2. **Lexical scoping** captured through access rules
3. **Resource management** via linear and graded types
4. **Protocol specification** through session types
5. **Effect tracking** for context operations
6. **Path-dependent types** for precise context access
7. **Safety guarantees** including window limits
8. **Practical features** like gradual typing and inference

The type system ensures that:
- Context access respects hierarchical relationships
- Resources are managed correctly
- Protocols are followed
- Effects are tracked
- Window limits are never exceeded

This foundation enables building robust, type-safe context engineering systems with formal guarantees about their behavior.