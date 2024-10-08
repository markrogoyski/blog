# ANONYMOUS FACTORIAL - Y-COMBINATOR

Let's define the typical recursive factorial.

We start with the base case, and then the recursive case.


```python
def fact(n):
    if n <= 1:
        return 1
    return n * fact(n - 1)
```

Let's run it to verify it computes factorial.

```python
for x in (0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10):
    print(x, fact(x))
```

```
0 1
1 1
2 2
3 6
4 24
5 120
6 720
7 5040
8 40320
9 362880
10 3628800
```

The fact that we defined a function with a name, by definition, means that it is not anonymous.
Let's take our function and inline it into the print statement where we currently have the call to the named `fact(x)`.
We can do this by converting it into a lambda.

Lambdas are generally single line functions, so let's first convert our named function into a one liner.

```python
def fact(n):
    return 1 if n <= 1 else n * fact(n - 1)
```

Running this we see it still computes factorial. Now we are ready to inline our factorial as an anoymous lambda function.

Next we inline the lambda.

```python
def fact(n):
    return 1 if n <= 1 else n * fact(n - 1)


for x in (0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10):
    print(x,
          (lambda n: 1 if n <= 1 else n * fact(n - 1))(x)
    )

```

Even though we inlined an annoymous lambda, it still works, because we still have a named function `fact` defined. Let's delete that and try it again.


```python
for x in (0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10):
    print(x,
          (lambda n: 1 if n <= 1 else n * fact(n - 1))(x)
    )
```

Now run this, and we see that the base cases of 0 and 1 still work, but once we get to 2 which requires recursion, it fails with the following error.

```
0 1
1 1
NameError: name 'fact' is not defined
```


This makes complete sense, because we deleted the named function `fact`. However, we still have a call to `fact` in our anonymous lambda. 
This can't work, because fact no longer exists.

We now have a dilemma. How do we recurse when we need to call a function by its name, but that function doesn't exist?

Well, just look a bit to the left, we have a lambda, which is an anonymous function. What if we replaced our call to `fact` with the same lambda, because that lambda _is_ factorial, presuming that the recursive call to factorial works.

```python
for x in (0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10):
    print(x,
          (lambda n: 1 if n <= 1 else n * (
              (lambda n: 1 if n <= 1 else n * fact(n - 1))
          )(n - 1))(x)
    )
```


Now if we run this, the first recursive case of 2 computes, but 3 and onward fail with the same error: `NameError: name 'fact' is not defined`.

```
0 1
1 1
2 2
NameError: name 'fact' is not defined
```


What if we inlined another lambda where we still have `fact` defined?

```python
for x in (0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10):
    print(x,
          (lambda n: 1 if n <= 1 else n * (
              (lambda n: 1 if n <= 1 else n * (
                  (lambda n: 1 if n <= 1 else n * fact(n - 1))
              )(n - 1))
          )(n - 1))(x)
    )
```

```
0 1
1 1
2 2
3 6
NameError: name 'fact' is not defined
```

Let's just keep doing it.

```python
for x in (0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10):
    print(x,
        (lambda n: 1 if n <= 1 else n * (
            (lambda n: 1 if n <= 1 else n * (
                (lambda n: 1 if n <= 1 else n * (
                    (lambda n: 1 if n <= 1 else n * (
                        (lambda n: 1 if n <= 1 else n * fact(n - 1))
                    )(n - 1))
                )(n - 1))
            )(n - 1))
        )(n - 1))(x)
    )

```

```
0 1
1 1
2 2
3 6
4 24
5 120
NameError: name 'fact' is not defined
```

As long as we never compute a recursive case higher than the number of lambdas we defined, it works! But that is not a winning strategy.

Clearly this doesn't work, but perhaps it gives us a hint as how to proceed. We want to call a function at the recursive case, but for that to work, we need it to be defined.
If we define it as a python `def`, then it isn't anonymous. However, what if we just presume that it exists! We can just define another lambda, and say the input to that 
is the recursive factorial function, and then our inner lambda can go ahead and call factorial! At each step of the way, we can pass in another lambda that takes `fact` as its argument
and continue defining inner lambdas that use that passed-in `fact` to compute factorial.

```python
for x in (0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12):
    print(x,
        (lambda fact: lambda n: 1 if n <= 1 else n * fact(n - 1))(
            (lambda fact: lambda n: 1 if n <= 1 else n * fact(n - 1))(
                (lambda fact: lambda n: 1 if n <= 1 else n * fact(n - 1))(
                    (lambda fact: lambda n: 1 if n <= 1 else n * fact(n - 1))(
                        (lambda fact: lambda n: 1 if n <= 1 else n * fact(n - 1))(... WHAT GOES HERE? ...)
                    )
                )
            )
        )(x)
    )
```

