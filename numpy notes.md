# numpy使用手记

## 基础

NumPy的核心是多维数组（ndarrays），与matlab很像

### 初始化

生成一维数组
```python
import numpy as np
a = np.array([0, 1, 2, 3, 4])
b = np.arange(5)
c = np.linspace(0, 4, 5, dtype = 'int')
```
上述结果都一样

生成多维数组
```python
d = np.array([[0, 1, 2],
             [3, 4, 5]])
e = np.arange(6).reshape(2, 3)
```

生成特殊数组
```python
d = np.array([[0, 1, 2],
             [3, 4, 5]])
e = np.arange(6).reshape(2, 3)
```
