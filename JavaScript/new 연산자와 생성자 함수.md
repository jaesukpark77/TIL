객체 리터럴 {...} 을 사용하면 객체를 쉽게 만들 수 있다.
`'new'` 연산자와 생성자 함수를 사용하면 유사한 객체 여러 개를 쉽게 만들 수 있다.

# 생성자 함수
생성자 함수(constructor function)와 일반 함수에 기술적인 차이는 없다.
다만 생성자 함수는 아래 두 관례를 따른다.

1. 함수 이름의 첫 글자는 대문자로 시작합니다.
2. 반드시 `'new'` 연산자를 붙여 실행합니다.

```javascript
function User(name) {
  this.name = name;
  this.isAdmin = false;
}

let user = new User("상혁");

console.log(user.name); // 상혁
console.log(user.isAdmin); // false
```

`new User(...)`를 써서 함수를 실행하면 아래와 같은 알고리즘이 동작한다.

1. 빈 객체를 만들어 `this`에 할당한다.
2. 함수 본문을 실행한다. 
`this`에 새로운 프로퍼티를 추가해 `this`를 수정한다.
3. `this`를 반환한다.

```javascript
function User(name) {
  // this = {};  (빈 객체가 암시적으로 만들어짐)

  // 새로운 프로퍼티를 this에 추가함
  this.name = name;
  this.isAdmin = false;

  // return this;  (this가 암시적으로 반환됨)
}
```

이제 `let user = new User("상혁")`는 아래 코드를 입력한 것과 동일하게 동작한다.

```javascript
let user = {
  name: "상혁",
  isAdmin: false
};
```

- 생성자는 재사용할 수 있는 객체 생성 코드를 구현하는 것
- 모든 함수는 생성자 함수가 될 수 있다.
- new를 붙여 실행한다면 어떤 함수라도 위에 언급된 알고리즘이 실행된다. 

### new function(){ … }
코드를 익명 생성자 함수로 감싸주는 방식을 사용할 수 있다.

```javascript
let user = new function() {
  this.name = "Jason";
  this.isAdmin = false;

  // 사용자 객체를 만들기 위한 여러 코드.
  // 지역 변수, 복잡한 로직, 구문 등의 다양한 코드가 여기에 들어갑니다.
};
```

위 생성자 함수는 익명 함수이기 때문에 어디에도 저장되지 않는다. 
처음 만들 때부터 단 한 번만 호출할 목적으로 만들었기 때문에 재사용이 불가능하다. 
이렇게 익명 생성자 함수를 이용하면 재사용은 막으면서 코드를 캡슐화 할 수 있다.

# 생성자 내 메서드
생성자 함수를 사용하면 매개변수를 이용해 객체 내부를 자유롭게 구성할 수 있다. 

```javascript
function User(name) {
  this.name = name;

  this.sayHi = function() {
    console.log( "제 이름은 " + this.name + "입니다." );
  };
}

let faker = new User("이상혁");

faker.sayHi(); // 제 이름은 이상혁입니다.

/*
faker = {
   name: "이상혁",
   sayHi: function() { ... }
}
*/
```