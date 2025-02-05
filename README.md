# hypr-tmpl
> A powerful, tiny, fast, &amp; secure templating engine written specifically for the Hypermedian Stack

## Table of Contents

## What does it look like?  Show me some examples, please!
> OK, let's learn with examples. Building up from nothing until we can use hypr-tmpl like a... web developer, that sounds good, right?  Alright!  Let's go!

### Simplest examples 

#### A Comment expression:
```handlebars
; everything after the first semicolon (;) is a comment
```
> ^ all comments get stripped from templates in transpilation, btw.

The generall convention is: `; use 1 semicolon if you're putting it on the same line as some code`,
`;; use 2 semicolons if they're the first non-whitespace characters on the line`, &
if the comment is at the Top or Bottom of the file:
`;;; use 3 semicolons`
`EOF`
...
`j/k`.  Much you still have to learn, young Skywalker.  

### List of types hypr-tmpl can check for: 
```handlebars
; types are easily remembered by this mnemonic: "A No Stub Function"... unless you can think of something better.
; TYPES: [A|N|O|S|T|U|B|F]
A 'array'
N 'number'
O 'object' (but not null, JS/TS folks!)
S 'string'
T 'type (inferred from context)
U 'unknown' (default: type must be checked & type-narrowed in the final compilation)
B 'boolean'
F 'function'
;...
as:A[S] 'array of strings'
at:A[T] 'array of TYPE'
au:A[U] 'array of unknown_things, potentially heterogeneous'
ab:A[B] 'array of booleans'
an:A[N] 'array of numbers'
;...
O{a:N,b:S,c:B} 'object with properties `a: number`, `b: string`, `c: boolean`
;...
A[O{s:S}] 'array of objects with property `s: string`
O{array:A[N],length:N}
; ... I think you get it.
```
> ^ you can read the `:` symbol as `"gets"`.
> So, a variable can get a TYPE (`an:A[N]`)... read:`"'an' gets an array of numbers."`,
> but it can also get a Value (`an:[1 2 3]`)... read`"'an' gets the array 1 2 3."`.
> In fact, it can get a TYPE, & a Value (`an:A[N]:[1 2 3]`)... read`"'an' gets an array of numbers, and that array gets 1 2 3"`. 

### Simple Expressions

#### A Conditional expression: 
```handlebars
; conditional statements are designated by a countdown:
{{3 (check_3}}; 3 means you want 3 sequential conditional checks, with a default (0)
  <span>3 is true</span>
{{2 (check_2)}}
  <span>2 is true, not 3</span>
{{1 (check_1)}}
  <span>1 is true, not 2, not 3</span>
{{0}}; otherwise...
  <span>neither 3, nor 2, nor 1 is true... here's the default, i guess...</span>
{{/3}}; <- number here must match the starting countdown number (`3`), or the template will not transpile (build/compile/run)
```

#### A Loop expression: 
```handlebars
; loop statements start with a 0, then a Direction to iterate, then a Maximum value (could be manual or derived from an array)
<ol>
{{0->3 [name A[N] value n unroll?=false]}}
  <li>{(n :(_ + 1) :)}) {value}</li>
{{/3}}; <- number here must match the upper bound (exclusive)
</ol>
```

#### A Reverse Loop expression:
```handlebars
; loop statements start with a 0, then a Direction to iterate, then a Maximum value (could be manual or derived from an array)
<ol>
{{0<-3 [name A[N] value n unroll=true]}}
  <li>{(n :(_ + 1) :)}) {value}</li>
{{/3}}; <- number here still must match the upper bound (exclusive)
</ol>
```

Congrats, script kiddie, you've earned a badge!

### Level 2

> ok, I fibbed a lil bit above: you need explicit Access Scopes `?` to pass data into expressions,
> & you need explicit Inject Scopes `.` to inject data into the interpolated HTML, to follow
> `The Principle of Least Privilege`, so, we have to be, again, VERY explicit in what we allow in,
> & what we allow injected, like so:

#### The Access Scope expression: 
```handlebars
;; assume there's an array up above of length 100
;; as well as a `should_unroll?:false`
{{? [array_from_above:A[N:100] should_unroll?:B]}}
  <ol>
  {{0->{N} [array_from_above :T:a val n should_unroll?]}}; <- now we're official, passing in the 2 variables from above.
  ;;                              ^   ^ `val` and `n` depend on the current iteration, they're just labels.  It's just how loops work.
    <li>You shall not inject any values into this HTML.</li>
  ;; ^ injecting values requires explicitly stating what may be injected, and it must be available in scope, or the template won't compile!
  {{/{N}}}; <- notice we can get fancy here, though, b/c it's within 'code section' (`{{_}}`)!
  ;;  ^ This is `access`ing `N`, not `inject`ing it: `inject` is only for within HTML sections...
  </ol>
{{/?}}
```

