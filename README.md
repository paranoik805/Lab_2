Лабораторная работа 2.  
====
# Немного информации о цели лабараторной работы
Обучить нейронную сеть с использованием техники обучения Transfer Learning. В данной работе использовалась нейронная сеть EfficientNet-B0. В первой части работы данная нейронная сеть будет со случайным начальным приближениеv. Во второй части работы будет использоваться предобученная нейронная сеть, так как техника Transfer Learning предполагает использование предварительно обученной нейронной сети.

# 2.обучить нейронную сеть EfficientNet-B0 (случайное начальное приближение) для решения задачи классификации изображений Oregon WildLife
* **Описание архитектуры:**   
 
* Размерность входного изображения (224x224x3): 
```
inputs = tf.keras.Input(shape=(RESIZE_TO, RESIZE_TO, 3))
```

* Использование нейронной сети EfficientNet-B0 с параметрами: include_top=True, weights=None, classes = NUM_CLASSES. Параметр include_top=True - использование слоев нейронной сети EfficientNet-B0, отвечающих за классификацию. Параметр weights=None - использование случайного начального приближения. Параметр classes = NUM_CLASSES -  количество классов для классификации изображений. (в нашем случае 20).
```
outputs = EfficientNetB0(include_top=True,weights=None, classes = NUM_CLASSES)(inputs)
```


 ### Графики обучения для нейронной сети с одним сверточным слоем:
 
Синяя линия - на валидации  
Оранжевая линия - на обучении  

 ***График метрики точности:*** 
<img src="./epoch_categorical_accuracy v1.svg">

 ***График функции потерь:*** 
 
<img src="./epoch_loss v1.svg">

### Анализ результатов:
Исходя из графика метрики точности и графика функции потерь видно, что сеть не обучается, так как с каждой эпохой метрика точности уменьшается, потери увеличиваются. Так же из графика метрики точности видно, что точность довольно низкая, достигает максимум около 10 процентов. Графики точности и потерь на валидационном наборе данных получилось скачущими, из за чего заметна большая разница в значениях на валидацинноном наборе данных и тренировочном.

# 3. С использованием техники обучения Transfer Learning. Обучить нейронную сеть EfficientNet-B0 (предобученную на базе изображений imagenet) для решения задачи классификации изображений Oregon WildLife

* **Описание структуры** 

* Использование нейронной сети EfficientNet-B0 с параметрами: include_top=False, input_tensor=inputs, pooling='avg', weights='imagenet'. Параметр include_top=False - использование слоев нейронной сети EfficientNet-B0 без классификации. Параметр input_tensor=inputs - подает входное изображение. Параметр pooling='avg' - применение глобального среднего объединения к выходным данным последнего сверточного слоя. Параметр weights=imagenet - использование предобученного начального приближения на базе imagenet.

```
x = EfficientNetB0(include_top=False, input_tensor=inputs, pooling='avg', weights='imagenet')(inputs) 
```

* "заморозка" сверточной части, так как мы используем уже обученную нейроную сеть:
```
x.trainable = False
```
Полносвязный Dense слой с 20 выходами и функцией активации softmax, которая определяет к какой категории и с какой вероятностью относится поданное на вход изображение:  
```
outputs = tf.keras.layers.Dense(NUM_CLASSES, activation=tf.keras.activations.softmax)(x)
```


 ### Графики обучения для нейронной сети с несколькими сверточными слоями:
 
Синяя линия - на валидации  
Оранжевая линия - на обучении  

 ***График метрики точности:*** 
<img src="./epoch_categorical_accuracy v2.svg">

 ***График функции потерь::*** 
 
<img src="./epoch_loss v2.svg">

### Анализ результатов:

Исходя из графика метрики точности и графика функции потерь видно, что сеть обучается, так как с каждой эпохой метрика точности увеличивается, а потери уменьшаются. Так же из графика метрики точности видно, что точность довольно высокая, достигает максимум около 90 процентов. Графики получилось не такие скачущие, как при случайном приближении, и с каждой эпохой значение графика метрики точности и графика функции потерь на валидационном наборе данных приближается к значению на тренировочном наборе данных. Из чего можно сказать, что результаты предобученной нейронной сети на базе imagenet лучше, чем нейронной сети со случайным приближением.
