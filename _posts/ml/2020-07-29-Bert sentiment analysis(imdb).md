---
layout: post
title: Bert sentiment analysis(imdb)
categories: [ML]
---

## IMDB bert


keras 홈페이지 튜토리얼에 있는 bert는 질문에 대한 답변을 예상하는 모델이라

구글링으로 여러 colab에 있는 내용과 튜토리얼을 섞어서 data는 imdb를 사용하는 sentiment analysis 만듬

집에 있는 데스크탑으로 돌리니까 메모리 부족이 떠서 colab에서 작업함


outpul layer 0~1 사이 확률

```
outputs = Dense(1, activation="sigmoid")(x)
loss binary_crossentropy 사용
```

outpul layer 0,1 값

```
outputs = Dense(2, activation="softmax")(x)
loss categorical_crossentropy 사용
```


## f1 score

![](/assets/images/2020-07-29-bert%20sentiment%20analysis(imdb)/2020-07-29-22-18-25.png)


![](/assets/images/2020-07-29-bert%20sentiment%20analysis(imdb)/2020-07-29-22-18-49.png)


## colab link

[https://github.com/SooWanKim/practice_nlp/blob/master/IMDB%20Bert.ipynb](https://github.com/SooWanKim/practice_nlp/blob/master/IMDB%20Bert.ipynb)