## Day 1

> Promise 相关问题

1. 聊一聊 Promise 的原理
1. 使用 Promise 封装 Ajax

   ```js
   function ajax({ method, url, data }) {
     return new Promise((resolve, reject) => {
       const xhr = new XMLHttpRequest();
       xhr.open(method, url);
       xhr.onreadystatechange = () => {
         if (xhr.readyState === 4) {
           if ((xhr.status >= 200 && xhr.status < 300) || xhr.status === 304) {
             resolve(xhr.responseText);
           } else {
             reject(xhr);
           }
         }
       };
       xhr.send(data);
     });
   }

   ajax({ method: "GET", url: "http://xxxx.com" }).catch((res) =>
     console.log("res1:", res)
   );

   ajax({ method: "POST", url: "http://google.com", data: "" }).then((res) =>
     console.log("res2:", res)
   );
   ```

1. Promise.all、Promise.allSettled、Promise.race、Promise.any、Promise.resolve、Promise.reject 分别有什么用？

   - Promise.all

     Promise.all 接受一个数组，如果这个数组里的所有 promise 都成功的话，返回由这些成功的 promise 得 data 所构成的数组 只要有一个 promise 失败，则返回该失败的 promise

   * Promise.allSettled

     Promise.allSettled 接受一个数组，和 all 不同的是，他会遍历数组的每一项，并把该项的结果写入结果数组对应的位置，最后返回这个数组

   * Promise.race

     Promise.race 接收一个数组，返回这个数组第一个被处理的 Promise 的结果

