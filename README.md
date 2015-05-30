# Expanded Generators

The async/await proposal creates a new function type for sequencing Promises.

```JavaScript
async function getStockPrice(name){
  let symbol = await getSymbol(name);
  let price = await getPrice(symbol);
  return price;
}
```

