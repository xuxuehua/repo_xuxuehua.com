---
title: "urllib3"
date: 2021-07-09 14:54
---





[toc]



# urllib3



## PoolManager()

```
import urllib3 
import json
http = urllib3.PoolManager() 

def lambda_handler(event, context): 
    url = "https://address_info"
    msg = {
        "Content": event['Records'][0]['Sns']['Message']
    }
    slack_user_id_rxu="xxx"
    message = event['Records'][0]['Sns']['Message']
    msg1 = f"<@{slack_user_id_rxu}> : {message}"
    encoded_msg = json.dumps({'text': msg1}).encode('utf-8')
    resp = http.request('POST',url, body=encoded_msg, headers={'Content-Type': 'application/json'})
    print({
        "message": event['Records'][0]['Sns']['Message'], 
        "status_code": resp.status, 
        "response": resp.data
    })
```





