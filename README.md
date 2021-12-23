# pykc-etag
[pykc lib](https://kite.trade/docs/connect/v3/websocket/) with Etag implementation, to save bandwidth for a non-modified response.
## Installation
```
git clone https://github.com/ranjanrak/async-ticker.git
```
## Concept
Store Etag value(as key:value pair) in the user's home directory `os.path.join(os.getenv("HOME"), ".pykiteconnect")` into `etag` using [dbm module](https://docs.python.org/3/library/dbm.html).<br>
Add `If-None-Match` header for all `GET` request, if etag value for request url is present in `etag.json`.</br> 
Update `etag` for required request url, when none 304 http status is received.

Sample output of : `$HOME/.pykiteconnect/etag`
```
{"https://api.kite.trade/orders": "W/\"i51p01GqP6TRPWsM\"", 
"https://api.kite.trade/instruments": "\"e33d497fcc6035e90fe24497955cc377\"", 
"https://api.kite.trade/portfolio/positions": "W/\"RWcYE7s6OQ2jiFp4\"", 
"https://api.kite.trade/portfolio/holdings": "W/\"7y6jV8XreUgYpPdQ\"",
"https://api.kite.trade/user/margins": "W/\"XLP3pyD5k0OVIlcO\"", 
"https://api.kite.trade/trades": "W/\"i51p01GqP6TRPWsM\""}
```
Sample `GET` request header with `If-None-Match`:
```
{'X-Kite-Version': '3', 'User-Agent': 'Kiteconnect-python/3.9.4', 
'If-None-Match': 'W/"i51p01GqP6TRPWsM"', 
'Authorization': 'token api_key:access_token'}
```

## Usage
Client side usage remains same as of [current kiteconnect API usage](https://github.com/zerodha/pykiteconnect#api-usage). 

```
import logging
from kiteconnect import KiteConnect

logging.basicConfig(level=logging.DEBUG)

kite = KiteConnect(api_key="your_api_key")

# Redirect the user to the login url obtained
# from kite.login_url(), and receive the request_token
# from the registered redirect url after the login flow.
# Once you have the request_token, obtain the access_token
# as follows.

data = kite.generate_session("request_token_here", api_secret="your_secret")
kite.set_access_token(data["access_token"])

# Fetch all orders
kite.orders()

# Fetch positions
kite.positions()
```

## Response

```
DEBUG:urllib3.connectionpool:https://api.kite.trade:443 "GET /portfolio/positions HTTP/1.1" 304 0
```
