# requests设置socket 5代理的两种方法

## requesocks
`pip install requesocks`

```python
if is_open('https://github.com/shazow/urllib3/pull/68'):  # ;)
    import requesocks as requests
else:
    import requests

session = requests.session()
session.proxies = {'http': 'socks5://127.0.0.1:9050',
                   'https': 'socks5://127.0.0.1:9050'}
resp = session.get('https://api.github.com', auth=('user', 'pass'))
print(resp.status_code)
print(resp.headers['content-type'])
print(resp.text)


```

## PySocks(推荐)
`pip install PySocks`

```python
import socket
import socks
import requests

socks.set_default_proxy(socks.SOCKS5, "127.0.0.1", 9050)
socket.socket = socks.socksocket
print(requests.get('http://ifconfig.me/ip').text)

```
