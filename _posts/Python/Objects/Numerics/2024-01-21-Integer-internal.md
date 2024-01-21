---
title : "Integer Internal Implementation after 3.12"



excerpt: "Python Intger Internal"

categories:
- Python
tags:
- [Python,Object,Builtin]
# classes : wide
toc: true
toc_sticky: true
---
# Integer Internal after Python 3.12

When using Python, you will use numeric objects such as integers. When using integers, the fact that it supports arbitary precision was surprising, but I just accepted it. Even if you don't worry about this, it can be okay because it is well abstracted. However, I think that in order to become a better engineer, you need to be able to see how things work at low-level rather than high-level. I think that decomposing high-level abstracted operations will be helpful in problem-solving skills because it is the same as decomposing a problem. Additionally, understanding the implementation at a low-level will increase the likelihood of applying this idea to other areas.

## Internal Representation
### Definition
```c
#define PyObject_HEAD                   PyObject ob_base;

typedef uint32_t digit;

typedef struct _PyLongValue {
    uintptr_t lv_tag; /* Number of digits, sign and flags */
    digit ob_digit[1];
} _PyLongValue;

struct _longobject {
    PyObject_HEAD
    _PyLongValue long_value;
};
```

Python's int class is implemented using a struct called _longobject. PyObject_HEAD means define the initial segment of PyObject. PyObject is the basic type of all Python Objects. This PyObject seems to use the layering concept of Compiler's Parser Tree. Compiler's PaserTree hierarchizes tokens and structures the program from the top token to the bottom token. PyObject appears to be the top token. PyObject_HEAD contains information such as the object's reference type and size. PyObject_HEAD is a macro required when creating or manipulating objects in the Python C API. A macro is a rule or pattern that determines which sequence a specific input sequence (string) is mapped to. Here #define is a macro.

lv_tag stores the number of digits, sign, flags required for other operations, etc.
ob_digit is an array, or pointer, to a 32bit int. However, here we can see that ob_digit is an array of size 1. ob_digit is an array where values are stored. If the size is set to 1, a Segmentation Fault error occurs when a size larger than that is assigned. However, Python is known to support arbitary precision.
This uses the flexible array member of C struct. A flexible array member is a concept that allows you to set an array of variable length in the last member of a struct.
```c
result = PyObject_Malloc(offsetof(PyLongObject, long_value.ob_digit) +
                              ndigits*sizeof(digit));
```
In fact, at the time of allocation, it is possible to support arbiratry precision by allocating memory as many digits as necessary.
### ob_digit

Now let's learn about ob_digit. The digit type value is stored in ob_digit. digit is a macro that extends uint32_t. From now on, we will consider digit as a 32-bit integer and only use the term digit.

ob_digit represents an integer value, and each element of the array is a digit. It is treated as a number of digits from 0 to 1, and the number of digits increases as the index increases. If an integer is expressed in decimal notation, a considerable number of digits are required. It is defined to store 32-bit data, but if only numbers up to 10 are stored, it will be a waste of storage space. Therefore, Python uses the $$2^{30}$$ base number system rather than the decimal system, that is, the base $$2^{30}$$ number system. The reason for $$2^{30}$$ is because the maximum base value to prevent overflow and underflow is $$2^{30}$$. 

### 3.12 이전과 차이점
All posts dealing with the internal aspect of Integer are explained based on the struct below.
```c
struct_longobject {
     PyObject_VAR_HEAD
     digit ob_digit[1];
};

typedef struct {
     PyObject ob_base;
     Py_ssize_t ob_size; /* Number of items in variable part */
} PyVarObject;
```
You can see that the definition is different from the struct I explained above. While searching for this, I found information about this in an issue of cpython.

```
I think in the What's New in 3.12 we should at least mention the change in the struct (calling out that using ob_size and ob_digits is no longer supported) and the new unstable public APIs (and what they're for).

```
https://github.com/python/cpython/issues/101291#issuecomment-1646132302

This is a comment from gvanrossum, the founder of Python. This means that ob_size is no longer used and has been replaced by a new representation called lv_tag. In fact, there is no difference in functionality from before, so if you look at cpython's source code, it would be good to just remember it. 

