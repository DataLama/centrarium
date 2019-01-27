---
layout: post
title: "[Tensorflow 기초] 1-1. 텐서 살펴보기"
date: 2019-01-27
author: datalama
categories: "tensorflow"
tags: tensorflow low-level
---

keras는 정말 아름다운 딥러닝 프레임워크다. 딥러닝을 처음 공부하는 입장에서 keras는 직관적이고 편하다. 특히, 간단한 딥러닝 프로젝트는 keras로도 충분히 좋은 결과를 얻을 수 있다. 하지만, 다음과 같은 고민을 하기 시작하면서 keras는 한계에 부딪히게 되었다.

* 학습한 딥러닝 모델을 serving.
* custom loss function, optimizer
* 클라우드에서 분산 학습하기.
* tf.estimator로 학습된 모델을 transfer learning하기.

그래서 빠르게 tensorflow의 low-level api들을 훑어보려고 한다. 곧 Tensorflow 2.0 시대가 도래하는 이 시점에서 tensorflow의 low-level을 깊게 공부하는게 맞는 일인가라는 의문이 들긴 하지만... 일을 하기 위해서는 해야되니 빠르게 살펴보자.

이번 포스트에서는 **tensorboard, tf.constant, tf.Operation, tf.dtypes**를 간단히 정리한다.

---


```python
import tensorflow as tf

## use eager_execution
tf.enable_eager_execution()
```

## 1. TensorBoard

> TensorBoard는 DeepLearning black-box모델의 내부를 살펴보는데 중요한 역할을 한다.

* TensorBoard tutorial - https://www.youtube.com/watch?v=eBbEDRsCmv4

### Tensorboard를 만드는 기초 step

* writer를 만들자.

``` python
    writer = tf.summary.FileWriter('./board/sample_2', sess.graph)
```

* session을 실행하고 학습된 노드를 추가하자.

``` python
    summary = sess.run(merged, feed_dict={X: step * 1.0, Y: 2.0})
    writer.add_summary(summary, step)
```

* command 창에서 아래의 커맨드를 실행한다.(graph log data가 저장된 경로와 tensorboard를 open할 port번호)

```
    tensorboard --logdir="./graphs" --port 6006
```

## 2. Constant op

* tensorflow에서 constant는 graph 내에서 상수, 시퀀스, 난수를 생성하는데 주로 사용한다.
* numpy와 유사한 메서드들을 통해 constant를 생성할 수 있다.

### 1) 핵심 constant 생성 함수

* __`tf.constant()`__

``` python
tf.constant(
    value,
    dtype=None,
    shape=None,
    name='Const',
    verify_shape=False
)
```

> __value__: A constant value (or list) of output type dtype. <br>
> __verify_shape__: Boolean that enables verification of a shape of values.

value는 list형태로 넘기면 되고, name은 적당히 정해준다.(name은 tensorboard에서 node들을 나타내는데 필요함)


```python
## tf.constant()
# constant of 1d tensor
a = tf.constant([2,2], name = "vector")

# constant of 2X2 tensor(matrix)
b = tf.constant([[0,1],[2,3]], name = 'matrix')
```

* __`tf.zeros`__

``` python
tf.zeros(
    shape,
    dtype=tf.float32,
    name=None
)
```


```python
## tf.zeros()
print(tf.zeros ([2 ,3 ], tf.int32))
```

    tf.Tensor(
    [[0 0 0]
     [0 0 0]], shape=(2, 3), dtype=int32)


* __`tf.zeros_like`__

``` python
    tf.zeros_like(
    tensor,
    dtype=None,
    name=None,
    optimize=True
)
```

input tensor와 dtype, shape이 동일한 tensor를 만들어준다. $\longrightarrow$ 뭔가 input데이터 갖고 장난칠 때 많이 사용할 듯.


```python
## tf.zeros_like()
print(tf.zeros_like(b))
```

    tf.Tensor(
    [[0 0]
     [0 0]], shape=(2, 2), dtype=int32)


