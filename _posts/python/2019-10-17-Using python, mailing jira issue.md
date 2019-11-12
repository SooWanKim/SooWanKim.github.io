---
layout: post
title:  Using python, mailing jira issue
categories: [Python]
---

## 사용 목적

jql 사용해서 원하는 jira issue 들을 요약하여 html 서식으로 mail 발송하기 위함

***

## 작업 순서

1. jira 권한 처리
2. jql로 원하는 jira issue 가져오기
3. html 형식에 맞게 issue 요약
4. stmp를 통한 mail 발송 (scheduling로 원하는 시간대별 처리 가능)

***

## 비고

jira, stmp, schedule 모듈 사용

config.ini로 계정 정보 및 설정 분리

mail_format.html로 메일 발송 서식 분리

***

## 작업 코드

```python
def MakeJira(config):
    options = {'server':'지라 주소'}
    jira = JIRA(options, basic_auth=( config['jira_info']['account_id'],config['jira_info']['account_password']))
    return jira

def ReadConfig():
    config = configparser.ConfigParser()
    config.sections()
    config.read('temp_config.ini')
    return config

def ReadHtml():
    with open('mail_format.html','r', encoding="utf8") as file:
        return file.read()

def FindJiraIssueInfoByFilter(jira, config):
    jira_filter = config['jira_info']['filter']
    jira_issue_format = config['jira_info']['issue_format']

    allIssue  = jira.search_issues(jira_filter)
    jira_issue_content = ""

    for issue in allIssue:
        jira_issue_content += jira_issue_format.format(issue.key, issue.key, issue.fields.summary, issue.fields.assignee.displayName)

    return jira_issue_content , allIssue.total

def SendEmail(config, mail_content):
    titletext = config['mail_info']['mail_title']
    address_book = [config['mail_info']['receiver']]

    msg = MIMEMultipart('alternative')
    sender = config['mail_info']['sender']
    subject = titletext
    body = mail_content

    msg['From'] = sender
    msg['To'] = ','.join(address_book)
    msg['Subject'] = subject
    msg.attach(MIMEText(body, 'html'))
    text = msg.as_string()
    # Send the message via our SMTP server
    s = smtplib.SMTP('메일서버', 포트)
    s.connect("메일서버")
    s.ehlo()
    s.starttls()
    s.login(config['mail_info']['account_id'], config['mail_info']['account_password'])
    s.sendmail(sender, address_book, text.encode('utf-8'))
    s.quit()

#read config
config = ReadConfig()

jira = MakeJira(config)

def SendScheduleMail():
    html_info = ReadHtml()
    html_info = html_info.replace("{제목}", config['mail_info']['content_title'])
    jira_issue_content , issue_total_count = FindJiraIssueInfoByFilter(jira, config)
    html_info = html_info.replace("{이슈 합계}", str(issue_total_count))
    html_info = html_info.replace("{내용}", jira_issue_content)
    SendEmail(config, html_info)

#schedule.every(30).seconds.do(SendScheduleMail)
#schedule.every().day.at(config['mail_info']['schedule_time']).do(SendScheduleMail)

SendScheduleMail()

print("start working")

# while True:
#     schedule.run_pending()
#
```