1. 手写 Promise.all、Promise.race、Promise.allSettled、Promise.any、Promise.resolve、Promise.reject

   - 手写 Promise.all

     ```js
     Promise.all2 = function (arr) {
       const promises = [...arr].map((item) =>
         item instanceof Promise ? item : Promise.resolve(item)
       );
       return new Promise((resolve, reject) => {
         let count = 0;
         const length = promises.length;
         const res = [];
         for (let i = 0; i < length; i++) {
           promises[i].then(
             (result) => {
               res[i] = result;
               count += 1;
               if (count === length) resolve(res);
             },
             (reason) => {
               reject(reason);
             }
           );
         }
       });
     };

     Promise.all2([
       new Promise((resolve) => setTimeout(() => resolve(1), 3000)), // 1
       new Promise((resolve) => setTimeout(() => resolve(2), 2000)), // 2
       new Promise((resolve) => setTimeout(() => resolve(3), 1000)), // 3
     ]).then((result) => {
       console.log(result);
     });

     Promise.all2([
       new Promise((resolve, reject) => setTimeout(() => resolve(1), 1000)),
       new Promise((resolve, reject) =>
         setTimeout(() => reject("Whoops!"), 2000)
       ),
       new Promise((resolve, reject) => setTimeout(() => resolve(3), 3000)),
     ]).then(
       (result) => {
         console.log(result);
       },
       (reason) => console.log(reason)
     );
     ```

   * 手写 Promise.allSettled

     ```js
     Promise.allSettled2 = function (arr) {
       const promises = [...arr].map((item) =>
         item instanceof Promise ? item : Promise.resolve(item)
       );
       return new Promise((resolve, reject) => {
         const res = [];
         let count = 0;
         for (let i = 0; i < promises.length; i++) {
           const promise = promises[i];
           promise.then(
             (result) => {
               count += 1;
               res[i] = { status: "fulfilled", value: result };
               if (count === promises.length) resolve(res);
             },
             (reason) => {
               count += 1;
               res[i] = { status: "rejected", reason: reason };
               if (count === promises.length) resolve(res);
             }
           );
         }
       });
     };

     let urls = [
       "https://api.github.com/users/iliakan",
       "https://api.github.com/users/remy",
       "https://no-such-url",
     ];

     Promise.allSettled2(urls.map((url) => fetch(url))).then((results) => {
       console.log(results);
     });
     ```

   * 手写 Promise.race

     ```js
     Promise.race2 = function (arr) {
       const promises = [...arr].map((item) =>
         item instanceof Promise ? item : Promise.resolve(item)
       );
       return new Promise((resolve, reject) => {
         for (let i = 0; i < promises.length; i++) {
           const promise = promises[i];
           promise.then(
             (result) => {
               resolve(result);
             },
             (reason) => {
               reject(reason);
             }
           );
         }
       });
     };

     Promise.race2([
       new Promise((resolve, reject) => setTimeout(() => resolve(1), 1000)),
       new Promise((resolve, reject) =>
         setTimeout(() => reject(new Error("Whoops!")), 5000)
       ),
       new Promise((resolve, reject) => setTimeout(() => resolve(3), 3000)),
     ]).then(
       (res) => console.log(res),
       (err) => {
         console.log(err);
       }
     ); // 4

     Promise.race2([
       new Promise((resolve, reject) => setTimeout(() => resolve(1), 1000)),
       new Promise((resolve, reject) =>
         setTimeout(() => reject(new Error("Whoops!")), 500)
       ),
       new Promise((resolve, reject) => setTimeout(() => resolve(3), 3000)),
       4,
     ]).then(
       (res) => console.log(res),
       (err) => {
         console.log(err);
       }
     ); // err

     Promise.race2([
       new Promise((resolve, reject) => setTimeout(() => resolve(1), 1000)),
       new Promise((resolve, reject) =>
         setTimeout(() => reject(new Error("Whoops!")), 500)
       ),
       new Promise((resolve, reject) => setTimeout(() => resolve(3), 3000)),
     ]).then(
       (res) => console.log(res),
       (err) => {
         console.log(err);
       }
     ); // 1
     ```

   * 手写 Promise.any

     ```js
     Promise.any2 = function (arr) {
       const promises = [...arr].map((item) =>
         item instanceof Promise ? item : Promise.resolve(item)
       );
       return new Promise((resolve, reject) => {
         for (let i = 0; i < promises.length; i++) {
           const promise = promises[i];
           let count = 0;
           promise.then(
             (result) => {
               resolve(result);
             },
             (err) => {
               count += 1;
               if (count === promises.length)
                 reject(new AggregateError("All promises were rejected"));
             }
           );
         }
       });
     };

     Promise.any2([
       new Promise((resolve, reject) =>
         setTimeout(() => reject(new Error("Whoops!")), 1000)
       ),
       new Promise((resolve, reject) => setTimeout(() => resolve(1), 2000)),
       new Promise((resolve, reject) => setTimeout(() => resolve(3), 3000)),
     ]).then((res) => console.log(res)); // 1

     Promise.any2([
       new Promise((resolve, reject) =>
         setTimeout(() => reject(new Error("Ouch!")), 1000)
       ),
       new Promise((resolve, reject) =>
         setTimeout(() => reject(new Error("Error!")), 2000)
       ),
     ]).catch((error) => {
       console.log(error);
       console.log(error.constructor.name); // AggregateError
       console.log(error.errors[0]); // Error: Ouch!
       console.log(error.errors[1]); // Error: Error!
     });
     ```

   * 手写 Promise.resolve

     ```js
     Promise.resolve2 = function (value) {
       return new Promise((resolve) => resolve(value));
     };

     console.log(Promise.resolve2(2));
     ```

   * 手写 Promise.reject

     ```js
     Promise.reject2 = function (err) {
       return new Promise((resolve, reject) => reject(err));
     };

     console.log(Promise.reject2(3));
     ```