* __`tf.ones`__
``` python
tf.ones(
    shape,
    dtype=tf.float32,
    name=None
)
```

* __`tf.ones_like`__

``` python
tf.ones_like(
    tensor,
    dtype=None,
    name=None,
    optimize=True
)
```

`tf.zeros`들과 비슷한 애들 다른 점은 0 대신 1로 채운다는 것이다.

* __`tf.fill`__

``` python
tf.fill(
    dims,
    value,
    name=None
)
```

얘는 지정한 값으로 채움


```python
## tf.fill()
print(tf.fill([2, 3], 8))
```

    tf.Tensor(
    [[8 8 8]
     [8 8 8]], shape=(2, 3), dtype=int32)


* __`tf.lin_space`__

``` python
tf.lin_space(
    start,
    stop,
    num,
    name=None
)
```

> create a sequence of num evenly-spaced values are generated beginning at start.<br>
> 생성되는 원소의 갯수$${stop - start}\over{num - 1}$$


```python
## tf.lin_space()
# tf.linspace() 랑 똑같음.
print(tf.lin_space(10.0 ,13.0 ,4 ,name='linspace'))
```

    tf.Tensor([10. 11. 12. 13.], shape=(4,), dtype=float32)


* __`tf.range`__

``` python
tf.range(limit, delta=1, dtype=None, name='range')
tf.range(start, limit, delta=1, dtype=None, name='range')
```

> create a sequence of numbers that begins at start and extends by increments of delta up to but not including limit<br>
> 약간..리스트 slicing 느낌?


```python
## tf.range()
start = 3
limit_1 = 18
limit_2 = 1
delta = 0.5
```


```python
## 증가 - 시작, 끝, 공차
print(tf.range(start, limit_1, delta))
```

    tf.Tensor(
    [ 3.   3.5  4.   4.5  5.   5.5  6.   6.5  7.   7.5  8.   8.5  9.   9.5
     10.  10.5 11.  11.5 12.  12.5 13.  13.5 14.  14.5 15.  15.5 16.  16.5
     17.  17.5], shape=(30,), dtype=float32)



```python
## 감소 - 시작, 끝, 공차
print(tf.range(start, limit_2, -delta))
```

    tf.Tensor([3.  2.5 2.  1.5], shape=(4,), dtype=float32)



```python
## python range스럽게
print(tf.range(limit_1))
```

    tf.Tensor([ 0  1  2  3  4  5  6  7  8  9 10 11 12 13 14 15 16 17], shape=(18,), dtype=int32)


#### *주의) tensorflow의  sequence는 interable 아님. $\longrightarrow$ 즉, 괜히 tensor로 반복문 돌리려고 하지마.*

### 2) 기타 constant 생성 함수

* 주로 random sample과 관련된 함수들.
* __tf.random_shuffle__과 __tf.set_random_seed__는 한번 짚고 넘어가면 좋을듯.

```python
    tf.random_normal
    tf.truncated_normal
    tf.random_uniform
    tf.random_shuffle
    tf.random_crop
    tf.multinomial
    tf.random_gamma
    tf.set_random_seed
```

## 3. Math Operations

### 1) 7가지 나눗셈 연산

tensorflow의 기본 나눗셈 방법은 총 7가지가 있다. 거시적인 관점에서 보면 `tf.div`는 tensorflow style의 나눗셈이고 `tf.divide` python 스타일의 나눗셈이다.


```python
## define
a = tf.constant([2, 2], name = 'a')
b = tf.constant([[0, 1], [2, 3]] , name = 'b')

print(a) # 2,
print(b) # 2, 2
```

    tf.Tensor([2 2], shape=(2,), dtype=int32)
    tf.Tensor(
    [[0 1]
     [2 3]], shape=(2, 2), dtype=int32)


다른 애들은 왜 쓰는지 지금은 잘 모르겠지만... 나중에 모델링하다보면 알지 않을까?


