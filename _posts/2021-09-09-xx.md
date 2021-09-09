```python
from sklearn.datasets import load_diabetes #사이킷런에서 당뇨병 환자 데이터 가져옴
diabetes = load_diabetes()
```


```python
print(diabetes.data.shape, diabetes.target.shape) # 입력 데이터와 타깃 데이터 크기
```

    (442, 10) (442,)



```python
diabetes.data[0:3] # 당뇨병 환자 데이터 앞부분 3개만
```




    array([[ 0.03807591,  0.05068012,  0.06169621,  0.02187235, -0.0442235 ,
            -0.03482076, -0.04340085, -0.00259226,  0.01990842, -0.01764613],
           [-0.00188202, -0.04464164, -0.05147406, -0.02632783, -0.00844872,
            -0.01916334,  0.07441156, -0.03949338, -0.06832974, -0.09220405],
           [ 0.08529891,  0.05068012,  0.04445121, -0.00567061, -0.04559945,
            -0.03419447, -0.03235593, -0.00259226,  0.00286377, -0.02593034]])




```python
diabetes.target[:3] # 당뇨병 환자 타깃 데이터 앞부분 3개만
```




    array([151.,  75., 141.])




```python
import matplotlib.pyplot as plt
plt.scatter(diabetes.data[:, 2], diabetes.target)
plt.xlabel('x')
plt.ylabel('y')
plt.show() #x축은 입력 데이터, y축은 타깃 데이터로 나타낸 그래프
```


    
![png](/images/Numerical_prediction_files/Numerical_prediction_4_0.png)
    



```python
#훈련 데이터 준비
x = diabetes.data[:, 2] #입력 데이터의 세번재 특성 분리하여 x에 저장
y = diabetes.target  #타깃 데이터를 y에 저장
```


```python
w = 1.0
b = 1.0 # 션형 회귀에서의 가중치(기울기) = w, 절편 = b 를 둘다 1로 임의로 지정

# y = w * x + b 형태
```


```python
y_hat = x[0]*w + b # 입력 데이터의 첫번째 샘플 x[0]에 대한 예측 데이터 y_hat을 구해본다.
print(y_hat)
```

    1.0616962065186886



```python
print(y[0]) # 첫 번째 샘플 데이터 x[0]에 대응하는 타깃값 y[0]을 출력
```

    151.0



```python
# y[0]과 우리가 예측한 y_hat과 차이가 너무 많이남 -> 우리가 만든 선형 방정식은 현재 데이터에 맞지 않다는 뜻

# w, b를 바꿔야 한다.!! 어떻게 ? y_hat이 y[0]에 비슷해 지도록
```


```python
w_inc = w + 0.1 # w를 변화시킨 값 w_inc는 w에 0.1을 더한다 (0.1은 아무 의미 x 그냥 더해보는 것)
y_hat_inc = x[0] * w_inc + b # 이번 선형 방정식에 w 대신 w_inc로 바꾼다. 
print(y_hat_inc) # w_inc로 가중치를 바꾸면서 나오게 된 에측 결과를 y_hat_inc
```

    1.0678658271705574



```python
#이전 예측값 y_hat보다 y_hat_inc가 좀더 y[0]에 가까워 지긴 했다.
```


```python
#w가 0.1 증가하면서 y_hat이 얼마나 변했는지 확인해 보면

w_rate = (y_hat_inc - y_hat) / (w_inc - w) # w_rate는 예측값의 증가 정도를 나타낸다. 
print(w_rate) # 이 w_rate를 훈련 데이터 x[0]에 대한 w의 변화율 이라고 한다.
```

    0.061696206518688734