1. 并发请求限制数量手写代码
1. 手写代码实现 Promisify
1. 说一说宏任务微任务的处理方式
1. 手写一个 Promise

   简单版(all test has been passed):

   ```js
   class Promise2 {
     status = "pending"; // 'pending','fulfilled','rejected'
     callbacks = []; // type handle = {succeed: Function,fail: Function} type callbacks = handle[]
     resolve(result) {
       if (this.status !== "pending") return; // 如果不是pending 不执行
       this.status = "fulfilled";
       nextTick(() => {
         // 遍历 callbacks，调用所有的 handle.success
         this.callbacks.forEach((handle) => {
           if (typeof handle.succeed === "function") {
             handle.succeed.call(undefined, result);
           }
         });
       });
     }
     reject(reason) {
       if (this.status !== "pending") return; // 如果不是pending 不执行
       this.status = "rejected";
       nextTick(() => {
         // 遍历 callbacks，调用所有的 handle.fail
         this.callbacks.forEach((handle) => {
           if (typeof handle.fail === "function") {
             handle.fail.call(undefined, reason);
           }
         });
       });
     }
     constructor(fn) {
       if (typeof fn !== "function") throw new Error("fn must be function");
       fn(this.resolve.bind(this), this.reject.bind(this));
     }
     then(succeed, fail) {
       const handle = { succeed: undefined, fail: undefined };
       if (typeof succeed === "function") {
         handle.succeed = succeed;
       }
       if (typeof fail === "function") {
         handle.fail = fail;
       }
       this.callbacks.push(handle);
       // 把函数推到 callbacks 里面
     }
   }
   ```

   完整版

   ```js
   class Promise2 {
     status = "pending"; // 'pending','fulfilled','rejected'
     callbacks = []; // [{succeed: f1,fail: f2,promise: Promise2}]
     resolve(result) {
       if (this.status !== "pending") return; // 如果不是pending 不执行
       this.status = "fulfilled";
       nextTick(() => {
         // 遍历 callbacks，调用所有的 handle[0]
         this.callbacks.forEach((handle) => {
           if (typeof handle.succeed === "function") {
             let x;
             try {
               x = handle.succeed.call(undefined, result);
             } catch (e) {
               return handle.promise.reject(e);
             }
             handle.promise.resolveWith(x);
           }
         });
       });
     }
     reject(reason) {
       if (this.status !== "pending") return; // 如果不是pending 不执行
       this.status = "rejected";
       nextTick(() => {
         // 遍历 callbacks，调用所有的 handle[0]
         this.callbacks.forEach((handle) => {
           if (typeof handle.fail === "function") {
             let x;
             try {
               x = handle.fail.call(undefined, reason);
             } catch (e) {
               return handle.promise.reject(e);
             }
             handle.promise.resolveWith(x);
           }
         });
       });
     }
     constructor(fn) {
       if (typeof fn !== "function") throw new Error("fn must be function");
       fn(this.resolve.bind(this), this.reject.bind(this));
     }
     then(succeed, fail) {
       const handle = {
         succeed: undefined,
         fail: undefined,
         promise: undefined,
       };
       if (typeof succeed === "function") {
         handle.succeed = succeed;
       }
       if (typeof fail === "function") {
         handle.fail = fail;
       }
       handle.promise = new Promise2(() => {}); // 为了在resolve / reject中 把上一个的promise的返回值 给了下一个promise
       this.callbacks.push(handle);
       // 把函数推到 callbacks 里面
       return handle.promise;
     }
     resolveWith(x) {
       // promise处理程序 对应规2.3
       if (this === x) this.reject(new TypeError());
       //2.3.1 如果promise和x引用同一个对象，则用TypeError作为原因拒绝（reject）promise。
       else if (x instanceof Promise2) {
         // 2.3.2 如果x是一个promise,采用promise的状态
         x.then(
           (result) => {
             this.resolve(result);
           },
           (reason) => {
             this.reject(reason);
           }
         );
       }
       if (x instanceof Object) {
         //  2.3.3另外，如果x是个对象或者方法
         let then;
         try {
           then = x.then; // 2.3.3.1 让x作为x.then. 3.5
         } catch (e) {
           this.reject(e); // 2.3.3.2 如果取回的x.then属性的结果为一个异常e,用e作为原因reject promise
         }
         if (typeof then === "function") {
           // 如果then是一个方法，把x当作this来调用它， 第一个参数为 resolvePromise，第二个参数为rejectPromise,其中:
           try {
             x.then(
               (y) => this.resolveWith(y), // 2.3.3.3.1 如果/当 resolvePromise被一个值y调用，运行 [[Resolve]](promise, y)
               (r) => this.reject(r) // 2.3.3.3.2 如果/当 rejectPromise被一个原因r调用，用r拒绝（reject）promise);
             );
           } catch (e) {
             this.reject(e); // 2.3.3.3.4 如果调用then抛出一个异常e,用e作为reason拒绝（reject）promise
           }
         } else {
           this.resolve(x); //2.3.3.4 如果then不是一个函数，用x完成(fulfill)promise
         }
       } else {
         this.resolve(x); // 2.3.3.4 如果then不是一个函数，用x完成(fulfill)promise
       }
     }
   }
   ```

   nextTick 的写法

   ```js
   function nextTick(fn) {
     if (process && typeof proess.nextTick === 'function') {
       return process.nextTick(fn);
     } else {
       let count = 0;
       const Observer = new MutationObserver(fn);
       const textNode = document.createTextNode(String(count));
       Observer.observe({ textNode,{
         charactor: true
       } });
       count += 1
       textNode.data = String(count)
     }
   }
   ```