```python
print(tf.div(b, a)) #정수나눗셈
print(tf.divide(b, a)) #실수 나눗셈
print(tf.truediv(b, a))
print(tf.floordiv(b, a))
print(tf.truncatediv(b, a))
print(tf.floor_div(b, a))
```

    tf.Tensor(
    [[0 0]
     [1 1]], shape=(2, 2), dtype=int32)
    tf.Tensor(
    [[0.  0.5]
     [1.  1.5]], shape=(2, 2), dtype=float64)
    tf.Tensor(
    [[0.  0.5]
     [1.  1.5]], shape=(2, 2), dtype=float64)
    tf.Tensor(
    [[0 0]
     [1 1]], shape=(2, 2), dtype=int32)
    tf.Tensor(
    [[0 0]
     [1 1]], shape=(2, 2), dtype=int32)
    tf.Tensor(
    [[0 0]
     [1 1]], shape=(2, 2), dtype=int32)



```python
print(tf.realdiv(b, a)) # only 실수 input에 대한 나눗셈... error를 뿜음.
```


    ---------------------------------------------------------------------------

    InternalError                             Traceback (most recent call last)

    <ipython-input-13-f31c4a59c6cb> in <module>()
    ----> 1 print(tf.realdiv(b, a)) # only 실수 input에 대한 나눗셈... error를 뿜음.


    ~/anaconda3/envs/tensorflow_p36/lib/python3.6/site-packages/tensorflow/python/ops/gen_math_ops.py in real_div(x, y, name)
       5902       else:
       5903         message = e.message
    -> 5904       _six.raise_from(_core._status_to_exception(e.code, message), None)
       5905
       5906


    ~/anaconda3/envs/tensorflow_p36/lib/python3.6/site-packages/six.py in raise_from(value, from_value)


    InternalError: Could not find valid device for node name: "RealDiv"
    op: "RealDiv"
    input: "dummy_input"
    input: "dummy_input"
    attr {
      key: "T"
      value {
        type: DT_INT32
      }
    }
     [Op:RealDiv]


### 2) 기타 사칙 연산


```python
print(a)
print(b)
```

    tf.Tensor([2 2], shape=(2,), dtype=int32)
    tf.Tensor(
    [[0 1]
     [2 3]], shape=(2, 2), dtype=int32)


#### _주의) numpy와 마찬가지로 broadcasting이 구현되어 있음._

* __덧셈__


```python
print(a + b)
```

    tf.Tensor(
    [[2 3]
     [4 5]], shape=(2, 2), dtype=int32)


* __뺄셈__


```python
print(a - b)
```

    tf.Tensor(
    [[ 2  1]
     [ 0 -1]], shape=(2, 2), dtype=int32)


* __곱셈(element-wise)__


```python
print(a * b)
```

    tf.Tensor(
    [[0 2]
     [4 6]], shape=(2, 2), dtype=int32)


### 3) tensordot

```python
    tf.tensordot(
        a,
        b,
        axes,
        name=None
    )
```
* **axes**: 축으로 scalar 또는 리스트를 지정할 수 있다.

* 어떻게 사용하느냐에 따라서 Outer Product(Tensor Product) 또는 Inner Product를 정의할 수 있다.
* Def) sums the product of elements from `a` and `b` over the indices specified by `a_axes` and `b_axes`.


```python
## define
c = tf.constant([[2, 2],[3,1]], name = 'c')
print(c)
d = tf.constant([[0, 1], [2, 3]] , name = 'd')
print(d)
```

    tf.Tensor(
    [[2 2]
     [3 1]], shape=(2, 2), dtype=int32)
    tf.Tensor(
    [[0 1]
     [2 3]], shape=(2, 2), dtype=int32)


**case 1**<br>
matrix multiplication