```python
print(x[0]) # 뭐지 ??? x[0]값과 w_rate(x[0]에 대한 w의 변화율)이 같은 값이다....

# w가 1만큼 증가한다고 가정해보자 그러면 y_hat은 x[0]만큼 증가 할 것 아닌가
# w 변화율 = w가 1만큼 증가할 때 y_hat이 얼만큼 증가하는지에 대한 의미와 같으니
# 당연히 선형 방정식에서는 w 변화율은 x값과 같을 것이다.
# 식으로도 증명 가능 
#w_rate == (y_hat_inc - y_hat) / (w_inc - w) == {(x[0] * w_inc + b) - (x[0] * w + b)} / (w_inc - w)
# = {x[0] * ((w + 0.1) - w)} / ((w + 0.1) - w) = x[0]
```

    0.0616962065186885



```python
# 이제는 가중치(기울기)를 업데이트 하는 방법에 대해 알아보자. (y_hat과 y가 비슷해지게 가중치를 업데이트 하는 방법ㅂ)

w_new = w + w_rate # 기존 가중치에 가중치의 변화율을 더하여 가중치를 업데이트 한다.
print(w_new)

#왜 이런 방법을 ?
# 1. 변화율이 양수일 때 : y_hat이 y에 미치지 못하는 상황에서 변화율이 양수이면 w를 증가 시켜야 y_hat이 증가한다.
# 이때 w의 변화율이 양수이므로 w에 w의 변화율을 더해주면 w는 증가하게 된다.

# 2. 변화율이 음수일 때 : y_hat이 y에 미치지 못하는 상황에서 변화율이 음수이면 w를 감소 시켜야 y_hat이 증가한다.
# 이때 w의 변화율이 음수이므로 w에 w의 변화율을 더해주면 w는 감소하게 된다.

# 즉 변화율이 양수이든 음수이든 w에 w변화율을 더해주면 y_hat이 y에 가까워지는 방향으로 w가 변한다.
```

    1.0616962065186888



```python
# 이전에는 w(가중치)를 변화시켰다면 b(절편)을 변화시켜 보자.
# 절편도 마찬가지 예측값이 타깃값에 가까워지도록 변화시키는 것이 목적!!

b_inc = b + 0.1 # 절편 b에 0.1을 더해보자 (0.1은 아무 의미 없음 그냥 더해보는 것)
y_hat_inc = x[0] * w + b_inc
print(y_hat_inc) # y_hat_inc가 y_hat 보다 y에 가까움
```

    1.1616962065186887



```python
b_rate = (y_hat_inc - y_hat) / (b_inc - b)
print(b_rate) #이번에 b의 변화율에 대해 구해보면 1이 나온다 딱 1!!!!

#선형 방정식에서 b가 1만큼 증가하면 y_hat또한 당연히 1만큼 증가하겠지
#식으로도 증명가능
#b_rate = (y_hat_inc - y_hat) / (b_inc - b) ={} (x[0] * w + b_inc) - (x[0] * w + b)} / (b_inc - b)
# = {(b + 0.1) - b} / {(b + 0.1) - b} = 1
```

    1.0



```python
# w와 마찬가지로 b(절편) 또한 업데이트를 하여 좀 더 타깃값과 유사한 예측값을 내놓게 해야한다.
b_new = b + b_rate # b_rate = 1
# w 업데이트와 같은방법으로 b(절편) 또한 기존 b에 b_rate를 더하여 b를 갱신한다. (b_rate 대신 1로 해도 됨)
```


```python
'''
위의 방법으로 w, b를 업데이트 한다고 생각해보자
w, b를 변화시켜도 y_hat이 매우 조금 변한다.
이 방법으로는 y와 y_hat의 차이가 매우 큰 경우 y_hat을 y와 유사한 값으로 업데이트 시키는 시간이 매우 오래 걸릴 것 이다.

따라서 위으 문제점을 해결하는 방법으로 "오차 역전파"를 이용한다ㅏ.

오차 역전파 (backpropagation)
'''
```


