# Explain Python with Cython

Full tutorial on Colab: [link](https://colab.research.google.com/drive/1abuBuRwBdFWbKTQxivA6q5ZUDlgGS9-c?usp=sharing).

The following example shows an artefact of Python - a result of Python built-in types (integers and doubles) optimizations.


```python
rate_orig = 12.43


def add_inplace(rate):
    # False, if 'rate' is a copy of 'rate_orig'
    # True, if 'rate' and 'rate_orig' are the same (point to the same virtual memory address)
    print(rate is rate_orig)  # prints True
    rate += 20
    print(rate is rate_orig)  # prints False
```

What is going on in `rate += 20` line?

Cython is a nice tool not only to optimize Pyhon code but also to investigate it from the C perspective. Running `cython -a` on the snippet above shows that the in-place addition operation is split into multiple logical instructions.

```
+1: rate_orig = 12.43
 2: 
+3: def add_inplace(rate):
+4:     print(rate is rate_orig)
+5:     rate += 20
  __pyx_t_1 = __Pyx_PyInt_AddObjC(__pyx_v_rate, __pyx_int_20, 20, 1, 0); if (unlikely(!__pyx_t_1)) __PYX_ERR(0, 5, __pyx_L1_error)
  __Pyx_GOTREF(__pyx_t_1);
  __Pyx_DECREF_SET(__pyx_v_rate, __pyx_t_1);
  __pyx_t_1 = 0;
+6:     print(rate is rate_orig)
```

Running locally:

```bash
cythonize -ai mymodule.c
python -c 'import mymodule'
```