```python
## 스칼라 방식
print(tf.tensordot(c,d,1))

## 리스트 방식
print(tf.tensordot(c,d,[[1],[0]])) # 앞 tensor의 열과 뒷 tensor의 행을 mapping
print(tf.tensordot(c,d,[[0],[1]])) # 반대로 matmul
```

    tf.Tensor(
    [[4 8]
     [2 6]], shape=(2, 2), dtype=int32)
    tf.Tensor(
    [[4 8]
     [2 6]], shape=(2, 2), dtype=int32)
    tf.Tensor(
    [[ 3 13]
     [ 1  7]], shape=(2, 2), dtype=int32)


**case 2**<br>
outer product

$$
C \otimes D =
\begin{pmatrix}
c_{11}D & \cdots & c_{1n}D \\
\vdots & \ddots & \vdots \\
c_{m1}D & \cdots & c_{mn}D
\end{pmatrix}
$$


```python
print(tf.tensordot(c,d,0))
```

    tf.Tensor(
    [[[[0 2]
       [4 6]]

      [[0 2]
       [4 6]]]


     [[[0 3]
       [6 9]]

      [[0 1]
       [2 3]]]], shape=(2, 2, 2, 2), dtype=int32)


**case 3**<br>
High dim
- 리스트 형태를 활용해서 원하는 축들을 기준으로 tensordot을 수행할 수 있다.
- eager execution을 통해 직접확인하면서 해보자.

#### _주의) shape뿐만 아니라 dtype이 다르면 연산에서 에러가 발생할 수 잇음 ._

### 4) broadcasting

Broadcasting을 간단히 설명하면, 차원이 다른 두 tensor 사이에 연산을 할 때, 작은 tensor를 큰 tensor의 shape에 맞춰 확장되어 연산을 진행하는 것이다. 이 과정은 자동으로 진행되고, **주의할 점은 broadcasting되는 것의 shape 뒷부분이 일치해야된다.(ex. `(100, 10, 30) / (10, 30)`)**

**Broadcasting**은 다음의 두 단계를 거친다.
> * 큰 tensor의 dim에 맞춰서 작은 tensor에 축들이 추가된다.
> * 작은 tensor의 원래 값이 새로운 축들에 맞춰서 반복되어 커진 tensor의 값을 채워간다.


```python
## 두 tensor 정의
a = tf.random_uniform((3,2,2))
b = tf.random_uniform((2,2))
print(a)
print()
print(b)
```

    tf.Tensor(
    [[[0.05262065 0.5109223 ]
      [0.811394   0.24999392]]

     [[0.68466794 0.04673004]
      [0.9466969  0.17511058]]

     [[0.913242   0.00363576]
      [0.69599676 0.12669218]]], shape=(3, 2, 2), dtype=float32)

    tf.Tensor(
    [[0.48536217 0.30512047]
     [0.48633003 0.15009856]], shape=(2, 2), dtype=float32)



```python
## 더 작은 tensor(b)가 broadcasting되어 동일한 shape을 만들어준 후 덧셈 연산을 진행하는 것을 확인할 수 있다.
a+b
```




    <tf.Tensor: id=140, shape=(3, 2, 2), dtype=float32, numpy=
    array([[[0.5379828 , 0.8160428 ],
            [1.297724  , 0.40009248]],

           [[1.1700301 , 0.3518505 ],
            [1.4330269 , 0.32520914]],

           [[1.3986042 , 0.30875623],
            [1.1823268 , 0.27679074]]], dtype=float32)>



## 4. Data Types

* tensorflow 대부분의 데이터 타입은 numpy의 데이터 타입와 interchangeble하다.(대체로!)
* > 0-d tensor == scalar <br>
  > 1-d tensor == vector <br>
  > 2-d tensor == matrices <br>
  > 3-d tensor == tensor

* tensorflow에는 qint가 정의되어 있다. (qbit?)
* 문자열을 numpy에서는 object로 다루는데 tensorflow는 string으로 다룬다.
* 가능하면 dtype을 명시하는 습관을 들여라. $\Longrightarrow$ 데이터 타입에 따른 에러를 미연에 방지할 수 있음.

---

본 포스트는 cs20과 tensorflow 공식 문서를 참고하여 만든 것입니다.