We have a problem though, at our inner-most level, we have to actually pass in the last factorial function. It seems like we are back where we started--we have to keep defining
more anonymous lambdas.

For now though, let's just pass in `None` and see what happens.

```python
for x in (0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12):
    print(x,
        (lambda fact: lambda n: 1 if n <= 1 else n * fact(n - 1))(
            (lambda fact: lambda n: 1 if n <= 1 else n * fact(n - 1))(
                (lambda fact: lambda n: 1 if n <= 1 else n * fact(n - 1))(
                    (lambda fact: lambda n: 1 if n <= 1 else n * fact(n - 1))(
                        (lambda fact: lambda n: 1 if n <= 1 else n * fact(n - 1))(None)
                    )
                )
            )
        )(x)
    )

```

We get a similar result, although our error message is slightly different this time.

```
0 1
1 1
2 2
3 6
4 24
5 120
TypeError: 'NoneType' object is not callable
```

Here is the problem with what we have: we have an inner lambda that computes factorial, and it makes a recursive call to `fact`. It relies on the outer lambda to provide it with `fact`.

The problem is that the outer lambda also relies at some point on someone passing it in `fact`, which we don't have, hence the failure.

What if, instead, we defined that missing function--the function that makes us factorial. Does that even make sense? Let's try it.

```python
for x in (0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10):
    print(x,
        (lambda make_fact: make_fact(...?...))(
            (lambda fact: lambda n: 1 if n <= 1 else n * fact(n - 1))
        )(x)
    )

```

We have our our previous `(lambda fact: lambda n: 1 if n <= 1 else n * fact(n - 1))` which, given a factorial function will provide it to the lambda that computes factorial, using
the passed-in factorial function. And now we've wrapped it in `lambda make_fact: make_fact(None)` which is a lambda that if given a function to make a factorial function, will call that function. And the thing we pass into it is our lambda that if given factorial will compute factorial. But what do we pass into `make_fact`, which is our inner lambda that itself needs factorial passed into it?

For now, how about nothing?

```python
for x in (0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10):
    print(x,
        (lambda make_fact: make_fact(None))(
            (lambda fact: lambda n: 1 if n <= 1 else n * fact(n - 1))
        )(x)
    )
```

```
0 1
1 1
TypeError: 'NoneType' object is not callable
```

That didn't work.

Let's think about what we are doing.

- We have a lambda that if given a function that makes a factorial `make_fact`, that if given the function that makes a factorial, will call it.
- The function we are passing in as `make_fact` is our inner lambda which if provided a factorial function, will execute one recursive call of factorial using the factorial functoin that was passed in.

But we didn't pass in a factorial function. That is why the resursive call fails. We actually passed in `None`.

We have to pass in something. We don't have factorial, but we do have a function that makes a factorial function right? Let's pass that in!


```python
for x in (0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10):
    print(x,
        (lambda make_fact: make_fact(make_fact))(
            (lambda fact: lambda n: 1 if n <= 1 else n * fact(n - 1))
        )(x)
    )
```

This doesn't make any sense now. Let's think it through.

- We have a lambda that if given a function that makes a factorial `make_fact`, will call `make_fact` with `make_fact` as its argument.
- The function we are passing in as `make_fact` is our inner lambda which if provided a factorial function, will execute one recursive call of factorial using the factorial function that was passed in.
- The function that was passed in was not factorial, but rather, a function that makes factorial `make_fact`, which is itself, the entire inner lambda `(lambda fact: lambda n: 1 if n <= 1 else n * fact(n - 1))`
- This can't work, because the call to `fact` is actually the call to `make_fact`. `fact` takes an int `n`, whereas `make_fact` is a lambda that takes a function.

To this doesn't work. Our inner call to `fact` is really a call to `make_fact`. We can't pass `make_fact` a number, we have to pass it a function.

What function did we pass `make_fact` in the outer lambda? Well, `make_fact`. And doing that makes a function that computes factorial, right? So, if we call `make_fact`, and pass it `make_fact`, it will return a function that computes factorial right? Let's do try that.

```python
for x in (0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10):
    print(x,
        (lambda make_fact: make_fact(make_fact))(
            (lambda make_fact: lambda n: 1 if n <= 1 else n * (make_fact(make_fact))(n - 1))
        )(x)
    )
```

Could this possibly work?

```
0 1
1 1
2 2
3 6
4 24
5 120
6 720
7 5040
8 40320
9 362880
10 3628800
```

It does!

But how on earth?

Let's analyze this in detail. First we remove the unnecessary bits and focus just on the anonymous factorial function.

```python
(lambda make_fact: make_fact(make_fact))(
    (lambda make_fact: lambda n: 1 if n <= 1 else n * (make_fact(make_fact))(n - 1))
)(x)
```

The inner lambda takes a function `make_fact` as an argument and returns a lambda that computes factorial of n.

