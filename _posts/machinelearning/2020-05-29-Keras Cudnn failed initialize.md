---
layout: post
title: keras Cudnn failed to initialize
categories: [MachineLearning]
---

## 실패 상황

딥러닝 강의를 공부하던중에

[https://github.com/hunkim/DeepLearningZeroToAll/blob/master/tf2/tf2-11-1-mnist_cnn.py](https://github.com/hunkim/DeepLearningZeroToAll/blob/master/tf2/tf2-11-1-mnist_cnn.py)

[https://github.com/hunkim/DeepLearningZeroToAll/blob/master/tf2/tf2-12-4-rnn_long_char.py](https://github.com/hunkim/DeepLearningZeroToAll/blob/master/tf2/tf2-12-4-rnn_long_char.py)

두 예제를 로컬에서 테스트 했을때 각각

cnn fail
```
Failed to get convolution algorithm. This is probably because cuDNN failed to initialize, so try looking to see if a warning log message was printed above.
	 [[node sequential/conv2d/Conv2D (defined at d:\project\python\deeplearningzerotoall\DeepLearningZeroToAll\tf2\tf2-11-1-mnist_cnn.py:42) ]]

```

rnn fail
```
 Fail to find the dnn implementation.
 ```

위와 같은 에러가 발생함. 구글링해서 찾아보니

CUDA, Tensorflow,cuDNN, NVIDIA 버전이 안맞거나, 메모리가 부족하다고 함

**사용버전은 : python 3.74 64bit, CUDA v10.2, Cudnn은 7.64**

## 해결 방안

[https://stackoverflow.com/questions/53698035/failed-to-get-convolution-algorithm-this-is-probably-because-cudnn-failed-to-in](https://stackoverflow.com/questions/53698035/failed-to-get-convolution-algorithm-this-is-probably-because-cudnn-failed-to-in)

위의 댓글에 해결책이 있음.


```python
os.environ['TF_FORCE_GPU_ALLOW_GROWTH'] = 'true'
```

를 넣으니 실패없이 넘어감.

stackoverflow 최고!!







