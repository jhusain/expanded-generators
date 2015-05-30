# Expanded Generators

The async/await proposal creates a new function type for sequencing Promises.

```JavaScript
async function getStockPrice(name){
  let symbol = await getSymbol(name);
  let price = await getPrice(symbol);
  return price;
}
```

```JavaScript
@push function* getStockPrices(name) {
  let symbol = await getSymbol(name),
    retryCount = 0;

  while(retryCount < 3){
    try {
      for*(let price on new Websocket('/' + symbol + '/prices')) {
        yield price;
      }
    }
    catch(e) {
      console.error(e);
    }
  }
}
```
