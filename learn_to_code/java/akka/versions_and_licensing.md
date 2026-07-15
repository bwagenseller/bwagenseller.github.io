# Akka: Versions and Licensing

> Cross-cutting reference page. The version/license story is **orthogonal** to the API you write
> ([Classic](/learn_to_code/java/akka/classic/) vs [Typed](/learn_to_code/java/akka/typed/)), so it
> lives here once instead of being repeated on every topic page.  

# Two Axes, Not One

The single most useful thing to keep straight: "newer Akka" can mean **two different things**, and they
do **not** line up at the same version.  

* <font color="purple">API axis</font> - **Classic** (`AbstractActor`, untyped `ActorRef`) vs **Typed** (`Behaviors`, `ActorRef<T>`). This is what changes how you write code.  
* <font color="purple">License axis</font> - which artifact you depend on and whether it is free. This is a dependency/legal concern, not a code one.  

> Do **not** organize notes as "pre-2.6 vs post-2.6" - that conflates the two axes. The meaningful
> breaks are *Classic vs Typed* (for code) and *Apache vs BSL* (for licensing).  

# API Axis: Classic vs Typed

* **Classic** is the original untyped API. It remains fully supported and widely used.  
* **Typed** (a.k.a. "Akka Typed") expresses the same concepts with `Behaviors` and `ActorRef<T>`, giving compile-time message-type safety.  
* **Key fact:** Typed did **not** replace Classic at a version boundary. **Akka 2.6 supports BOTH** and merely made Typed the *recommended* default. So 2.6 is where Typed *matured*, not where Classic died - you can write 2.6 code in either style.  

The concepts (singleton, sharding, WakeUp/rehydration, passivation, address-qualified refs) are
**identical** across both APIs - only syntax differs. That is exactly why this notes area splits into a
shared [Concepts](/learn_to_code/java/akka/concepts/) branch plus thin
[Classic](/learn_to_code/java/akka/classic/) and [Typed](/learn_to_code/java/akka/typed/) branches.  

# License Axis: Akka <= 2.6, Akka >= 2.7, and Pekko

| Artifact | Versions | License | Notes |
|---|---|---|---|
| **Akka** | <= 2.6.x | <font color="green">Apache 2.0</font> | Free, no license key. `2.6.21` is the last Apache Akka. |
| **Akka** | >= 2.7 (Sept 2022) | <font color="red">BSL</font> (Business Source License) | Source-available, but commercial use past a revenue threshold needs a paid Lightbend license. |
| **Apache Pekko** | forked from Akka 2.6.x | <font color="green">Apache 2.0</font> | Community fork of the last Apache Akka, now an Apache project. Package rename `akka.*` -> `org.apache.pekko.*`. Same APIs (Classic + Typed), no key. |

## What This Means In Practice

* Building on **Akka 2.6.x** (or **Pekko**) = Apache-licensed, no key, no commercial restriction.  
* Building on **Akka 2.7+** = BSL; fine for dev/eval, but commercial production use may require a paid license.  
* **Pekko** is the drop-in escape hatch if you want to stay Apache-licensed but keep getting updates: it is "2.6 continued" with a package rename.  

# One-Line Mental Model

> **Two axes, not one.** *API:* Classic <-> Typed (both live in 2.6+). *License:* Akka <= 2.6 = Apache, Akka >= 2.7 = BSL, **Pekko** = Apache fork of 2.6.  

# See Also

- [Concepts branch](/learn_to_code/java/akka/concepts/)  
- [Classic branch](/learn_to_code/java/akka/classic/)  
- [Typed branch](/learn_to_code/java/akka/typed/)  
