---
layout: post
title:  "Understanding Python module imports"
comments: true
share: true
tags:
  - self learning
  - python
  - software development
  - reference
  - experiment
  - module
  - review request
  - github
---

## Motivation

Despite using Python <img src="/assets/images/python-logo-only.png" height="16px"/> for several years, I frequently get confused about how module imports work. I had been putting off properly learning this but a few weeks ago, I decided to finally grab the rose by its thorns. I followed my preferred approach to self-learn by experimenting with codes, while reading the "theory" along the way as needed.

I document my experiments and learning for my future reference, but this hopefully helps others as well. 

If you're a Python expert, please feel free to critically review this post, and provide your feedback in the comments, especially wherever I have made mistakes. After understanding the mistakes, I will update the post, with full credit to you.

If you're a Python beginner—especially in the modules import topic—and plan to use this tutorial for self-learning, please do the following:
* Follow along by _writing_ the source codes—not just "copy pasting" my source codes.
* Perform your own experiments, then pause and reflect upon what you have understood, before the next experiment. 
* **Recommended**: Verbally explain what you're doing and what you have learned to a friend or a colleague. If you cannot find a human being, use a [rubber duck](https://careerfoundry.com/en/blog/web-development/rubber-duck-debugging/) or equivalent.
* Most importantly, *document your learning*—even if you're not sure you have understood correctly. The very act of writing it down will help you realize what you do _not_ understand. You can keep revising your document as you gain better understanding.

Although this approach may take you ten times longer, you will learn a lot more and save yourself a lot of headaches in future.

## Source codes

