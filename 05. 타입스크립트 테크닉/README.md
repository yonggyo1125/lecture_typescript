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
    console.log(user.info.name)
}
```

## keyof 연산자

- 타입에 대해 keyof 연산자를 사용하면 해당 타입이 가진 각 속성의 타입이 Union 타입을 반환합니다. 다음 코드와 같이 keyof의 결과는 리터럴 타입의 Union 타입으로 취급되므로, 객체에 존재하는 키를 사용해 무언가의 함수 처리를 수행하고자 할 때 안전하게 구현할 수 있습니다.

```javascript
interface User {
    name: string;
    age: number;
    email: string;
}

typeof UserKey = keyof User // 'name' | 'age' | 'email' 이라는 Union 타입이 된다.

const key1: UserKey = 'name' // 대입 가능하다.
const key2: UserKey = 'phone' // 컴파일 시 에러가 발생한다.

// 첫 번째 인수에 전달한 객체 타입의 속성명의 Union 타입과, 두 번째 인수로 전달한 값의 타입이 일치하지 않으면 타입 에러가 된다.
// T[k]에 따라 키에 대응하는 타입이 반환값의 타입이 된다(예를 들어, 위 User의 age를 key로 전달하면, 반환값 쪽은 number가 된다).
function getProperty<T, K extends keyof T>(obj: T, key: K): T[k] {
    return obj[key]
}

const user: User = {
    name: 'Hana',
    age: 36,
    email: 'test@example.com'
}

// name은 타입의 키로 존재하기 때문에 올바르게 string 타입의 값을 반환한다.
const userName = getProperty(user, 'name')

// gender는 객체의 키로 존재하지 않으므로, 컴파일 시 에러가 된다.
const userGender = getProperty(user, 'gender')
```

### 인덱스 타입

- 인덱스 타입(Index signature)을 사용하면 객체의 속성이 변할 때, 모아서 타입을 정의할 수 있습니다. 각 속성에 대응하는 타입을 정의할 수 없을 때 간단하게 기술할 수 있습니다.

```javascript
// 속성명은 임의의 number로서 다루는 타입을 정의한 예이다.
type SupportVersions = {
  [env: number]: boolean,
};

// string의 속성에 정의한 경우 에러가 발생한다.
let versions: SupportVersions = {
  102: false,
  103: false,
  104: true,
  v105: true, // -> error가 된다.
};
```

## readonly

- 타입스크립트에서는 타입 앨리어스, 인터페이스, 클래스에 대해 readonly 속성을 지정할 수 있습니다. readonly가 지정된 속성은 변경할 수 없습니다.

```javascript
type User = {
    readonly name: string;
    readonly gender: string;
}

let user: User = { name: 'Hana', gender: 'Male' }

// 아래 대입을 수행했을 때 컴파일 시 에러가 발생한다.
user.gender = 'Female'
```

- 자바스크립트의 재대입 불가 기능으로 const 기능이 있으나, 둘의 용도는 다릅니다. const는 변수의 대입에 대해 수행하는 선언, readonly는 객체나 클래스의 속성에 대해 수행하는 선언으로 컴파일 시 에러를 감지할 수 있습니다.
- 또한, Readonly 타입이라는 제네릭 타입도 있습니다. 다음 코드와 같이 Readonly 타입에 타입 앨리어스를 지정하면, 모든 속성이 변경 불가능한 타입이 작성됩니다.

```javascript
type User = {
  name: string,
  gender: string,
};

type UserReadonly = Readonly<User>;

let user: User = { name: "Hana", gender: "Male" };
let userReadonly: UserReadonly = { name: "Hana", gender: "Male" };

user.name = "Jinho"; // OK
userReadonly.name = "Jinho"; // 컴파일 시 에러가 발생한다
```

## unknown

- unknown은 any와 마찬가지로 모든 값을 대입할 수 있는 타입입니다. 하지만 any와 달리 대입된 값 상태 그대로는 임의의 함수나 속성으로 접근할 수 없습니다. typeof나 instanceof 등을 사용해 타입 안전한 상태를 만든 뒤, 변숫값에 접근하는 함수 등의 처리를 실행할 수 있습니다.

```javascript
// any와 마찬가지로 어떤 값에도 unknown으로 대입할 수 있다
const x: unknown = 123;
const y: unknown = "Hello";

// 함수나 속성에 접근했을 때, unknown 타입 그대로는 컴파일 시 에러가 발생한다.
// error TS2339: Property 'toFixed' does not exist on type 'unknown'.
console.log(x.toFixed(1));
// error TS2339: Property 'toLowerCase' does not exists on type 'unknown'.
console.log(y.toLowerCase());