> So how do we actually get the values in there, then?  How do we `inject` either `val` or `n`?

#### The Inject Scope expression:
```handlebars
; assume there's an array up above of length 100
; as well as a `should_unroll?:false`
{{? [array_from_above:A[N:100] should_unroll?:B]}}
  <ol>
  {{0->{N} [array_from_above :T:a val n should_unroll?]}}; <- now we're official, passing in the 2 variables from above.
    {{. [val n]}}
    <li>success!  #{n} = {val}</li>
    {{/.}}
  {{/{N}}}
  </ol>
{{/?}}
```

> Wow, good job... don't let it get to your head, though: how was the Access Scope (`?`) able to access anything in the first place?
> Answer: we pretended.  Like children.
> ...Nooooo, no no.  Not over yet.  We have to have values passed into the template, but since this _**IS**_ our first rodeo, we have to pretend again.

### Level 3
> The key is to have a `require` code section:

#### The Require expression: 
```handlebars
{{require: [[a:A[S] b:N c:B]]}}; <- we demand that once we're transpiled, it gets the array of strings `a`, a number `b`, & a boolean `c`
  {{? [a b c]}}; <- we're accessing all 3 of them
    <ol>
    {{0->{b} [a :T:a char n c]}}
      {{. [char n]}}; <- notice how we're only using `b` for Loop length: it can't be injected, but we do use it by accessing it.
      <li>{char} = a[{n}]</li>
      {{/.}}
    {{/{b}}}
    </ol>
  {{/?}}
{{/require}}
```
The Require section tends to be on the outermost scope of a template file 100% of the time.  The rest of the time, well...

> Hey!  Want to hear some bad news?  We haven't told the template to transpile using any particular method, so it actually won't produce HTML in the end, lol!

### Transpilation
There are 3 stages in which a template can be transpiled into a REAL template/partial, to which we can pass data & so render into the final HTML string
1. Build-time
2. Compile-time
3. Runtime

