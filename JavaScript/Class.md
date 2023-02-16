# 클래스란?
자바스크립트에서 클래스는 함수의 한 종류이다.

```javascript
class User {
  constructor(name) { this.name = name; }
  sayHi() { console.log(this.name); }
}

// User가 함수라는 증거
console.log(typeof User); // function
```

`class User {...}` 문법 구조 분석
1. User라는 이름을 가진 함수를 만든다. 
함수 본문은 생성자 메서드 constructor에서 가져온다. 
생성자 메서드가 없으면 본문이 비워진 채로 함수가 만들어진다.
2. sayHi같은 클래스 내에서 정의한 메서드를 User.prototype에 저장한다.

`new User`를 호출해 객체를 만들고, 객체의 메서드를 호출하면 메서드를 prototype 프로퍼티를 통해 가져온다. 이 과정이 있기 때문에 객체에서 클래스 메서드에 접근할 수 있다.

```javascript
class User {
  constructor(name) { this.name = name; }
  sayHi() { console.log(this.name); }
}

// 클래스는 함수입니다.
console.log(typeof User); // function

// 정확히는 생성자 메서드와 동일합니다.
console.log(User === User.prototype.constructor); // true

// 클래스 내부에서 정의한 메서드는 User.prototype에 저장됩니다.
console.log(User.prototype.sayHi); // console.log(this.name);

// 현재 프로토타입에는 메서드가 두 개입니다.
console.log(Object.getOwnPropertyNames(User.prototype)); // constructor, sayHi
```

# 클래스 vs 순수 함수
```javascript
// class User와 동일한 기능을 하는 순수 함수를 만들어보겠습니다.

// 1. 생성자 함수를 만듭니다.
function User(name) {
  this.name = name;
}
// 모든 함수의 프로토타입은 'constructor' 프로퍼티를 기본으로 갖고 있기 때문에
// constructor 프로퍼티를 명시적으로 만들 필요가 없습니다.

// 2. prototype에 메서드를 추가합니다.
User.prototype.sayHi = function() {
  console.log(this.name);
};

// 사용법:
let user = new User("John");
user.sayHi();
```

순수 함수로 클래스 역할을 하는 함수를 선언하는 방법과 class 키워드를 사용하는 방법의 결과는 거의 같다.
그런데 두 방법에는 중요한 차이가 몇 가지 있다.
1. class로 만든 함수엔 특수 내부 프로퍼티인 `[[IsClassConstructor]]: true`가 이름표처럼 붙는다.
클래스 생성자를 new와 함께 호출하지 않으면 에러가 발생하는데 이 때 `[[IsClassConstructor]]: true`가 사용됩니다.

```javascript
class User {
  constructor() {}
}

console.log(typeof User); // User의 타입은 함수이긴 하지만 그냥 호출할 수 없습니다.
User(); // TypeError: Class constructor User cannot be invoked without 'new'
```

2. 클래스에 정의된 메서드는 열거할 수 없다(non-enumerable). 
클래스의 `prototype` 프로퍼티에 추가된 메서드의 `enumerable` 플래그는 `false`다.
`for..in`으로 객체를 순회할 때, 메서드는 순회 대상에서 제외하고자 하는 경우가 많으므로 이 특징은 꽤 유용하다.

3. 클래스는 항상 `엄격 모드`로 실행된다(use strict). 
클래스 생성자 안 코드 전체엔 자동으로 엄격 모드가 적용된다.

# 클래스 표현식
함수처럼 클래스도 다른 표현식 내부에서 정의, 전달, 반환, 할당할 수 있다.

```javascript
let User = class {
  sayHi() {
    console.log("안녕하세요.");
  }
};
```

기명 함수 표현식(Named Function Expression)과 유사하게 클래스 표현식에도 이름을 붙일 수 있다.

클래스 표현식에 이름을 붙이면, 이 이름은 오직 클래스 내부에서만 사용할 수 있다.

