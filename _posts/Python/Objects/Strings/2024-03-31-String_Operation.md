---
title : "문자열 조작 : 여러가지 솔루션과 내부구현 비교"



excerpt: "Python String Operation"

categories:
- Python
tags:
- [Python,Object,Builtin,PythonInternal,String]
# classes : wide
toc: true
toc_sticky: true
---
String is a built-in object frequently used in Python. An object is  something that is processed or created. There are various opeartions to achieve the same result. Let's compare these methods.
## Concatenation
String Concatenation means connecting two or more strings. String concatenation can be performed in Python through the join method, + operator, and append method. Let's find out specifically how concatenation occurs through the internal implementation of each operation.
Concatenation of String is implemented in Cpython's Objects/unicodeobject.c file.
###  + operator , += operator
The \+ operator is implemented as a function called PyUnicodeObject_Concat. ([Link](https://github.com/python/cpython/blob/1e4f00ebd8db44a031b61eed0803b4c3d731aed7/Objects/unicodeobject.c#L10944))
This function returns PyObject, the top-level token of a Python object. The concatenated string is returned through this PyObject. The key codes in the internal implementation are as follows:. 
```c

    maxchar = PyUnicode_MAX_CHAR_VALUE(left);
    maxchar2 = PyUnicode_MAX_CHAR_VALUE(right);
    maxchar = Py_MAX(maxchar, maxchar2);
    new_len = left_len + right_len;
        /* Concat the two Unicode strings */
    result = PyUnicode_New(new_len, maxchar);
    if (result == NULL)
        return NULL;
    _PyUnicode_FastCopyCharacters(result, 0, left, 0, left_len);
    _PyUnicode_FastCopyCharacters(result, left_len, right, 0, right_len);
    assert(_PyUnicode_CheckConsistency(result, 1));
    return result;
```
Here, left and right are two strings to be concatenated. The reason for calculating max_char is to check whether only 1 byte, 2 bytes, or 4 bytes are used. This is checked because the memory size taken up varies depending on the encoding method of the string. Add the length of left and the length of right to find the new length new_len. Create a new empty string result using length new_len and max_char. Copy left and right to this result in order. And, the result is returned.
The \+= operator copies the right string to the left string, similar to the above. This operation is defined as a function called PyUnicode_Append. ([Link](https://github.com/python/cpython/blob/1e4f00ebd8db44a031b61eed0803b4c3d731aed7/Objects/unicodeobject.c#L10993
))
The important parts of the internal implementation are:  
```c

    left_len = PyUnicode_GET_LENGTH(left);
    right_len = PyUnicode_GET_LENGTH(right);

    new_len = left_len + right_len;

    if (unicode_modifiable(left)
        && PyUnicode_CheckExact(right)
        && PyUnicode_KIND(right) <= PyUnicode_KIND(left)
        /* Don't resize for ascii += latin1. Convert ascii to latin1 requires
           to change the structure size, but characters are stored just after
           the structure, and so it requires to move all characters which is
           not so different than duplicating the string. */
        && !(PyUnicode_IS_ASCII(left) && !PyUnicode_IS_ASCII(right)))
    {
        /* append inplace */
        if (unicode_resize(p_left, new_len) != 0)
            goto error;

        /* copy 'right' into the newly allocated area of 'left' */
        _PyUnicode_FastCopyCharacters(*p_left, left_len, right, 0, right_len);
    }
    else {
        maxchar = PyUnicode_MAX_CHAR_VALUE(left);
        maxchar2 = PyUnicode_MAX_CHAR_VALUE(right);
        maxchar = Py_MAX(maxchar, maxchar2);

        /* Concat the two Unicode strings */
        res = PyUnicode_New(new_len, maxchar);
        if (res == NULL)
            goto error;
        _PyUnicode_FastCopyCharacters(res, 0, left, 0, left_len);
        _PyUnicode_FastCopyCharacters(res, left_len, right, 0, right_len);
        Py_DECREF(left);
        *p_left = res;
    }
    assert(_PyUnicode_CheckConsistency(*p_left, 1));
    return;


```

First, check whether the size of the left is larger than the size of the right with PyUnicode_KIND, and whether the left can be resized to a size as large as new_len with unicode_resize. If possible, copy right to left next to left_len. Otherwise, it behaves like PyUnicode_Concat.
### Join
Join concatenates string sequences using a given seperator and returns a Unicode string as a result. Join is implemented in Cpython as _PyUnicode_JoinArray. ([Link](https://github.com/python/cpython/blob/1e4f00ebd8db44a031b61eed0803b4c3d731aed7/Objects/unicodeobject.c#L9586
))  
```c
for (i = 0; i < seqlen; i++) {
        size_t add_sz;
        item = items[i];
        add_sz = PyUnicode_GET_LENGTH(item);
        item_maxchar = PyUnicode_MAX_CHAR_VALUE(item);
        maxchar = Py_MAX(maxchar, item_maxchar);
        if (i != 0) {
            add_sz += seplen;
        }

        sz += add_sz;

        last_obj = item;
    }

    res = PyUnicode_New(sz, maxchar);
    /* Catenate everything. */
```
First, the size of each item in the sequence participating in the join is assigned to add_sz, and the size of the seperator is added after the first item. Add all of this to sz and create an empty unicode using maxchar (maximum number of bytes) and sz.

```c
if (use_memcpy) {
        for (i = 0; i < seqlen; ++i) {
            Py_ssize_t itemlen;
            item = items[i];

            /* Copy item, and maybe the separator. */
            if (i && seplen != 0) {
                memcpy(res_data,
                          sep_data,
                          kind * seplen);
                res_data += kind * seplen;
            }

            itemlen = PyUnicode_GET_LENGTH(item);
            if (itemlen != 0) {
                memcpy(res_data,
                          PyUnicode_DATA(item),
                          kind * itemlen);
                res_data += kind * itemlen;
            }
        }
        assert(res_data == PyUnicode_1BYTE_DATA(res)
                           + kind * PyUnicode_GET_LENGTH(res));
    }
    else {
        for (i = 0, res_offset = 0; i < seqlen; ++i) {
            Py_ssize_t itemlen;
            item = items[i];

            /* Copy item, and maybe the separator. */
            if (i && seplen != 0) {
                _PyUnicode_FastCopyCharacters(res, res_offset, sep, 0, seplen);
                res_offset += seplen;
            }

            itemlen = PyUnicode_GET_LENGTH(item);
            if (itemlen != 0) {
                _PyUnicode_FastCopyCharacters(res, res_offset, item, 0, itemlen);
                res_offset += itemlen;
            }
        }
        assert(res_offset == PyUnicode_GET_LENGTH(res));
    }


    return res;
```
If you can use memcpy, use memcpy to copy the seperator as much as sep* kind (character size), and then copy the item using memcpy. If memcpy cannot be used, copy is performed using the internal copy functinon.

### Join vs operator

So, which is faster: Join or operator? Concatenation using an operator connects two strings, but concatenation using Join connects a sequence of strings. If you concatenate multiple strings, the process of creating and copying a new result string as many as the number of string sequences will be repeated. However, if you use Join, the total length of the string sequence will be copied only once. Therefore, LowerBound can be seen as having a larger operator.

## Search
This time, we will learn about the operation of checking whether a query is a substring of a string. In Python, you can check if it is a substring through the str.find() method and the in operator.
### find
In Python, find is called in the order any_find_slice -> ##_find_slice. When searching for this, the find_slice function with the prefix ,ucslib, asciilib, ucs2lib, and ucs4lib is called. However, if you search for it on github, you cannot find it. This is because:
```c

#define STRINGLIB(F)             ucs1lib_##F

#define STRINGLIB(F)             ucs2lib_##F

#define STRINGLIB(F)             asciilib_##F

```  
In the above macro, STRINGLIB(F) means to use ucslib, ucs2lib, and asciilib as prefixes.
```c

Py_LOCAL_INLINE(Py_ssize_t)
STRINGLIB(find_slice)(const STRINGLIB_CHAR* str, Py_ssize_t str_len,
                     const STRINGLIB_CHAR* sub, Py_ssize_t sub_len,
                     Py_ssize_t start, Py_ssize_t end)
{
    return STRINGLIB(find)(str + start, end - start, sub, sub_len, start);
}
```
Actually, the above function is defined in Objects/stringlib/find.h. In other words, STRINGLIB(find_slice) can be considered the same as ascliib_find_slice, ucs1lib_find_slice, and ucs2lib_findslice. STRINGLIB(find_slice) is said to be implemented internally as booyer_more.

### in operator 

```c
static PySequenceMethods unicode_as_sequence = {
    (lenfunc) unicode_length,       /* sq_length */
    PyUnicode_Concat,           /* sq_concat */
    (ssizeargfunc) unicode_repeat,  /* sq_repeat */
    (ssizeargfunc) unicode_getitem,     /* sq_item */
    0,                  /* sq_slice */
    0,                  /* sq_ass_item */
    0,                  /* sq_ass_slice */
    PyUnicode_Contains,         /* sq_contains */
};



```
```c


    switch (kind1) {
    case PyUnicode_1BYTE_KIND:
        result = ucs1lib_find(buf1, len1, buf2, len2, 0) != -1;
        break;
    case PyUnicode_2BYTE_KIND:
        result = ucs2lib_find(buf1, len1, buf2, len2, 0) != -1;
        break;
    case PyUnicode_4BYTE_KIND:
        result = ucs4lib_find(buf1, len1, buf2, len2, 0) != -1;
        break;
    default:
        Py_UNREACHABLE();
    }

```


The in operator internally calls PyUnicode_Contains. PyUnicode_Contains uses STRINGLIB(find), just like find.

### in vs find

Since both in operator and find use the STRINGLIB function, there doesn't seem to be much difference in performance.


## String Transformation
### map
### list comprehension

## Split
The way to split a string in Python is to use re.split from the re library or use the built-in split. In general, built-in split is faster than re.
## replacing
Likewise, there are two methods: re.sub in the re library and replace, a built-in method. As expected, str.replace(), a built-in method, is superior in terms of performance.
## prefix, suffix
In Python, there are ways to obtain a prefix or suffix by using built-in methods or slicing.

### startswith,endswith


The built-in methods for obtaining suffix and prefix in Python include stratswith and endwith. startswith,endswith calls the tailmatch function internally through a loop.

```c

if (PyUnicode_READ(kind_self, data_self, offset) ==
        PyUnicode_READ(kind_sub, data_sub, 0) &&
        PyUnicode_READ(kind_self, data_self, offset + end_sub) ==
        PyUnicode_READ(kind_sub, data_sub, end_sub)) {
        /* If both are of the same kind, memcmp is sufficient */
        if (kind_self == kind_sub) {
            return ! memcmp((char *)data_self +
                                (offset * PyUnicode_KIND(substring)),
                            data_sub,
                            PyUnicode_GET_LENGTH(substring) *
                                PyUnicode_KIND(substring));
        }
        /* otherwise we have to compare each character by first accessing it */
        else {
            /* We do not need to compare 0 and len(substring)-1 because
               the if statement above ensured already that they are equal
               when we end up here. */
            for (i = 1; i < end_sub; ++i) {
                if (PyUnicode_READ(kind_self, data_self, offset + i) !=
                    PyUnicode_READ(kind_sub, data_sub, i))
                    return 0;
            }
            return 1;
        }
    }

    return 0;
```
For tailmatch, if the sizes of the substring and subject string are the same, they should be compared using memcmp. If they are not the same, all cases should be considered and compared. In the case of startswith, endswith, only memcmp will be called because the size of the comparison target and substring are the same.

### slicing  
```c
static PyMappingMethods unicode_as_mapping = {
    (lenfunc)unicode_length,        /* mp_length */
    (binaryfunc)unicode_subscript,  /* mp_subscript */
    (objobjargproc)0,           /* mp_ass_subscript */
};

```

In Python strings, it is defined that mapping subscript is used by defining a function called unicode_subscript.
```c
        src_kind = PyUnicode_KIND(self);
        src_data = PyUnicode_DATA(self);
        if (!PyUnicode_IS_ASCII(self)) {
            kind_limit = kind_maxchar_limit(src_kind);
            max_char = 0;
            for (cur = start, i = 0; i < slicelength; cur += step, i++) {
                ch = PyUnicode_READ(src_kind, src_data, cur);
                if (ch > max_char) {
                    max_char = ch;
                    if (max_char >= kind_limit)
                        break;
                }
            }
        }
        else
            max_char = 127;
        result = PyUnicode_New(slicelength, max_char);
        if (result == NULL)
            return NULL;
        dest_kind = PyUnicode_KIND(result);
        dest_data = PyUnicode_DATA(result);

        for (cur = start, i = 0; i < slicelength; cur += step, i++) {
            Py_UCS4 ch = PyUnicode_READ(src_kind, src_data, cur);
            PyUnicode_WRITE(dest_kind, dest_data, i, ch);
        }
        assert(_PyUnicode_CheckConsistency(result, 1));
        return result;
```

Looking at the internal implementation of the unicode subscript, a slice-length dest unicode is created and then each character is written to dest.

### slicing vs starswith,endswith
Slicing compares only one substring, but startswith and endswith accept multiple substrings as arguments in the form of a tuple and can compare them.


## reverse

There are two ways to reverse a string in Python. You can use slicing or reversed and join. Both slicing and join set dest unicode and then copy src data to dest unicode. However, calling reversed will cause a lot of overhead, so using slicing will be faster.