> 代码输出题

1. 代码输出什么

```javascript
setTimeout(() => console.log(1), 0);
new Promise((resolve) => {
  resolve();
  console.log(2);
}).then(() => {
  console.log(3);
});
console.log(4);
```

1. 输出什么

```javascriptt
new Promise(function f1(resolve){
  console.log(1);
  setTimeout(function f2() {
    console.log(2);
  });
  resolve(1);
}).then(function f3(res) {
  console.log(3);
})

setTimeout(function f4() {
  console.log(4);
})

console.log(5);
```

3. 输出什么

```javascript
setTimeout(function f1() {
  console.log(1);
});

new Promise(function f2(resolve) {
  resolve();
  console.log(2);
}).then(function f3() {
  console.log(3);
  Promise.resolve()
    .then(function f4() {
      console.log(4);
    })
    .then(function f5() {
      Promise.resolve().then(function f6() {
        console.log(5);
      });
    });
});

console.log(6);
```

4. 输出什么

```javascript
console.log(1);
setTimeout(function f1() {
  console.log(2);
  Promise.resolve()
    .then(function f2() {
      console.log(3);
      setTimeout(function f3() {
        console.log(4);
      });
    })
    .then(function f4() {
      console.log(5);
    });
}, 0);
console.log(6);
```

5. 输出什么

```javascript
const pro = new Promise(function f1(resolve) {
  const innerpro = new Promise(function f2(resolve) {
    setTimeout(function f3() {
      resolve(1);
    }, 0);
    console.log(2);
    resolve(3);
  });
  innerpro.then(function f4(res) {
    console.log(res);
  });
  resolve(4);
  console.log(5);
});
pro.then(function f5(res) {
  console.log(res);
});
console.log(6);
```

6. 输出什么

```javascript
async function async1() {
  console.log("async1 start");
  await async2();
  console.log("async1 end");
}
async function async2() {
  console.log("async2 start");
  return new Promise((resolve, reject) => {
    resolve();
    console.log("async2 promise");
  });
}
console.log("script start");
setTimeout(function () {
  console.log("setTimeout");
}, 0);
async1();
new Promise(function (resolve) {
  console.log("promise1");
  resolve();
})
  .then(function () {
    console.log("promise2");
  })
  .then(function () {
    console.log("promise3");
  });
console.log("script end");
```

7. 输出什么

```javascript
new Promise((resolve, reject) => {
  reject("error");
})
  .then(
    () => {
      console.log("ok 1");
    },
    (err) => {
      console.log("error 1: " + err);
    }
  )
  .then(
    () => {
      console.log("ok 2");
    },
    (err) => {
      console.log("error 2: " + err);
    }
  )
  .catch((err) => {
    console.log("catch 1: " + err);
  });
```

> 补充题

1. 如何中断一个 Promise
