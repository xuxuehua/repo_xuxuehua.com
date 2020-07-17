---
title: "smtplib"
date: 2020-07-13 14:43
---
[toc]



# smtplib

buildin class for Python3



## send mail

```
import smtplib
from email import encoders
from email.mime.base import MIMEBase
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
SENDER = "me@xurick.com"
MAIL_SERVER = "i.elb.amazonaws.com"
MAIL_PORT = 587
EMAIL_TEMPLATE = """
Hi all,
{mail_msg}
Thanks & Regards
"""
def send_email(tmp_filepath_list, receiver_list, mail_subject, mail_msg):
    message = MIMEMultipart()
    message['From'] = SENDER
    receivers = (
        ",".join(receiver_list) if isinstance(receiver_list, list) else receiver_list
    )
    message['To'] = receivers
    message['Subject'] = mail_subject
    mail_content = EMAIL_TEMPLATE.format(mail_msg=mail_msg)
    message.attach(MIMEText(mail_content, 'plain'))
    if not isinstance(tmp_filepath_list, (tuple, list)):
        tmp_filepath_list = [tmp_filepath_list]
    for tmp_filepath in tmp_filepath_list:
        filename = tmp_filepath.split('/')[-1]
        payload = MIMEBase('application', 'octate-stream')
        payload.set_payload(open(tmp_filepath, 'rb').read())
        encoders.encode_base64(payload)
        payload.add_header('content-disposition', 'attachment', filename=filename)
        message.attach(payload)
    smtpObj = smtplib.SMTP(MAIL_SERVER, MAIL_PORT)
    smtpObj.sendmail(SENDER, receiver_list, message.as_string())
    smtpObj.quit()
```

