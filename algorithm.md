## Javascript 알고리즘 정리

### Array

```js
// n = 5인 경우 [1,2,3,4,5] 배열 생성
const arr = Array.from({ length: n }, (_, i) => i + 1)
```

### 중복X 조합 (Combination)

```js
let k = 3
let arr = [1, 2, 3, 4, 5] // 이 배열에서
let res = []

const combination = (start, temp) => {
  if (temp.length === k) {
    // k개 뽑는 조합(Combination)
    res.push([...temp])
    return
  }

  for (let i = start; i < arr.length; i++) {
    temp.push(arr[i])
    combi(i + 1, temp)
    temp.pop()
  }
}

combi(0, [])
console.log(res) // [ [1,2], [1,3], [1,4], [2,3], [2,4], [3,4] ]
```

### 중복O 순열 (Permutation)

> `console.log`는 매우 느린 작업이다  
> 속도를 높이기 위해서 문자열에 저장 후, 마지막에 한 번 만 `console.log` 해주는 것이 훨씬 빠르다.

```js
let k = 3
let arr = [1, 2, 3, 4, 5]
let res = ''

const permutation = (temp) => {
  if (temp.length === k) {
    // 여기서 console.log(temp.join(' ')) 하게되면 시간 초과 ⚠️
    res += `${temp.join(' ')}\n`
    return
  }

  for (let i = 0; i < n; i++) {
    temp.push(arr[i])
    permutation(temp)
    temp.pop()
  }
}

permutation([])
console.log(res)
```
