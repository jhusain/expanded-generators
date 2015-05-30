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
      retryCount++;
      console.error(e);
    }
  }
}
```

```JavaScript
// all driver functions must be generators to support clean up on side-exit
function* push(genF) {
  return new Observable(function(generator) {
    var gen = genF(),
      subscription;
      
      
    function step(nextF) {
      var next;
      try {
        next = nextF();
      } catch(e) {
        // finished with failure, reject the promise
        generator.throw(e); 
        return;
      }
      if (next.await) {
        // could be Promise from await or Observable from for*
        observable = Observable.from(next.value);
        subscription = observable[Symbol.observer]({
          // next is body of for*(...on...), yields 
          throw(e) {
            generator.throw(e);
          },
          return(v) {
            // executed if there's a break statement
          });
      }
      else if(next.done) {
        // finished with success, resolve the promise
        generator.return(next.value);
        return;
      } 
      else if (next.value && next.value.then) {
        // not finished, chain off the yielded promise and `step` again
        Promise.cast(next.value).then(function(v) {
          step(function() { return gen.next(v); });      
        }, function(e) {
          step(function() { return gen.throw(e); });
        });
      }
      else {
        // ES6 tail recursion prevents stack growth
        step(function() { return gen.next(next.value)});
      }
    }
    step(function() { return gen.next(undefined); });
  });
}  
}
