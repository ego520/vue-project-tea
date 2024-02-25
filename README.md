# do mixin for python
  This project wants to implement a mixin library like ruby's module.

## What mixin is
In object-oriented programming languages, a mixin is a class which contains a combination of methods from other classes. How such a combination is done depends on the language, but it is not by inheritance. If a combination contains all methods of combined classes it is equivalent to multiple inheritance.
For more information, please refer to this wiki:
http://en.wikipedia.org/wiki/Mixin

## install pymixin

install it from pip:

    pip install mixin

In fact, the pymix package only has one file, it is mixin.py, so you can also copy it directly to your project directory.

## how to use pymixin

### a simple example

Import the Mixin class and mixin decorator from the mixin module:

    >>> from mixin import Mixin, mixin

The user's mixin class should always inherit from the Mixin class:

    >>> class MixinA(Mixin):
    ...     def func_mixin_a(self):
    ...             return 'do_func_mixin_a'
    ...

and then use the mixin decorator to add the mixin to a class:

    >>> @mixin(MixinA)
    ... class A(object): pass
    ...
    >>> a = A()
    >>> a.func_mixin_a()
    'do_func_mixin_a'

### add a mixin to another mixin

The mixin should not be inherited by another class:

    >>> class MixinA(Mixin): pass
    ...
    >>> class A(MixinA): pass
    ...
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
      File "mixin.py", line 58, in __new__
        raise InheritMixinError(clsname)
    __main__.InheritMixinError: A

But you can add a mixin to another mixin:

    >>> class MixinA(Mixin):
    ...     def func_mixin_a(self):
    ...             return 'do_func_mixin_a'
    ...
    >>> @mixin(MixinA)
    ... class MixinB(Mixin):
    ...     def func_mixin_b(self):
    ...             return 'do_func_mixin_b'
    ...


Then add the MixinB to a normal class

    >>> @mixin(MixinB)
    ... class M(object): pass
    ...
    >>> m = M()
    >>> m.func_mixin_a()
    'do_func_mixin_a'
    >>> m.func_mixin_b()
    'do_func_mixin_b'

### a mixin class can not be instantiated

    >>> class MixinA(Mixin): pass
    ...
    >>> a = MixinA()
    Traceback (most recent call last):
      File "<stdin>", line 1, in <module>
      File "mixin.py", line 44, in mixin_new
        raise InstantiationMixinError(cls)
    __main__.InstantiationMixinError: <class '__main__.MixinA'>

### inherit multi mixin

Two kind of methods to inherit multi mixins, one is writing multi mixins in one decorator:

    >>> class MixinA(Mixin): pass
    ...
    >>> class MixinB(Mixin): pass
    ...
    >>> @mixin(MixinA, MixinB)
    ... class M(object): pass
    ...

Another is using multi decorators:

    >>> @mixin(MixinA)
    ... @mixin(MixinB)
    ... class M(object): pass
    ...

### inherit priority

When writes multi mixins to one decorator, the left mixin has a higher priority

    >>> class MixinA(Mixin):
    ...     def mixin_func(self):
    ...             return 'do_mixin_a'
    ...
    >>> class MixinB(Mixin):
    ...     def mixin_func(self):
    ...             return 'do_mixin_b'
    ...
    >>> @mixin(MixinA, MixinB)
    ... class M(object): pass
    ...
    >>> m = M()
    >>> m.mixin_func()
    'do_mixin_a'

When writes multi mixins in multi decorators, the up mixin has a higher priority:

    >>> @mixin(MixinA)
    ... @mixin(MixinB)
    ... class M(object): pass
    ...
    >>> m = M()
    >>> m.mixin_func()
    'do_mixin_a'

If the class has a funciton which has the same name as the mixin, the mixin method will be overwrite:

    >>> class MixinA(Mixin):
    ...     def mixin_func(self):
    ...             return 'do_mixin_a'
    ...
    >>> @mixin(MixinA)
    ... class A(object):
    ...     def mixin_func(self):
    ...             return 'real_class_mixin_a'
    ...
    >>> a = A()
    >>> a.mixin_func()
    'real_class_mixin_a'

If a class has a father class, and the father class has a same name method as the mixin, the father class' method will be overwrite:

    >>> class MixinA(Mixin):
    ...     def mixin_func(self):
    ...             return 'do_mixin_a'
    ...
    >>> class Father(object):
    ...     def mixin_func(self):
    ...             return 'father_mixin'
    ...
    >>> @mixin(MixinA)
    ... class A(Father): pass
    ...
    >>> a=A()
    >>> a.mixin_func()
    'do_mixin_a'

## python version support

Tested on python2.6, python2.7 and python3.4