The source codes of my experiments are available on my github repository [`heavens-arena`](https://github.com/dragondive/heavens-arena) on the [`python/modules` branch](https://github.com/dragondive/heavens-arena/commits/python/modules). Every experiment is a separate commit on this branch, and the output of the code is included as a comment within the `main.py` file.

## Experiments and learning

---
### **Experiment no. 0**: [start with Hello World](https://github.com/dragondive/heavens-arena/commit/95c9cbd4c74278a99eef73a62e2f5cc5e331a689)

`main.py`:

```python
print("Hello World")

# PS C:\WORK\dragondive\heavens-arena\python\modules> python main.py
# Hello World
```

> **Wisdom, i.e. ज्ञान (Gyan)**: Following the rich traditions of software development.

---
### **Experiment no. 1**: [Call a method from another module in same directory](https://github.com/dragondive/heavens-arena/commit/0455423598654fac141da405b77d2722e4a4d440)

`main.py`:

```python
import message_provider

print(message_provider.get_message())
print(type(message_provider))
print(dir(message_provider))

# PS C:\WORK\dragondive\heavens-arena\python\modules> python main.py
# Hello World
# <class 'module'>
# ['__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__', 'get_message']
```

`message_provider.py`:

```python
def get_message():
    return "Hello World"
```

> **Wisdom**:
>  
> * `import`ing another module from the current directory is intuitive. The next experiment will explore this slightly more.
> * [`type()`](https://docs.python.org/3/glossary.html#term-type) shows that `message_provider` is a `module`.
> * From the [`dir()`](https://docs.python.org/3/library/functions.html?highlight=dir#dir) output, of interest here is the `get_message` function, which is invoked from `main.py`.
> * These two methods will become more useful to understand the outcomes of the next experiments.

---
### **Experiment no. 2**: [Run python from a different directory](https://github.com/dragondive/heavens-arena/commit/c0d357e435908b9b0583e7069ae8e1659a64ce00)

`main.py`:

```python
import message_provider
import sys

print(message_provider.get_message())
print(sys.path)

# PS C:\WORK\dragondive\heavens-arena> python .\python\modules\main.py
# Hello World
# ['C:\\WORK\\dragondive\\heavens-arena\\python\\modules', 'C:\\WORK\\python', 'C:\\Users\\aravi\\AppData\\Local\\Programs\\Python\\Python311\\python311.zip', 'C:\\Users\\aravi\\AppData\\Local\\Programs\\Python\\Python311\\Lib', 'C:\\Users\\aravi\\AppData\\Local\\Programs\\Python\\Python311\\DLLs', 'C:\\Users\\aravi\\AppData\\Local\\Programs\\Python\\Python311', 'C:\\Users\\aravi\\AppData\\Local\\Programs\\Python\\Python311\\Lib\\site-packages']
```

> **Wisdom**:
>  
> * Python interpreter searches for the modules in the `sys.path`. _[NOTE: Subsequent experiments will show this is not fully accurate, but this understanding is ok for now.]_
> * The first entry in the `sys.path` is the directory that contains the input script (in this case, `main.py`). Hence, any other modules in that same directory will be discovered in the same way, even if python is invoked from a different directory. See the documentation for more details: [The initialization of the sys.path module search path](https://docs.python.org/3/library/sys_path_init.html)

---

### **Experiment no. 3**: [Move other module to other directory](https://github.com/dragondive/heavens-arena/commit/51cc644e4b25e02996a580a3aa8dfbb8a15288fe), then [import only the other directory](https://github.com/dragondive/heavens-arena/commit/a61b47df540b15662faefdf1ae387fa58dd4e3a4)

The directory structure is now as following:

```bash
python/modules/
    +-- message/
            +-- message_provider.py
    +-- main.py
```

`main.py`:

```python
import message

print(type(message))
print(dir(message))
print(message.message_provider.get_message())   # error!

# PS C:\WORK\dragondive\heavens-arena\python\modules> python main.py
# <class 'module'>
# ['__doc__', '__file__', '__loader__', '__name__', '__package__', '__path__', '__spec__']
# Traceback (most recent call last):
#   File "C:\WORK\dragondive\heavens-arena\python\modules\main.py", line 5, in <module>
#     print(message.message_provider.get_message())
#           ^^^^^^^^^^^^^^^^^^^^^^^^
# AttributeError: module 'message' has no attribute 'message_provider'
```

> **Wisdom**:
>  
> * The directory gets imported as a `module`, but the submodules within that directory (in this case, `message_provider`) are not automatically imported into scope. 
> * By itself, importing the directory has little benefit. However, by using an `__init__.py` file in the directory, the directory import can be used for more practical purposes. I shall cover this in more detail in a future post.

---

### **Experiment no. 4**: [Import the module from the other directory](https://github.com/dragondive/heavens-arena/commit/27f4f259b4bf3c1218f79a57cc82caf05a61e999)

`main.py`:

```python
import message.message_provider

print(type(message))
print(dir(message))
print(type(message.message_provider))
print(dir(message.message_provider))
print(message.message_provider.get_message())

# PS C:\WORK\dragondive\heavens-arena\python\modules> python main.py   
# <class 'module'>
# ['__doc__', '__file__', '__loader__', '__name__', '__package__', '__path__', '__spec__', 'message_provider']
# <class 'module'>
# ['__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__', 'get_message']
# Hello World
```

> **Wisdom**:
>  
> * When a submodule is imported, it also becomes available as an attribute of the parent module.
> * All the members of the submodule are also available, but need to be accessed using the full module qualification (in this case, `message.message_provider.get_message`).
> * The members can also be imported as `from module.submodule import member`, then the member can be used without full module qualification. The difference between the two will be covered in subsequent experiments.

---

### **Experiment no. 5**: [Create a conflict with a standard module](https://github.com/dragondive/heavens-arena/commit/551caffeb74dfe6678d3be894e92d8be2f11317f) then "resolve" it by [creating an `__init__.py` file](https://github.com/dragondive/heavens-arena/commit/69256117b741a5bff4d15dd06e9b4bfaa3e7bf33)

The documentation on [Packages](https://docs.python.org/3/tutorial/modules.html#packages) says this:

> The \_\_init\_\_.py files are required to make Python treat directories containing the file as packages. This prevents directories with a common name, such as string, unintentionally hiding valid modules that occur later on the module search path.

The documentation on [Module Search Path](https://docs.python.org/3/tutorial/modules.html#the-module-search-path) says this:

> When a module named `spam` is imported, the interpreter first searches for a built-in module with that name.

To properly understand this, I created this "conflict" by renaming the `message` directory to `string`.

**NOTE: I must emphasize, in case it is not already clear from the context, that this should not be done in a production environment.** I do it in this experiment only to improve my understanding of how the module import works. This deeper knowledge is frequently useful while debugging "strange" problems even in "well-written" software.

`main.py`—without `__init__.py`:

```python
import string.message_provider

print(type(string))
print(dir(string))
print(type(string.message_provider))
print(dir(string.message_provider))
print(string.message_provider.get_message())

# PS C:\WORK\dragondive\heavens-arena\python\modules> python main.py
# Traceback (most recent call last):
#   File "C:\WORK\dragondive\heavens-arena\python\modules\main.py", line 1, in <module>
#     import string.message_provider
# ModuleNotFoundError: No module named 'string.message_provider'; 'string' is not a package
```

`main.py`—with `__init__.py`:

```python
import string.message_provider
print(type(string))
print(dir(string))
print(type(string.message_provider))
print(dir(string.message_provider))
print(string.message_provider.get_message())

# PS C:\WORK\dragondive\heavens-arena\python\modules> python main.py
# <class 'module'>
# ['__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__path__', '__spec__', 'message_provider']
# <class 'module'>
# ['__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__', 'get_message']
# Hello World
```

> **Wisdom**:
>  
> _[In experiment no. 2, I had mentioned it is not fully accurate that the Python interpreter searches for modules in `sys.path`. This is where I properly resolve it.]_
>
> * The python interpreter first searches for built-in modules with that name. Only when it doesn't find it there, it looks into `sys.path`. Hence, a directory with a name matching a built-in module (in this case, `string`) will cause a conflict.
> * It is not advised to use such conflicting names in a production environment. Nonetheless, if one wants to do it anyway, the documentation suggests to create an `__init__.py` file in the directory. This causes the interpreter to treat that directory as a package, and take precedence over the built-in module.

It wasn't clear to me how creating the `__init__.py` causes the interpreter to give precedence to the "local" module over the built-in module, even after searching on the internet and reading the official documentation for several hours. I also asked this question in the python forum: [How exactly does \__init__.py influence module search order?](https://discuss.python.org/t/how-exactly-does-init-py-influence-module-search-order/24759)

This article [Python behind the scenes #11: how the Python import system works](https://tenthousandmeters.com/blog/python-behind-the-scenes-11-how-the-python-import-system-works/) by [Victor Skvortsov](https://github.com/r4victor) gives the most clear explanation. In particular:

> How does it work? When Python traverses path entries in the path (`sys.path` or parent's `__path__`) during the module search, it remembers the directories without `__init__.py` that match the module's name. If after traversing all the entries, it couldn't find a regular package, a Python file or a C extension, it creates a module object whose `__path__` contains the memorized directories.
>
> The initial idea of requiring `__init__.py` was to prevent directories named like `string` or `site` from shadowing standard modules. Namespace package do not shadow other modules because they have lower precedence during the module search.

I wrote an email to Victor thanking him for the article, and in turn, he pointed me to [PEP 420 – Implicit Namespace Packages](https://peps.python.org/pep-0420/) for further reading. I will take my time to understand the PEP and make a new blog post describing what I learn.

---

### **Experiment no. 6**: [Import a third module from the second module](https://github.com/dragondive/heavens-arena/commit/f6526093e100d0e3610306225a8a180ebc863cc9)

After reverting the "conflict" commits, I raised the complexity of the module import learning by importing a third module from the second module. This also prepares the stage for the circular import related learning, which further helps to better understand how the module import works.

The directory structure is now as following:

```bash
python/modules/
    +-- answer/
            +-- answer_provider.py
    +-- message/
            +-- message_provider.py
    +-- main.py
```

`answer_provider.py`:

```python
def get_answer():
    return 42
```

`message_provider.py`:

```python
from answer.answer_provider import get_answer

def get_message():
    return "Hello " + str(get_answer())
```

`main.py`:

```python
import message.message_provider

print(message.message_provider.get_message())

# PS C:\WORK\dragondive\heavens-arena\python\modules> python main.py
# Hello 42
```

> **Wisdom**:
>  
> In `message_provider.py`, I used `from X import Y` instead of `import X`. This was due to my misunderstanding that the latter would cause a circular import, based on my previous experiences with the `#include` directive of C & C++. However, after the subsequent experiments, I understood there wouldn't have been a circular import anyway.
>  
> This experiment induced me to properly learn the following:
> * Circular import
> * The difference between `import X` and `from X import Y`

---

### **Experiment no. 7**: [Import from a deeper level directory](https://github.com/dragondive/heavens-arena/commit/c5201d46707306894b4681bc62ce9f1223d798bb)

The directory structure is now as following:

```bash
python/modules/
    +-- answer/
            +-- deep/
                  +-- answer_provider.py
    +-- message/
            +-- message_provider.py
    +-- main.py
```

`message_provider.py`:

```python
import answer.deep.answer_provider

def get_message():
    print(type(answer))
    print(dir(answer))
    print(type(answer.deep))
    print(dir(answer.deep))
    print(type(answer.deep.answer_provider))
    print(dir(answer.deep.answer_provider))
    return "Hello " + str(answer.deep.answer_provider.get_answer())
```

`main.py`:

```python
import message.message_provider
print(message.message_provider.get_message())

# PS C:\WORK\dragondive\heavens-arena\python\modules> python main.py
# <class 'module'>
# ['__doc__', '__file__', '__loader__', '__name__', '__package__', '__path__', '__spec__', 'deep']
# <class 'module'>
# ['__doc__', '__file__', '__loader__', '__name__', '__package__', '__path__', '__spec__', 'answer_provider']
# <class 'module'>
# ['__builtins__', '__cached__', '__doc__', '__file__', '__loader__', '__name__', '__package__', '__spec__', 'get_answer']
# Hello 42
```

> **Wisdom**:
>  
> * As intuitively expected, modules within nested directory can be imported by qualifying them with the full module structure.
> * Each "level" of submodules is of `module` type, and its direct child modules are its attributes.

---

### **Experiment no. 8**: [Create a circular import](https://github.com/dragondive/heavens-arena/commit/f023600bb40a1dc6da2aa583f52fdacc532b88de)

**NOTE: I must emphasize *again*, in case it is not already clear from the context, that in a production environment, module organization should be carefully done to avoid potential circular imports at all.** I create circular imports here and in subsequent experiments only to improve my understanding of how the module import works. As you will notice, this has also helped me to properly understand (at least to some extent) the difference between `import X` and `from X import Y`; and about how the interpreter handles the module table.

`answer_provider.py`:

```python
from message.message_provider import get_real_message

def get_answer():
    print(get_real_message())
    return 42
```

`message_provider.py`:

```python
from answer.deep.answer_provider import get_answer

def get_message():
    return "Hello " + str(get_answer())

def get_real_message():
    return "The One Piece is REAL!!!"
```

`main.py`:

```python
import message.message_provider
print(message.message_provider.get_message())

# PS C:\WORK\dragondive\heavens-arena\python\modules> python main.py
# Traceback (most recent call last):
#   File "C:\WORK\dragondive\heavens-arena\python\modules\main.py", line 1, in <module>
#     import message.message_provider
#   File "C:\WORK\dragondive\heavens-arena\python\modules\message\message_provider.py", line 1, in <module>
#     from answer.deep.answer_provider import get_answer
#   File "C:\WORK\dragondive\heavens-arena\python\modules\answer\deep\answer_provider.py", line 1, in <module>
#     from message.message_provider import get_real_message
# ImportError: cannot import name 'get_real_message' from partially initialized module 'message.message_provider' (most likely due to a circular import) (C:\WORK\dragondive\heavens-arena\python\modules\message\message_provider.py)
```

> **Wisdom**:
>  
> The circular import is related to the topic of the difference between `import X` and `from X import Y` which was mentioned before. The most clear explanations I found are [this StackOverflow answer](https://stackoverflow.com/a/40094439) and [this Software Engineering SE answer](https://softwareengineering.stackexchange.com/a/187471). After reading them together, I summarize my understanding as below:
> * The interpreter maintains a module table, which has a mapping from the module name to a "reference" to the respective module.
> * When the interpreter encounters an import statement `import X`:
>   * If `X` does not exist in the module table:
>     * The interpreter looks for the module using the module search path.
>     * When it finds the module, it adds a new entry to the module table.
>     * It also binds the name `X` to the module in the local scope.
>     * Then it starts "executing" the statements in the module, which may include (trying to) import other modules specified.
>   * If `X` already exists in the module table:
>     * The interpreter binds the name `X` in the local scope to the known module reference.
>  
> * When the interpreter encounters an import statement `from X import Y`, it performs all the steps as above, except that it does not bind the name `X` to the local scope, but instead binds the name `Y` to the member from the module `X`.
>  
> In this specific experiment, the "execution" proceeds like this:
 
```python
# NOTE: Omitting directory names for brevity
# main.py
import message_provider  # >-----+
                         #   [1] |
# message_provider.py    # <-----+    <-----------------+
from answer_provider import get_answer  # >----+        |
                                        #  [2] |    [3] |
# answer_provider.py                    # <----+        |
from message_provider import get_real_message  # >------+
```
 
> [1] The interpreter finds `message_provider` and adds a reference to the module table. It then starts "executing" the `message_provider` module.
> [2] While `message_provider` has not been fully "executed", the interpreter looks for `answer_provider` and adds it to the module table.
> [3] The interpreter already has `message_provider` in the module table, but it has not yet encountered `get_real_message`. Hence, it cannot bind this name in the local scope of `answer_provider`. But until it does that, it cannot proceed to "execute" the rest of `message_provider`—where it would have found `get_real_message`!.
> This creates the circular import problem.

---

### **Experiment no. 9**: [Resolve the circular import by importing entire module](https://github.com/dragondive/heavens-arena/commit/61a5d652ea93292740d48b475139aa07f22b4560)

The source code change from experiment no. 8 is that in `answer_provider.py`, the full module `message.message_provider` is imported, not only `get_real_message`. This resolves the circular import as explained below:

`answer_provider.py`:

```python
import message.message_provider

def get_answer():
    print(get_real_message())
    return 42
```

`main.py`:

```python
import message.message_provider
print(message.message_provider.get_message())

# PS C:\WORK\dragondive\heavens-arena\python\modules> python main.py
# The One Piece is REAL!!!
# Hello 42
```

> **Wisdom**:
>  
> In this experiment, the "execution" proceeds like this:
 
```python
# NOTE: Omitting directory names for brevity
## main.py
import message_provider  # >-----+
                         #   [1] |
## message_provider.py   # <-----+
from answer_provider import get_answer  # >---+
#                                         [2] |
# ... rest                    +               |  <-----+
#     of                  [5] |               |        |
#     the                     |               |        |
#     module ...              X               |        |
#                                             |        |
## answer_provider.py                     <---+        |
import message_provider       #     +                  |
#                               [3] |                  |
# ... rest                          |                  |
#     of                            |                  |
#     the                           |              [4] |
#     module ...                    X   ---------------+
```

> [1] The interpreter finds `message_provider` and adds a reference to the module table. It then starts "executing" the `message_provider` module.
> [2] It looks for `answer_provider` and adds it to the module table. It then starts "executing" the `answer_provider` module.
> [3] It already has `message_provider` in the module table, so binds the name `message_provider` to it in `answer_provider`'s local scope. It then continues to "execute" rest of the `answer_provider` module.
> [4] After the end of "executing" `answer_provider`, it returns to the `message_provider` module.
> [5] It continues "executing" rest of the `message_provider` module until the end.
Hence, the circular import problem does not occur.

---

### **Experiment no. 10**: [Second module fully includes third module, but third module partially includes second module](https://github.com/dragondive/heavens-arena/commit/957c88063458f0fe3c146655a3de075222f48e42)

I did this experiment mainly to verify my learning from experiment no. 8. The only difference is `message_provider` now fully `import`s `answer_provider`, and not only the `get_answer` method. Following the same "execution" flow as in experiment no. 9, I figured out this should _also_ lead to a circular import, and it did.

`message_provider.py`:

```python
import answer.deep.answer_provider

def get_message():
    return "Hello " + str(answer.deep.answer_provider.get_answer())

def get_real_message():
    return "The One Piece is REAL!!!"
```

`main.py`:

```python
import message.message_provider
print(message.message_provider.get_message())

# PS C:\WORK\dragondive\heavens-arena\python\modules> python main.py
# Traceback (most recent call last):
#   File "C:\WORK\dragondive\heavens-arena\python\modules\main.py", line 1, in <module>
#     import message.message_provider
#   File "C:\WORK\dragondive\heavens-arena\python\modules\message\message_provider.py", line 1, in <module>
#     from answer.deep.answer_provider import get_answer
#   File "C:\WORK\dragondive\heavens-arena\python\modules\answer\deep\answer_provider.py", line 1, in <module>
#     from message.message_provider import get_real_message
# ImportError: cannot import name 'get_real_message' from partially initialized module 'message.message_provider' (most likely due to a circular import) (C:\WORK\dragondive\heavens-arena\python\modules\message\message_provider.py)
```

---

### **Experiment no. 11**: ["Hack" to resolve the circular import by putting the import statement at end of the module](https://github.com/dragondive/heavens-arena/commit/c013b0b113cbc16b9cd9efc03c53c841a76eb25e)

This was another experiment I did mainly to verify my learning from experiment no. 8. In `message_provider`, I placed the `import answer.deep.answer_provider` statement at the end of the module. I expected that this should resolve the circular import, and indeed, it did.

**NOTE: Once again, this "hack" should not be used in a production environment.**

`message_provider.py`:

```python
def get_message():
    return "Hello " + str(answer.deep.answer_provider.get_answer())

def get_real_message():
    return "The One Piece is REAL!!!"

import answer.deep.answer_provider
```

`main.py`:

```python
import message.message_provider
print(message.message_provider.get_message())

# PS C:\WORK\dragondive\heavens-arena\python\modules> python main.py
# The One Piece is REAL!!!
# Hello 42
```

> **Wisdom**:
> 
> In experiment no. 8, the circular import occured when the interpreter was "executing" `answer_provider` module. It tried to import `message.message_provider.get_real_message`, which it did not know yet. However, when the `import` statement is placed at the end, the interpreter already knows this method before it starts "executing" `answer_provider` module, hence circular import does not occur. 

---

### **Experiment no. 12**: [Always import full module to fix the circular import](https://github.com/dragondive/heavens-arena/commit/19e89816b103859e00f5f687d2263bfab0b0f04e)

This was the final experiment to conclude—for now—my learning from experiment no. 8. I changed the `import` statements everywhere to import the full module, and as expected, this did not cause any circular imports.

`answer_provider.py`:

```python
import message.message_provider

def get_answer():
    print(message.message_provider.get_real_message())
    return 42
```

`main.py`:

```python
import message.message_provider
print(message.message_provider.get_message())

# PS C:\WORK\dragondive\heavens-arena\python\modules> python main.py
# The One Piece is REAL!!!
# Hello 42
```

> **Wisdom**:
> 
> For reasons I cannot fully describe clearly, this experiment reminded me of the _forward declaration_ in C & C++. The forward declaration is used in many cases where two (or more) `class`es (or `struct`s) refer to each other. For example, `class A` holds a pointer (or reference) to `class B`, while `class B` includes an instance of `class A`. In such cases, it becomes necessary to forward declare `class B` (without fully defining it) before the definition of `class A`.
> 
> The module import table in Python feels similar because it holds only a reference to the module, even when it is not fully initialized. If instead, it required the module to be fully interpreted for being imported, then it would be practically impossible to avoid circular imports.

---

## Next steps

In the course of this learning, I also got to know about the following:

* Some special situations where the `__init__.py` file is used in ways besides what has been described in this post.
* Python 3.3 introduced namespace packages in addition to the regular packages. As a result, the `__init__.py` is not strictly required in many common situations. Moreover, a package can be organized across multiple directories.
* `locals()`, `globals()`, `sys.modules` and a few other built-in methods and/or variables that relate to module imports.
* `PEP 420 Implicit Namespace Packages` as already mentioned in this post.

I thought to explore these topics to get better understanding, but I didn't want to risk losing my learning by waiting too long to write it down. Hence, I decided to push this first installment of Gyan out of the door, and I will write about the other topics in a separate post later.