When it makes what should be the recursive call to `fact`, it does not have a factorial function. What it has is the argument `make_fact` that was passed in. `make_fact` is a function that will return a function that computes factorial.

`make_fact` is in fact the inner lambda. When we call `make_fact`, we pass in `make_fact` as its argument.

What is `make_fact`? It is the entire inner lambda `(lambda make_fact: lambda n: 1 if n <= 1 else n * (make_fact(make_fact))(n - 1))`.

Calling `make_fact(make_fact` returns the inner lambda, with `make_fact` passed into it as its argument, so it is therefore a function that computes factorial. And when it gets to its recursive call of `make_fact(make_fact)`, it has `make_fact` as an argument to invoke that returns the inner lambda, because `make_fact` itself is the inner lambda.

So that returns a new function that computes factorial, which is the entire inner lambda `(lambda make_fact: lambda n: 1 if n <= 1 else n * (make_fact(make_fact))(n - 1))`.

What is `make_fact`? It is the entire inner lambda `(lambda make_fact: lambda n: 1 if n <= 1 else n * (make_fact(make_fact))(n - 1))`.

Calling `make_fact(make_fact` returns the inner lambda, with `make_fact` passed into it as its argument, so it is therefore a function that computes factorial. And when it gets to its recursive call of `make_fact(make_fact)`, it has `make_fact` as an argument to invoke that returns the inner lambda, because `make_fact` itself is the inner lambda.

Wait a minute...we have an infinite loop!

But how does this process get started? See the outer lambda `(lambda make_fact: make_fact(make_fact))`. We pass into it `make_fact` and call `make_fact(make_fact)`. What is `make_fact`? It is the entire inner lambda `(lambda make_fact: lambda n: 1 if n <= 1 else n * (make_fact(make_fact))(n - 1))`. And what do we pass into that as an argument: `n`!


While this works, it doesn't look like factorial. It's almost something else, which just happens to compute factorial.

Let's refactor it by extracting our the mind-bending inner call to `make_fact(make_fact)` and try to get back our regular factorial method. We can this by wrapping it in yet another lambda. This new lambda will take make_fact, and by executing that with the call to `make_fact(make_fact)` will have created a function that computes one recursive call of factorial, and then we pass that in to our originally-defined double lambda that takes in factorial as an argument and calls it directly with `n - 1`.

```python
for x in (0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10):
    print(x,
        (lambda make_fact: make_fact(make_fact))(
            lambda make_fact: (lambda fact: lambda n: 1 if n <= 1 else n * fact(n - 1))(
                lambda n: (make_fact(make_fact))(n)
            )
        )(x)
    )
```

It still computes factorial.
```
0 1
1 1
2 2
3 6
4 24
5 120
6 720
7 5040
8 40320
9 362880
10 3628800
```

If we look in the middle, we see we got our original anonymous factorial lambda back:
```python
(lambda fact: lambda n: 1 if n <= 1 else n * fact(n - 1))
```

Let's think about this again:

```python

```python
for x in (0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10):
    print(x,
        (lambda make_fact: make_fact(make_fact))(
            lambda make_fact: (lambda fact: lambda n: 1 if n <= 1 else n * fact(n - 1))(
                lambda n: (make_fact(make_fact))(n)
            )
        )(x)
    )
```

The trick is we've just added another layer of currying functions, but it is still the same trick. But now in our extra lambda wrapper we could call the call to `make_fact(make_fact)` as just `fact` now, but make no mistake, it is still `make_fact(make_fact)` just with a different name, as we are now passing it in as the final lambda.

We can actually fully extract the factorial function out of this by separating the part that makes factorial an anonymous recursive function, from the factorial definition.

```python
for x in (0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10):
    print(x,
          (lambda y: (lambda f: f(f))(
              lambda f: y(lambda n: (f(f))(n))
          ))

          (lambda fact: lambda n: 1 if n <= 1 else n * fact(n - 1))

          (x)
          )`
```

Starting with the second lambda, we have an impossible anonymous recursive factorial function that cannot possibly work.

Above that, we have what is known as the Y-combinator, which is a function, that if given an impossible anonymous recursive function that cannot possible work, returns a version of it that does work.

And to test that we didn't just pull some shenanigans that just happens to compute factorial, here it is with an impossible version of an anonymous recusrive Fibonacci function that cannot possible work, but it does!

```python
for x in (1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12):
    print(x,
        (lambda y: (lambda f: f(f))(
              lambda f: y(lambda n: (f(f))(n))
        ))

        (lambda fib: lambda n: 1 if n <= 2 else fib(n - 1) + fib(n - 2))

        (x)
    )
```

```
1 1
2 1
3 2
4 3
5 5
6 8
7 13
8 21
9 34
10 55
11 89
12 144
```
