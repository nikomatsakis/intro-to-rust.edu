class: center
name: title
count: false

# Rust! Rust!

.p60[![Ferris](./images/ferris.svg)]

.me[.grey[*by* **Nicholas Matsakis**]]

.footnote[
    Online on Github at [nikomatsakis/ECE290-2023](https://github.com/nikomatsakis/ECE290-2023) -- in [rendered form too!](https://nikomatsakis.github.io/ECE290-2023/#1)
]

---

# Who am I?

.row[
.column40[![Niko with a snake](./images/withsnake.jpg)]
.column60[
* Involved in Rust since 2010
* Co-lead of Rust language design, types teams
* Rust team at Amazon
]
]

???

Hi! My name is Niko Matsakis. I've been working in Rust a long time, and so I expect some of you know who I am already, but for the rest of y'all, let me introduce myself.

I started working on Rust in 2010. I was involved in the very first public release of Rust, 0.1. We've come a long way since those days! As one tiny exmaple, when I started, Rust was 32-bit only. My first contribution, as I recall, was implementing support for 64-bit systems. Good times. Since then, I've been involved in a lot of other things. I was the first lead of the Rust compiler team and was involved in the Rust core team, back when that was a thing. At this point though my primary role is as co-lead of the Rust language design team.

Since 2021, I've been working at Amazon, where I manage our Rust team. This is a team of folks whose full-time job is to contribute to the Rust project, Tokio, and other parts of the ecosystem. As some of you may know, Amazon's been making very heavy use of Rust. At this point, every S3 GET request, every Lambda invocation, and a good number of other things make use of services implemented in Rust. Being at Amazon has been a really interesting experience, since it lets me work very closely with the teams here, figuring out exactly where Rust can help them -- and where it holds them up. We can then take those lessons, mix them with the experiences of others in other contexts, and find ways to make Rust better for everyone.

---

# Rust adoption

.p80[![Rust in batgirl](./images/RustInBatGirl.png)]

???

Of course, the watershed moment was when Rust appeared in the batgirl comic in 2017.
Boom, our job is done!

--

.batgirlcircle[&nbsp;]

???

Oh, and I'd like to callout that she says Rust is not complicated. Thanks batgirl comic authors! 
I like this PR.

---

# Rust adoption

* Rust in the cloud

???

We are seeing Rust in the cloud. Both to build the cloud itself, like at (hello!) Amazon and Microsoft, but also to build things *in* the cloud. A lot of people are finding that writing their cloud apps in Rust is a good choice. It saves them money. It requires less beefy machines and runs more reliably. 

--

* Rust in kernels

???

We are seeing Rust in kernels -- Rust for linux? Amazing. Windows kernel? Amazing. 

Rust was designed to be versatile, usable for high-level and low-level applications alike, and seeing it stretch from the machine kernel up to distributed applications in the cloud is a really cool validation of that design.

--

* Rust in **your** workplace?

???

For more and more people, working in Rust is a reality. 
If you're using Rust in your workplace, I'd love to hear about it after the talk.
But maybe you're not yet, and you'd like to be.
Or maybe you yourself are not convinced, and you'd like to know what all the fuss is about.


---

# Why do people *start* using Rust?

**Efficiency:**

> **Our decision to use Rust was about tail latency.** We didn’t want the tail latency. The better tail latencies with Rust were crazy significant.
> <br><br>
> .small[— AWS Lambda Principal Engineer, 2022]

???

To answer that, I think you have to break the question into two questions. The first one is why do they *start* using Rust. And for many, the answer is "performance" -- that's what gets people's attention. Look at this quote from a Lambda PE: when Lambda was building their new sandbox assignment service, they were primarily looking to achieve tighter tail latencies, and they found that since Rust didn't require a garbage collector, it offered a big advantage in that regard.

Or, to put it in picture form...

---

# Why do people *start* using Rust?

.center[
.p60[![Wiley prepares to fly](./images/wiley-batman-prepare.gif)]
]

???

...they wanted to feel like this! And it's true that, on average, Rust programs tend to perform really, really well. At the same time, the programming language is only a small piece of whether a program runs quikly. My experience is that while performance may get people *interested* in Rust, what makes them *love* Rust is something different.

---

# Why do people *keep* using Rust?

**Reliability:**

> **If we were to make it again, the other reason is correctness.** A lot of our worst outages have been about unexpected exceptions. In Rust, every error response is enumerated in the type system and there’s the right syntactical goodness to handle it without being heavy weight. That is really nice.
> <br><br>
> .small[— AWS Lambda Principal Engineer, 2022 (same interview)]

???

Here is that same Lambda PE, continuing their statement. See what he is saying here? What they found as they built things in Rust was that, yes, predictable performance is great, but the *real* win comes from the fact that things tend to "just work" more often than not.

---

# Why do people *keep* using Rust?

.center[.p60[![Wiley flies](./images/wiley-batman-flies.gif)]]

???

So, something like this, in picture form.

---

# Why do people *keep* using Rust?

.page-center[
> My biggest compliment to Rust is that **it's boring**, and this is an amazing
compliment.
> <br><br>
> .small[— Chris Dickinson, engineer at NPM, 2019]
]

.footnote[From a [2019 Rust whitepaper](https://www.rust-lang.org/static/pdfs/Rust-npm-Whitepaper.pdf).]

???

This is something we've been hearing a long time. Here is a quote from a whitepaper we wrote in the Rust open source org, around 2019, talking about NPM's adoption of Rust. You can see this engineer said what he likes about Rust is that it's *boring* -- he isn't getting "exciting" pages in the middle of the night.

---

# Efficiency *requires* Reliability

.center[.p60[![Wiley prepares to fly](./images/wiley-batman-prepare.gif)]]

???

But really, these two things, efficiency and reliability, they are not in conflict. In fact, they require each other. After all, for the most part, efficiency doesn't come fro mthe programming language. It comes from architectural decisions that you make in developing the code. So why don't people just make these same architectures using some other language?

---

# Efficiency *requires* Reliability

.center[.p60[![Wiley crashes and burns](./images/wiley-batman-fail.gif)]]

???

The answer is: experience. They've tried, and they found that when they build really ambitious designs, they tend to fall over. This is very much true in C++, where you run into segmentation faults and the like, but it's also true in languges with a garbage collector. In that case, it might be data races, or weird problems caused by mutating some shared structures.

---

.page-center[
> We had a lot of ideas to improve performance, but we were hesitant to introduce them into critical systems given the risk of subtle bugs. With Rust, we achieved double-digit percentage performance improvements. **Rust’s type system gave us the confidence to make those optimizations without worry.**
> <br/><br/>
> .small[— Seth Markle, S3 Senior Principal Engineer, 2022]
]

???

Here is a quote from Seth Markle, talking about the S3 BlobAssembler library. What they found was, after rewriting in Rust, they were able to do a bunch of optimizations they had wanted to do for a long time, but which they were too afraid to do in the old codebase.

I have a theory for why this is. Rust's goal was to have the compiler detect the memory safety errors in your code and flag them ahead of time. But **compilers are not very smart**. I mean, hey, we didn't have ChatGPT yet. So, to make this work, we have a type system that makes you structure your code in a way that correctness is obvious. It pushes you to a shallower structure, with fewer dense object graphs and more explicit parameters and connections between code. Without that, the compiler just can't understand your code.

But here's the catch: **neither can you**.  I mean, maybe you can when you are writing it. But can you understand it when it's 2am and you got woken up with an urgent bug report? Or, perhaps more importantly, can your *intern* understand it? Will they remember all the invariants they have to maintain to get it working right?


---
# Let's tell a story

.p30[![Barbara](./images/Barbara.png)]

???

This is Barbara. She's an SDE3 working here at Amazon.

---
name: thumbnails

# Let's tell a story

```rust
fn make_thumbnails(images: &[Image]) -> Vec<Image> {
    images
        .iter()
        .map(|image| image.make_thumbnail())
        .collect()
}
```

.abspos.left30.top350.p30[![Barbara](./images/Barbara.png)]

???

One day, Barbara is reading over some code that creates thumbnails for a vector of images in the application.

---
template: thumbnails
.arrow.abspos.left400.top90.rotSW[![Arrow](./images/Arrow.png)]

---
template: thumbnails
.line2[![Arrow](./images/Arrow.png)]

---
template: thumbnails
.line3[![Arrow](./images/Arrow.png)]

.abspos.left500.top165[
```rust
// impl Iterator<Item = &Image>
```
]

--

.arrow.abspos.left600.top145.rotS[![Arrow](./images/Arrow.png)]

--

.arrow.abspos.left730.top145.rotS[![Arrow](./images/Arrow.png)]

---
template: thumbnails
.line4[![Arrow](./images/Arrow.png)]

.abspos.left500.top195[
```rust
// impl Iterator<Item = Image>
```
]

--

.arrow.abspos.left250.top175.rotSW[![Arrow](./images/Arrow.png)]
--

.arrow.abspos.left450.top175.rotSW[![Arrow](./images/Arrow.png)]

--

.arrow.abspos.left730.top175.rotS[![Arrow](./images/Arrow.png)]

---
template: thumbnails
.line5[![Arrow](./images/Arrow.png)]

.abspos.left500.top220[
```rust
// ?
```
]

--

.arrow.abspos.left550.top150.rotNW[![Arrow](./images/Arrow.png)]

--

.abspos.left500.top220[
```rust
// Vec<Image>
```
]

---

name: make-thumbnails-at-top

```rust
fn make_thumbnails(images: &[Image]) -> Vec<Image> {
    images
        .iter()
        .map(|image| image.make_thumbnail())
        .collect()
}
```

---

template: make-thumbnails-at-top

.abspos.left650.top300.p30.fliplr[![Barbara](./images/Barbara.png)]

.abspos.left300.top350[
.speech-bubble.barbara.right[
Did you know that iterators <br>
are just an ordinary library?
]]

---

template: make-thumbnails-at-top
name: with-translated-code

```rust
fn make_thumbnails(images: &[Image]) -> Vec<Image> {
    let mut i = 0;
    let l = images.len();
    let mut output = Vec::with_capacity(l);
    while i < l {
        output.push(images[i].make_thumbnail());
        i += 1;
    }
    output
}
```

---

template: with-translated-code

.arrow.abspos.left430.top300.rotSW[![Arrow](./images/Arrow.png)]

---

template: with-translated-code

.arrow.abspos.left340.top420.rotNW[![Arrow](./images/Arrow.png)]

---

```rust
fn make_thumbnails(images: &[Image]) -> Vec<Image> {
    images
        .iter()
        .map(|image| image.make_thumbnail())
        .collect()
}
```

```rust
fn make_thumbnails(images: &[Image]) -> Vec<Image> {
    let mut i = 0;
    let l = images.len();
    let mut output = Vec::with_capacity(l);
    while i < l {
        output.push(unsafe { images.get_unchecked(i).make_thumbnail() });
        i += 1;
    }
    output
}
```

.arrow.abspos.left500.top420.rotNW[![Arrow](./images/Arrow.png)]

--

.abspos.left650.top450.p30.fliplr[![Barbara](./images/Barbara.png)]

.abspos.left300.top550[
.speech-bubble.barbara.right[
Which would *you* rather write?
]]

---

template: thumbnails

.abspos.left300.top415[
.speech-bubble.left.barbara[
*Oh hey, I can make this faster!*
]]

---
name: thumbnailspar

# Let's tell a story

```rust
fn make_thumbnails(images: &[Image]) -> Vec<Image> {
    images
        .par_iter()
        .map(|image| image.make_thumbnail())
        .collect()
}
```

.abspos.left30.top350.p30[![Barbara](./images/Barbara.png)]

.line3[![Arrow](./images/Arrow.png)]

---
template: thumbnailspar

.abspos.left300.top415[
.speech-bubble.left.barbara[
*Rayon would be perfect for this!*
]]

---
.page-center[
.huge[Time passes...]
]

---
name: meetalan
# Let's tell a story

```rust
fn make_thumbnails(images: &[Image]) -> Vec<Image> {
    images
        .par_iter()
        .map(|image| image.make_thumbnail())
        .collect()
}
```

.abspos.left30.top350.p30[![Barbara](./images/Barbara.png)]

.abspos.left500.top350.p30[![Alan](./images/Alan.png)]

???

So some time later, Barbara has an intern Alan.

---
template: meetalan

.abspos.left300.top415[
.speech-bubble.left.barbara[
Your job is to<br>add telemetry
]]

--

.abspos.left420.top550[
.speech-bubble.right.alan[
OK!
]]

???

Alan's job is to add telemetry to this product.
In particular, they want to count how many thumbnails they made.

---
name: thumbnailsbug

# Let's tell a story

```rust
fn make_thumbnails(images: &[Image]) -> Vec<Image> {
    let mut counter = 0;
    let vec = images
        .par_iter()
        .map(|image| {
            counter += 1; 
            image.make_thumbnail()
        })
        .collect();
    log(counter);
    vec
}
```

.abspos.left500.top350.p30[![Alan](./images/Alan.png)]

???

So some time later, Barbara has an intern Alan.
Alan's job is to add telemetry to this product.
In particular, they want to count how many thumbnails they made.

---
template: thumbnailsbug

.line2[![Arrow](./images/Arrow.png)]

.abspos.left170.top475[
.speech-bubble.right.alan[
Let's see, I'll need a counter...
]]

---
template: thumbnailsbug

.line6[![Arrow](./images/Arrow.png)]

.abspos.left170.top475[
.speech-bubble.right.alan[
...add 1 for each image...
]]

---
template: thumbnailsbug

.line10[![Arrow](./images/Arrow.png)]

.abspos.left170.top475[
.speech-bubble.right.alan[
...and log it for telemetry. Done!
]]

---
template: thumbnailsbug

.abspos.left250.top350.p30.fliplr[![Barbara](./images/Barbara.png)]

.abspos.left280.top440[
.thought.barbara.bubble1[&nbsp;]
]

.abspos.left310.top410[
.thought.barbara.bubble2[&nbsp;]
]

.abspos.left340.top390[
.thought.barbara.bubble3[&nbsp;]
]

.abspos.left25.top475[
.speech-bubble.barbara[
*Gotta finish that PR-FAQ.*
]]

--

.abspos.left25.top570[
.speech-bubble.barbara.right[
Looks great! Ship it!
]]

--

.line6[![Arrow](./images/Arrow.png)]

---
.page-center[
![rewind](./images/rewind.gif)
]

---
template: thumbnailsbug

.line10[![Arrow](./images/Arrow.png)]

.abspos.left170.top475[
.speech-bubble.right.alan[
...and log it for telemetry. Done!
]]

---
template: thumbnailsbug
name: thumbnailsbugferris

.line6[![Arrow](./images/Arrow.png)]

.abspos.left235.top350.p60[
![Ferris](./images/rustacean-flat-gesture.png)
]

.abspos.left25.top570[
.speech-bubble.topright.ferris[
Woah there! What's this?
]]

---
template: thumbnailsbug

.line6[![Arrow](./images/Arrow.png)]

.abspos.left235.top350.p60[
![Ferris](./images/rustacean-flat-gesture.png)
]

.abspos.left150.top570[
.speech-bubble.right.alan[
Gee, thanks Ferris! My hero!
]]

---
.page-center[
![rewind](./images/rewind.gif)
]

---

template: thumbnailsbugferris

---
template: thumbnailsbug
name: stupid-compiler

.abspos.left500.top350.p30[![Alan is sad](./images/Alan-Sad.png)]

---
template: stupid-compiler

.abspos.left350.top475[
.speech-bubble.right.alan[
Stupid compiler.
]]

.abspos.left300.top580[
.speech-bubble.right.alan[
Help me!
]]

---
template: stupid-compiler

.abspos.left250.top350.p30.fliplr[![Barbara](./images/Barbara.png)]

.abspos.left75.top475[
.speech-bubble.barbara.right[
Ah, yeah, this.<br>
Use `AtomicUsize`.
]]


---
name: thumbnailsfixed

# Let's tell a story

```rust
fn make_thumbnails(images: &[Image]) -> Vec<Image> {
    let counter = AtomicUsize::new();
    let vec = images
        .par_iter()
        .map(|image| {
            counter.fetch_add(1, Ordering::SeqCst);
            image.make_thumbnail()
        })
        .collect();
    log(counter.load(Ordering::SeqCst));
    vec
}
```

.abspos.left500.top350.p30[![Alan](./images/Alan.png)]

---
template: thumbnailsfixed

.line2[![Arrow](./images/Arrow.png)]

---
template: thumbnailsfixed

.line6[![Arrow](./images/Arrow.png)]

---
template: thumbnailsfixed

.line10[![Arrow](./images/Arrow.png)]

---
template: thumbnailsfixed

.abspos.left320.top470[
.speech-bubble.right.alan[
Welp, now I know!
]]

--

.abspos.left200.top200[
.p100[![GI Joe](./images/knowing-is-half-the-battle.gif)]
]

---

# Takeaways

* High-level code, low-level performance

--
* Extensible, library oriented

--
* Reliability makes high performance maintainable

---

# On extensibility

- Easy to **expose** a high-performance API
- Hard to **help users control it**

.center[
![cast spell, burn self](images/firespell.gif)
]

---

name: rust-tools

# Rust's tools

* Ownership and borrowing
    * Reason about permissions
* Enums
    * Enumerate states and match against them
* Traits
    * Categorize types, define generic interfaces
* Procedural macros
    * Generate boilerplate

---

template: rust-tools

.arrow.abspos.left10.top115[![Arrow](./images/Arrow.png)]

---

# Ownership and borrowing

.center[.p80[![Office space stapler](./images/office-space.gif)]]

---

name: every-language-lets-you-give

# Every language lets you give

```go
func foo() {
  honey := Honey { .. }
  channel <- honey
  honey.eat();
}
```

.gologo[
<img src="./images/Go-Logo_Black.svg" alt="Go logo" width="50px" height="50px">
]

.footnote[
    [Images from original Winnie the Pooh, retouched by Paul K.](https://www.flickr.com/photos/bibliodyssey/3066783212/in/album-72157610318114895/)
]

---

template: every-language-lets-you-give

.pooh[
![Person on the left](./images/Winnie-the-Pooh.png)
]

.line1[
![Point at `func foo`](./images/Arrow.png)
]


---

template: every-language-lets-you-give

.pooh[
![Person on the left](./images/Winnie-the-Pooh.png)
]

.line2[
![Point at `Gift { }`](./images/Arrow.png)
]


--

.honey-left[🍯]

---

template: every-language-lets-you-give

.pooh[
![Person on the left](./images/Winnie-the-Pooh.png)
]

.line3[
![Point at `channel <- regalo`](./images/Arrow.png)
]

.honey-left[🍯]

--

.piglet[
![Person on the right](./images/Piglet.png)
]

---

template: every-language-lets-you-give

.pooh[
![Person on the left](./images/Winnie-the-Pooh.png)
]

.line3[
![Point at `channel <- regalo`](./images/Arrow.png)
]

.honey-center[🍯]

.piglet[
![Person on the right](./images/Piglet.png)
]

---

template: every-language-lets-you-give

.pooh[
![Person on the left](./images/Winnie-the-Pooh.png)
]

.piglet[
![Person on the right](./images/Piglet.png)
]

.honey-center[🍯]

.line4[
![Point at `pot.eat`](./images/Arrow.png)
]

---

template: every-language-lets-you-give

.pooh[
![Person on the left](./images/Winnie-the-Pooh.png)
]

.piglet[
![Person on the right](./images/Piglet.png)
]

.honey-center-tip[🍯]

.line4[
![Point at `pot.eat`](./images/Arrow.png)
]

---

template: every-language-lets-you-give

.pooh[
![Person on the left](./images/Winnie-the-Pooh.png)
]

.piglet[
![Person on the right](./images/Piglet.png)
]

.honey-center-tip[🍯]

.col-right[
```go
// The other goroutine
honey := <- channel
honey.eat()
```
]

.line3r[
![Point at `regalo.open`](./images/Arrow.png)
]

.piglet[
![Person on the right](./images/Piglet.png)
]

--

.piglet[
![Person on the right](./images/Piglet-sad.png)
]

---

# What went wrong?

.piglet[
![Person on the right](./images/Piglet-sad.png)
]

Two ingredients:

- Mutation
- Sharing

.footnote[
    [Images from original Winnie the Pooh, retouched by Paul K.](https://www.flickr.com/photos/bibliodyssey/3066783212/in/album-72157610318114895/)
]

--

Rust's solution:

- Support sharing and mutation
  - but **not at the same time**

---

name: rust-lets-you-take-away

# Rust lets you take away

```rust
fn main() {
  let honey = Honey::new();
  channel.send(honey);
  honey.eat();
}
```

.footnote[
    [Images from original Winnie the Pooh, retouched by Paul K.](https://www.flickr.com/photos/bibliodyssey/3066783212/in/album-72157610318114895/)
]

---

template: rust-lets-you-take-away

.line1[![Highlight `fn main`](./images/Arrow.png)]

.pooh[![Person on the left](./images/Winnie-the-Pooh.png)]

---

template: rust-lets-you-take-away

.line2[![Highlight `let regalo`](./images/Arrow.png)]

.pooh[![Person on the left](./images/Winnie-the-Pooh.png)]

.honey-left[🍯]

---

template: rust-lets-you-take-away

.line3[![Highlight the call to eat](./images/Arrow.png)]

.pooh[
![Person on the left](./images/Winnie-the-Pooh.png)
]

.honey-left[🍯]

---

name: rust-lets-you-take-away-2
template: rust-lets-you-take-away

.col-right[
```rust
impl<T> Channel<T> {
  fn send(&mut self, data: T) {
    ...
  }
}
```
]

---

template: rust-lets-you-take-away-2

.line1r[![Highlight the eat fn](./images/Arrow.png)]
.honey-left[🍯]
.pooh[![Person on the left](./images/Winnie-the-Pooh.png)]
.piglet[![Person on the right](./images/Piglet.png)]

---

template: rust-lets-you-take-away-2

.line1-impl[![Highlight `impl`](./images/Arrow.png)]
.honey-left[🍯]
.pooh[![Person on the left](./images/Winnie-the-Pooh.png)]
.piglet[![Person on the right](./images/Piglet.png)]

---

template: rust-lets-you-take-away-2

.line1-generics[![Highlight `<T>`](./images/Arrow.png)]
.honey-left[🍯]
.pooh[![Person on the left](./images/Winnie-the-Pooh.png)]
.piglet[![Person on the right](./images/Piglet.png)]

---

template: rust-lets-you-take-away-2

.line2-data[![Highlight `data: T`](./images/Arrow.png)]
.honey-left[🍯]
.pooh[![Person on the left](./images/Winnie-the-Pooh.png)]
.piglet[![Person on the right](./images/Piglet.png)]

--

.line3-regalo[![Highlight `regalo`](./images/Arrow.png)]

---

template: rust-lets-you-take-away-2

.line2-data[![Highlight `data: T`](./images/Arrow.png)]
.line3-regalo[![Highlight `regalo`](./images/Arrow.png)]
.honey-right[🍯]
.pooh[![Person on the left](./images/Winnie-the-Pooh.png)]
.piglet[![Person on the right](./images/Piglet.png)]

---

template: rust-lets-you-take-away

.line4[![Highlight `regalo.open`](./images/Arrow.png)]
.pooh[![Person on the left](./images/Winnie-the-Pooh.png)]

---

template: rust-lets-you-take-away

.line4[![Highlight `regalo.open`](./images/Arrow.png)]

```
error[E0382]: use of moved value: 'honey'
  --> src/main.rs:13:4
     |
  12 |    channel.send(honey)
     |                 ----- value moved here
  13 |    honey.eat();
     |    ^^^^^ value used here after move
```

--

.abspos.left650.top300.p30.fliplr[![Barbara](./images/Barbara.png)]

.abspos.left300.top350[
.speech-bubble.barbara.right[
Did you know that *channels* <br>
are just an ordinary library?
]]

---

.page-center[
> We recently found a bug in our previous service written in Java. **It was caused by us using a closed object (like a dropped object in Rust, if we want to compare)**. Wouldn’t happen in Rust.
> <br><br>
> .small[— Lambda engineer, 2022]
]

---

.page-center[
> Drop semantics are soooo nice for correctness. **We make heavy, heavy use of the pattern of vending tokens that carry some permission or capability that is released on drop.**
> <br><br>
> .small[— S3 engineer, 2022]
]

---

template: rust-tools

.arrow.abspos.left10.top190[![Arrow](./images/Arrow.png)]

---

name: goal-status

# Enums

```rust
enum Status {
    Red,
    Green,
}
```

---

template: goal-status

.line1[![Arrow](./images/Arrow.png)]

---

template: goal-status

.line2[![Arrow](./images/Arrow.png)]

---

template: goal-status

.line3[![Arrow](./images/Arrow.png)]

---

template: goal-status

```rust
struct Goal {
    status: Status,
}
```

.arrow.abspos.left10.top315[![Arrow](./images/Arrow.png)]

---

template: goal-status

```rust
match goal.status {
    Status::Red => {
        ...
    }
}
```

.abspos.left20.top305[❌]

---

template: goal-status

```rust
match goal.status {
    Status::Red => {
        ...
    }

    Status::Green => {
        ...
    }
}
```

.abspos.left20.top305[✅]

---

name: enum3

# Enums

```rust
enum Status {
    Red,
    Yellow,
    Green,
}
```

```rust
match goal.status {
    Status::Red => {
        ...
    }

    Status::Green => {
        ...
    }
}
```

--

.line3[![Arrow](./images/Arrow.png)]

--

.abspos.left20.top335[❌]

---

name: option

# Option

```rust
enum Option<T> {
    Some(T),
    None,
}
```

---
template: option

.line1[![Arrow](./images/Arrow.png)]

---
template: option

.line2[![Arrow](./images/Arrow.png)]

---
template: option

.line3[![Arrow](./images/Arrow.png)]

---
template: option

```rust
struct Goal {
    status: Option<Status>,
}
```

.arrow.abspos.left10.top315[![Arrow](./images/Arrow.png)]

---

name: result

# Result

```rust
enum Result<O, E> {
    Ok(O),
    Err(E),
}
```

---

template: result

.line2[![Arrow](./images/Arrow.png)]

---

template: result

.line3[![Arrow](./images/Arrow.png)]

---

name: result-with-fn
template: result

```rust
fn read_data(path: &Path) -> Result<Data, std::io::Error> {
    ...
}
```

---

template: result-with-fn

.arrow.abspos.top250.left400.rotSW[![Arrow](./images/Arrow.png)]

---

template: result-with-fn

.arrow.abspos.top250.left450.rotSW[![Arrow](./images/Arrow.png)]

---

template: result-with-fn

.arrow.abspos.top250.left600.rotSW[![Arrow](./images/Arrow.png)]

---

template: rust-tools

.arrow.abspos.left10.top260[![Arrow](./images/Arrow.png)]

---

name: traits

# Traits

```rust
trait Iterator {
    type Item;

    fn next(&mut self) -> Option<Self::Item>;
}
```

---

template: traits

.arrow.abspos.top100.left190.rotSW[![Arrow](./images/Arrow.png)]

---

template: traits

.arrow.abspos.top150.left10[![Arrow](./images/Arrow.png)]

---

template: traits

.arrow.abspos.top200.left10[![Arrow](./images/Arrow.png)]

---

template: traits

.arrow.abspos.top170.left450.rotSW[![Arrow](./images/Arrow.png)]

---

# Generic functions: v1

```rust
fn take_one<T>(t: T) -> Option<T::Item>
where
    T: Iterator,
{
    /* ... */
}
```

--

.line3[![Arrow](./images/Arrow.png)]

---

# Generic functions: v2

```rust
fn take_one<T, I>(t: T) -> Option<I>
where
    T: Iterator<Item = I>,
{

}
```

.line3[![Arrow](./images/Arrow.png)]

---

# Generic functions: v3

```rust
fn take_one<I>(t: impl Iterator<Item = I>) -> Option<I> {

}
```

.arrow.abspos.left350.top90.rotSW[![Arrow](./images/Arrow.png)]


---

# Modeling concepts

```rust
unsafe trait Send {
    /* safe to give ownership to another thread */
}

unsafe trait Sync {
    /* safe to be referenced from multiple threads at once */
}
```

---

template: thumbnailsbug
name: thumbnailsbugferris

.line6[![Arrow](./images/Arrow.png)]

.abspos.left235.top350.p60[
![Ferris](./images/rustacean-flat-gesture.png)
]

.abspos.left25.top570[
.speech-bubble.topright.ferris[
This is how I knew this code was suspicious!
]]

.abspos.left600.top570[
.speech-bubble.alan[
Right on, dude!
]]

---

# Curious to learn more about that?

.center[
<iframe width="560" height="315" src="https://www.youtube.com/embed/wXoY91w4Agk?si=SnN2JdzjKconpq03" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>
]

Search for [Rust: Putting Ownership to Use](https://www.youtube.com/watch?v=wXoY91w4Agk) from Curry On 2017

---

template: rust-tools

.arrow.abspos.left10.top330[![Arrow](./images/Arrow.png)]

---

# Trait Clone

```rust
trait Clone {
    fn clone(&self) -> Self;
}
```

--

.line2[![Arrow](./images/Arrow.png)]

---

name: impl-clone

# Implementing clone is annoying

```rust
struct Data {
    x_s: Vec<u32>,
    y_s: Vec<u32>,
}

impl Clone for Data {
    fn clone(&self) -> Data {
        Data {
            x_s: self.x_s.clone(),
            y_s: self.y_s.clone(),
        }
    }
}
```

---

template: impl-clone

.arrow.abspos.left10.top260[![Arrow](./images/Arrow.png)]

---

template: impl-clone

.arrow.abspos.left10.top350[![Arrow](./images/Arrow.png)]

---

template: impl-clone

.arrow.abspos.left10.top380[![Arrow](./images/Arrow.png)]

---

# Let the compiler do it for you

```rust
#[derive(Clone)]
struct Data {
    x_s: Vec<u32>,
    y_s: Vec<u32>,
}
```

--

.line1[![Arrow](./images/Arrow.png)]

---

name: custom-derive

# Why stop there?

```rust
use serde::{Serialize, Deserialize};

#[derive(Clone, Serialize, Deserialize)]
struct Data {
    x_s: Vec<u32>,
    y_s: Vec<u32>,
}
```

---

template: custom-derive

.line1[![Arrow](./images/Arrow.png)]

---

template: custom-derive

.arrow.abspos.left300.top150.rotSW[![Arrow](./images/Arrow.png)]

---

name: proc-macros

# Procedural macros

```rust
use duchess::{java, prelude::*};

duchess::java_package! {
    package java.lang;

    public class java.lang.Object {
        public java.lang.Object();
        public native int hashCode();
        public boolean equals(java.lang.Object);
        public java.lang.String toString();
        public final native void notify();
        public final native void notifyAll();
    }
}
```

.footnote[
    [Duchess crate](https://duchess-rs.github.io/duchess/)
]

---

template: proc-macros

.arrow.abspos.left10.top170[![Arrow](./images/Arrow.png)]

---

template: proc-macros

.arrow.abspos.left290.top145.rotSW[![Arrow](./images/Arrow.png)]

---

# Coming up

.huge[.huge[🔭]] .metelescope[.p50[![me](./images/ferris.svg)]]

---

template: rust-tools

# Thank you! 💜

---

# Extra slides


---

name: range

```rust
struct Range { from: u32, to: u32 }

impl Iterator for Range {
    type Item = u32;

    fn next(&mut self) -> Option<Self::Item> {
        let from = self.from;
        if from < self.to {
            self.from += 1;
            Some(from)
        } else {
            None
        }
    }
}
```

---

template: range

.arrow.abspos.top15.left10[![Arrow](./images/Arrow.png)]

---

template: range

.arrow.abspos.top65.left10[![Arrow](./images/Arrow.png)]

---

template: range

.arrow.abspos.top90.left10[![Arrow](./images/Arrow.png)]

---

template: range

.arrow.abspos.top140.left10[![Arrow](./images/Arrow.png)]

---

# The Rust 2024 Edition is coming

![Dancing Ferris](./images/dancing-ferris.gif)

???

---

# Rust editions in a nutshell

"Breaking changes where no code breaks"

* Every crate declares its *Rust edition* (e.g., `edition = "2024"`)
* Compiler understands *all* editions
* Editions interoperate (we never split the ecosystem)
* Adopting the newest edition is automated
    * Just run `cargo fix --edition`

--

What all this means:

* Upgrade on **your schedule, not ours**
    * (and not your dependencies' either)

---

# Type system improvements

* ▰▰▰▰▰ Generic associated types 
* ▰▰▰▰▱ Async functions in traits ()
* ▰▰▰▱▱ Type alias impl trait
* ▰▰▱▱▱ Impl trait "everywhere"
* ▰▱▱▱▱ Implied bounds

--

.abspos.left600.top200[.huge[.huge[🤔]]]