## Add
```c
/* Add the absolute values of two integers. */

static PyLongObject *
x_add(PyLongObject *a, PyLongObject *b)
{
    Py_ssize_t size_a = _PyLong_DigitCount(a), size_b = _PyLong_DigitCount(b);
    PyLongObject *z;
    Py_ssize_t i;
    digit carry = 0;

    /* Ensure a is the larger of the two: */
    if (size_a < size_b) {
        { PyLongObject *temp = a; a = b; b = temp; }
        { Py_ssize_t size_temp = size_a;
            size_a = size_b;
            size_b = size_temp; }
    }
    z = _PyLong_New(size_a+1);
    if (z == NULL)
        return NULL;
    for (i = 0; i < size_b; ++i) {
        carry += a->long_value.ob_digit[i] + b->long_value.ob_digit[i];
        z->long_value.ob_digit[i] = carry & PyLong_MASK;
        carry >>= PyLong_SHIFT;
    }
    for (; i < size_a; ++i) {
        carry += a->long_value.ob_digit[i];
        z->long_value.ob_digit[i] = carry & PyLong_MASK;
        carry >>= PyLong_SHIFT;
    }
    z->long_value.ob_digit[i] = carry;
    return long_normalize(z);
}

```

Python implementation of addition of arbitrary digit integers a and b. It receives two integers, a and b, as parameters, and sets the number of digits of a to always be larger than b.
And, it creates the number z, which has one more digit than a.
We start adding from the 1 digit, and the result of the and operation of carry and PyLong_MASK is assigned to the 1 digit.
PyLong_MASK is an integer with 30 bits masked as 1. 001111… . It is composed of 32 bits like 111. Since an integer in Python is a number with a base of $$2^{30}$$, only the values of 30 bits must be assigned to the ith digit of z. Additionally, it explicitly functions to prevent overflow and underflow.
By shifting the carry bits by 30, addition to the new digits continues. After adding the integer of the smallest digit, addition is performed on the remaining digits.

The basic addition, x_add, is defined and then x_add is called for the various additions. It appears that addition was implemented by borrowing a structure in which the upper tokens of the parser tree are hierarchically divided into basic tokens. 
## Sub
```c
/* Subtract the absolute values of two integers. */

static PyLongObject *
x_sub(PyLongObject *a, PyLongObject *b)
{
    Py_ssize_t size_a = _PyLong_DigitCount(a), size_b = _PyLong_DigitCount(b);
    PyLongObject *z;
    Py_ssize_t i;
    int sign = 1;
    digit borrow = 0;

    /* Ensure a is the larger of the two: */
    if (size_a < size_b) {
        sign = -1;
        { PyLongObject *temp = a; a = b; b = temp; }
        { Py_ssize_t size_temp = size_a;
            size_a = size_b;
            size_b = size_temp; }
    }
    else if (size_a == size_b) {
        /* Find highest digit where a and b differ: */
        i = size_a;
        while (--i >= 0 && a->long_value.ob_digit[i] == b->long_value.ob_digit[i])
            ;
        if (i < 0)
            return (PyLongObject *)PyLong_FromLong(0);
        if (a->long_value.ob_digit[i] < b->long_value.ob_digit[i]) {
            sign = -1;
            { PyLongObject *temp = a; a = b; b = temp; }
        }
        size_a = size_b = i+1;
    }
    z = _PyLong_New(size_a);
    if (z == NULL)
        return NULL;
    for (i = 0; i < size_b; ++i) {
        /* The following assumes unsigned arithmetic
           works module 2**N for some N>PyLong_SHIFT. */
        borrow = a->long_value.ob_digit[i] - b->long_value.ob_digit[i] - borrow;
        z->long_value.ob_digit[i] = borrow & PyLong_MASK;
        borrow >>= PyLong_SHIFT;
        borrow &= 1; /* Keep only one sign bit */
    }
    for (; i < size_a; ++i) {
        borrow = a->long_value.ob_digit[i] - borrow;
        z->long_value.ob_digit[i] = borrow & PyLong_MASK;
        borrow >>= PyLong_SHIFT;
        borrow &= 1; /* Keep only one sign bit */
    }
    assert(borrow == 0);
    if (sign < 0) {
        _PyLong_FlipSign(z);
    }
    return maybe_small_long(long_normalize(z));
}
```
For subtraction, a default subtraction called x_sub is defined. After subtraction in the loop, the and operation of the result of masking and subtraction is assigned to the ith digit.
I was confused about this at first. It took me a while to understand why it was assigned that way. The highest bit of the integer data type in C language is the sign bit. I vaguely knew it was a sign bit, but it plays a very important role in bit operations. Exactly, when the highest bit becomes 1, it means $$-2^{31}$$. I thought it was just a symbolic meaning that the highest bit in 2's complement or 1's complement expression was 1, but in internal operation, it meant a negative number.
If the calculated value of the digit is negative, the two highest bits will be set to 1. If you perform the and operation with PyLong_MASK, the result is $$ 2^{30}$$. In other words, it has the same effect as borrowing 1 from the digit above.
Next, shift the resulting value to the right by 30 bits and check whether the sign bit is 1. If it is 1, set borrow to 1 and then start by subtracting 1 when calculating the next digit.
This lets us know that we have implemented subtraction as we know it. 

