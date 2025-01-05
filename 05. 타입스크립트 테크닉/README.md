# 타입스크립트 테크닉

> 타입스크립트를 사용할 때 알아 두어야 할 편리한 고급 기능을 소개합니다.

## 옵셔널 체이닝

- 옵셔널 체이닝(Optional Chaining)을 사용하면 중첩된 객체의 속성이 존재하는가에 관한 조건 분기를 간단하게 기술할 수 있습니다. 이제까지는 안전한 코드를 작성하려면 null 또는 undefined를 체크하는 if 조건 분기를 작성하거나, obj.prop1 && obj.prop1.prop2 같이 체크해야 했습니다.
- 다음 코드처럼 속성 접근 시 옵셔널 체이닝 기능의 ?를 사용하면 null 또는 undefined가 될 수 있는 객체에 대해 안전하게 처리를 기술할 수 있습니다.

```javascript
// null이 될 수 있는 social이라는 속성의 타입을 정의한다.
interface User {
    name: string
    social?: {
        facebook: boolean
        twitter: boolean
    }
}

let user: User

user = { name: 'Hana', social: { facebook: true, twitter: true }}

// true가 출력된다.
console.log(user.social?.facebook)

user = { name: 'Hana' }

// social이 존재하지 않는 경우에도 다음 코드는 실행 시 에러가 되지 않는다.
console.log(user.social?.facebook)
```

## 논-널 어서션 연산자

- 컴파일 옵션 --strictNullChecks를 지정해 컴파일하면, 타입스크립트는 일반적으로 null일 가능성이 있는 객체에 대한 접근을 에러로 취급합니다. null이 아님을 나타내고 싶을 때는 논-널 어서션(Non-Null Assertions)이라는 기능을 사용해 명시적으로 컴파일러에게 문제가 없음을 전달할 수 있습니다. 논-널로 나타낼 변수 등에 !를 붙입니다.

```javascript
// user가 null이면, 실행 시 에러가 될 가능성이 있는 속성에 접근하면 컴파일 에러
// !를 사용해 명시적으로 지정함으로써 컴파일 에러를 억제
function processUser(user?: User) {
    let s = user!.name
}
```

- ?를 사용하는 옵셔널 체이닝과 다소 비슷하지만, 논-널 어셔션은 어디까지나 에러를 발생시키지 않아도 된다고 타입스크립트 컴파일러에 알려줄 뿐이고, 실행 시에 에러가 발생할 가능성은 있습니다. 한편, 옵셔널 체이닝은 트랜스파일되어 생성된 자바스크립트에 null 체크 코드를 추가하므로 실행 시 에러가 발생하지 않습니다.

## 타입 가드

- 타입스크립트에서 if 문이나 switch 문의 조건 분기에서 타입 체크를 수행할 때, 해당 조건 분기 블록 이후는 변수의 타입이 필터링되는 추론을 수행합니다. 이것이 타입 가드(type guard)입니다.
- number와 string의 Union 타입으로 정의된 인수를 typeof를 사용해 string 타입의 판정을 하는 if문을 작성했다고 가정합니다. if 블록 이후의 인수인 변수는 자동으로 number 타입으로 취급됩니다.

```javascript
function addOne(value: number | string) {
  if (typeof value === "string") {
    return Number(value) + 1;
  }
  return value + 1;
}

console.log(addOne(10)); // 11
console.log(addOne("20")); // 21
```

- 타입 가드 기능을 사용하면 실행 시 에러를 발생시키기 쉬운 as를 사용하는 타입 어서션보다 안전하게 타입을 사용한 코드를 작성할 수 있습니다.
- 옵셔널 속성으로 정의된 값을 if 문으로 필터링할 때도 마찬가지로 타입 가드 기능을 활용해 if 문 안에서는 null 안전한 속성으로 다룰 수 있습니다. 다음 코드는 뒤에서 설명할 --strictNullChecks라는 컴파일 옵션을 활성화한 경우의 예시입니다.

```javascript
// 옵셔널 속성으로 info를 정의한다.
type User = {
    info?: {
        name: string;
        age: number;
    }
}

let response = {}
// response는 JSON 형식의 API 응답이 대입딘다고 가정한다. User에 타입 어서션을 한다.
const user = (response as any) as User

// 옵셔널 속성에 대한 타입 가드를 수행한다.
if (user.info) {
    // 옵셔널 속성 하위 속성인 user.info.name에 접근해도 에러가 발생하지 않는다.
    // 만약 if의 조건이 없을 때는 Object is possible 'undefined'.라는 에러가 발생한다.
}
```
