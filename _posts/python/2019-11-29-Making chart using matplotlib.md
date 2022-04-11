---
layout: post
title: Making chart using matplotlib
categories: [Python]
---

## 기능

- 차트만들때 사용.
- 몇줄의 코드로 아래와 같은 차트를 쉽게 만들어준다.

![](/assets/images/2019-11-29-Python%20matplotlib/2019-11-29-11-46-00.png)

## test용 코드

```python

def make_report(date):
    fig, ax1 = plt.subplots(figsize=(13, 7))

    my_data1 = []
    my_data2 = []
    total_my_try_cout = []

    # data를 랜덤으로 넣기
    for i in range(0, len(date)):
        my_data1.append(round(random.uniform(0, 1), 2))
        my_data2.append(round(random.uniform(0, 1), 2))
        total_my_try_cout.append(random.rannge(1000, 10000))

    # total_try_count
    ax1.set_title("My Report") # 차트 이름
    ax1.set_xlabel("Date", fontsize=10)
    ax1.set_ylabel("Try Count", fontsize=10)
    ax1.tick_params(axis="x", labelrotation=30, labelsize=8) # date 표시를 예쁘게 하기
    ax1.bar(date, total_my_try_cout, 0.5, label="total_my_try_cout", color=(0, 0, 1, 0.4))  # color  rgba로 세팅
    maxy_value = max(total_my_try_cout)
    ax1.set_yticks(np.arange(0, maxy_value, 2000)) # y값의 값 표시를 2000단위로 끈어서 보여준다

    # y축을 왼쪽은 try count 표시, 오른쪽은 date 표시하기 위해
    ax2 = ax1.twinx()

    # error rate
    ax2.set_ylabel("My Data", fontsize=10)

    ax2.plot(date, my_data1, linewidth=0.5, marker=".", color="#1E90FF")
    ax2.plot(date, my_data2, linewidth=0.5, marker=".", color="#FF8500")

    # plot에 x축에 위치하는 y값에 수치 표시
    for a, b in zip(date, my_data1):
        plt.text(a, b + 0.015, str(b), fontsize=8, color="#1E90FF")

    # plot에 x축에 위치하는 y값에 수치 표시
    for a, b in zip(date, my_data2):
        plt.text(a, b, str(b), fontsize=8, color="#FF8500")

    ax2.axis(option="tight")
    ax2.set_yticks(np.arange(0, 1, 0.25)) # y값의 표시를 0.25 단위로 끈어서 보여준다.

    plt.legend(["my_data1", "my_data2"], loc='upper right') # 이 그래프가 어떤건지 보여준다
    ax1.legend(loc='upper left') # 이 그래프가 어떤건지 보여준다

    # fig.savefig(filename, dpi=300)
    fig.savefig("이미지이름.png")
    plt.close()

```

## 위의 결과 이미지

![](/assets/images/2019-11-29-Python%20matplotlib/2019-11-29-13-41-03.png)


## 테이블도 만듬

![](/assets/images/2019-11-29-Making%20chart%20using%20matplotlib/2020-02-27-14-55-12.png)

