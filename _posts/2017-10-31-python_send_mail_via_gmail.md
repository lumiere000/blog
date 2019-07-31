---
layout: posts
title: using python to send alert mail via gmail tls
image: /img/python.png
tags: python mail monitoring
category: python
---

## zabbix config

goto administration > media types > create media type

```markdown
name : gmail-auth-smtp
type : Script
Script name : gmail-auth-smtp.py
Script parameters :
{ALERT.SENDTO}
{ALERT.SUBJECT}
{ALERT.MESSAGE}
```

```markdown
goto administration > users > admin > media
add media
type : gmail-auth-smtp
send to : your@email.com
select your alert level
update
```

```markdown
configuration > actions > report problems to zabbix administrators > action
add condition with host name or host group you wanted to send alert
operations > edit
send only to : gmail-auth-smtp
update
```

## set log and config file

#### create log file for debug (do it inside container if you using docker)

```markdown
touch /tmp/alert_mail.log
chmod 777 /tmp/alert_mail.log
```

```markdown
vi /usr/lib/zabbix/alertscripts/gmail-auth-smtp.py
chmod 755 /usr/lib/zabbix/alertscripts/gmail-auth-smtp.py
```

or if you using my configured docker :

```markdown
/home/zabbix/zbx_env/usr/lib/zabbix/alertscripts/gmail-auth-smtp.py
chmod 755 /home/zabbix/zbx_env/usr/lib/zabbix/alertscripts/gmail-auth-smtp.py
```

gmail-auth-smtp.py:

```markdown
#!/usr/bin/python
# -*- coding: utf-8 -*-
"""
Zabbix SMTP Alert script for gmail.
"""
import logging
logging.basicConfig(filename='/tmp/alert_mail.log', level=logging.DEBUG,
format='%(asctime)s - %(levelname)s - %(message)s')
logger = logging.getLogger(__name__)
logging.debug('Alert mail send begin.')

import sys
import smtplib
from email.MIMEText import MIMEText
from email.Header import Header
from email.Utils import formatdate

# Mail Account
MAIL_ACCOUNT = 'your@email.com'
MAIL_PASSWORD = 'yourpassword'

# Sender Name
SENDER_NAME = u'Zabbix Alert'

# Mail Server
SMTP_SERVER = 'smtp.gmail.com'
SMTP_PORT = 587
# TLS
SMTP_TLS = True

def send_mail(recipient, subject, body, encoding='utf-8'):
    session = None
    msg = MIMEText(body, 'plain', encoding)
    msg['Subject'] = Header(subject, encoding)
    msg['From'] = Header(SENDER_NAME, encoding)
    msg['To'] = recipient
    msg['Date'] = formatdate()
    try:
        session = smtplib.SMTP(SMTP_SERVER, SMTP_PORT)
        if SMTP_TLS:
            session.ehlo()
            session.starttls()
            session.ehlo()
            session.login(MAIL_ACCOUNT, MAIL_PASSWORD)
        session.sendmail(MAIL_ACCOUNT, recipient, msg.as_string())
    except Exception as e:
        raise e
    finally:
    # close session
        if session:
            session.quit()

if __name__ == '__main__':
    """
    recipient = sys.argv[1]
    subject = sys.argv[2]
    body = sys.argv[3]
    """
    if len(sys.argv) == 4:
        logger.debug('Recipient: %s',sys.argv[1])
        logger.debug('Subject: %s',sys.argv[2])
        logger.debug('Body: %s',sys.argv[3])
        send_mail(
            recipient=sys.argv[1],
            subject=sys.argv[2],
            body=sys.argv[3])
    else:
        print u"""requires 3 parameters (recipient, subject, body)
\t$ zabbix-gmail.sh recipient subject body
"""
```

test:

```markdown
./gmail-auth-smtp.py test@yourmail.com yoursubject yourmessage
```

you can see message log in /tmp/alert_mail.log
