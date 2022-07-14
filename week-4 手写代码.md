# Week 4

> 视频[讲解在这里](https://xiedaimala.com/courses/6c9cc8d2-6b28-4af1-879b-58ef595898a3/random/fac5597765?#/common)

> 手写代码

1. 实现一个数组去重

   ```js
   function deduplication(arr) {
     return Array.from(new Set(arr));
   }

   {
     console.log(deduplication([1, 2, 3, 4, 4, 4, 4, 4, 4, 1, 1, 1, 1]));
   }
   ```

2. 如果不用 Set 怎么去重?

   ```js
   function deduplication2(arr) {
     for (let i = 0; i < arr.length; i++) {
       for (let j = i + 1; j < arr.length; j++) {
         if (arr[i] === arr[j]) {
           arr.splice(j, 1);
           j--;
         }
       }
     }
   }

   {
     const arr = [1, 2, 3, 4, 4, 4, 4, 4, 4, 1, 1, 1, 1];
     deduplication2(arr);
     console.log("2:", arr);
   }

   function deduplication2(arr) {
     const set = new Set();
     for (let i = 0; i < arr.length; i++) {
       if (!set.has(arr[i])) {
         set.add(arr[i]);
       } else {
         arr.splice(i, 1);
         i--;
       }
     }
   }

   {
     const arr = [1, 2, 3, 4, 4, 4, 4, 4, 4, 1, 1, 1, 1];
     deduplication2(arr);
     console.log("2:", arr);
   }
   ```

3. 如何在原数组上去重?
4. 如果数组里是多个对象，如何按对象属性字段去重?
5. 手写对象或者数组的深拷贝

   ```js
   //手写深拷贝

   function deepClone(source, map = new Map()) {
     if (map.get(source)) {
       return map.get(source);
     }
     if (source instanceof Object) {
       let dist;
       if (source instanceof Array) {
         dist = new Array();
       } else if (source instanceof Function) {
         dist = function (...args) {
           return source.call(null, ...args);
         };
       } else if (source instanceof RegExp) {
         dist = new RegExp(source.source, source.flags);
       } else if (source instanceof Date) {
         dist = new Date(source);
       } else {
         dist = new Object();
       }
       map.set(source, dist);
       for (let key in source) {
         if (source.hasOwnProperty(key)) {
           dist[key] = deepClone(source[key], map);
         }
       }
       return dist;
     } else {
       return source;
     }
   }

   {
     let a = [1, 2, 3, 4, 5, [1, 2, 3]];
     let b = deepClone(a);
     console.log(a, b, a === b);
   }

   {
     let a = (a, b) => a + b;
     let b = deepClone(a);
     console.log(a(1, 2), b(1, 2), a === b);
   }

   {
     let a = {
       b: 1,
       c: "12",
       d: { aa: 1, bb: false },
       e: [1, 2, 3, 4, 5],
     };

     let b = JSON.parse(JSON.stringify(a));

     console.log(a);
     console.log(b);
     console.log(a === b);
   }

   {
     console.log("-------环--------");
     const a = { name: "jack" };
     a.self = a;
     const a2 = deepClone(a);
     console.log(a, a2, a !== a2);
     console.log(a.name === a2.name);
     console.log(a.self !== a2.self);
   }
   ```

6. 如果存在循环引用怎么办
7. 如何深拷贝一个函数
8. 实现一个 trim 函数

   ```js
   function trim(s) {
     return s.replace(/(^\s?) | (\s?$)/g, "");
   }

   console.log(trim("  aaaa   "));
   console.log("  aaaa   ");
   ```

9. 手写一个 curry 函数

   ```js
   function curry(fn, params = []) {
     return function (...args) {
       if (params.length + args.length === fn.length) {
         return fn(...params, ...args);
       } else {
         return curry(fn, [...params, ...args]);
       }
     };
   }

   {
     const add = (a, b, c, d) => a + b + c + d;
     const newAdd = curry(add);
     console.log(newAdd(1)(2)(3)(4));
     console.log(newAdd(1, 2)(3, 4));
   }
   ```

10. 写一个观察者 EventBus，含 emit、on、once、off 这些方法

    ```js
    class EventHub {
      constructor() {
        this.cache = {}; // [{name: fn1,fn2,fn3}]
      }
      on(name, fn) {
        this.cache[name] = this.cache[name] || [];
        this.cache[name].push(fn);
      }
      off(name, fn) {
        let idx = (this.cache[name] || []).indexOf(fn);
        if (idx >= 0) this.cache[name].splice(idx, 1);
      }
      emit(name, ...args) {
        (this.cache[name] || []).forEach((fn) => fn(...args));
      }
    }

    const test1 = (message) => {
      const eventHub = new EventHub();
      console.assert(eventHub instanceof Object === true, "eventHub是个对象");
      console.log(message);
    };

    // On Emit
    const test2 = (message) => {
      const eventHub = new EventHub();
      let called = false;
      eventHub.on("xxx", (y, z) => {
        called = true;
        console.assert("y", y, y === "今天林志玲结婚了");
        console.assert("z", z, z === "zzz");
      });
      eventHub.emit("xxx", "今天林志玲结婚了", "zzz");
      setTimeout(() => {
        console.assert("called:", called, called === true);
      }, 1000);
      console.log(message);
    };

    const test3 = (message) => {
      const eventHub = new EventHub();
      let called = false;
      let fn1 = () => {
        called = true;
      };
      eventHub.on("yyy", fn1);
      eventHub.off("yyy", fn1);
      eventHub.emit("yyy");
      setTimeout(() => {
        console.assert(called === false);
      }, 1000);
      console.log(message);
    };

    test1("eventHub 可以创建对象");
    test2("on之后 emit 函数会执行");
    test3("off有效");
    ```

11. 实现一个 instancdof

    ```js
    function myInstanceof(source, target) {
      if (typeof source !== "object" && source !== null) {
        throw new Error();
      }
      if (typeof target !== "function") {
        throw new Error();
      }
      let p = source;
      while (p) {
        if (p === target.prototype) return true;
        p = p.__proto__;
      }
      return false;
    }

    {
      const c = new Cat("jack");
      console.log(myInstanceof([], Array));
      console.log(myInstanceof([], Object));
      console.log(myInstanceof([], Function));
      console.log(myInstanceof(c, Cat));
    }
    ```

12. 实现一个 new

    ```js
    function myNew(fn, ...args) {
      if (typeof fn !== "function") throw new Error();
      const obj = Object.create(fn.prototype);
      const res = fn.call(obj, ...args);
      return typeof res === "object" && res !== null ? res : obj;
    }

    {
      Cat.prototype.say = function () {
        console.log(this.name);
      };
      function Cat2(name) {
        this.name = name;
        return [];
      }
      const c = myNew(Cat, "j");
      console.log(c);
      c.say();
      console.log(myNew(Cat2, "a"));
    }
    ```

13. 用 call/apply 实现 bind

    ```js
    Function.prototype.mybind = function (thisArg, ...args) {
      const fn = this;
      function bindFn(...rest) {
        if (this instanceof bindFn) {
          const ctx = Object.create(fn.prototype);
          fn.call(ctx, ...args, ...rest);
          return ctx;
        } else {
          return fn.call(thisArg, ...args, ...rest);
        }
      }
      return bindFn;
    };

    {
      const a = (a, b) => a + b;

      const fa = a.mybind({}, 1, 2);

      console.log(fa());

      const myCat = Cat.mybind({ name: "jact" });

      const cat = new myCat("hua");
      console.log(cat);
      cat.say();
    }
    ```

14. 实现 call

    ```js
    Function.prototype.mycall = function (thisArg, ...args) {
      const fn = this;
      const ctx = thisArg;
      const symbol = Symbol();
      Object.prototype[symbol] = fn;
      const res = ctx[symbol](...args);
      delete Object[symbol];
      return res;
    };

    {
      function b(aa) {
        return this.a + aa;
      }

      console.log(b.mycall({ a: 1 }, 4));
      console.log(b.mycall({ a: 2 }, 4));

      function c(bb, aa) {
        return bb + aa;
      }
      console.log(c.mycall({ a: 2 }, 4, 1));
    }
    ```

15. 不用 call/apply 实现 bind

    综上两题

16. 手写防抖

    ```js
    function debounce(fn, delay) {
      let timer;
      return function (...args) {
        if (timer) clearTimeout(timer);
        timer = setTimeout(() => {
          fn(...args);
        }, delay);
      };
    }
    ```

17. 写节流

    ```js
    function throttle(fn, delay) {
      let timer;
      return function (...args) {
        if (timer) return;
        fn(...args);
        timer = setTimeout(() => {
          timer = null;
        }, delay);
      };
    }
    ```

18. 手写 Array 的 map、filter、reduce

    ```js
    Function.prototype.mycall = function (thisArg, ...args) {
      const fn = this;
      const ctx = thisArg;
      const symbol = Symbol();
      Object.prototype[symbol] = fn;
      const res = ctx[symbol](...args);
      delete Object[symbol];
      return res;
    };

    {
      function b(aa) {
        return this.a + aa;
      }

      console.log(b.mycall({ a: 1 }, 4));
      console.log(b.mycall({ a: 2 }, 4));

      function c(bb, aa) {
        return bb + aa;
      }
      console.log(c.mycall({ a: 2 }, 4, 1));
    }

    function debounce(fn, delay) {
      let timer;
      return function (...args) {
        if (timer) clearTimeout(timer);
        timer = setTimeout(() => {
          fn(...args);
        }, delay);
      };
    }

    function throttle(fn, delay) {
      let timer;
      return function (...args) {
        if (timer) return;
        fn(...args);
        timer = setTimeout(() => {
          timer = null;
        }, delay);
      };
    }

    Array.prototype.mymap = function (fn) {
      const arr = this;
      const res = [];
      for (let i = 0; i < arr.length; i++) {
        res.push(fn(arr[i], i, arr));
      }
      return res;
    };

    {
      const a = [1, 2, 3, 4, 5];

      const b = a.mymap((item) => item * 2);
      console.log(b);
    }

    Array.prototype.myfilter = function (fn) {
      const arr = this;
      const res = [];
      for (let i = 0; i < arr.length; i++) {
        if (fn(arr[i], i, arr)) res.push(arr[i]);
      }
      return res;
    };

    {
      const a = [1, 2, 3, 4, 5];

      const b = a.myfilter((item) => item % 2 === 0);
      console.log(b);
    }

    Array.prototype.myreduce = function (fn, init) {
      const arr = this;
      let prev = init || arr[0];
      let i = init ? 0 : 1;
      for (; i < arr.length; i++) {
        const cur = arr[i];
        prev = fn(prev, cur, i, arr);
      }
      return prev;
    };

    {
      const a = [1, 2, 3, 4, 5, 6];
      const d = a.myreduce((prev, cur) => {
        return prev + cur;
      });

      const e = a.reduce((prev, cur) => {
        return prev + cur;
      });

      console.log(d, e);
    }
    ```

19. 写一个数组拍平 flat

    ```js
    Array.prototype.myflat = function () {
      const arr = this;
      let tmp = [];
      arr.forEach((item) => {
        tmp = tmp.concat(Array.isArray(item) ? item.myflat() : item);
      });
      return tmp;
    };

    {
      const a = [1, 2, 3, [4, 5, 6, [7, 8, [9]]], 4, 5, 6, [8, 8, 8]];
      console.log(a.myflat());
    }
    ```

20. 手写一个原型继承
21. 用 class 写一个继承
22. 用 Promise 封装一个 delay 函数
23. 实现字符串大数相加
24. 手写 LazyMan
