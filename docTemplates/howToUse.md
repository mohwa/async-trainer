# How to Use

### Execution order-based apis

These features are based execution order like a `Promise.all` and `Promise.race` of a native api
 
```javascript
import {
  seq,
  seqAll,
  map,
  mapAll,
  race,
  raceAll,
  retry,
  retryAll,
  assert,
  assertAll,
} from 'meari';

let p1, p2, p3;

p1 = Promise.resolve(1);
p2 = Promise.resolve(2);
p3 = Promise.resolve(3);

seq([p1, p2, p3])
  .then(values => {
    console.log(values);
    /*
    [
      { status: 'fulfilled', value: 1, index: 0 },
      { status: 'fulfilled', value: 2, index: 1 },
      { status: 'fulfilled', value: 3, index: 2 }
    ]
     */
  })
  .catch(v => {
    console.log(v);
  })
  .finally(() => {
    console.log('finally');
  });

p1 = Promise.resolve(1);
p2 = Promise.reject(new Error());
p3 = Promise.resolve(3);

seqAll([p1, p2, p3])
  .then(values => {
    console.log(values);
    /*
    [
      { status: 'fulfilled', value: 1, index: 0 },
      {
        status: 'rejected',
        value: Error:
          at Object.<anonymous> (/Users/user/htdocs/mohwa/async-trainer/examples/index.js:27:27)
          ...
        index: 1
      },
      { status: 'fulfilled', value: 3, index: 2 }
    ]
     */
  })
  .finally(() => {
    console.log('finally');
  });

p1 = Promise.resolve(1);
p2 = Promise.resolve(2);
p3 = Promise.resolve(3);

map([p1, p2, p3], ({ value }) => `TEST_${value}`)
  .then(values => {
    console.log(values); // [ 'TEST_1', 'TEST_2', 'TEST_3' ]
  })
  .catch(e => {
    console.dir(e);
  })
  .finally(() => {
    console.log('finally');
  });

p1 = Promise.resolve(1);
p2 = Promise.reject(new Error());
p3 = Promise.resolve(3);

mapAll([p1, p2, p3], ({ status, value }) => {
  if (status !== 'rejected') {
    return `TEST_${value}`;
  } else {
    return value;
  }
})
  .then(values => {
    console.log(values);
    /*
      [
        'TEST_1',
        Error:
            at Object.<anonymous> (/Users/user/htdocs/mohwa/async-trainer/examples/index.js:68:27)
            ...
        'TEST_3'
      ]    
     */
  })
  .finally(() => {
    console.log('finally');
  });

p1 = Promise.resolve(1);
p2 = Promise.resolve(2);
p3 = Promise.resolve(3);

race([p1, p2, p3])
  .then(values => {
    console.log(values);
    /*
    [
      { status: 'fulfilled', value: 1, index: 0 },
      { status: 'fulfilled', value: 2, index: 1 },
      { status: 'fulfilled', value: 3, index: 2 }
    ]    
     */
  })
  .catch(e => {
    console.dir(e);
  })
  .finally(() => {
    console.log('finally');
  });

p1 = Promise.resolve(1);
p2 = Promise.reject(new Error());
p3 = Promise.resolve(3);

raceAll([p1, p2, p3])
  .then(values => {
    console.log(values);
    /*
    [
      { status: 'fulfilled', value: 1, index: 0 },
      { status: 'fulfilled', value: 3, index: 2 },
      {
        status: 'rejected',
        value: Error:
            at Object.<anonymous> (/Users/user/htdocs/mohwa/async-trainer/examples/index.js:117:27)
            ...
        index: 1
      }
    ]    
     */
  })
  .finally(() => {
    console.log('finally 1');
  });

p1 = () => Promise.resolve(1);
p2 = () => Promise.resolve(2);
p3 = () => Promise.resolve(3);

retry([p1, p2, p3], 3, 1000)
  .then(values => {
    console.log(values);
    /*
    [
      { status: 'fulfilled', value: 1, index: 0 },
      { status: 'fulfilled', value: 2, index: 1 },
      { status: 'fulfilled', value: 3, index: 2 }
    ]
     */
  })
  .catch(value => {
    console.log(value);
  });

p1 = () => Promise.resolve(1);
p2 = () => Promise.reject(new Error());
p3 = () => Promise.resolve(3);

retryAll([p1, p2, p3], 3, 1000).then(values => {
  console.log(values);
  /*
  [
    { status: 'fulfilled', value: 1, index: 0 },
    {
      status: 'rejected',
      value: Error:
          at callback (/Users/user/htdocs/mohwa/async-trainer/examples/index.js:161:33)
          ...
      index: 1
    },
    { status: 'fulfilled', value: 3, index: 2 }
  ]  
   */
});

let cb1, cb2, cb3;

cb1 = () => true;
cb2 = () => true;
cb3 = () => true;

assert([cb1, cb2, cb3], 3, 1000)
  .then(values => {
    console.log(values);
    /*
    [
      { status: 'truly', value: true, index: 0 },
      { status: 'truly', value: true, index: 1 },
      { status: 'truly', value: true, index: 2 }
    ]    
     */
  })
  .catch(value => {
    console.log(value);
  });

cb1 = () => true;
cb2 = () => false;
cb3 = () => true;

assertAll([cb1, cb2, cb3], 3, 1000)
  .then(values => {
    console.log(values);
    /*
    [
      { status: 'truly', value: true, index: 0 },
      { status: 'falsely', value: false, index: 1 },
      { status: 'truly', value: true, index: 2 }
    ]    
     */
  });
```

## Main apis

These features are can use more easily and simply sometimes in certain situation

```javascript
import {
  once,
  delay,
  every,
  some,
  toSync
} from 'meari';

once(() => { console.log('once'); }, 1000)(); // once

delay(1000).then(() => { console.log('start'); }); // start

let p1, p2, p3;

p1 = Promise.resolve(1);
p2 = Promise.resolve(2);
p3 = Promise.resolve(3);

every([p1, p2, p3]).then(v => {
  console.log(v); // true
});

p1 = Promise.reject(1);
p2 = Promise.reject(2);
p3 = Promise.resolve(3);

some([p1, p2, p3]).then(v => {
  console.log(v); // true
});

p1 = () => {
  return new Promise((resolve) => {
    setTimeout(() => resolve(1), 300);
  });
};

p2 = () => {
  return new Promise((resolve) => {
    setTimeout(() => resolve(2), 100);
  });
};

toSync(p1()).then(v => console.log(v));
toSync(p2()).then(v => console.log(v));

// 1
// 2
```
