# Open Database of Cryptocurrency Exchanges

ODCE is a project to list cryptocurrency exchanges in a .json file that has a particular structure that teaches how to interpret the response for each exchange and find the price information for high, low, last, etc.

# Java interpreter

For a Java reader for this, see https://github.com/lucaszanella/OCDE_Java

#Javascript interpreter

Javascript version coming soon


# How to add new exchanges and cryptocurrencies

There are 3 types of json responses you'll encounter while digging through the API of an Exchange. They are: ```VARIABLES_IN_URL_SAME_STRUCTUE```, which is when you put the currency you want in the URL and receive a json with an expected structure that is equal for all currencies, ```VARIABLES_IN_URL```, which is when the variables still go in the URL but for each coin the structure is different (believe me, there was an exchange that did it), and the third type is ```VARIABLES_IN_JSON_SAME_URL```, when there is just one URL and you get all coins at once. 

Below, there's a detailed explanation of each of these 3 types:

```VARIABLES_IN_URL_SAME_STRUCTUE``` (type "A" for shot)

In this type of json response, the variables go in the URL, but all responses have the same structure, you can locate the "high", "low", "last", etc prices just by specifying how to navigate through the json one key at the time. It's better to show with an example.

This is the response from Mercado Bitcoin's api at https://mercadobitcoin.net/api/ticker_litecoin/

```
{"ticker":{"high":151.55000000,"low":146.00000000,"vol":1272.32158715,"last":149.28000000,"buy":147.51000000,"sell":148.00000000,"date":1500716827}}
```


This is the entire json that describes how to get prices from Mercado Bitcoin's api:

```
  "mercado_bitcoin": {
    "name": "Mercado Bitcoin",
    "countries": ["BR"],
    "url": "mercadobitcoin.com.br",
    "type": "A",
    "structure": ["ticker.high", "ticker.low", "ticker.last", "ticker.buy", "ticker.sell"],
    "api": {
      "bitcoin/real": "mercadobitcoin.net/api/ticker",
      "litecoin/real": "mercadobitcoin.net/api/ticker_litecoin"
    }
  }
```

See how easy it is, you just have to specify in the array ```structure``` where to locate, in order, the itens: highest price, lowest price, last price, etc. The rest is self explanatory.

The second type of document is:

```VARIABLES_IN_URL``` (type "B")

In this example, the URLs still have the variables for which coin pair you want to retrieve information, but each request has its own structure. Believe me, I had to acknowledge this model just because of the Kraken exchange. Let's see its response for https://api.kraken.com/0/public/Ticker?pair=XBTUSD
```

{"error":[],"result":{"XXBTZUSD":{"a":["2816.19600","1","1.000"],"b":["2805.43800","1","1.000"],"c":["2805.43800","0.10037796"],"v":["3234.19701737","8708.72759268"],"p":["2752.85779","2720.08507"],"t":[6954,18485],"l":["2651.96100","2614.70000"],"h":["2836.00000","2836.00000"],"o":"2670.00000"}}}
```

It has its own structure for each coin, just because of that ```XXBTZUSD``` key, so for each URL I had to specify the structure:

```
"kraken": {
    "name": "Kraken",
    "countries": ["US"],
    "url": "kraken.com",
    "type": "B",
    "api": {
      "bitcoin/euro": ["api.kraken.com/0/public/Ticker?pair=BTCEUR",["result.XXBTZEUR.h.0","result.XXBTZEUR.l.0","result.XXBTZEUR.c.0"]],
      "bitcoin/dollar": ["api.kraken.com/0/public/Ticker?pair=XBTUSD",["result.XXBTZUSD.h.0","result.XXBTZUSD.l.0","result.XXBTZUSD.c.0"]]
    }
}
      
```

The third type is when you have a fixed URL and you retrieve all coins at once:

```VARIABLES_IN_JSON_SAME_URL``` (type "C")

I had to do it because of Poloniex. See its output for https://poloniex.com/public?command=returnTicker

```
{"BTC_BCN":{"id":7,"last":"0.00000055","lowestAsk":"0.00000056","highestBid":"0.00000055","percentChange":"-0.08333333","baseVolume":"123.32216782","quoteVolume":"217515631.43380606","isFrozen":"0","high24hr":"0.00000060","low24hr":"0.00000055"},
"BTC_BELA":{"id":8,"last":"0.00006618","lowestAsk":"0.00006617","highestBid":"0.00006527","percentChange":"0.00501138","baseVolume":"27.73633320","quoteVolume":"428180.17870394","isFrozen":"0","high24hr":"0.00006736","low24hr":"0.00006200"}
```

So the structure to describe it is:

```
  "poloniex": {
    "name": "Poloniex",
    "countries": ["US"],
    "url": "poloniex.com",
    "type": "C",
    "endpoint": "poloniex.com/public?command=returnTicker",
    "api": {
      "bitcoin/dollar": ["USDT_BTC.highestBid", "USDT_BTC.lowestAsk", "USDT_BTC.last"],
      "ethereum/dollar": ["USDT_ETH.highestBid", "USDT_ETH.lowestAsk", "USDT_ETH.last"]
     }
}
``` 

