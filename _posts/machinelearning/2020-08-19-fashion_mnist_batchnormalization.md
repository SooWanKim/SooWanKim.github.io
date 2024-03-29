---
layout: post
title: Fashion Mnist BatchNormalization
categories: [MachineLearning]
---

## BatchNormalization 사용하기


```python
import numpy as np
import random
import tensorflow as tf

random.seed(777)  # for reproducibility
learning_rate = 0.001
batch_size = 100
training_epochs = 10
nb_classes = 10

# (x_train, y_train), (x_test, y_test) = tf.keras.datasets.mnist.load_data()
(x_train, y_train), (x_test, y_test) = tf.keras.datasets.fashion_mnist.load_data()
print(x_train.shape)

class_names = ['T-shirt/top', 'Trouser', 'Pullover', 'Dress', 'Coat',
               'Sandal', 'Shirt', 'Sneaker', 'Bag', 'Ankle boot']

x_train = x_train.reshape(x_train.shape[0], 28 * 28) / 255.0
x_test = x_test.reshape(x_test.shape[0], 28 * 28) / 255.0

y_train = tf.keras.utils.to_categorical(y_train, nb_classes)
y_test = tf.keras.utils.to_categorical(y_test, nb_classes)

X = tf.keras.layers.Input(shape=[784])

H = tf.keras.layers.Dense(256)(X)
H = tf.keras.layers.BatchNormalization()(H)
H = tf.keras.layers.Activation("relu")(H)

H = tf.keras.layers.Dense(256)(X)
H = tf.keras.layers.BatchNormalization()(H)
H = tf.keras.layers.Activation("relu")(H)

Y = tf.keras.layers.Dense(nb_classes, activation="softmax")(H)
model = tf.keras.models.Model(X, Y)
model.compile(loss="categorical_crossentropy", optimizer=tf.keras.optimizers.Adam(lr=learning_rate), metrics=["accuracy"])
model.summary()

model.fit(x_train, y_train, batch_size=batch_size, epochs=training_epochs)

# predict 10 random hand-writing data
y_predicted = model.predict(x_test)
for x in range(0, 10):
    random_index = random.randint(0, x_test.shape[0] - 1)
    prdicted_index = np.argmax(y_predicted[random_index])
    print("index: ", random_index, "actual y: ", np.argmax(y_test[random_index]), "predicted y: ", prdicted_index)
    print(class_names[prdicted_index])


# evaluate test set
evaluation = model.evaluate(x_test, y_test)
print("loss: ", evaluation[0])
print("accuracy", evaluation[1])

```