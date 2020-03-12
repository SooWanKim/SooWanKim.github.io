---
layout: post
title: Google Cloud Vision API 사용
categories: [Python]
---

## Google Vision API

GCP(Google Cloud Platform)에서 사용 가능한 라이브러리

이미지에서 텍스트를 감지하고 추출할 수 있음

> 개발자는 Cloud Vision API를 사용하여 이미지 라벨링, 얼굴 및 특징 감지, 광학 문자 인식(OCR), 유해성 콘텐츠 태그 지정과 같은 시각적 감지 기능을 애플리케이션에 손쉽게 통합할 수 있습니다.

## 사용 목적

회사 구내 식당 메뉴가 이미지로 올라오는데 이를 메신저에 텍스트로 뿌려 주고 싶음


## 사용 방법

우선 회사 식당 메뉴 이미지가 월, 화, 수, 목, 금 이렇게 5개의 열로 나오고 A 식당 점심, B 식당 점심, 샐러드 점심, 저녁

이렇게 4개의 행으로 일주일 메뉴 정보가 20개 나온다.

vision api에서 이미지 정보를 읽어서 텍스트로 분류 해주고 있는데, 이미지를 요일별로 분류하기 위해서 PIL를 통해 clipping을 한다.

요일별로 clipping된 이미지를 vision api에 보내고 텍스트 정보를 teams에 incomming webhook으로 발송


## 사용 코드

```python

from PIL import Image
import os
import io
from google.cloud import vision
from google.cloud.vision import types
from google.oauth2 import service_account
from PIL import Image
import pymsteams
from _datetime import timedelta
from datetime import datetime
import schedule
import time

script_path = os.path.dirname(os.path.realpath(__file__))
CONST_IMAGE_FILENAME = os.path.join(script_path, "bob.jpg")
CONST_CREDENTIALS_FILENAME = os.path.join(script_path, "인증키.json")


# vision api로 클립된 이미지 정보를 보내서 식당 메뉴 정보를 받아온다.
def get_bob_text(cropped_img):
    imgByteArr = io.BytesIO()
    cropped_img.save(imgByteArr, format="PNG")
    imgByteArr = imgByteArr.getvalue()

    image_info = vision.types.Image(content=imgByteArr)
    response = client.document_text_detection(image=image_info)
    texts = response.text_annotations
    return texts[0].description

my_credentials = service_account.Credentials.from_service_account_file(CONST_CREDENTIALS_FILENAME)

client = vision.ImageAnnotatorClient(credentials=my_credentials)

file_name = CONST_IMAGE_FILENAME

def main():
    image = Image.open(file_name)

    # 이미지를 요일 별 식당별로 클립 하기위한  영역 정보
    dict_day_bob_clip = {
        "Monday": {"A 식당 점심": (124, 140, 240, 393), "B 식당 점심": (124, 393, 240, 650), "샐러드": (124, 650, 240, 793), "저녁": (124, 796, 240, 1020)},
        "Tuesday": {"A 식당 점심": (240, 140, 359, 390), "B 식당 점심": (240, 393, 359, 650), "샐러드": (240, 650, 358, 793), "저녁": (240, 796, 360, 1020)},
        "Wednesday": {"A 식당 점심": (359, 140, 470, 390), "B 식당 점심": (359, 393, 470, 650), "샐러드": (359, 650, 470, 793), "저녁": (359, 796, 470, 1020)},
        "Thursday": {"A 식당 점심": (470, 140, 585, 390), "B 식당 점심": (470, 393, 583, 650), "샐러드": (470, 650, 583, 793), "저녁": (470, 796, 583, 1020)},
        "Friday": {"A 식당 점심": (583, 140, 693, 390), "B 식당 점심": (583, 393, 693, 650), "샐러드": (583, 650, 694, 793), "저녁": (584, 796, 694, 1020)},
    }

    date = datetime.now()
    day = date.strftime("%A")

    # 요일에 맞는 클립된 이미지를 vision api에 보내서 메뉴정보를 가져옴
    a_launch = get_bob_text(image.crop(dict_day_bob_clip[day]["A 식당 점심"]))
    b_launch = get_bob_text(image.crop(dict_day_bob_clip[day]["A 식당 점심"]))
    salad = get_bob_text(image.crop(dict_day_bob_clip[day]["샐러드"]))
    dinner = get_bob_text(image.crop(dict_day_bob_clip[day]["저녁"]))

    print(a_launch)
    print(b_launch)
    print(salad)
    print(dinner)

    # 메뉴 정보를 teams 채널로 발송
    myTeamsMessage = pymsteams.connectorcard("in comming webhook 주소")
    myTeamsMessage.text("오늘의 식당 메뉴")

    myMessageSection = pymsteams.cardsection()
    myMessageSection.title(day + " 식당 메뉴")

    myMessageSection.addFact("A 식당 점심", a_launch)
    myMessageSection.addFact("A 식당 점심", b_launch)
    myMessageSection.addFact("샐러드", salad)
    myMessageSection.addFact("저녁", dinner)

    myTeamsMessage.addSection(myMessageSection)

    myTeamsMessage.send()


schedule_time = "10:00"

schedule.every().monday.at(schedule_time).do(main)
schedule.every().tuesday.at(schedule_time).do(main)
schedule.every().wednesday.at(schedule_time).do(main)
schedule.every().thursday.at(schedule_time).do(main)
schedule.every().friday.at(schedule_time).do(main)

print("bob notification bot ..\r")

while True:
    schedule.run_pending()
    time.sleep(1)

```

## 결과

![](/assets/images/2020-03-12-Google%20Cloud%20Vision%20API%20사용/2020-03-12-18-40-09.png)

텍스트는 정렬좀 해야할듯 ㅋ