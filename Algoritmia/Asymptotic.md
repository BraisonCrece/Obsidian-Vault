[Asymptotic analysis](https://en.wikipedia.org/wiki/Asymptotic_analysis) is widely used in Computer Science to describe the [limiting behavior](https://en.wikipedia.org/wiki/Limit_(mathematics)) of algorithm resource use.

We look at functions to see their behavior (often to track resource consumption) **when an input gets arbitrarily large (big).**

But first off, **what is a function**? What does `y = 2x` mean? Let's go _way_ back.

It means this:
 
- Each variable is something we are **tracking**. Here we track `y` and `x`. They can be _anything_ we want them to be
- `y` and `x` are intimately related by our [function](https://en.wikipedia.org/wiki/Function_(mathematics)) that we defined
- **Each time we gain 1 unit of x, we gain 2 units of y**
- `x` is **input** and `y` is **output** (Remember: we can have more than 1 input variable influencing `y`)

So what is `y` and what is `x` (or our series of inputs if we had more)? Whatever we want them to be.

**Commonly:**

- We call the `x` an `n` instead (if we have a single input)
- Then `y` can measure the comparisons, exchanges, iterations, etc. of an algorithm
- `n` can be the length of an array, the length of a string, etc.
- If we have more than 1 input (like if we have 2 input strings) we may use `m` and `n` to precisely denote each respective input.

**Asymptotics**

In asymptotic analysis, we care about what a function does when `n` is very large.

We take the concrete function (e.g. `y = x`) and extract a "higher meaning" from it. Something that we can use to relate it into a **family** of other functions.

At very large inputs, many functions look very similar zoomed far out.

But asymptotic behaviour only tells part of the true picture of what is happening, it is a very theoretical measure and in day-to-day programming you will be more concerned with [real elapsed time](https://en.wikipedia.org/wiki/Elapsed_real_time).


# **Asymptotic Bounding**

---

In asymptotic analysis we often want to _bound_ how good or how bad an algorithm's resource use gets [_asymptotically_](https://en.wikipedia.org/wiki/Asymptotic_analysis).

This brings us to the idea of a _best_, _average_, & _worst_ case input.

At our disposal are different bounds:

- Upper bounds (often we see `O( )` "big O", `o( )` "little o", etc.)
- Exact bounds (`Θ( )` "theta" which combines the lower and upper bound)
- Lower bounds (`Ω( )` "big omega", `ω( )` "little omega")

**Often:**

- We will only use the "bigs" (`O( )` "big O" and `Ω( )` "big omega")
- We use Θ "theta" when we want to be really specific about what is happening

Y**ou can use any bound you want for any case** (best, average, worst).

You can also give a very loose `O( )` upper-bound if you aren’t really sure on how bad the worst case can look.

**It just makes more sense to:**

- Use an upper bound (`O( )` "big O") for a worst-case `=>` "How bad can performance get?"
- Use a lower bound (`Ω( )` "big omega") for a best-case `=>` "How good can performance get?"

**But it wouldn't be wrong to:**

- lower bound a worst-case input
- upper bound a best-case input

It'd just tell you less about what you really want to know.