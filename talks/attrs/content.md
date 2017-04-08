attrs
----------

@tintvrtkovic<!-- .element: style="padding-top:200px;" -->

April 2017



<!-- .slide: data-background="#FFFFFF" -->
![Nanobit](img/nanobit.png)<!-- .element: style="border: 0;box-shadow: none;" -->



* Curio
* Trio



Haskell: What if everything was a monad?


C: What if everything was a pointer?


Ruby: What if everything was monkey patched?


Python: What if everything was a dict?


<p>Python: What if everything was a dict?</p>
<p>Java: What if everything was an object?</p>
<p>JavaScript: What if everything was a dict *and* an object?</p>


### PLT

* dicts: unstructured data
* classes: structured data



##### Map Position

* X (integer)
* Y (integer)
* orientation (integer)



<pre><code>
    >>> p = {'x': 10, 'y': 10, 'o': 1}

</code></pre>


<pre><code>
    >>> p = {'x': 10, 'y': 10, 'o': 1}
    >>> p
    {'o': 1, 'y': 10, 'x': 10}

</code></pre>


<pre><code>
    >>> p = {'x': 10, 'y': 10, 'o': 1}
    >>> p['x']
    10

</code></pre>


<pre><code>
    >>> p = {'x': 10, 'y': 10, 'o': 1}
    >>> d = dict(p)  # Or d = p.copy()
    >>> d
    {'o': 1, 'y': 10, 'x': 10}

</code></pre>


<pre><code>
    >>> a = {'x': 10, 'y': 10, 'o': 1}
    >>> b = {'x': 10, 'y': 10, 'o': 1}
    >>> a == b
    True

</code></pre>


<pre><code>
    >>> p = {'x': 10, 'y': 10, 'o': 1}
    >>> import json
    >>> json.dumps(p)
    '{"o": 1, "y": 10, "x": 10}'

</code></pre>



<pre><code>
    >>> class Position(object):
    ...     pass

</code></pre>


<pre><code>
    >>> class Position(object):
    ...     def __init__(self, x, y, o):
    ...         self.x = x
    ...         self.y = y
    ...         self.o = o

    >>> Position(10, 10, 1)
    <__main__.Position object at 0x7eff06051b70>

</code></pre>


<pre><code>
    >>> class Position(object):
    ...     def __init__(self, x, y, o):
    ...         self.x = x
    ...         self.y = y
    ...         self.o = o

    ...     def __repr__(self):
    ...         return '{}(x={}, y={}, o={})'.format(
    ...             self.__class__.__name__,
    ...             self.x, self.y, self.o)

    >>> Position(10, 10, 1)
    Position(x=10, y=10, o=1)

</code></pre>


<pre><code>
    >>> class Position(object):
    ...     def __init__(self, x, y, o):
    ...         self.x = x
    ...         self.y = y
    ...         self.o = o

    >>> from copy import copy

    >>> a = Position(10, 10, 1)
    >>> b = copy(a)
    >>> b
    Position(x=10, y=10, o=1)

</code></pre>


<pre><code>
    >>> class Position(object):
    ...     def __init__(self, x, y, o):
    ...         self.x = x
    ...         self.y = y
    ...         self.o = o

    ...     def copy(self):
    ...         return self.__class__(self.x, self.y. self.o)

    >>> a = Position(10, 10, 1)
    >>> b = a.copy()
    >>> b
    Position(x=10, y=10, o=1)

</code></pre>


* copy.copy(): 7.23 us +- 0.15 us

* a.copy(): 977 ns +- 31 ns


<pre><code>
    >>> class Position(object):
    ...     def __init__(self, x, y, o):
    ...         self.x = x
    ...         self.y = y
    ...         self.o = o

    ...     def copy(self):
    ...         return self.__class__(self.x, self.y. self.o)

    >>> a = Position(10, 10, 1)
    >>> b = a.copy()
    >>> a == b
    False

</code></pre>


<pre><code>
    >>> class Position(object):
    ...     def __init__(self, x, y, o):
    ...         self.x = x
    ...         self.y = y
    ...         self.o = o

    ...     def __eq__(self, other):
    ...         return (self.x == other.x and
    ...                 self.y == other.y and
    ...                 self.o == other.o)

    >>> a = Position(10, 10, 1)
    >>> b = a.copy()
    >>> a == b
    True

</code></pre>


