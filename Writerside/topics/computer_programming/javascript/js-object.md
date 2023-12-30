# 객체

## property accessor

'getter'와 'setter' 형태의 메쏘드를 사용해 객체의 속성에 접근할 수 있다.

```js
const person = {
  firstName: 'XXX',
  lastName: 'YYY',

  get fullName() {
    return `${this.firstName} ${this.lastName}`;
  },

  set fullName(name) {
    [this.firstName, this.lastName] = name.split(' ');
  }
};

console.log(person.fullName); // XXX YYY
```
