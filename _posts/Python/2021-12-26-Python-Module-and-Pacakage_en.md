---
title : "Module and Package"



excerpt: "Module and Package"

categories:
  - Python
tags:
  - [Python,Module,Package]
# classes : wide
toc: true
toc_sticky: true
---
## Python Module and Pacakage

The final project was done by BoostCampAi Tech. It was a medium-sized project in my opinion. Since it was a project that went through the stages of user flow, system design, prototype, and serving, I managed to manage the project in multiple modules and package units beyond this. In the early days, when importing a module, it was managed with an absolute path. To manage modules and packages, I imported them by specifying a detailed path with hardcoding. Realizing this problem, I changed it to relative import again, but because I was the only one studying the package and module, the team members did not understand it, so I went back to the absolute path. I thought that if I had informed my team members well at this time. In addition, when learning ML models, it was okay to simply manage multiple modules in one directory, but if I collaborate with engineers in multiple domains, I can project a package unit. I think that I will manage this, so I would like to take an opportunity to organize this content properly. (It is a very large amount of content, so I will organize the contents I studied while doing the project and additionally organize and update it.) (This book is about Python -Look at the cookbook and study it, and it is a closed book that I wrote in my word.)

## hierachical package and module

```python
graphics 
	__init.py
	primitive/
		__init__.py
		line.py
		fill.py
		text.py
	pic/
		__init__.py
		png.py
		jpg.py
```

Let's say there is a package called graphics. How do I import `fill.py`, `text.py` and `png.py`?

```python
import graphics.primitive.line
from graphics.primitive import fill
from graphics.pic import png
```

## `__init__.py`  

In python, you can include `__init__.py` in each package. When this package is imported, `__init__.py` is executed.

When you import `graphics.primitive.line`, you first import `graphics.__init__.py` and `graphics.primitive.__init__.py`. `__init__.py` doesn't have to be blank.

```python
from . import png
from . import jpg
```

If, let's say that `pic.__init__.py` is written as above.

```python
import graphics.primitive.line
from graphics.primitive import fill
import pic
```

If you import pic, you will import png and jpg.

`__init__.py` was mandatory before Python 3.3, but it is not necessary now.

##  `__all__`

When import * is used, all attributes and methods in the module are imported every time. I use import *, but is there a more sophisticated way to import a module? Just use `__all__ = []` .

```python
def spam():
	pass
	
def mac():
	pass

blah = 42

__all__=['spam' , 'mac']
```

이 module을 import를 하게 되면, namespace에 `spam` , `mac`  만이 포함 되어있지않음을 확인할 수 있습니다. 만약에 , empty list가 `__all__` 에 assign 된다면 아무것도 import 하지 않을 것입니다.

## realtive import

Each time, you can import a module through hard coding, but you can reduce the amount of coding by using a relative path.

```python

    A
        __init__.py
        grok.py
        Gabi.py
    B
        __init__.py
        mba.py
        ccd.py
```

There are such packages.

```python
from . import Gabi
```

If you import Gabi from `grok`, you will use `.`. `.` is a relative path meaning the current directory.

```python
from ..B import mba
```

When importing mba from `grok`, `..` is used. `..` means the same level as the package to which it belongs.

`.` , `..` These two are very useful, but not universal.

```python
from . import Gabi  # OK
import .Gabi   # Error
```

This syntax is only allowed in import statements using from . You cannot import `.Gabi` .

```python
E
	__init__.py
    c.py
my_package
	__init__.py
    A
    	__init__.py
        grok.py
        Gabi.py
    B
    	__init__.py
        mba.py
        ccd.py


from ...E import c  # Error
```

Dotted name patterns cannot go beyond the definition of the package to which they belong. Let's say we import a module called c from package D, which is at a higher level than the level of package A. It seems to be something, but we encounter an error in the syntax.

And, this relative import must be configured while following the rules for modules and packages.

Relative import does not work in a simple top-level (starting point) module script, not a package. Also, if the module in the package is executed as a simple script, it will not work.



```python
python my_package/A/grok.py  # relative import error

python -m my_package.A.grok  # OK
```