<pre><code style="max-height: 600px;">
    >>> class Position(object):
    ...     def __init__(self, x, y, o):
    ...         self.x = x
    ...         self.y = y
    ...         self.o = o

    >>> a = Position(10, 10, 1)
    >>> import json
    >>> json.dumps(a)

    Traceback (most recent call last):
      File "stdin", line 1, in module
      File "/usr/lib/python3.5/json/__init__.py", line 230, in dumps
        return _default_encoder.encode(obj)
      File "/usr/lib/python3.5/json/encoder.py", line 198, in encode
        chunks = self.iterencode(o, _one_shot=True)
      File "/usr/lib/python3.5/json/encoder.py", line 256, in iterencode
        return _iterencode(o, 0)
      File "/usr/lib/python3.5/json/encoder.py", line 179, in default
        raise TypeError(repr(o) + " is not JSON serializable")
      TypeError: Position(x=10, y=10, o=1) is not JSON serializable

</code></pre>


<pre><code>
    >>> class Position(object):
    ...     def __init__(self, x, y, o):
    ...         self.x = x
    ...         self.y = y
    ...         self.o = o

    ...     def to_dict(self):
    ...         return {'x': self.x, 'y': self.y, 'o': self.o}

    >>> import json
    >>> json.dumps(Position(10, 10, 1).to_dict())
    '{"x": 10, "y": 10, "o": 1}'

</code></pre>


<pre><code style="max-height: 600px;">
    >>> class Position(object):
    ...     def __init__(self, x, y, o):
    ...         self.x = x
    ...         self.y = y
    ...         self.o = o

    ...     def __repr__(self):
    ...         return '{}(x={}, y={}, o={})'.format(
    ...             self.__class__.__name__,
    ...             self.x, self.y, self.o)

    ...     def copy(self):
    ...         return self.__class__(self.x, self.y. self.o)

    ...     def __eq__(self, other):
    ...         return (self.x == other.x and
    ...                 self.y == other.y and
    ...                 self.o == other.o)

    ...     def to_dict(self):
    ...         return {'x': self.x, 'y': self.y, 'o': self.o}

</code></pre>


<pre><code>
    >>> a = {'x': 10, 'y': 10, 'o': 1}
    >>> a['xc'] = 11

    >>> b = Position(10, 10, 1)
    >>> b.xc = 11

</code></pre>


Both dicts and normal classes are non-declarative.



<!-- .slide: style="padding-top:0;" -->
![Hynek Schlawak](img/hynek.jpeg)

@hynek


## Characteristic

https://github.com/hynek/characteristic

<pre><code style="margin-top: 100px;">
    >>> from characteric import attributes

    >>> @attributes(['x', 'y', 'o'])
    ... class Position(object):
    ...    pass

</code></pre>


## attrs

https://github.com/hynek/attrs


## attrs

https://github.com/python-attrs/attrs


<pre><code style="margin-top: 100px;">
    >>> import attr

    >>> @attr.s
    ... class Position(object):
    ...    x = attr.ib()
    ...    y = attr.ib()
    ...    o = attr.ib()

    >>>

</code></pre>


<pre><code style="margin-top: 100px;">
    >>> import attr

    >>> @attr.s
    ... class Position(object):
    ...    x = attr.ib()
    ...    y = attr.ib()
    ...    o = attr.ib()

    >>> Position(10, 10, 1)
    Position(x=10, y=10, o=1)

</code></pre>


<pre><code style="margin-top: 100px;">
    def __init__(self, x, y, o):
        self.x = x
        self.y = y
        self.o = o

</code></pre>


<pre><code style="margin-top: 100px;">
    >>> import attr

    >>> @attr.s
    ... class Position(object):
    ...    x = attr.ib()
    ...    y = attr.ib()
    ...    o = attr.ib()

    >>> Position(10, 10, 1) == Position(10, 10, 1)
    True

</code></pre>


<pre><code style="margin-top: 100px;">
    >>> import attr

    >>> @attr.s
    ... class Position(object):
    ...    x = attr.ib()
    ...    y = attr.ib()
    ...    o = attr.ib()

    >>> a = Position(10, 10, 1)
    >>> b = attr.evolve(a)  # Legacy: attr.assoc
    >>> b
    Position(x=10, y=10, o=1)
    >>> a is b
    False

</code></pre>


