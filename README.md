<div align='center'>

<h1>Documentation for Lotusdew Trading Api</h1>
<p>Trading api for lotusdew trading platform and it's documentation</p>

<h4> <span> · </span> <a href="https://github.com/pranavtomar01/IIIT-Campus-Hiring/blob/master/README.md"> Documentation </a> <span> · </span> <a href="https://github.com/pranavtomar01/IIIT-Campus-Hiring/issues"> Report Bug </a> <span> · </span> <a href="https://github.com/pranavtomar01/IIIT-Campus-Hiring/issues"> Request Feature </a> </h4>


</div>

# :notebook_with_decorative_cover: Table of Contents

- [About the Project](#star2-about-the-project)
- [Prerequisites](#bangbang-prerequisites)

## :star2: About the Project
- This project contains a brief desciption of the Trading API's, their functions and their responses.

### :bangbang: Prerequisites

- Python 3
- websocket-client
```bash
pip3 install websocket-client
```
- websocket
```bash
pip3 install websocket
```

### :test_tube: Running Tests

Run the websocket code to test the connection
```bash
python3 websocket_request.py
```

## :toolbox: Getting Started

## Documentation : 
#### Connecting to the trading server via Websocket connection : 
* To establish the connection we first try to create connection and check the response : 
```Python
from websocket import create_connection

ws = websocket.create_connection("url", sslopt={"cert_reqs": ssl.CERT_NONE})
print(ws.status) #Check the status of connection, "101" for successfull connection
```

* Authenticate the client with the following code : 
```Python
conn = {
    "topic" : "api:join",
    "event" : "phx_join",
    "payload" :
        {
            "phone_no" : "phone_number" #"1234567890"
        },
    "ref" : ""
    }

ws.send(json.dumps(conn))
print(ws.recv()) # This statement will give response for authentication 
```
-  Response for Authentication will be of format(JSON) :
```Json
{
  "event":"phx_reply",
  "payload":{
    "response":"User Authenticated",
    "status":"ok"
  },
  "ref":"",
  "topic":"api:join"
}
```
Else, In case of invalid user:
```Json
{
    "event": "phx_reply",
    "payload": {
        "response": "User does not exist",
        "status": "error"
    },
    "ref": "",
    "topic": "api:join"
}
```

### Request for LTP(Last Traded Price) for multiple tickers :
After successfully creating connection and authenticate the client to the websocket, Now we are ready to subscribe one or multiple tickers to get their ltp data : 
Request for LTP is as follows :
```Python
tickers = ["symbol_of_equity"]
ltp_tickers = {
  "topic" : "api:join",
  "event" : "ltp_quote",
  "payload" : tickers,
  "ref" : ""
}
ws.send(json.dumps(ltp_tickers)) #This will subscribe the list of tickers passed in the payload 
```
- This will subscribe the websocket with the ticker data which is sent continously to the connection from websocket at a time gap of 1 second.
To recieve the data sequentially, use :
```Python
data = ws.recv()
print(data)
```
The "data" recieved is a JSON format data with the ltp of the equity:
```JSON
{
    "event": "ltp_response",
    "payload": [
        {
            "expiry_date": "0",
            "instrument": "EQUITY",
            "option_type": "EQ",
            "strike_price": 0,
            "symbol": "EQUITY_SYMBOL"
        },
        157, #NSE Unique token
        544450, #LTP in paisa, at the point in time
        4, #Quantity traded on the instance
        "2013-12-13T15:29:59Z"
    ],
    "ref": null,
    "topic": "api:join"
}
```
## How data is sent from websocket ?
- The data of every tickers is recieved from the websocket response at ltp_response event. Average time between two ltp response events is 1 second for an individual ticker.

## How to get the ltp data from the code ?
- Can be done in mutliple ways, whereas one example is given below:
```Python
while True:
    data = json.dumps(ws.recv())
    processed_data = process_function(data)
```
Now we can keep track of the Symbol for which we have received data in "data" variable by checking "data["payload"]["symbol"]"
- The other way to receive the data in program is to ws.run_forever() as listed in <a href="https://websocket-client.readthedocs.io/en/latest/examples.html"> Websocket-client docs </a>


### Place Orders :
```Python
order = {
            "topic" : "api:join",
            "event" : "order",
            "payload" :
                {
                    "phone_no" : "1234512345",
                    "symbol" : "EQUITY_SYMBOL", 
                    "buy_sell" : "B", #"B" for BUY orders and "S" for SELL orders
                    "quantity" : 1, #Quantity to be traded
                    "price" : 1012.34 #Price can be taken from ltp, the orders will be placed at the same price
                },
            "ref" : ""
        }
ws.send(json.dumps(order))
```
Response for successful placed order : 
```JSON

```