```python
err = y[0] - y_hat # 에러 값을 구한다. 에러값 = 실제 값 - 예측값 
'''
오차 역전파 : 
이전에 w, b를 갱신할때 w에는 w_rate를 더해주고, b에는 b_rate를 더해줬음
하지만 오차 역전파 방법은 w에 w_rate * 에러값, b에는 b_rate * 에러값 을 더해주는 방식으로 w와 b를 갱신한다.

왜 이런방법을 쓸까?

w_new = w + w_rate * err 
b_new = b + b_rate * err  #

print(w_new, b_new)
```

    911.1983904448475 156.2427820067777



```python
y_hat = x[1] * w_new + b_new
err = y[1] - y_hat
w_rate = x[1]
b_rate = 1 
w_new = w_new + w_rate * err
b_new = b_new + b_rate * err
print(w_new, b_new)
```

    14.132317616381767 75.52764127612664



```python
for x_i, y_i in zip(x, y):
  y_hat = x_i * w + b

  err = y_i - y_hat

  w_rate = x_i
  w = w + w_rate * err

  b_rate = 1
  b = b + b_rate * err

print(w, b)
```

    587.8654539985689 99.40935564531424



```python
plt.scatter(x, y)
pt1 = (-0.1, -0.1 * w + b)
pt2 = (0.15, 0.15 * w + b)

plt.plot([pt1[0], pt2[0]], [pt1[1], pt2[1]])
plt.xlabel('x')
plt.ylabel('y')
plt.show()
```


    
![png](/images/Numerical_prediction_files/Numerical_prediction_22_0.png)
    



```python
for i in range(0, 100):
  for x_i, y_i in zip(x, y):
    y_hat = x_i * w + b
    err = y_i - y_hat

    w_rate = x_i
    w = w + w_rate * err

    b_rate = 1
    b = b + b_rate * err

print(w, b)
```

    913.5973364345905 123.39414383177204



```python
plt.scatter(x, y)
pt1 = (-0.1, -0.1 * w + b)
pt2 = (0.15, 0.15 * w + b)
plt.plot([pt1[0], pt2[0]], [pt1[1], pt2[1]])
plt.xlabel('x')
plt.ylabel('y')
plt.show()
```


    
![png](/images/Numerical_prediction_files/Numerical_prediction_24_0.png)
    



```python
x_new = 0.18
y_pred = x_new * w + b
print(y_pred)
```

    287.8416643899983



```python
plt.scatter(x, y)
plt.scatter(x_new, y_pred)
plt.xlabel('x')
plt.ylabel('y')
plt.show()
```


    
![png](/images/Numerical_prediction_files/Numerical_prediction_26_0.png)
    



```python
class Neuron:
  def __init__(self):
    self.w = 1.0
    self.b = 1.0

  def forpass(self, x):
    y_hat = x * self.w + self.b

    return y_hat

  def backprop(self, x, err):
    w_rate = x
    b_rate = 1

    w_grad = w_rate * err
    b_grad = b_rate * err

    return w_grad, b_grad

  def fit(self, x, y, epochs = 100):
    for i in range(epochs):
      for x_i, y_i in zip(x, y):
        y_hat = self.forpass(x_i)
        
        err = -(y_i - y_hat)

        w_grad, b_grad = self.backprop(x_i, err)

        self.w -= w_grad
        self.b -= b_grad

neuron = Neuron()
neuron.fit(x, y)

plt.scatter(x, y)
pt1 = (-0.1, -0.1 * neuron.w + neuron.b)
pt2 = (0.15, 0.15 * neuron.w + neuron.b)

plt.plot([pt1[0], pt2[0]], [pt1[1], pt2[1]])

plt.xlabel('x')
plt.ylabel('y')

plt.show()
```


    
![png](/images/Numerical_prediction_files/Numerical_prediction_27_0.png)
    



```python
import numpy as np
test_array = np.array([1,2,3,4])
print(test_array)

test_array = test_array.reshape(2,2)
print(test_array)
```

    [1 2 3 4]
    [[1 2]
     [3 4]]

