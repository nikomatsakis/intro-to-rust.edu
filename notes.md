major points

* Every org has to make their own choices
* 
* I am not here

---

> We had a lot of ideas to improve performance, but we were hesitant to introduce them into critical systems given the risk of subtle bugs. With Rust, we achieved double-digit percentage performance improvements. Rust’s type system gave us the confidence to make those optimizations without worry.
> <br/>
> <br/>
> — Seth Markle, S3 Senior Principal Engineer 

---

“With low input effort, Rust is frequently a nicer starting point in terms of memory usage and thread safety. With conscious effort and experience, you can get many of the same traits from Java, often at much higher effort because you're doing it only once it becomes a problem.” — Anthony Virtuoso, DBS DataPipeline Senior PE

---

“We used Rust to rewrite an agent that runs on every Athena instance. Our new Rust-based agent (still being rolled out) is poised to free up 20% of the total memory in Athena’s fleet, helping to address the #1 cause of customer-facing errors, out-of-memory failures. After that experience, I became a convert, and I look for opportunities to use Rust everywhere now. Still, I wouldn’t recommend it to your average team because the integration with other AWS services is just too rough.” — Anthony Virtuoso, DBS DataPipeline Senior PE

---

> “We knew it would prevent buffer overflow, but we were less aware of the benefits around controlling concurent access to memory. Not as much of a security thing as it is a correctness and bug reduction benefit. It is significantly tougher to write concurrent memory access bugs in Rust because of the ownership model.”
> <br><br>
> — Dylan Marriner, Lambda PE

---

https://broadcast.amazon.com/videos/510782

Alexandru Ene 

https://broadcast.amazon.com/videos/710780

FireTV's journey from Java to Native 

* Cloud dev environments 
    * Efe Karakus - https://phonetool.amazon.com/users/karakuse
    * Rust + WebAssembly
    * “Do you really your SDE1s working in golang”

* This is why Rust 
    * In a nutshell
        * Golang is geared at get you up and going fast
        * Rust is geared at code that is meant to last
    * Reliability —
        * benefits that Amazon teams have seen
        * what Golang doesn’t protect you from
    * Moves
    * Error handling
    * Productivity
        * Get quotes from Valvan, Eric Karulf
        * Bring in alexene + prime video
* Tenets from Mark’s doc
    * 
* Applications
    * client-side — but they may be building a client side process daemon
    * browser-based webassembly ??
    * node-js interop is definitely important
    * potentially Python, but probably less
* Things Chase’s org doesn’t care about
    * whole org is client-side solutions, with few exceptions
    * don’t care about coral or internal interop

Some specific aspects of Go

* zero-based initializers
* defer blocks (vs destructors)
* error handling -- checking it

[ROB PIKE](https://commandcenter.blogspot.com/2012/06/less-is-exponentially-more.html)

There's an unrelated aspect of Go's design I'd like to touch upon: Go was designed to help write big programs, written and maintained by big teams.

What you're given is a set of powerful but easy to understand, easy to use building blocks from which you can assemble—compose—a solution to your problem. It might not end up quite as fast or as sophisticated or as ideologically motivated as the solution you'd write in some of those other languages, but it'll almost certainly be easier to write, easier to read, easier to understand, easier to maintain, and maybe safer.

what makes Rust reliable?


vs:

One difference is null safety

Drop semantics are soooo nice for correctness. We make heavy, heavy use of the pattern of vending tokens that carry some permission or capability that is released on drop. In Java, you are not supposed to rely on the garbage collector to ever do anything. It’s legal for the collector to never collect something. This obviously applies to stuff like lock guards and such. — Grant Slatton

Tools like Loom and Shuttle are awesome. One of my first, big projects on R2D2 was rewriting its concurrent write-back cache / IO scheduler monstrosity. This took months to convince myself it was correct. And it had to be correct, otherwise R2D2 could lose data. Writing such a thing in Rust would literally be trivial due to ownership semantics and the existence of tools like loom and shuttle. — Grant Slatton

XG

* L6, Lambda:
    * I guess for me when I joined the team there weren’t a lot of people on the team using Rust, I think SG was maybe the only person on the team actively also writing Rust. The learning curve was definitely high. It took me many weeks or at least a month before starting to feel productive writing Rust. But then soon after, initially I spent a lot of time fighting the compiler, but that time significantly shrank as the time goes. I personally feel like I spent a lot less time debugging than in other languages. 
        * Speaking of an example, I think recently we found a bug in our previous service written in Java. It was caused by us using a closed object (like a dropped object in Rust, if we want to compare). Wouldn’t happen in Rust.
        * Another service I know using Java as well, the counting service front end, there was a race condition, a multi-threading bug. Some object we’re supposed to copy but we were somehow passing a reference, and some other thread sets the value in the config. That bug also wouldn’t happen in Rust. Wouldn’t need to spend time debugging that.



Dylan Marriner

    * Our decision to use Rust was tail latency. We didn’t want the tail latency. 
        * Ran tests that compared java to go to Rust.
        * The better tail latencies with Rust were crazy significant.
        * We were already doing a rewrite but it is the only way to hit the tail latency numbers we want.
        * That was our initial reason.
        * If we were to make it again, the other reason is correctness. I think Max feels the same way— the error handling. The fact that every error response is enumerated or encapsulated in the type system, and there’s the right syntactical goodness so that you can handle it without being too heavy weight. That is really nice.
            * We’ve run into Runtime Exceptions we didn’t expect. A lot of our worst outages have been about unexpected exceptions.
            * We could have panics but most of what we run into wouldn’t have been a panic, it would’ve been a error type.

Things medium

> producing a mega portable binary is possible but not the default

Things not so great about Rust

> DM: I wouldn’t say it’s super versatile, I wouldn’t use Rust for data exploration, in part because of the data exploration. Anything where I need a fast turnaround time on running my code, I wouldn’t use Rust. 



> We had a lot of ideas to improve performance, but we were hesitant to introduce them into critical systems given the risk of subtle bugs. With Rust, we achieved double-digit percentage performance improvements. Rust’s type system gave us the confidence to make those optimizations without worry.
> <br/> <br/>
> — Seth Markle, S3 Senior Principal Engineer 

---


.page-center[
> Rust helped with a lot of problems we faced with C++. We spent a lot of time with gdb, memory dumps, etc. Moving to Rust helped with those problems. **I’ve only had one crash in the last 3 years and that was because we had some unsafe code in Rust.** That was the only time I had to use gdb with a core dump. Otherwise we have never had a crash in the 3 years of a running.
> <br><br>
> — N.A., SDE3 from EC2 Peregine
]