// 타입 안정한 상황에서 함수나 속성에 접근해서 실행할 수 있다
if (typeof x === "number") {
  console.log(x.toFixed()); // 123.0
}

if (typeof y == "string") {
  console.log(y.toLowerCase()); // hello
}
```

- 이렇게 unknown은 임의의 타입을 대입할 수 있는 any와 같은 특성을 가지면서 보다 타입이 불명확한 값을 나타내는 기능을 강조한 것입니다. 변수를 사용할 때는 타입을 지정함으로써 any로는 할 수 없었던 컴파일시 에러를 사전에 감지할 수 있습니다. 결과적으로 any를 사용하는 것보다 안전한 코드를 작성할 수 있습니다.

## 비동기 Async/Await

- 비동기 처리 API인 Promise의 간략한 구문에 해당하는 것이 Async/Await의 기능입니다. 이 기능은 타입스크립트라기보다는 ECMAScript 사양 범위에 해당합니다. 간단한 샘플 코드를 소개합니다. 타입스크립트에서 async/await를 사용할 때 참고하기 바랍니다.

```javascript
// 비동기 함수를 정의합니다.
function fetchFromServer(id: string): Promise<{ success: boolean }> {
  return new Promise((resolve) => {
    setTimeout(() => {
      resolve({ success: true });
    }, 100);
  });
}

// 비동기 처리를 포함하는 async function의 반환값의 타입은 Promise이다.
async function asyncFunc(): Promise<string> {
  // Promise한 값을 await하면 내용을 추출할 수 있다(그렇게 보인다)
  const result = await fetchFromServer("111");
  return `The result: ${result.success}`;
}

// await 구문을 사용하기 위해서는 function 안에서 호출해야 한다.
(async () => {
  const result = await asyncFunc();
  console.log(result);
})();

// Promise로서 다룰 때는 다음과 같이 기술합니다.
asyncFunc().then((result) => console.log(result));
```

## 타입 정의 파일

- TypeScript로 웹 애플리케이션을 개발하는 도중 자바스크립트로 작성된 외부 라이브러리를 로딩하고 싶을 때가 많을 것입니다. 타입스크립트에서는 자바스크립트 라이브러리를 로딩해서 실행할 수 있지만, 타입정의 정보가 없을 때는 타입 안전한 코드를 작성할 수 없습니다.
- 그래서 타입스크립트에는 자바스크립트 모듈에 대해, 타입 정보를 부여할 수 있는 타입 정의 파일이라는 구조를 제공합니다.
- 이를 도입하는 방법은 크게 2가지입니다.
  - @types로 대표되는 공개된 타입 정의 파일을 도입한다.
  - 타입 정의 파일을 직접 작성한다.

### 타입 정의 파일 도입

- 첫 번째는 <b>@types/\[라이브러리명\]</b>으로 공개된 타입 정의 파일을 설치하는 방법입니다. 현재 타입스크립트 보급과 함께, 여러 유명한 자바스크립트 라이브러리들이 npm 저장소에 타입 정의 정보를 공개하고 있습니다. 예를 들어, 제이쿼리를 사용할 때 다음과 같이 npm에서 <b>@types/jquery</b>를 로딩해서 타입 정보를 부여한 제이쿼리를 다룰 수 있습니다.

```javascript
$ npm install --save-dev @types/jquery
```

- 또한, 위처럼 설치하지 않더라도 라이브러리에 포함된 경우도 있습니다. 그때는 별도로 npm으로 설치하지 않아도 됩니다.

### 타입 정의 파일 작성

- 의존하는 자바스크립트의 라이브러리가 타입 정의 파일을 포하하고 있지 않거나, 공개돼 있지 않을 때가 있습니다.
- 그때는 직접 .d.ts라는 확장자를 가진 타입 정의 파일을 설치하고, 로딩해서 사용할 수 있습니다. 다음 코드는 프로젝트에 자바스크립트 코드가 있을 때, .d.ts를 정의해 해당 코드를 타입스크립트에서 사용하는 예시입니다.
- ./lib/hello.js라는 자바스크립트의 유틸리티 함수가 존재한다고 가정했습니다.

```javascript
export.hello = function(name) {
    console.log(`Hello, ${name}`)
}
```

- ./lib/hello.d.ts라는 타입 정의 파일을 작성합니다.

```javascript
export function hello(name: string): void
```

- 위와 같이 정의 파일을 설치하면 자바스크립트 유틸리티 함수가 타입 정보를 가진 타입스크립트 코드로 작동합니다.

```javascript
import { hello } from "./lib/hello";

// 에러: name 인수를 전달하지 않으므로, 컴파일러 에러가 발생한다
hello();
```
