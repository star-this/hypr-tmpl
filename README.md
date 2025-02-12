# hypr-tmpl
> A powerful, tiny, fast, &amp; secure templating engine written specifically for the Hypermedian Stack

## Table of Contents
- [hypr-tmpl](#hypr-tmpl)
  - [Table of Contents](#table-of-contents)
  - [What does it look like?  Show me some examples, please!](#what-does-it-look-like--show-me-some-examples-please)
    - [The Simplest Examples](#the-simplest-examples)
      - [Persistent HTML Comment expression:](#persistent-html-comment-expression)
      - [Ephemeral HTML Comment expression:](#ephemeral-html-comment-expression)
      - [The simplest hypr-tmpl Comment expression (the Semicolon):](#the-simplest-hypr-tmpl-comment-expression-the-semicolon)
      - [Flavors of hypr-tmpl Comments (1, 2, \& 3 Semicolons):](#flavors-of-hypr-tmpl-comments-1-2--3-semicolons)
      - [A more complex hypr-tmpl Comment expression:](#a-more-complex-hypr-tmpl-comment-expression)
    - [List of types hypr-tmpl can check for:](#list-of-types-hypr-tmpl-can-check-for)
    - [Simple Expressions](#simple-expressions)
      - [A Do Block expression:](#a-do-block-expression)
      - [A Do Stream expression:](#a-do-stream-expression)
      - [A Single Conditional expression:](#a-single-conditional-expression)
      - [A Multiple Conditional expression:](#a-multiple-conditional-expression)
      - [A Forward Loop expression:](#a-forward-loop-expression)
      - [A Reverse Loop expression:](#a-reverse-loop-expression)
    - [Level 2: Access \& Inject expressions](#level-2-access--inject-expressions)
      - [The Access Scope expression:](#the-access-scope-expression)
      - [The Inject Scope expression:](#the-inject-scope-expression)
    - [Level 3: Require expressions \& Directives for the Transpiler](#level-3-require-expressions--directives-for-the-transpiler)
      - [The Require expression:](#the-require-expression)
    - [Transpilation (Level 3, continued...)](#transpilation-level-3-continued)
      - [The Build expression:](#the-build-expression)
        - [Macros, \& Micros, \& Pipes, Oh My!](#macros--micros--pipes-oh-my)
      - [The Compile expression:](#the-compile-expression)
      - [The Run expresssions:](#the-run-expresssions)
    - [Level 4: Import \& Define directives](#level-4-import--define-directives)
      - [The Import expressions:](#the-import-expressions)
        - [The Relative Import expression:](#the-relative-import-expression)
        - [The Absolute Import expression:](#the-absolute-import-expression)
        - [The Named Import expression:](#the-named-import-expression)
      - [The Define expressions:](#the-define-expressions)
    - [Level 5: Macros, Micros, \& Selectors](#level-5-macros-micros--selectors)
    - [Level 6: Web Components](#level-6-web-components)
    - [Level 7: Use it or Lose it](#level-7-use-it-or-lose-it)

## What does it look like?  Show me some examples, please!
> OK, let's learn with examples. Building up from nothing until we can use hypr-tmpl like a... web developer, that sounds good, right?  Alright!  Let's go!

^ [Back to ToC](#table-of-contents)

### The Simplest Examples

#### Persistent HTML Comment expression:
```html
<!-- everything after the first 2 dashes (--) is a comment, just like in HTML -->
```
> ^ N.B., these (normal) HTML comments will remain in the final HTML.

^ [Back to ToC](#table-of-contents)

#### Ephemeral HTML Comment expression:
```html
<!--- if there even 1 more than 2 dashes, /^<!(-{3,}?)\b.+\b(-{3,}?)>$/g, e.g., `---` or `-----`, in a row, it's still a comment, albeit an abnormal one, --->
<!----- HOWEVER, these abnormal HTML comments get REMOVED in the transpilation process ----->
<!-- (unlike this one, which will remain in the final HTML, since it's a normal HTML comment) -->
```
> ^ Again, to reiterate, another time, the abnormal HTML comments (having more than 2 dashes) WILL be stripped from the final HTML in transpilation.  Use this to your advantage!

^ [Back to ToC](#table-of-contents)

#### The simplest hypr-tmpl Comment expression (the Semicolon):
```handlebars
; "everything after even just one semicolon (`;`) is a comment for the rest of the line."
```
> ^ ALL hypr-tmpl comments get stripped from templates in transpilation, btw.

^ [Back to ToC](#table-of-contents)

#### Flavors of hypr-tmpl Comments (1, 2, & 3 Semicolons):
```handlebars
;;; "3-semicolon Comment(s) at the *TOP* of the file"

;; 2-semicolon Comment on its own line, no indentation.
;; (This comment is in the *MIDDLE* of the file somewhere)
;; (and there can be several of them in a row)
;; ...

;; 2-semicolon Comment could be just a single line, all by itself.

{{some_code_section}}; <- "1-semicolon Comment"
                    ;; "2-semicolon Comment on its own line,"
                    ;; "indented for code alignment."
                    ;; "Comments like these can"
                    ;; "'extend' the 'code section'" 
                    ;; "they're attached to,"
                    ;; "but once you see an 'HTML section',"
                    ;; "the 'code section' ends, i.e., unless"
                    ;; "another 'code section' follows,"
                    ;; "if you see some <element-tag>,"
                    ;; "an 'HTML section' has just begun,"
                    ;; "& if you want to make a comment"
                    ;; "you MUST switch to HTML comments,"
                    ;; "like so:"
  <some-element-starting-an-html-section semicolons="false">
    <!-- whether a persistent HTML comment -->
    <!--- or an ephemeral HTML comment --->
    <!------ you MUST NOT use Semicolon Comment expressions ------>
    <!--- until another 'code section' switches it back to hypr-world --->
  </some-element-starting-an-html-section>
{{/some_code_section}};
                      ;; "^ (*NOTE-THERE'S-NO-COMMENT-ABOVE*"
                      ;; "... just the 1 semicolon)"
                      ;; "this pattern is used to prevent"
                      ;; "something malicious from being injected"
                      ;; "in between the code section"
                      ;; "and the semicolon, which,"
                      ;; "to remind you, everything on a line"
                      ;; "after the first semicolon is stripped"
                      ;; "in the transpilation process."

;;;; "... technically, you can use as many semicolons as you want,"
;;;;;;;;;;;;;;;;;;;;;;;;; "but there's really no need to go past 3,"
;;;;;;;;;;;;;;;;;;;;;;;;;;;;; "unless you're into that kinda thing."

;;; "3-semicolon Comment(s) at the *BOTTOM* of the file"
```


So the general convention is:
- `;;;`: a comment that goes at the top of the file, e.g., the name, the description, caveats, etc.
- `;;`: a comment that goes on its own line, often used to explain what the code is doing when the comment is long
- `{{code_section_followed_by_a_semi_but_no_comment}};`: a pseudo-comment, to indicate that the code section is followed by a semicolon, but no comment.  This is useful to prevent something malicious from being injected in between the code section and the semicolon, which, to remind you, everything on a line after the first semicolon is stripped in the transpilation process.
- `{{after_some_code_section}}; quick_comment`: a comment that goes on the same line as some code, usually to QUICKLY explain something notable about the code... if the comment is going to be long, you should follow up on the next line with a 2-semicolon comment, indented to line up the comment with the 1-semicolon comment, with as many lines (& thus indented 2-semicolon comments) as needed.
- `;;;`: a comment that goes at the BOTTOM of the file, e.g., the code owners/maintainers, etc., that you can use to quickly look up who to ask if something goes wrong
- `(EOF)`: stands for `End Of File`, in case you're wondering.  You don't write it explicitly, but sometimes you'll see it somewhere, sometime in your life, and it's essentially just an invisible special character to indicate the end of the file (although I added the parentheses to make it more explicit: you don't ever actually type it out, unless perhaps you're writing your own Operating System... or a compiler for a language that requires an explicit `EOF` character (?!).).
...

^ [Back to ToC](#table-of-contents)

#### A more complex hypr-tmpl Comment expression:
```handlebars
;;; comments.compile.hypr-tmpl
;;;
;;; a template To Make The World a Better Place (TM),
;;; because I believe that we can succeed,
;;; not just by Doing Well, but by Doing Good.
;;;
;;;              -- Gavin Belson, former CEO of Hooli

{{code_section_a}}; <- "comment on 'code_section_a'"
  <span>Kid A</span>
{{/code_section_a}}; "btw this was a great album..."
                  ;; "it got better as time went on."
                  ;; "i'd take it as a compliment"
                  ;; "if you said `hypr-tmpl` is like"
                  ;; "the Kid A of templating languages."

;; "just adding a comment here where we can pretend"
;; "we're adding some important/helpful context..."

                  ;; "(Preperatory comment for 'section_b')"
                  ;; "this is only possible since we're"
                  ;; "still within a 'code section'."
                  ;; "Otherwise, we'd have to switch to"
                  ;; "HTML comments."
{{code_section_b}}; <- "comment on 'code_section_b'"
                  ;; "This comment adds to the"
                  ;; "1-semicolon comment above,"
                  ;; "and is still within a 'code section',"
                  ;; "since it 'extends' the 'code section',"
                  ;; "so it's allowed. And hopefully helpful."
  <span>Kid B</span>
  <!--- ^ This is valid text, but it's not a Radiohead Album, so the HTML spec should be revised. --->
{{/code_section_b}};

;;; Code Maintainers: ['Team Radiohead', 'Thom Yorke Wannabe', 'Jonny Greenwood Wishhecouldbe']
```

^ [Back to ToC](#table-of-contents)

### List of types hypr-tmpl can check for: 
```handlebars
;;; types are easily remembered by this mnemonic: "A No Stub Function"... unless you can think of something better.
;; TYPES: [A|N|O|S|T|U|B|F]
`A`; 'array'
`N`; 'number'
`O`; 'object' (but not null, JS/TS folks!)
`S`; 'string'
`T`; 'type (inferred from context)
`U`; 'unknown' (default: type must be checked & type-narrowed in the transpilation process)
`B`; 'boolean'
`F`; 'function' (TS/JS function to construct Macros, Micros, etc.)
;;...

a_s:A[S]; 'array of strings'
a_t:A[T]; 'array of TYPE'
a_u:A[U]; 'array of unknown_things, potentially heterogeneous'
a_b:A[B]; 'array of booleans'
a_n:A[N]; 'array of numbers'
;;...

O{a:N,b:S,c:B}; 'object with properties `a: number`, `b: string`, `c: boolean`
;;...

A[O{s:S}]; 'array of objects with property `s: string`
O{array:A[N],length:N}; 'object with property `array` (an array of numbers), and `length` (a number)'

;;; ... I think you get the gist.
```
> ^ HINT: it can be helpful to when reading colons in hypr-tmpl to give them names, depending on their position in the expression, and its related context.

...

> you can read the `:` symbol as `"gets"`, when it comes after a variable name.
> So, a variable can get a TYPE (`a_n:A[N]`)... read:`"'a_n' ('a sub n') gets an array of numbers."`,
> but it can also get a Value (`a_n:[1 2 3]`)... read`"'a_n' ('a sub n') gets the array 1 2 3"`.
> In fact, it can get a TYPE _**AND**_ a Value (`a_n:A[N]:[1 2 3]`)... read `"'a_n' ('a sub n') gets an array of numbers, which is given the values 1 2 3"`. 

...

> BTW, you can also read the `:` symbol as `"gives"` (or `"is given"`, like in the previous example), when it comes before a variable/TYPE name.
> Thus, `array :T` can be read as `"array gives its type to 'T'"`.  This is useful when you want to know what type of array you're dealing with.  if you already know its type is, say, `A[N]`, you probably want to make it explicit somewhere rather conspicuous (and often higher up in the file), then further down the line, if you need to specify its type, you can just have the transpiler engine infer it for you with `:T`.

...

> Furthermore, you can also read the `:` symbol as `"cons"` (as in The List **CONS**tructor).
> For example, `o_abc:O{a:N,b:S,c?:B} :a:b:c?:` can be read as "`o_abc` ('o sub abc') 'gets' the Type of 'an object' 'with properties' '`a`, `b`, & `c?`', 'of types' '`N`, `S`, & `B`', respectively (`O{a:N,b:S,c?:B}`), &, if it matches, 'gives' (`:`) itself to 'list `a`' `:`(`'cons'`) '`b`' `:`(`'cons'`) '`c?`' `:`(`cons` `nil` or a following `micro`, etc)" ... which ends up looking like (`:a:b:c:?:`).  

...

> Similarly, you can also infer the type of the object with `:T`, as in `o_abc:T :a:b:c:`, which can be read as either "`o_abc` 'gets' its type from `T`", or "`o_abc` 'gives' its type to `T` (via type inference), which 'gives' itself to list `a` 'cons' `b` 'cons' `c` (i.e.,`:a:b:c:`)." It works with arrays, too: `a_abcde:A[N] :a:b:c:d:e:` can be read as: "`a_abcde` ('a sub abcde') 'gets' an array of numbers (`A[N]`), & 'gives' itself to list `a` 'cons' `b` 'cons' `c` 'cons' `d` 'cons' `e` (or `:a:b:c:d:e:`)."

...

> To recap, depending on the position, you can read the `:` symbol as:
> - `"gets"`
> - `"gives"` / `"is given"`
> - `"cons"`

^ [Back to ToC](#table-of-contents)

### Simple Expressions

#### A Do Block expression:
```handlebars
;;; A simple do statement, you'll see that
;;; it's kind of like the `if` statement,
;;; but without the conditional check...
;;; similar to an 'otherwise' expression.
;;; Its purpose is to enforce the rendering
;;; of a single chunk of HTML
;;; (as opposed to a stream of chunks),
;;; and it's usually used by a `page` expression
;;; to demarcate a single block of HTML,
;;; to be rendered all at once.

{{0}}
  {code_expression}
{{/0}}; <- "number here must match `0`,"
      ;; "or the template will not transpile (build/compile/run)"

;;; "we'll learn more about this, and how to use it, later"
```
> HINT: you can read this expression as 'do' (`code_expression`).

^ [Back to ToC](#table-of-contents)

#### A Do Stream expression:
```handlebars
;;; a more advanced do statement: 
;;; it's like a `do` block expression,
;;; but it's used by a `loop` expression
;;; to build up a several chunks of HTML,
;;; to be rendered as they are received
;;; over a stream.  It's a stream of chunks, 
;;; to be rendered in order, although
;;; they might be received over-the-wire
;;; out of order: & so they have chunk ids,
;;; to render them in the correct order.

{{0 chunk:N:limit:N}}
  {code_expression}
{{/{limit}}}; <- "number here must match `limit`,"
            ;; "so that the template knows"
            ;; "where it's at & when to stop"
            ;; "or the template will not transpile"
            ;; "(build/compile/run)"

;;; "we'll learn more about this, and how to use it, later"
```
> HINT: you can read this expression as 'stream' `chunk` 'from' `0` 'to' `limit` 'with' (`code_expression`).

^ [Back to ToC](#table-of-contents)

#### A Single Conditional expression: 
```handlebars
;;; similar to a `do` statement, but an `if` statement comes with a conditional check (surrounded by parentheses)
;;; conditional statements are designated by a countdown:
;;; if you want a single conditional check, use 0

{{0 (condition?:B)}};
  <span>The expression above evaluates to true, so this HTML is rendered.</span>
{{/0}}; <- number here must match 
      ;; the starting countdown number (`0`),
      ;; or the template will not transpile 
      ;; (build/compile/run)
```
> HINT: you can read this expression as 'if' `condition` is true, render (...); 

^ [Back to ToC](#table-of-contents)

#### A Multiple Conditional expression: 
```handlebars
;;; conditional statements are designated by a countdown:
;;; if you want 3 sequential conditional checks, with a default (0)

{{3 (condition_3?)}}; 
  <span>Case 3 is true</span>
{{2 (condition_2?)}}
  <span>Case 2 is true, but not 3</span>
{{1 (condition_1?)}}
  <span>Case 1 is true, but neither 2, nor 3 is true</span>
{{0}}; Otherwise, Do:
  <span>neither 3, nor 2, nor 1 is true... we have to resort to the default, i guess.</span>
{{/3}}; <- number here must match the starting countdown number (`3`), or the template will not transpile (build/compile/run)
```
> HINT: you can read the `{{3 (condition_3?)}} ... {{/3}}` in one of two ways (I haven't decided which one I think is better yet, so for now, I'll just give you both, but eventually I'll decide which one I think is better, and then I'll update the hint): 

...

**First way**:
- "`3` 'checks' `condition_3?`: if true, renders (...); otherwise,"
- "`2` 'checks' `condition_2?`: if true, renders (...); otherwise,"
- "`1` 'checks' `condition_1?`: if true, renders (...); otherwise,"
- "`0` renders (...).";

...

**Second way**:
- "'option' `3` is `condition_3?`? Then render (...). Otherwise,"
- "'option' `2` is `condition_2?`? Then render (...). Otherwise,"
- "'option' `1` is `condition_1?`? Then render (...). Otherwise,"
- "`0` renders (...).";

...

**(Combined) Third way**:
- "'option' `3` 'checks if' `condition_3?` 'is true'; 'if so, renders' (...); otherwise,"
- "'option' `2` 'checks if' `condition_2?` 'is true'; 'if so, renders' (...); otherwise,"
- "'option' `1` 'checks if' `condition_1?` 'is true'; 'if so, renders' (...); otherwise,"
- "'option' `0` 'renders' (...).";

...

> I'm actually starting to lean towards the third way, myself.  It's more verbose, but it's also more explicit, and it's easier to read.  I'll have to give it some more thought.

^ [Back to ToC](#table-of-contents)

#### A Forward Loop expression: 
```handlebars
;;; loop statements start with a 0, 
;;; then a Direction to iterate, 
;;; then a Maximum value (exclusive): 
;;; could be 'manual' or 'derived' (from an array)

<ol>
{{0->3 [name A[N] value n unroll?=false]}}; <-" `unroll?`" 
                                          ;; "is a convention" 
                                          ;; "for a name"
                                          ;; "that is a"
                                          ;; "boolean var,"
                                          ;; "ending in `?`,"
                                          ;; "so it's a `B`"
                                          ;; "(although it"
                                          ;; "can sometimes"
                                          ;; "be `B%N`,"
                                          ;; "which means"
                                          ;; "an `N`"
                                          ;; "interpreted as"
                                          ;; "a `B`,"
                                          ;; "where:"
                                          ;; "`0=false`,"
                                          ;; "& all other"
                                          ;; "`N=true`, "
                                          ;; "while still"
                                          ;; "retaining its"
                                          ;; "`N` value)."
                                          ;; "`unroll?`"
                                          ;; "indicates"
                                          ;; "if the loop"
                                          ;; "should unroll"
                                          ;; "for streaming"
                                          ;; "purposes,"
                                          ;; "by convention."
                                          ;; "Not required,"
                                          ;; "default=`false`"
                                          ;; "so the loop"
                                          ;; "WON'T unroll."
  {{0}}; <- "This is not required here,"
      ;; "because `unroll?=false`,"
      ;; "but I'm trying to hammer home"
      ;; "the behavior of not unrolling"
      ;; "the loop: it'll be a block of"
      ;; "HTML, not a stream of HTML chunks."
  <li>
    <p>You! Shall! Not! ...Inject anything into this element!</p>
    <p>(Well, THAT came out MUCH less dramatically than I hoped...  Maybe the Balrog just... I don't know, maybe he gives up & goes away...  He can't have been had a good workout routine or have been in a real street fight for who knows how long... Oh no...  He's increasing his shadow and flame.  Ok, this is getting real.  Better brace myself.  And I really don't want to die just yet.  Although... I will get to come back and replace my miserable excuse of a boss... so awful, just absolutely wretched.  And he doesn't even wear The Signature White anymore... such a Classic, such a waste!  He's off gallavanting and scheming and plotting in "Many Colors." "Ring-maker," he calls himself... oh, for goodness' sake.  Maybe if he just tried a little pipe weed the Hobbits make for once, perhaps he'd be somewhat more tolerable.  And the Uruk-Hai!  Who does that?!  REMIND you of anyone?  hmmm?  I thought so.  How did I even get on to him, when I have a literal Fallen Maia, an Evil Spirit & chief servant of Morgoth, just a bad-guy in general, STARING at me like he wants to snuff me out like a little bug?  I better pay more attention.  Maybe strike the bridge with my staff, that'll increase the dramatic tension, for sure!  Got to be an alpha, you know?  Fake it 'til you make it, Cirdan told me... although it was mostly about his beard, being an elf and all...  Wait a minute, why is this in a List Item element?  You mean you're not just going to intrude on my personal thoughts, you're going to REPEAT them?  Clearly, there are fouler things than Orcs in the Deep places of the Internet.)</p>
  </li>
  <!--- ^ We'll learn how to Inject `value` later. --->
  {{/0}}
{{/3}}; <- "number here must match 'MAX': the upper bound (exclusive)"
</ol>
```
> HINT: you can read this expression as: `'loop'` 'from' `0` 'until' `3` (exclusive) over array of numbers `'name'`, with offset labels `value` and `n` per iteration, and for each render (...), not unrolled."

^ [Back to ToC](#table-of-contents)

#### A Reverse Loop expression:
```handlebars
;;; loop statements start with a 0, then a Direction to iterate, then a Maximum value (could be manual or derived from an array)

<ol>
{{0<-3 [name A[N] value n unroll?=true]}}; <- "unrolling the" 
                                         ;; "loop is a"
                                         ;; "transpilation"
                                         ;; "detail affecting" 
                                         ;; "performance:"
                                         ;; "depending on"
                                         ;; "WHEN"
                                         ;; "the template is"
                                         ;; "transpiled &"
                                         ;; "WHAT"
                                         ;; "is transpiled,"
                                         ;; "i.e., how large" 
                                         ;; "the array & HTML"
                                         ;; "are, & how many"
                                         ;; "iterations we"
                                         ;; "must render,"
                                         ;; "or, simply put,"
                                         ;; "'Cardinality'"
                                         ;; "(a fun word);"
                                         ;; "these will"
                                         ;; "affect streaming" 
                                         ;; "performance to"
                                         ;; "the User-Agent,"
                                         ;; "but the end"
                                         ;; "result should be"
                                         ;; "the same in the"
                                         ;; "HTML, at least"
                                         ;; "if they aren't"
                                         ;; "using Firefox,"
                                         ;; "which has known"
                                         ;; "issues streaming"
                                         ;; "Standard Web"
                                         ;; "Responses."
  {{0 :chunk:(N-n):limit:(N):}}; <- "The engine will know "
                              ;; "to subtract 1 from `N`,"
                              ;; "so we don't have to worry"
                              ;; "about it."
  <!--- the HTML won't know what {value} is, ---> 
  <!--- since it's not Injected (we'll learn how to do that later).  --->
  <li>Iteration SHOULD equal the number of the list reversed (if we knew how to Inject `value`, which WE don't yet... I do, but I'm not going to tell you how yet).</li>
  {{/0}}
{{/3}}; <- "number here still must match MAX,"
      ;; "the Loop's upper bound (exclusive)"
      ;; "or it won't transpile (build/compile/run)."
</ol>
```
> HINT: you can read this this expression as: `'loop'` 'TO' `0` 'FROM' `3` 'EXCLUSIVE' 'OVER' the 'ARRAY OF NUMBERS' `'{name}'`, 'WITH' offset labels `'{value}'` 'AND' `'{n}'` per iteration, 'UNROLLED'...".  Then, seeing the Do Stream expression above, you can follow up by saying: "...'STREAMING' as `3` 'CHUNKS'."

^ N.B., the Loop-Offset-based streaming (using N & n as the arbitrary chunk numbers... and thus chunk sizes, implicitly) is an artifact of the fact that the first implementation of the `hypr-tmpl` templating engine was (/ will be) written in either TypeScript (hopefully), or, (if I have to) Javascript. 

I haven't decided as of this moment, as I'm writing the language frontend to backend, to get the API just right.  I'd prefer TypeScript, though, if I'd have my druthers / can hack it, not that I'm a TypeScript fanboy, but we are dealing with types, and TS would actually help out a lot in that regard.  

To be as clear as a politician (or maybe even neither less nor equally clearer): the chunk count, (implicit) sizes, & stream boundaries, as used in this example (Loop-Offset-based streaming) are NOT a feature of the templating language itself.  It's an artifact of the templating engine's implementation, rather (e.g., you could pass in an `A` to `chunk`, and let the engine implementation infer the optimal chunk sizes/boundaries from the HTML to be rendered, using the array & its type as a hint, and let it go to town on the streaming, and then what do ya know, Zig Zip Zap, it'd be done in a nanojiffy).  So, if one were to, say, write an engine implementation in Zig, the streaming could be done in such a way that it'd be orders of magnitude more efficient, and the Loop-Offset-based chunking/sizing would be replaced with something more appropriate for the language.  So, it's not a feature of the templating language itself, but it will be a reality to deal with until such time as you can use a more efficient implementation in another language, so it's not something that you should fret over for now.  However, when I DO get around to writing a Zig implementation, I'll be sure to make it as efficient as I can... and then I'll bring in OTHER people (experts in zig / low-level programming) who, like, know stuff, & whatnot, so it WILL, eventually, be a reality to not deal with, but rather, fall in love with.  In addition, it should be implemented in such a way that it's not only more efficient, but also more flexible, more powerful, more expressive, AND you wouldn't have to explicity state chunk numbers & sizes up front in the language, OR be expected to go into low-level byte-coding and bit-twiddling and whatever-swizzling to make it go vroom vroom: it will Just Work.  Although, at that point, it might be a good idea to give folks  the option via some... optional thing... to do the low-level stuff, should they need to, if they're so inclined, if they want it real bad.  But, as the poet said:
> We'll burn that bridge when we get there.

^ [Back to ToC](#table-of-contents)

Congrats, script kiddie, you've earned yourself a badge for your whizbang grit & gumption!  

But you're not done. 

because

> You Ain't Seen Nothin' Yet!

So

> Stand by Me

Because

> The Best Is Yet to Come

Until you finally get it, and sing out loud:

> At Last!


### Level 2: Access & Inject expressions

> OK, so about the 'Injections' above: you need explicit Access Scopes ({{`?`}}) to mark data as passable into expressions.

...

> Oh, & you need explicit Inject Scopes ({{`.`}}) to inject that data into the interpolated HTML

...

> Attempting to harness `The Principle of Least Privilege` as a means for `Security-by-Default`, so, we have to be, again, **VERY explicit** in what we allow to be referenced in the first place,
> & then we have to be **VERY explicit** in what we allow into our interpolated HTML.
> So join me as we learn how to access & inject data into our templates.

#### The Access Scope expression: 
```handlebars
;;; assume there's an array up above of length 100
;;; as well as a `unroll?:false`

{{? [array_from_above:A[N:100] unroll?:B]}};
  <ol>
  {{0->{N} [array_from_above :T val n unroll?]}}; <- "cool!"
                                                ;; "both"
                                                ;; "the array"
                                                ;; "& unroll?"
                                                ;; "are in"
                                                ;; "scope."
  <!---                         ^   ^ "`val` and `n` depend on the current iteration, so technically they're just what I'm calling 'value labels': they're not TRUE variables in the full sense, captured from above in a closure." --->
  <!--- It's just how loops work in hypr-tmpl. --->
    <li>You shall not inject any values into this HTML.</li>
  <!--- ^ "Actiually Injecting variables requires explicitly stating what may be injected, and it must be available in scope, or the template won't compile!" --->
  {{/{N}}}; <- "N.B.,"
          ;; "we got a little fancy"
          ;; "& accessed `{N}` here"
          ;; "as the `MAX` value for the loop,"
          ;; "b/c it's part of a 'code section' (`{{...}}`),"
          ;; "vs an 'HTML section' (<element>, etc.),"
          ;; " & the length of the array is explicit!"
          ;; "..."
          ;; "We could have gotten even fancier,"
          ;; "& Accessed `{array_from_above}`"
          ;; "itself for the MAX value endcap,"
          ;; "and it would've worked just fine,"
          ;; "since it already knows the `length`"
          ;; "of the array (otherwise, we'd have"
          ;; "to Infer or explicitly figure out"
          ;; "the `length`of the array (`{array.length}`),"
          ;; "which might be ugly / a pain,"
          ;; "but we could still do it)."
          ;; "This is about as fancy as we're"
          ;; "going to get for now, though,"
          ;; "young grasshopper."
  <!--- ^ "The beginning & end of this section are 'Accessing' type `N` as its value in `{{/N}}`, NOT 'Injecting' it:" --->
  <!--- (`inject` can ONLY happen within an HTML section) --->
  </ol>
{{/?}};
```

> [**Mr. Miyagi-San**]: "Good job, young grasshopper... but do not let success get to head... very dangerous...  very very dangerous." 


> [**Young grasshopper Daniel-Kun**]: "So how was the Access Scope (`?`) able to access anything in the first place?"


> [**Mr. Miyagi-San**]: "Sensei will tell: ... ... ...We pretend.  Like little children.  But you must be grown-up now."


> [**Young grasshopper Daniel-Kun**]: "So how do we actually get the values in there, then?"


> [**Mr. Miyagi-San**]: "Nooooo, no no no no no.  You not ready.  Sensei-Kayfabe-Kata not over yet.  You have much to learn.  In Okinawa, Father inject many values in many Templates.  As young boy, Sensei watch and listen to Father; Sensei learn from Father.  Now, Sensei have many tricks up sleeve.  Now, you young grasshopper, and Sensei old man.  Template need values injected, but since this first rodeo for young grasshopper, we pretend, like children, once more, young grasshopper.  You child.  You watch.  You listen.  I teach.  You follow instruction.  You practice.  You learn."

OK, enough Karate Kid homage (if you are too illiterate to get the reference, AI probably will take your job, or at least prevent you from getting a programming one, so you'd better learn to educate yourself... by imbibing a stream of the Classics, like Shakespeare's "Hamlet", or Dostoevsky's "The Brothers Karamazov" or "Crime and Punishment"... or even better, all of them, AND "The Karate Kid").  Now, must get back to learn template arts.


^ [Back to ToC](#table-of-contents)

> So how do we actually get the values in there, then?  How do we `inject` either `val` or `n`?

#### The Inject Scope expression:
```handlebars
;;; assume there's an array up above of length 100
;;; as well as a `unroll?:false`

{{? [array_from_above:A[N:100] unroll?:B]}};
  <ol>
  {{0->{N} [array_from_above :T val n unroll?]}};
    <li>
    {{. [val n]}}; <- "now we're in business!"
      <div>
        <span>success! <code>#{n} = {val}</code></span>
      </div>
    {{/.}};
    {{. []}}; <- "An empty array prevents all injection!"
            ;; "Is it necessary?  I don't think so, but..."
            ;; "It at least is quite explicit, which means"
            ;; "[1] it's simpler to **generate templates**,"
            ;; "**by automation**, whether or not one finds"
            ;; "it's more readable.  Which is good, because"
            ;; "[2] it's **more readable**. Kinda like it."
            ;; "[3] There's a certain **symmetry** to it..."
            ;; "which helps in the learning process, and"
            ;; "[4] it might help as the codebase grows,"
            ;; "when we decide 'now we actually need to"
            ;; "inject something into this section,'"
            ;; "then, like trailing commas, it helps"
            ;; "**keep the git diffs clean**!"
            ;; "OK, I think I've reasoned my way through it."
            ;; "It stays.  (at least for now...)"
      <div>
        <span>Note you can ensure EXPLICITLY that NOTHING is injected by using an empty array <code>[]</code> within the <code>{{.}}</code> section, which is similar to the Gandalf Method (remember right before the Balrog fight?), i.e., just not including a <code>{{.}}</code> section at all, that is to say, via an IMPLICIT mechanism (which beginners/juniors/n00bs/sophmores/etc. might find a little bit "Magical" (see what I did there?), and so bang their heads against the wall trying to figure out why the values are not being injected (or perhaps even better / more explicit / more <code>secure-by-default</code>, why the template engine is refusing to transpile the offending files at all...)).</span>
      </div>
    {{/.}};
    </li>
  {{/{N}}};
  </ol>
{{/?}}
```

> ^ You're probably wondering how the Access Scope (`?`) was able to access anything in the first place.  Well, we just 'assumed' we could (And you know what they say about assuming).  So, let's figure out how not to assume, figure out how actually to receive data into the template, so we can get back to learning the templating language / engine.

^ [Back to ToC](#table-of-contents)

### Level 3: Require expressions & Directives for the Transpiler
> The key is to have a `require` 'code section':

#### The Require expression: 
```handlebars
{{require: [[a:A[S] b:N c?:B]]}}; <- "we demand that after we're transpiled, on render the template function gets passed an array of strings `a`, a number `b`, & a boolean `c?` (the language can just call it `c`, but the template will call it `c?`, and it will Just Work, because it's a boolean, see?)" 
  {{? [a b c?]}}; <- "we're accessing all 3 of them here, because we want to use `b` for the Loop length, & `c?` to determine whether or not the Loop is unrolled"
    <ol>
    {{0->{b} [a :T char n c?]}}; <- "N.B., we're Accessing `b` for the Loop length, and inferring the type `T` from the array of strings `a`"
      <li>
      {{. [char n]}}; <- "we're Injecting `char` and `n` into the HTML"
        <div>{char} = a[{n}]</div>
      {{/.}}
      {{. []}}; <- "we've forbidden anyone from injecting anything into this HTML section"
        <div>&nbsp;</div>
      {{/.}}
      </li>
    {{/{b}}}
    </ol>
  {{/?}}
{{/require}}
```
The Require section tends to be 1 of 3 section types (the other 2 we'll learn about later) on the outermost scope of a template file 100% of the time.  The rest of the time, well...

> Hey!  Want to hear some bad news?  We haven't told the template to transpile using any particular method, so it actually won't produce HTML in the end, lol!

^ [Back to ToC](#table-of-contents)

### Transpilation (Level 3, continued...)
There are 3 stages in which a template can be transpiled into a REAL template/partial, to which we can pass data & so render into the final HTML string
1. **Build-time** (static: transpiled on build: must rebuild the app / restart the dev-server to see changes)
2. **Compile-time** (static: transpiled at startup time if in production / hot-swappable if you're using a fast-enough dev-server)
3. **Runtime** (dynamic: transpiled when the salient route is first hit in production /  hot-swappable if you're using the dev-server)

**Caveat**: there can be only one of these in a given template, they are mutually exclusive.  Also, the **filename**.`PENULTIMATE`.extension must match the directive...
- A `build` directive must be in a single-template file **NAME**.`build`.hypr-tmpl (or, if you're on Windows: **NAME**.`build`.hyp), and can have no `compile` or `run` directives.
- A `compile` directive must be in a single-template file **NAME**.`compile`.hypr-tmpl (or .hyp...), and can have no `build` or `run` directies.
- A `run` directive must be in a single-template file **NAME**.`run`.hypr-tmpl (or .hyp...), and can have no `build` or `compile` directives.

^ [Back to ToC](#table-of-contents)

#### The Build expression: 
Build-time happens once, only once: either when the dev-server starts up, or when you explicitly run the build script for production/testing.  
This stage is for things that fit correspondingly, like blogs, Static Site Generation, Partials to be re-used in many other Templates, etc.  Use your creativity.

Here's how the previous example looks with a **build**:
```handlebars
;;; "weird-list.build.hypr-tmpl"

{{require: [[a:A[S] b:N c?:B%N]]}}; <- "FYI, about c?:"
                    ;; "(which is for `unroll?`)" 
                    ;; "it's typically a boolean,"
                    ;; "but it could be a number, too,"
                    ;; "when you're dealing with large numbers"
                    ;; "of iterations and want to unroll"
                    ;; "the loop in chunks for performance"
                    ;; "by streaming those chunks."
  ;; "..."
  ;; "it feels like maybe something should go here..."
  {{build: [[a b c?]]}}; <- "?! is this one redundant?"
    <!--- or maybe something could go here... --->
    {{? [a b c?]}}; <- "?! or is this one redundant?"
      <!--- "... something seems off ..." --->
      <ol>
      {{0->{b} [a :T:a char n c?]}};
        {{. [char n]}};
        <li>{char} = a[{n}]</li>
        {{/.}};
      {{/{b}}};
      </ol>
    {{/?}};
  {{/build}};
{{/require}};
```
> ^ did you think that template was kinda weird?  Good, it was.  I'm going to toss you into the pool without much warning or explanation,
> but hopefully you won't drown.

^ [Back to ToC](#table-of-contents)

##### Macros, & Micros, & Pipes, Oh My!
These new function-like expressions are called `Macro` expressions, but the one we're going to use, `let`, technically is a `special_form`, 'cause it's built in, see?  But you can still call it a `Macro` if you want, they're pretty similar.  There are also what are called `micros`, which are like `macros`, but for subexpressions.  We'll get to those later.
> Macros transform data via `pipes`, & sometimes with the help of `selectors`, `micros`, or both. There are a few types of `pipes`:
- `|` :: `data-pipe`; 
  - `(data | f ... :)::optional_name` => `new_data`
  - `(data | f :selector: :micro ... :)::optional_name` => `new_data`
- `|>` :: `selector-pipe`; 
  - `(data :selector: |> g ... :)::optional_name` => `new_data`
  - `(data :selector: |> g :micro ... :)::optional_name` => `new_data`
- `:=` :: `closure-pipe`; 
  - `(let local_var := data | f ... :);`
  - `(let local_var := data :selector: |> g ... :);`

```handlebars
;;; "weird-list.build.hypr-tmpl"

{{require: [[a:A[S] b:N c?:B]]}};
  (let first_word := a | first :); <- "this can be read:"
                                ;; "'let `first_word` be"
                                ;; "defined as `a` piped"
                                ;; "to `first`';"
  (let MAX := b :); <- "read: 'let `MAX:B` be defined as `b`';"
  (let unroll? := c? :); <- "FYI, b/c this is a `build`"
                      ;; "template, this won't matter"
                      ;; "that much, whether or not"
                      ;; "the loop is unrolled..."
                      ;; "might as well have the option"
                      ;; "though, so still rename `c?:B`,"
                      ;; "to `unroll?` to pass to the Loop."
  ;; "N.B., we've only renamed `b` to `MAX` and `c?` to `unroll?`,"
  ;; "but we've _**TRANSFORMED**_ `a` INTO `first_word` (locally):"
  ;; "we're going to end up using both `a` and `first_word`"
  ;; "for a total of 4 variables in the build section:"
  {{build: [[a first_word MAX unroll?]]}}; <- "now we're about"
                          ;; "to enter the HTML realm..." 
                          ;; "after the `<ol>` tag,"
                          ;; "we have to do HTML comments,"
                          ;; "except for end of line comments"
                          ;; "on valid code sections."
    <ol>
    {{? [a MAX unroll?]}};
    <!---^ generally we want to push `Access` exprs (`?`) or `Inject` exprs (`.`) as far DOWN the template as we possibly can, --->
    <!---  reducing the chance Betty the Bot / Hannah Hacker / whoever it is --->
    <!---  that's trying to attack our site has less room to work with, etc. --->
    <!---  but the Loop expression needs some variables, so we're going to have to pass them in --->
      {{0->{MAX} [a :T word n c?]}};
        {{? [word first_word]}};
          {{1 (given :word:first_word: |> is :(_ = _))}}; <- "LOOK!  A micro!"
              <!--- This one has a special subexpression `:(_ = _)` --->
              <!--- called a `micro`.  A `micro` can be automatically generated --->
              <!--- by the transpiler, if its slot(s) matches the variable(s) passed in. --->
            {{. [first_word]}}; <- "Same as the`Access`"
                              ;; "advice above, we push"
                              ;; "the Inject expression as"
                              ;; "far DOWN as possible"
              <li>{(first_word | captialize :)}</li>
            {{/.}};
          {{0}};
            {{. [word]}}; <- "pushed as far DOWN as possible"
              <li>{word}</li>
            {{/.}};
          {{/1}};
        {{/?}};
      {{/{MAX}}};
    {{/?}};
    </ol>
  {{/build}};
{{/require}};
```

Whew.  That's a lot.  Why don't we reuse this template mostly and only change the directive for the next 2 examples, so we can focus on imagining what their difference actually _IS_, and _WHY_ we'd want to use each.

^ [Back to ToC](#table-of-contents)

#### The Compile expression: 

```handlebars
;;; weird-list.compile.hypr-tmpl

{{require: [[a:A[S] b:N c?:B%N]]}};
  (let first_word := a | first :); <- "let `first_word`"
                                ;; "be defined as `a` piped"
                                ;; "through to `first`."
  (let MAX := b :);
  (let unroll? := c? :); <- "Now, since this template"
                      ;; "is a compile template,"
                      ;; "this _might_ have effect,"
                      ;; "we'd have to look into it..."
                      ;; "if c?:B, we unroll fully, but"
                      ;; "if c?:N, we unroll in chunks"
                      ;; "to stream to the client,"
                      ;; "which might be a good compromise"
                      ;; "between speed and load."
                      ;; "By declaring `c?:B%N`, we're"
                      ;; "letting the transpiler know"
                      ;; "that whoever uses this template"
                      ;; "will more often than not"
                      ;; "be dealing with a large"
                      ;; "number of iterations,"
                      ;; "so it might behoove us"
                      ;; "to unroll in chunks,"
                      ;; "and this is the way to do it."
                      ;; "(`B%N` is shorthand for `B` or `N`,"
                      ;; "where `N` gets interpreted as"
                      ;; "`true` unless `N=0` :`N=false`."
                      ;; "This gives us a little more)"
                      ;; "flexibility, but not too much."
  {{compile: [[a first_word MAX unroll?]]}};
    <ol>
    {{? [a MAX unroll?]}};
      {{0->{MAX} [a :T word n c?]}};
        {{? [word first_word]}};
          {{1 (given :word:first_word: |> is :(_ = _))}};
            {{. [first_word]}};
              <li>{(first_word | captialize :)}</li>
            {{/.};}
          {{0}};
            {{. [word]}};
              <li>{word}</li>
            {{/.}};
          {{/1}};
        {{/?}};
      {{/{MAX}}};
    {{/?}};
    </ol>
  {{/compile}};
{{/require}};
```

Generally you should prefer your templates be either `build` or `compile`, for performance & security reasons (& maybe even a few other reasons I'm not thinking of right now)...  I'll chew on it for later...

Bearing that in mind, remember that `build` only happens ONCE on starting the build or dev server: so MOST of the time you'll want `compile`, unless you're creating a partial that's going to be used in another template, or you're making a blog or something super static.

^ [Back to ToC](#table-of-contents)

#### The Run expresssions: 

The `run` is ONLY for when you find yourself in a situation where you find yourself genuinely in need of it.  It has slower performance, because it has to transpile on-the-fly, when its route is first hit, & while the server is actively running and serving other routes & other clients; it's causes slowdown & potentially extra Garbage Collection. On top of this, it is very well might be easier for a bad actor to tamper with it.  Have you seen some of the ethical hackers at work, injecting and XSS'ing and such?  It's crazy, and you really don't want to give them an femtometer. Everything transpiled is sanitized by default: you have to go out of your way to use unescaped content.  But you still want to Validate your Inputs... Always.  It doesn't matter if you run a tad bit slower: if you get had, you're no longer your own, and you may not even know it.  What does it profit you at that point just how fast your server runs, or how fast you serve teamplates, when you're in reality serving a baddie?  There's more to do, but this was just an aside on web security, for the sake of ...brightening your day, i guess...

Here's the same example, this time using `run`:

```handlebars
;;; weird-list.run.hypr-tmpl

{{require: [[a:A[S] b:N c?:B%N]]}};
  (let first_word := a | first :); <- "let `first_word`"
                                ;; "be defined as:"
                                ;; "`a` piped to `first`."
  (let MAX := b :);
  (let unroll? := c? :); <- "Now, b/c this is a run template,"
                        ;; "this SHOULD have effect,"
                        ;; "one way or the other"
                        ;; "(depending on the Cardinality,"
                        ;; "of course...)"
  {{run: [[a first_word MAX unroll?]]}};
    <ol>
    {{? [a MAX unroll?]}};
      {{0->{MAX} [a :T word n c?]}};
        {{? [word first_word]}};
          {{1 (given :word:first_word: |> is :(_ = _))}};
            {{. [first_word]}};
              <li>{(first_word | captialize :)}</li>
            {{/.}};
          {{0}};
            {{. [word]}};
              <li>{word}</li>
            {{/.}};
          {{/1}};
        {{/?}};
      {{/{MAX}}};
    {{/?}};
    </ol>
  {{/run}};
{{/require}};
```

^ [Back to ToC](#table-of-contents)

### Level 4: Import & Define directives

> So, we've learned how to use `require` to bring in data, and how to use `build`, `compile`, and `run` to transpile that data into HTML.  But what if we want to use the same data in multiple templates?  Or, what if we want to use the same HTML in multiple templates?  That's where `import` and `define` come in.

#### The Import expressions:

^ [Back to ToC](#table-of-contents)

##### The Relative Import expression:
```handlebars
;;; some-page.compile.hypr-tmpl

;; `~` is the configured 'template' directory, so it's relative to that.
;; `@` is how we reference that variable we're importing.

(~/components/elements/project-name-responsive-header.compile.hypr-tmpl @project-name-responsive-header)
(~/components/elements/project-name-responsive-footer.compile.hypr-tmpl @project-name-responsive-footer)

(~/components/partials/welcome-user-by-name-outer.build.hypr-tmpl @welcome-user-by-name-outer)
(~/components/partials/welcome-user-by-name-inner.build.hypr-tmpl @welcome-user-by-name-inner)
(~/components/partials/the-header.build.hypr-tmpl @the-header)

(~/project/auth/views/login-form.compile.hypr-tmpl @login-form)
(~/project/auth/views/signup-form.compile.hypr-tmpl @signup-form)

(~/project/auth/layouts/login-or-signup.build.hypr-tmpl @login-or-signup)
(~/project/welcome/layouts/welcome-landing.compile.hypr-tmpl @welcome-landing)

{{require: [[default_to_login?:B]]}};
  {{@login-or-signup [[default_to_login?:B]]}};
    {{? [default_to_login?]}};
      {{1 (default_to_login?)}};
        {{@login-form /}};
      {{0}};
        {{@signup-form /}};
      {{/1}};
    {{/?}};
  {{/login-or-signup}};
{{/require}};

{{require: [[user_name:S]]}};
  {{@welcome-landing}};
    {{@project-name-responsive-header}};
      {{@the-header}};
        {{@welcome-user-by-name-outer}};
          {{? [user_name]}};
            {{. [user_name]}};
              {{@welcome-user-by-name-inner [user_name] /}};
            {{/.}};
          {{/?}};
        {{/welcome-user-by-name-outer}};
      {{/the-header}}
    {{/project-name-responsive-header}}
    {{@project-name-responsive-footer}}
      {{@the-footer /}};
    {{/project-name-responsive-footer}};
  {{/welcome-landing}};
{{/require}};

;;; ^ this is where a `define` directive would go, if we had one.
;;; we haven't tackled `define` yet, but we will soon.
```

^ [Back to ToC](#table-of-contents)

##### The Absolute Import expression:

(lol, there IS NO Absolute Import expression, because why would you want to give someone else's code access to your entire project?  That's like giving someone else access to your entire house, even though you only want to give them access to the kitchen.  Same thing.)

^ Consider using the [Relative Import expression](#the-relative-import-expression) instead.

If you want to import a template from a different project, you can use the Named Import (`import:`) directive.  It's similar to the `require:` directive, but it's relative to the **project root**, not the configured **template directory**, AND you have to have already installed the library containing the template(s) you want to use in your project, and included it/them in the configuration file, to be ready to use in your project.

^ [Back to ToC](#table-of-contents)

##### The Named Import expression:

...  `{{import: library-scope/library-name/template-name :template-type:}}`.  This is how you import a template from a library or framework.  It's similar to the `require:` directive, but it's relative to the **project root**, specifically it looks to your installed libraries, and must be configured as such, with each library having its own configuration block in the configuration file.  It's not the configured **template directory**, AND you have to have already installed the library containing the template(s) you want to use in your project, and included it/them in the configuration file, to be ready to use in your project.

Also, you should list your external imports before your project-local imports, both so that you can use the external imports in your project-local imports, and so that they don't mess with your project-local imports.  Similar to CSS, where you list your imports before your styles.  Last one wins, so if you have a project-local import that, say, defines a custom-element that has the same name as an external import, the project-local import will be defined instead of the external import, but the transpiler will either warn you or error out, depending on the configuration.

```handlebars
;;; better-lorem-ipsum-p.compile.hypr-tmpl

;; whitespace is weird in browsers, 
;; & `&nbsp;` causes its own set of problems, 
;; so we're going to use a custom-element to 
;; render a "Non-Coalescing-SPace" (since &ncsp; DNE)
;; called `hypr-ncsp` to render a space that wraps, etc.
;; in a way that is intuitive, and basically invisible.
;; That's the goal, in fact: to take it for granted,
;; & create such a widespread usage that it becomes
;; a bone-fide Web Standard.
{{import: star-this/hypr-cel/hypr-ncsp :custom-element:}}

{{require: [[lorem_ipsum:S]]}};
  (let words := lorem_ipsum | split :" ": :);
  {{build: [[words]]}};
    <p>
    {{? [words]}};
      {{. [words]}};
        {{0->{words} [words A[S] word n]}};
          {{. [word]}};
            {{1 (n ~= words.length-1)}}; <- "~= :'not equal to'"
              {word}<hypr-ncsp />
            {{0}};
              {word}
            {{/1}};
          {{/.}};
        {{/{words}}};
      {{/.}};
    {{/?}};
    </p>
  {{/build}};
{{/require}};
```

^ [Back to ToC](#table-of-contents)

#### The Define expressions:

...  `{{define: component-name :template-type:}}`, where `template-type` is one of `custom-element`, `partial`, or `Web-Component`.  This is how you define / 'export' a template.  It's local to your project, and can be imported into any template in your project via the Relative Import expression.  It's a way to turn templates into components to use in the browser, or to make partials to empower your templates, to then turn into components to use... remember, actual-use >> theoretical-reuse (The DRY Fallacy, or "Don't Repeat Yourself": in reality, you should only create abstractions that are actually used & useful, so allow yourself to repeat yourself, because that's how you see what needs to be abstracted).  A good rule of thumb is only consider abstracting something when you've used it at least 3-5 times in your project.  People will fight you on this, but stand firm in your mind.  Remember, DRY is the first thing we teach programmers--not because it's the most important--but because it's the EASIEST thing to teach.  It's also the most dangerous thing to teach the young & inexperienced, the impressionable, & the non-critical thinkers, because it's the easiest thing to abuse.  So be careful.  And again, actual-use >> theoretical-reuse.

If you're creating a library or framework, then that ***IS*** your project, and so the same usual rules apply.  This is how you'd define the templates that will be used by the library/framework's users.  Be especially mindful of the fact that the `define:` directive is a compile-time directive, so it will only be available to compile-time & build-time templates, not at run-time.  This means that it will not be available to the `run:` directive.

```handlebars
;;; better-lorem-ipsum-p.compile.hypr-tmpl

;; let's import the `hypr-ncsp` custom-element
;; from the `hypr-cel` library, and use it
;; to define our `better-lorem-ipsum-p`
;; custom-element.
{{import: star-this/hypr-cel/hypr-ncsp :custom-element:}}

{{require: [[lorem_ipsum:S]]}};
  (let words := lorem_ipsum | split :" ": :);
  {{build: [[words:A[S]]]}};
    <better-lorem-ipsum-p>
    {{? [words]}};
      {{. [words]}};
        {{0->{words} [words :A[S]: word n]}};
          {{. [word]}};
            {{1 (n ~= words.length-1)}}; <- "~= :'not equal to'"
              {word}<hypr-ncsp />
            {{0}};
              {word}
            {{/1}};
          {{/.}};
        {{/{words.length}}};
      {{/.}};
    {{/?}};
    </better-lorem-ipsum-p>
  {{/build}};
{{/require}};

{{define: better-lorem-ipsum-p :custom-element:}}
  <script type="module">
  class BetterLoremIpsumP extends HTMLParagraphElement {
    constructor() {
      super();
    }
    connectedCallback() {
      this.innerHTML = this.textContent;
    }
    disconnectedCallback() {
      delete this.textContent;
      this.innerHTML = null;
      delete this.innerHTML;
    }
    attributeChangedCallback(name, oldValue, newValue) {
    }
  }
  customElements.define('better-lorem-ipsum-p', BetterLoremIpsumP);
  </script>
{{/define}}
```

^ [Back to ToC](#table-of-contents)

### Level 5: Macros, Micros, & Selectors

... TBD ...

### Level 6: Web Components

... in rare cases, you may need to use a Web Component in your project, rather than the preferred `:custom-element:` template type. 

**Web Components** = *Custom Elements* + **Shadow DOM** (+ HTML Templates + HTML Imports)

This is how you do it.

... TBD ...

### Level 7: Use it or Lose it
```
(use hypr-tmpl | productivity :now:future: |> increase :(_ * 10) | creativity | status-100x-er | impress-boss-and-coworkers :)::practice-makes-permanent;
```

The final level of this tutorial is to use hypr-tmpl to its fullest extent, and to become a master of it.  This is how you do it: you use it.  You practice using it like you're getting paid to build high-quality, performant, and secure web applications.  You practice using it until it becomes second nature.  You practice using it until you can use it without thinking.  You practice using it until you are proficient beyond belief.  And then you keep practicing.  Because that's how you become a master.  And that's how you become a master of hypr-tmpl.  So when you go to use it, whether to persuade your boss to let you use it in production, or to persuade your coworkers to use it, you will already be able to use it like a true professional who has mastered the tool.  Always remember, "Practice makes permanent:" so skate to where the puck's going, not where it's been.  And if you can't skate, then walk, and if you can't walk, then crawl.  But whatever you do, keep moving forward.  And never, ever give up.  Ever. (Mic drop).