<pre><code>
    >>> import attr

    >>> @attr.s
    ... class Position(object):
    ...    x = attr.ib()
    ...    y = attr.ib()
    ...    o = attr.ib()

    >>> a = Position(10, 10, 1)

    >>> import json
    >>> json.dumps(attr.asdict(a))
    '{"o": 1, "x": 10, "y": 10}'

</code></pre>


<pre><code>
    >>> import attr

    >>> @attr.s
    ... class Position(object):
    ...    x = attr.ib()
    ...    y = attr.ib()
    ...    o = attr.ib()

    >>> a = Position(10, 10, 1)
    >>> a.xc = 11

</code></pre>


<pre><code style="margin-top: 100px;">
    >>> import attr

    >>> @attr.s(slots=True)
    ... class Position(object):
    ...    x = attr.ib()
    ...    y = attr.ib()
    ...    o = attr.ib()

    >>> a = Position(10, 10, 1)
    >>> a.xc = 11
    Traceback (most recent call last):
      File "stdin", line 1, in module
    AttributeError: 'Position' object has no attribute 'xc'

</code></pre>


<pre><code style="max-height: 600px;">
    >>> class Position(object):
    ...     __slots__ = ('x', 'y', 'o')

    ...     def __init__(self, x, y, o):
    ...         self.x = x
    ...         self.y = y
    ...         self.o = o

    ...     def __repr__(self):
    ...         return '{}(x={}, y={}, o={})'.format(
    ...             self.__class__.__name__,
    ...             self.x, self.y, self.o)

    ...     def copy(self):
    ...         return self.__class__(self.x, self.y. self.o)

    ...     def __eq__(self, other):
    ...         return (self.x == other.x and
    ...                 self.y == other.y and
    ...                 self.o == other.o)

    ...     def to_dict(self):
    ...         return {'x': self.x, 'y': self.y, 'o': self.o}

</code></pre>



## attrs API


### <code>attr.ib()</code>

* default
* validator
* repr
* cmp
* hash
* init
* convert
* metadata


### <code>attr.s()</code>

* repr
* str
* cmp
* hash
* init
* slots
* frozen


<pre><code style="max-height: 600px;">
    >>> import attr
    >>> from attr.validators import instance_of

    >>> @attr.s(slots=True, frozen=True)
    ... class MyClass(object):
    ...    a = attr.ib(convert=str)
    ...    b = attr.ib(validator=instance_of(str))
    ...    c = attr.ib(default=0)

    >>> MyClass(0, 'test')
    MyClass(a='0', b='test', c=0)

    >>> MyClass(0, 'test').b = 'a'
    Traceback (most recent call last):
      File "stdin", line 1, in module
      File "/home/tin/pg/attrs/src/attr/_make.py", line 217, in _frozen_setattrs
        raise FrozenInstanceError()
    attr.exceptions.FrozenInstanceError

</code></pre>


<pre><code style="max-height: 600px;">
    >>> import attr
    >>> from attr.validators import instance_of

    >>> @attr.s(slots=True, frozen=True)
    ... class MyClass(object):
    ...    a = attr.ib(convert=str)
    ...    b = attr.ib(validator=instance_of(str))
    ...    c = attr.ib(default=0)

    >>> attr.fields(MyClass)
    (Attribute(name='a', default=NOTHING, validator=None, repr=True,
               cmp=True, hash=None, init=True, convert=&lt;class 'str'&gt;,
               metadata=mappingproxy({})))

    >>> attr.fields(MyClass).c
    Attribute(name='c', default=0, validator=None, repr=True, cmp=True,
              hash=None, init=True, convert=None,
              metadata=mappingproxy({}))

</code></pre>


<pre><code style="max-height: 600px;">
    def shallow_copyable(cls):
        fields = attr.fields(cls)
        script = """
def copy(self):
    return cls(
"""
        for a in fields:
            script += "    self.{0},\n".format(a.name)
        script += "    )"
        bytecode = compile(script, "copy", "exec")
        globs, locs = {'cls': cls}, {}
        eval(bytecode, globs, locs)
        cls.copy = locs['copy']
        return cls

</code></pre>


<pre><code style="max-height: 600px;">
    >>> import attr
    >>> from attr.validators import instance_of

    >>> @shallow_copyable
    ... @attr.s(slots=True, frozen=True)
    ... class MyClass(object):
    ...    a = attr.ib(convert=str)
    ...    b = attr.ib(validator=instance_of(str))
    ...    c = attr.ib(default=0)

    >>> MyClass(0, 'test').copy()
    MyClass(a='0', b='test', c=0)

