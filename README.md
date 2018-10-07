# gonad.py

gonad.py enables you to perform monadic computations with the list comprehension syntax. It is powered by [ast transformations](https://docs.python.org/3.7/library/ast.html).

# Example: the Maybe monad

Let's define the Maybe monad like this:

```
class Maybe(object):

    def __init__(self, value):
        self.value = value

    @classmethod
    def unit(cls, value):
        return cls(value)

    @classmethod
    def nothing(cls):
        return cls(None)

    def is_nothing(self):
        return self.value is None

    def bind(self, f):
        if self.value is None:
            return self
        return f(self.value)

    def map(self, f):
        if self.value is None:
            return self
        new_value = f(self.value)
        return Maybe.unit(new_value)

```

We can then adapt the semantics of the comprehension syntax with this:
```
@gonad(Maybe)
def f():
    return [
        (x + y)
        for x in Maybe(5)
        for y in Maybe(6)
    ]

f()  # outputs `Just 11`

@gonad(Maybe)
def g():
    return [
        (x + y)
        for x in Maybe(5)
        for y in Maybe.nothing()
    ]

g()  # outputs `Nothing`
```

# Comprehending an arbitrary monad

You can provide any class which implements `unit` and `bind` methods to the `gonad`decorator.

# Status of the project

This is still in a very experimental stage, and is probably not ready yet for production use.

# References

This idea of monad comprehension is very similar to [Haskell's do notation](https://en.wikibooks.org/wiki/Haskell/do_notation). This is also inspired by this [blog post](http://blog.sigfpe.com/2012/03/overloading-python-list-comprehension.html).
