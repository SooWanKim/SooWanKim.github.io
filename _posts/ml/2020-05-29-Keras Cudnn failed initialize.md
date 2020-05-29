---
layout: post
title: keras Cudnn failed to initialize
categories: [ML]
---

## 실패 상황

딥러닝 강의를 공부하던중에

https://github.com/hunkim/DeepLearningZeroToAll/blob/master/tf2/tf2-11-1-mnist_cnn.py에 예제를 로컬에서 테스트하는데


```
Failed to get convolution algorithm. This is probably because cuDNN failed to initialize, so try looking to see if a warning log message was printed above.
	 [[node sequential/conv2d/Conv2D (defined at d:\project\python\deeplearningzerotoall\DeepLearningZeroToAll\tf2\tf2-11-1-mnist_cnn.py:42) ]]

```
사용버전은 : python 3.74 64bit, CUDA v10.2, Cudnn은 7.64

위와 같은 에러가 발생함. 내용은 모델에 Conv2D사용할때 문제가 생기는거 같아서 찾아보니

CUDA, Tensorflow,cuDNN, NVIDIA 버전이 안맞거나, 메모리가 부족하다고 함


## 해결 방안

https://stackoverflow.com/questions/53698035/failed-to-get-convolution-algorithm-this-is-probably-because-cudnn-failed-to-in

위의 댓글에 해결책이 있음.


```python
os.environ['TF_FORCE_GPU_ALLOW_GROWTH'] = 'true'
```

를 넣으니 실패없이 넘어감.