</code></pre>


<pre><code style="max-height: 600px;">
    >>> import attr

    >>> Position = attr.make_class('Position', ['x', 'y', 'o'])

    >>> Position(10, 10, 1)
    Position(x=10, y=10, o=1)

</code></pre>


<pre><code style="max-height: 600px;">
    >>> import attr

    >>> @attr.s(slots=True)
    ... class Position(object):
    ...    x = attr.ib()
    ...    y = attr.ib()
    ...    o = attr.ib()

    >>> attr.asdict(Position(10, 10, 1))
    {'y': 10, 'x': 10, 'o': 1}

    >>> attr.astuple(Position(10, 10, 1))
    (10, 10, 1)

    >>> Position(*attr.astuple(Position(10, 10, 1)))
    Position(x=10, y=10, o=1)

</code></pre>


<pre><code style="max-height: 600px;">
    >>> import attr

    >>> @attr.s(slots=True, frozen=True)
    ... class MyClass(object):
    ...    a = attr.ib(convert=str)
    ...    b = attr.ib(validator=instance_of(str))
    ...    c = attr.ib(default=0)

    >>> a = MyClass(0, 'test')

    >>> attr.evolve(a, b='another_test')
    MyClass(a='0', b='another_test', c=0)

</code></pre>


<pre><code style="max-height: 600px;">
    >>> import attr

    >>> @attr.s(slots=True, frozen=True)
    ... class MyClass(object):
    ...    a = attr.ib(convert=str)
    ...    b = attr.ib(validator=instance_of(str))
    ...    c = attr.ib(init=False)

    ...    def __attrs_post_init__(self):
    ...        self.c = self.a + self.b

    >>> a = MyClass(0, 'test')
    MyClass(a='0', b='test', c='0test')

</code></pre>



## cattrs

https://github.com/tinche/cattrs


<pre><code>
    >>> import attr

    >>> attr.s(slots=True)
    ... class A:
    ...     a = attr.ib(convert=int)

</code></pre>


<pre><code>
    >>> import attr
    >>> from attr.validators import instance_of

    >>> @attr.s(slots=True)
    ... class A:
    ...     a = attr.ib(convert=int)

    >>> @attr.s(slots=True)
    ... class B:
    ...     b = attr.ib(validator=instance_of(A))

</code></pre>


<pre><code>
    >>> a = A(1)
    >>> b = B(a)
    >>> attr.asdict(b)
    {'b': {'a': 1}}

    >>> B(**attr.asdict(b))
    TypeError: ...

</code></pre>


<pre><code>
    >>> import attr
    >>> from attr.validators import instance_of

    >>> @attr.s(slots=True)
    ... class A:
    ...     a = attr.ib(convert=int)

    >>> @attr.s(slots=True)
    ... class B:
    ...     b = attr.ib(validator=instance_of(A))

    >>> @attr.s(slots=True)
    ... class C:
    ...    c = attr.ib(validator=...)  # type: Dict[int, B]

</code></pre>


<pre><code>
    >>> a = A(1)
    >>> b = B(a)
    >>> c = C({1: b})
    >>> json.dumps(attr.asdict(c))
    '{"c": {"1": {"b": {"a": 1}}}}'

    >>> C(**json.loads(...))
    No way.

</code></pre>


<pre><code>
    >>> import attr, cattr, typing

    >>> @attr.s(slots=True)
    ... class A:
    ...     a = cattr.typed(int, convert=int)

    >>> @attr.s(slots=True)
    ... class B:
    ...     b = cattr.typed(A, validator=instance_of(A))

    >>> @attr.s(slots=True)
    ... class C:
    ...    c = cattr.typed(typing.Dict[int, B], validator=...)

</code></pre>


<pre><code>
    >>> a = A(1)
    >>> b = B(a)
    >>> c = C({1: b})
    >>> json.dumps(cattr.unstructure(c))
    '{"c": {"1": {"b": {"a": 1}}}}'

    >>> cattr.structure(json.loads('{"c": {"1": {"b": {"a": 1}}}}'), C)
    C(c={1: B(b=A(a=1))})

</code></pre>


<pre><code>
    >>> cattr.structure([1, 2, 3], typing.Tuple[float, str, int])
    (1.0, '2', 3)

</code></pre>
