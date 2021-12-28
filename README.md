# pykc-etag
[pykc lib](https://kite.trade/docs/connect/v3/websocket/) with Etag implementation, to save bandwidth for a non-modified response.
## Installation
```
git clone https://github.com/ranjanrak/async-ticker.git
```
## Concept
Store Etag value(as hash key:value pair) in the user's home directory `os.path.join(os.getenv("HOME"), ".pykiteconnect")` into `etag` and cache value into `cache` file.<br>
Add `If-None-Match` header for all `GET` request, if etag value for request url is present in `etag.json`.</br> 
Update `etag` and `cache` for required request url, when none 304 http status is received.</br> 
Refer [code here](https://github.com/ranjanrak/pykc-etag/blob/main/connect.py#L847) for cache storage mechanism. Refer cache validation [code here](https://github.com/ranjanrak/pykc-etag/blob/main/connect.py#L945). 

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

# Fetch holdings
kite.holdings()
```

## Response

```
DEBUG:urllib3.connectionpool:https://api.kite.trade:443 "GET /portfolio/holdings HTTP/1.1" 304 0

{'tradingsymbol': 'APOLLOPIPE', 'exchange': 'NSE', 'instrument_token': 3676417, 'isin': 'INE126J01016',
 'product': 'CNC', 'price': 0, 'quantity': 3, 'used_quantity': 0, 't1_quantity': 0, 'realised_quantity': 3,
 'authorised_quantity': 0, 'authorised_date': '2021-12-28 00:00:00', 'opening_quantity': 3, 'short_quantity': 
 0, 'collateral_quantity': 0, 'collateral_type': '', 'discrepancy': False, 'average_price': 582.666667, 
 'last_price': 539.8, 'close_price': 539.65, 'pnl': -128.60000100000002, 'day_change': 0.14999999999997726, 
 'day_change_percentage': 0.027795793569902208} .....
```
