---
tags = ["python", "mail"]
date = "2012-03-04"
title = "通过POP3协议读取指定邮件"
slug = "python-pop3"
weight = 620304
---

先要安装maillib库

假设我要获得一周来，豆瓣发送的每个邮件中的第一个网址

```python
# -*- coding: utf-8 -*-

import poplib  
import maillib  
from datetime import datetime, timedelta  
  
  
def email_filter(sender, body):  
    target = "http://"  
    sender, body = sender[1], body.split("\n")  
    if sender == "webmaster@douban.com":  
        for line in body:  
            if target in line:  
                return line.strip()  
  
  
def read_email(email, password, host, port=110, days=0):  
    conn = poplib.POP3(host, port)  
    #conn.set_debuglevel(1) #输出调试信息  
    conn.user(email)  
    conn.pass_(password)  
  
    links = []  
    nr = conn.stat()[0] #获取邮件数量  
    for i in range(nr, 0, -1):  
        server_msg, body, octets = conn.retr(i)  
        msg = maillib.Message.from_string( "\n".join(body) )  
        today = datetime.now().replace(hour=0, minute=0, second=0, microsecond=0)  
        if msg.date < today - timedelta(days=days):  
            break  
        link = email_filter(msg.sender, msg.body)  
        if link:  
            links.append(link)  
    return links  
  
  
if __name__ == "__main__":  
    links = read_email("me@126.com", "pass", host="pop.126.com", days=7)  
    for link in links:  
        print link
```