---
layout: post
title: 주피터랩을 마크다운 파일 테스트
categories: TIL
---

# 마크다운 문서를 위한 테스트입니다.


```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
%matplotlib inline
```


```python
def pprint(arr):
    print("type: {}".format(type(arr)))
    print("shape: {}, dimension: {}, dtype: {}".format(arr.shape, arr.ndim, arr.dtype))
    print("Array's Data:\n",arr)
```


```python
a = np.linspace(0, 1, 5)
pprint(a)
```

    type: <class 'numpy.ndarray'>
    shape: (5,), dimension: 1, dtype: float64
    Array's Data:
     [0.   0.25 0.5  0.75 1.  ]
    


```python
plt.plot(a, 'o')
plt.show()
```


![png](C:/Users/Owner/Desktop/Programming/kjh50400.github.io/assets/output_4_0.png)


이것으로 2020-01-26 TIL을 마치겠습니다
