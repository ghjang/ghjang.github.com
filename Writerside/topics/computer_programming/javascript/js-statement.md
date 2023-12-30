# 문장

자바스크립트의 문장은 세미콜론(;)으로 끝내도되고 안끝내도 된다. 하지만 세미콜론을 붙이는 것을 권장한다.

## 반복문

### for

```javascript
for (let i = 0; i < 10; ++i) {
  console.log(i);
}
```

### for ... in

```javascript
const obj = {
  a: 1,
  b: 2,
  c: 3
};

for (const key in obj) {
  console.log(key, obj[key]);
}
```

### for ... of

```javascript
const arr = [1, 2, 3];

for (const item of arr) {
  console.log(item);
}
```

### while

```javascript
let i = 0;

while (i < 10) {
  console.log(i);
  ++i;
}
```

### do ... while

```javascript
let i = 0;

do {
  console.log(i);
  ++i;
} while (i < 10);
```

## 조건문

### if ... else

```javascript
const n = 1;

if (n > 0) {
  console.log('n is positive');
} else if (n < 0) {
  console.log('n is negative');
} else {
  console.log('n is zero');
}
```

### switch

```javascript
const n = 1;

switch (n) {
  case 1:
    console.log('n is 1');
    break;
  case 2:
    console.log('n is 2');
    break;
  default:
    console.log('n is not 1 or 2');
}
```

자바스크립트에서 '문자열'에 대한 'switch 문'을 사용할 수 있다.

```javascript
const str = 'foo';

switch (str) {
  case 'foo':
    console.log('str is foo');
    break;
  case 'bar':
    console.log('str is bar');
    break;
  default:
    console.log('str is not foo or bar');
}
```

## 제어문

### break

```javascript
for (let i = 0; i < 10; ++i) {
  if (i > 5) {
    break;
  }
  console.log(i);
}
```

### continue

```javascript
for (let i = 0; i < 10; ++i) {
  if (i % 2 === 0) {
    continue;
  }
  console.log(i);
}
```

## 레이블 문

```javascript
outer: for (let i = 0; i < 3; ++i) {
  for (let j = 0; j < 3; ++j) {
    if (i + j === 3) {
      break outer;
    }
    console.log(`inner [${i}, ${j}]`);
  }
}
```

## try ... catch ... finally

```javascript
try {
  console.log('try block');
} catch (e) {
  console.log('catch block');
} finally {
  console.log('finally block');
}
```

## with

```javascript
const obj = {
  a: 1,
  b: 2,
  c: 3
};

with (obj) {
  console.log(a, b, c);
}
```

## debugger

```javascript
function foo() {
  debugger;
  console.log('foo');
}

foo();
```

## var

```javascript
var x = 1;

if (true) {
  var x = 2;
}

console.log(x);
```

## let

```javascript
let x = 1;

if (true) {
  let x = 2;
}

console.log(x);
```

## const

```javascript
const x = 1;

if (true) {
  const x = 2;
}

console.log(x);
```
