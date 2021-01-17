## 手写promiseAll

```js
function promiseAll1(promiseArray) {
    return new Promise(function (resolve, reject) {
        if (Array.isArray(promiseArray)) {
            return reject(new Error("Promise must be an array"));
        }
        let resolveCount = 0;
        let promiseNum = promiseArray.length;
        let resolveValue = [];
        for (let i = 0; i < promiseNum; i++) {
            Promise.resolve(promiseArray[i]).then(
                (value)  => {
                    resolveValue[i] = value;
                    resolveCount++;
                    if (resolveCount === promiseNum) {
                        return resolveValue;
                    }
                },
                (reason) => {
                    return reject(reason);
                }
            )
        }
    })
}
```

## 手写一个结合all和race的方法，所有都resolved/reject时才返回，并返回所有的结果

```js
async function  allResolve(promises){
   const success = Array.from(promises).map(promise=>{
 			Promise.resolve(promise).then(val=>val).catch(err=>err);
      })
   return await Promise.all(success);
}
```
## Promise（A+规范）、then、all方法

## Iterator遍历器实现

## async实现原理（spawn函数）

## Thunk函数实现（结合Generator实现异步）