## Mul
Multiplication is implemented with the karatsuba algorithm. The Karatsuba algorithm is effective for multiplication of large digits. This has an upper bound of $$O(n^{\log_{2}{3}})$$ for the multiplication of two n-digit numbers. It shows better performance than the classic $$ O(n^{2})$$ algorithm. For example, if n=1024, the existing algorithm requires 1,048,576 single-digit multiplications, but karatsuba requires 59,049 single-digit multiplications.
If the number of digits is small, perform the traditional multiplication algorithm. When performing a traditional multiplication algorithm, if the two numbers have the same number of digits, the multiplication algorithm in the University of Waterloo's [reference material](https://cacr.uwaterloo.ca/hac/about/chap14.pdf) is performed. If the digits are different, perform the multiplication algorithm you learn in school.
```python
for (i = 0; i < size_a; ++i) {
            twodigits carry = 0;
            twodigits f = a->long_value.ob_digit[i];
            digit *pz = z->long_value.ob_digit + i;
            digit *pb = b->long_value.ob_digit;
            digit *pbend = b->long_value.ob_digit + size_b;

            SIGCHECK({
                    Py_DECREF(z);
                    return NULL;
                });

            while (pb < pbend) {
                carry += *pz + *pb++ * f;
                *pz++ = (digit)(carry & PyLong_MASK);
                carry >>= PyLong_SHIFT;
                assert(carry <= PyLong_MASK);
            }
            if (carry)
                *pz += (digit)(carry & PyLong_MASK);
            assert((carry >> PyLong_SHIFT) == 0);
        }

``` 

Here, f is the value of the ith digit of a, pz is a pointer indicating the digit of the multiplication result, pb is a pointer pointing to the digit of b, and pbend is a pointer pointing to the first digit of b. This code simply performs C language multiplication and then prevents overflow and underflow and shifts the carry. `carry += *pz + *pb++ * f;` adds the ith digit of a * the ith digit of b to the ith digit of z. Use the inplace operator to increase the value of pb, that is, the number of digits. In `*pz++ = (digit)(carry & PyLong_MASK);`, assign a value smaller than the size of base to the ith digit of pz with PyLong_MASK. At the same time, the value of pz, that is, the number of digits of z, is increased. The optimization of pointer's arithmetic operation and inplace operation combined may be confusing, so I explained it separately.

## Divide
If you look inside Python's division, single-digit division is simply performed using the C language division operation. However, when the number of digits increases, it is optimized using Algorithm D proposed by Professor Donald Knuth. This is included in chapter 4.3.1 of The Art of Computer Programming, Vol.2. I will not introduce this separately. Many division tokens utilize the basic operation x_divrem, and it would be good to know that x_divrem is internally composed of Algorithm D.
In addition, modular operators, floor operations, and operations that return both the quotient and remainder are also implemented. For single bits, they are implemented using the basic operations of the C language, and for long digits, they are implemented using x_divrem. .

## Integer up to 256

It's simple, but integers up to 256 are cached in memory in advance. Therefore, each time an operation is performed, an object is created, but since integers up to 256 refer to values in memory, a new object is not created.
## Conclusion
So far, I have not been interested in how the actual operations are implemented while using operators such as +, -, *,/, %,//, etc. By actually looking at the internal implementation, I learned in depth why 2's complement expression is effective. Integers are 32-bit ints that support arbitrary precision, but in the case of floats, operations are relatively simple because they utilize C's double type as is. Basic operations except divide and mul support simple operations that pass the number of digits, but I learned how various ideas used in this process are implemented at a low level. I felt the need to study Computer Architecture in depth again.
## References

https://www.codementor.io/@arpitbhayani/how-python-implements-super-long-integers-12icwon5vk  


https://rushter.com/blog/python-integer-implementation/  


https://github.com/python/cpython/pull/102464  


https://github.com/python/cpython/blob/main/Include/cpython/longintrepr.h#L89  


https://github.com/python/cpython/issues/101291  


https://github.com/python/cpython/blob/main/Include/cpython/longintrepr.h#L47C1-L55C27


https://ko.wikipedia.org/wiki/%EC%B9%B4%EB%9D%BC%EC%B6%94%EB%B0%94_%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98