Caveat: there can be only one of these in a given template, they are mutually exclusive.  Also, the filename.`PENULTIMATE`.extension must match the directive...
- A `build` directive must be in a file NAME.`build`.hypr-tmpl (or, if you're on Windows: NAME.`build`.hyp), and can have no `compile` or `run` directives.
- A `compile` directive must be in a file NAME.`compile`.hypr-tmpl (or .hyp...), and can have no `build` or `run` directies.
- A `run` directive must be in a file NAME.`run`.hypr-tmpl (or .hyp...), and can have no `build` or `compile` directives.

#### The Build expression: 
Build-time happens once, only once: either when the dev server starts up, or when you explicitly run the build script for production/testing.  
This stage is for things that fit correspondingly, like blogs, Static Site Generation, Partials to be re-used in other Templates, etc.  Use your creativity.

Here's how the previous example looks with a build:
```handlebars
{{require: [[a:A[S] b:N c:B]]}}
  ;; feels like maybe something could go here...
  {{build: [[a b c]]}}; <- ?! is this one redundant?
    ;; or maybe something could go here...
    {{? [a b c]}}; <- ?! or is this one redundant?
      ;; ... something seems wrong ...
      <ol>
      {{0->{b} [a :T:a char n c]}}
        {{. [char n]}}
        <li>{char} = a[{n}]</li>
        {{/.}}
      {{/{b}}}
      </ol>
    {{/?}}
  {{/build}}
{{/require}}
```
> ^ did you think that template was kinda weird?  Good, it was.  I'm going to jump forward a few steps without much explanation,
> but hopefully you won't drown.

These new  are called `Macro` expressions, but the one we're going to use, `let`, technically is a `"special_form"`, 'cause it's built in, see?  But you can still call it a `Macro` if you want.

```handlebars
;;; weird-list.build.hypr-tmpl

{{require: [[a:A[S] b:N c:B]]}}
  (let first_char := (a |> first)); <- this can be read: "let first_char be defined as `a` piped through `first` (another `"macro"`);
  (let MAX := b); <- pretty simple, amirite?
  (let unroll? := c); <- FYI, b/c this is a build template, this won't matter that much, whether or not the loop is unrolled...
  ;; N.B., we've only renamed b->MAX and c->unroll?, but we've TRANSFORMED a INTO first_char: were going to end up using both `a` and `first_char`
  ;; for a total of 4 variables (see below):
  {{build: [[a first_char MAX unroll?]]}}; <- usually the code below  could access these variables from the directive, but the <ol> below 'blocks' access...
    <ol>
    {{? [a first_char MAX unroll?]}}; <- if it weren't for the <ol> on the line above, we could've just accessed the variables from the `build` directive!
    ;;^ generally we want to push "access" exprs (`?`) or "inject" exprs (`.`) as far DOWN the template as we possibly can,
    ;;  reducing the chance we / Hannah Hacker / whoever it is that's trying to attack our site has a harder time, etc.
      {{0->{MAX} [a :T:a char n c]}}
        {{1 (is :char: :(_ === first_char))}}; <- LOOK!  Another Macro!  This one has a special subexpression `:(_ === first_char)` called a `"micro"`...
          {{. [first_char]}}; <- same as `access` advice above, push as far DOWN as possible
            <li>{(first_char | captialize :)}</li>
          {{/.}}
        {{0}}
          {{. [char]}}; <- same, push as far DOWN as possible
            <li>{char}</li>
          {{/.}}
        {{/1}}
      {{/{MAX}}}
    {{/?}}
    </ol>
  {{/build}}
{{/require}}
```

Whew.  That's a lot.  Why don't we reuse this template mostly and only change the directive for the next 2 examples, so we can focus on imagining what their difference actually _IS_, and _WHY_ we'd want to use each.

#### The Compile expression: 

```handlebars
;;; weird-list.compile.hypr-tmpl

{{require: [[a:A[S] b:N c:B]]}}
  (let first_char := (a |> first)); <- "let first_char be defined as `a` piped through `first`."
  (let MAX := b)
  (let unroll? := c); <- Now, b/c this is a compile template, this _might_ have effect, I'd have to think about it...
  {{compile: [[a first_char MAX unroll?]]}}
    <ol>
    {{? [a first_char MAX unroll?]}}
      {{0->{MAX} [a :T:a char n c]}}
        {{1 (is :char: |> :(_ === first_char))}}
          {{. [first_char]}}
            <li>{(first_char | captialize :)}</li>
          {{/.}}
        {{0}}
          {{. [char]}}
            <li>{char}</li>
          {{/.}}
        {{/1}}
      {{/{MAX}}}
    {{/?}}
    </ol>
  {{/compile}}
{{/require}}
```

Generally you want your templates to be either build or compile, for multiple reasons...  I'll get to them later...
Bearing that in mind, remember that `build` only happens ONCE on starting the build or dev server: so MOST of the time you want `compile`.

#### The Run expresssions: 

The `run` is ONLY for when you find yourself in a situation where you genuinely need it.  It has slower performance, because it has to transpile while the server is running and serving other clients, and it is generally considered easier to tamper with it.  Have you seen some of the ethical hackers at work, injecting and XSS'ing and such?  It's crazy, and you don't want to give them an inch. Everything transpiled is sanitized by default: you have to ho out of your way to use unescaped content.  But you still want to Validate your Inputs... always.  It doesn't matter if you go a tad bit slower: if you get had, you're no longer your own, and you may not even know it.  There's more to do, but this was just an aside on web security, for the sake of ...fun, i guess...

Here's the same example, this time using `run`:

```handlebars
;;; weird-list.run.hypr-tmpl

{{require: [[a:A[S] b:N c:B]]}}
  (let first_char := (a | first)); <- "let `first_char` be defined as `a` piped through `first`."
  (let MAX := b)
  (let unroll? := c); <- Now, b/c this is a run template, this WILL have effect,
  ;;                     one way or the other (depending on the Cardinality, of course...).
  {{run: [[a first_char MAX unroll?]]}}
    <ol>
    {{? [a first_char MAX unroll?]}}
      {{0->{MAX} [a :T:a char n c]}}
        {{1 (is :char: |> :(_ === first_char))}}
          {{. [first_char]}}
            <li>{(first_char | captialize :)}</li>
          {{/.}}
        {{0}}
          {{. [char]}}
            <li>{char}</li>
          {{/.}}
        {{/1}}
      {{/{MAX}}}
    {{/?}}
    </ol>
  {{/run}}
{{/require}}
```

... More to Come, but I'm tired rn & it's time for a lil' ol' `nappsipoo` ...