```javascript
let User = class MyClass {
  sayHi() {
    console.log(MyClass); // MyClass라는 이름은 오직 클래스 안에서만 사용할 수 있습니다.
  }
};

new User().sayHi(); // 원하는대로 MyClass의 정의를 보여줍니다.

console.log(MyClass); // ReferenceError: MyClass is not defined, MyClass는 클래스 밖에서 사용할 수 없습니다.
```

‘필요에 따라’ 클래스를 동적으로 생성하는 것도 가능하다.

```javascript
function makeClass(phrase) {
  // 클래스를 선언하고 이를 반환함
  return class {
    sayHi() {
      console.log(phrase);
    };
  };
}

// 새로운 클래스를 만듦
let User = makeClass("안녕하세요.");

new User().sayHi(); // 안녕하세요.
```

# getter와 setter
클래스도 getter나 setter, 계산된 프로퍼티(computed property)를 지원한다.

```javascript
class User {

  constructor(name) {
    // setter를 활성화합니다.
    this.name = name;
  }

  get name() {
    return this._name;
  }

  set name(value) {
    if (value.length < 4) {
      console.log("이름이 너무 짧습니다.");
      return;
    }
    this._name = value;
  }

}

let user = new User("상혁");
console.log(user.name); // 상혁

user = new User(""); // 이름이 너무 짧습니다.
```

# 클래스 필드
'클래스 필드(class field)'라는 문법을 사용하면 어떤 종류의 프로퍼티도 클래스에 추가할 수 있다.

```javascript
class User {
  name = "상혁";

  sayHi() {
    console.log(`${this.name}님 안녕하세요!`);
  }
}

new User().sayHi(); // 상혁님 안녕하세요!
```

클래스를 정의할 때 '<프로퍼티 이름> = <값>'을 써주면 간단히 클래스 필드를 만들 수 있다.

클래스 필드의 중요한 특징 중 하나는 User.prototype이 아닌 개별 객체에만 클래스 필드가 설정된다는 점이다.

```javascript
class User {
  name = "상혁";
}

let user = new User();
console.log(user.name); // 상혁
console.log(User.prototype.name); // undefined
```

아울러 클래스 필드엔 복잡한 표현식이나 함수 호출 결과를 사용할 수 있다.

```javascript
class User {
  name = prompt("이름을 알려주세요.", "상혁");
}

let user = new User();
console.log(user.name); // 상혁
```

### 클래스 필드로 바인딩 된 메서드 만들기
자바스크립트에서 `this`는 동적으로 결정된다.
따라서 객체 메서드를 여기저기 전달해 전혀 다른 컨텍스트에서 호출하게 되면 this는 메서드가 정의된 객체를 참조하지 않는다.

```javascript
class Button {
  constructor(value) {
    this.value = value;
  }

  click() {
    console.log(this.value);
  }
}

let button = new Button("안녕하세요.");

setTimeout(button.click, 1000); // undefined
```

이렇게 `this`의 컨텍스트를 알 수 없게 되는 문제를 '잃어버린 `this`(losing this)'라고 한다.
1. `setTimeout(() => button.click(), 1000)` 같이 래퍼 함수를 전달하기
2. 생성자 안 등에서 메서드를 객체에 바인딩하기

이 두 방법 말고 클래스 필드를 사용해도 문제를 해결할 수 있다.
```javascript
class Button {
  constructor(value) {
    this.value = value;
  }

  click = () => {
    console.log(this.value);
  }
}

let button = new Button("안녕하세요.");

setTimeout(button.click, 1000); // undefined
```

클래스 필드 `click = () => {...}`는 각 `Button` 객체마다 독립적인 함수를 만들어주고 이 함수의 `this`를 해당 객체에 바인딩시켜준다. 
따라서 개발자는 `button.click`을 아무 곳에나 전달할 수 있고, `this`엔 항상 의도한 값이 들어가게 된다.

클래스 필드의 이런 기능은 브라우저 환경에서 메서드를 이벤트 리스너로 설정해야 할 때 특히 유용하다.