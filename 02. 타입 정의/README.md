# 타입 정의

- 여기에서는 타입스크립트의 가장 큰 기능이라고 할 수 있는 타입 기능에 관해 설명합니다. 타입스크립트는 자바스크립트 문법을 기본으로 하지만, 타입이나 클래스 등 일부 코드 작성법이 다릅니다.
- 타입스크립트에서는 변수나 인수명 뒤에 **: 타입**과 같이 타입 애너테이션(`Type Annotation`)이라 불리는 타입 정보를 붙여서 변수나 인수에 저장할 값을 제한할 수 있습니다, 그리고 타입 애너테이션은 변수 데이터 타입이 명확한 경우 등 일부 조건에서는 생략할 수도 있습니다.

```javascript
// firstName 뒤에 string 타입을 붙여, 문자열 이외의 값을 전달하지 못하게 할 수 있다.
function sayHello(firstName: string) {
  console.log("Hello " + firstName);
}

let firstName: string = "Hana";
sayHello(firstName);
```

- 타입 애너테이션에 반환할 값을 대입하면 타입스크립트 컴파일 시 에러가 발생합니다. 예를 들어 `string` 타입을 받는 것으로 정의한 코드에 `number` 타입의 변수를 대입하면 컴파일 시 다음과 같은 에러가 발생합니다.

```javascript
let age: number = 36;
sayHello(age);
```

- 또 다른 타입 정의의 예를 소개합니다. 문자열을 정의하고, 그 변수에 대해 함수로 호출하면 에러가 발생합니다. 타입스크립트는 정적 타입으로 컴파일을 전제로 하는 언어이므로, 컴파일 에러를 통해 문제점을 파악할 수 있습니다.

```javascript
const message = "hello!";

// 자바스크립트에는 실행 시에 에러가 발생하는 것에 비해, 타입스크립트에서는 컴파일 시에 다음과 같은 에러가 발생한다.
// error TS2349: This expression is not callable. Type 'String' has no call signatures.
message();
```

- 여기까지 소개한 예시들은 매우 간단한 것들입니다. 이것만으로는 타입스크립트의 장점을 느끼기 어려울 것입니다.
- UI의 폼이나 API를 경유해 받은 값을 확실하게 검사하지 않아 예상치 못한 에러가 발생하는 일은 실제 개발 환경에서도 일어납니다. 이런 문제는 타입을 사용해 종류를 제한/판별합으로써 대처할 수 있습니다. 타입스크립트에서는 문자열이나 숫자 타입뿐만 아니라 객체, 클래스, 함수 등에도 타입 정의 정보를 부여할 수 있습니다.
- 타입스크립트는 자바스크립트와 달리 타입에 묶인 변수의 전달을 수행하는 정적 타입 체크 기능을 통해 안전하게 구현을 할 수 있습니다.

### 변수

- 타입스크립트는 타입 정의 이외에는 자바크립트와 동일한 규칙으로 변수를 선언합니다.
- 변수 선언에는 `var`, `let`, `const`를 사용합니다. 변수명 뒤에 **: 타입**을 추가해 타입 애너테이션을 합니다(생략 가능). 타입스크립트의 변수는 자바스크립트와 마찬가지로 `var` 키워드를 사용해서 선언합니다. 스코프 규칙이나 대입 시 작동은 자바스크립트의 그것과 같습니다. ES6에서는 자바스크립트에 `let`과 `const`라는 키워드를 사용하는 두 가지 새로운 변수 선언이 도입됐습니다. 자바스크립트 수퍼셋인 타입스크립트 역시 이 새로운 타입의 변수 선언을 지원합니다.

```javascript
var 변수: 타입 = 값;
let 변수: 타입 = 값;
const 변수: 타입 = 값;
let employeeName = "John";
// 또는
let employeeName: string = "John";
```

- `let`을 사용해 변수를 선언할 경우, var로 선언된 변수의 스코프가 해당 변수를 포함하는 함수에서까지 사용할 수 있는 반면, 블록 스코프로 선언된 변수는 해당 변수를 포함하는 블록 안에서만 사용할 수 있습니다.

```javascript
function calc(isSum: boolean) {
  let a = 100;
  if (isSum) {
    // a가 정의된 안쪽의 블록 스코프 안의 사용이므로 에러가 발생하지 않는다.
    let b = a + 1;
    return b;
  }

  // error TS2304: Cannot find name 'b'.
  // var로 정의한 경우는 에러가 발생하지 않지만, let으로 정의했을 때는 에러가 발생한다.
  return b;
}
```

- const는 상수를 나타내는 이름 그대로, 값을 변경할 수 없는 상수를 선언합니다. 한번 값이 대입되면 다시 대입할 수 없습니다.
- const 변수는 let 변수와 같은 스코프 규칙을 갖습니다.

```javascript
const num: number = 100;

// 값을 재대입하면 컴파일러 에러가 된다.
num = 200;
```

- 현재의 웹 프런트앤드 개발에서는 주로 let, const를 사용합니다.

### 원시 타입

- 자바스크립트에서 자주 사용되는 원시(primitive) 타입인 string(문자열), number(수치), boolean(논리값)은 타입스크립트에 대응하는 타입이 있습니다. 이 타입들은 자바스크립트의 typeof 연산자를 사용할 때 표시되는 이름과 같습니다.

```javascript
let age: number = 36;
let isDone: boolean = false;
let color: string = "파랑";
```

- 다른 타입의 값을 대입하고자 할 때는 에러가 발생합니다. 위와 같이 타입을 붙여 대입한 변수는 이후 정적 타입의 대상이 됩니다.

```javascript
let mynumber: string = "200";
mynumber = "이백"; // string 타입이므로 문제없이 대입할 수 있다.
mynumber = 200; // string 타입 변수에 number 타입을 대입하려 하면 컴파일러 에러가 발생한다. Type 'number' is not assignable to type 'string'.
```

### 배열

- 배열에 타입을 지정할 때는 그 배열을 구성하는 타입과 \[\] 표기를 사용합니다. 예를 들어, number의 배열이라면 number\[\]라는 구문을 사용합니다.

```javascript
const array: string[] = [];
array.push("abc");
array.push(1); // 배열 타입과 맞지 않으므로 에러가 된다.
```

- 이 구문은 string\[\]등 원시 타입 외에도 뒤에서 설명할 인터페이스나 타입 앨리어스(type alias) 등에도 대응합니다. User[] 등과 같이 표기할 수도 있습니다.
- 배열은 \[\]를 사용하는 방법 이외에 Array\<string\>과 같은 제네릭으로 표기할 수도 있습니다.
- \['foo', 1\]과 같이 여러 타입이 있는 배열인 경우에는 Union 타입이나 튜플(Tuple)을 사용해 다음과 같이 표기할 수 있습니다.

```javascript
const mixedArray = ["foo", 1];
const mixedArrayU: (string | number)[] = ["foo", 1]; // Union 타입
const mixedArrayT: [string, number] = ["foo", 1]; // 튜플
```

### 객체 타입

- 객체(object)는 키(key)와 값(value)을 이용한 데이터 형식 인스턴스입니다. 타입스크립트는 다음과 같이 키 이름과 값의 쌍을 지정해 객체 타입을 정의할 수 있습니다.

```javascript
{ 키_이름_1: 값1; 키_이름_2: 값_2; ...}
let 변수: { 키명_1: 타입_1; 키명_2: 타입_2; ... } = 객체
const 변수: { 키명_1: 타입_1; 키명_2: 타입_2; ... } = 객체
var 변수: { 키명_1: 타입_1; 키명_2: 타입_2; ... } = 객체
``` 

- 다음은 객체 타입 정의 예시입니다.

```javascript
// string 타입의 name과 number 타입의 age를 가진 객체 타입을 정의한다
const user: { name: string; age: number } = {
  name: 'Hana',
  age: 36
}

console.log(user.name)
console.log(user.age)
```

- 객체 타입은 일부 또는 모든 속성을 ? 를 사용해 옵셔널(optional, 선택 가능) 속성으로 지정할 수 있습니다. 옵셔널 속성으로 타입을 정의하면 해당 속성이 존재하지 않아도 문제없이 작동합니다. 

```javascript
function printName(obj: { firstName: string; lastName?: string}) {
  // ...
}

// 다음 패턴은 모두 정상 작동한다
printName({ firstName: 'Hana' })
printName({ firstName: 'Hana', lastName: 'Kim' })
```

- 객체 타입은 코드가 길어지기 때문에 타입 앨리어스와 함께 사용하는 경우가 많습니다. 

### any
- 타입스크립트에서는 `any`라 불리는 특별한 타입이 있습니다. 이름 그대로 모든 타입을 허용하는 타입입니다. 특정한 값에 대해 타입 체크 구조를 적용하고 싶지 않을 때 사용합니다.

```javascript
let user: any = { firstName: 'Hana' }
// 다음 행의 코드는 모두 컴파일러 에러가 발생하지 않는다.
user.hello()
user()
user.age = 100
user = 'hello'

// 다른 타입으로 대입해도 에러가 발생하지 않는다. 
const n: number = user
```

- `any`를 사용하면 타입 체크 기능이 작동하지 않게 됩니다. 따라서 타입스크립트를 사용하는 장점을 활용할 수 없습니다. 자바스크립트 프로젝트를 타입스크립트로 마이그레이션하는 과정이 아닌 한, 기본적으로는 `any`를 사용하지 않는 것이 바람직합니다.

### 함수

- 타입스크립트의 함수에서는 인수와 반환값의 타입을 지정할 수 있습니다.

```javascript
function(인수_1: 타입_1, 인수_2: 타입_2 ...): 반환값 {
  // ...
}
```

- 다음 코드는 함수의 인수와 반환값에 string을 정의한 예시입니다.

```javascript
function sayHello(name: string): string {
  return `Hello ${name}`
}

sayHello('Hana')
```

- 옵셔널 인수도 정의할 수 있습니다. 옵셔널 인수는 인수명 뒤에 ?를 붙입니다.

```javascript
function sayHello(name: string, greeting?: string): string {
  return `${greeting} ${name}`
}

// 다음은 모두 문제없이 작동한다
sayHello('Hana')
sayHello('Hana', 'Hello') // Hello Hana
```

- 인수를 정의할 때 기본값을 지정할 수 있습니다. 함수를 호출할 때 해당 인수를 지정하지 않으면 기본값이 설정됩니다.

```javascript
function sayHello(name: string, greeting: string = 'Hello'): string {
  return `${greeting} ${name}`
}

// 다음은 모두 문제없이 작동한다
sayHello('Hana') // Hello Hana
sayHello('Hana', 'Hey') // Hey Hana
```

- 인수나 반환값의 타입에 다양한 타입을 지정할 수 있습니다. 다음은 함수를 인수로 받는 함수의 타입 지정 예시입니다.

```javascript
// 이름과 포맷 함수를 인수로 받아 포매팅한 뒤 콘솔에 출력하는 함수를 정의한다. 
function printName(firstName: string, formatter: (name: string) => string) {
  console.log(formatter(firstName))
}

// '씨'를 뒤에 붙이는 이름 포맷 함수를 정의한다
function formatName(name: string): string {
  return `${name} 씨`
}

printName('홍길동', formatName) // 홍길동 씨 
```

- 화살표 함수(arrow function)의 경우는 다음과 같이 타입을 지정합니다.

```javascript
(인수명: 인수_타입): 반환값_타입 => 자바스크립트_식
let sayHello = (name: string): string => `Hello ${name}`
```

#### 함수 타입
- 지금까지 함수의 인수나 반환값에 타입을 붙이는 내용에 관해 주로 설명했습니다. 타입스크립트(자바스크립트)는 인수에도 함수를 사용할 수 있습니다. 함수 그 자체의 타입을 기입하는 방법을 소개합니다.
- 다음과 같은 표기법으로 함수 타입을 나타냅니다. **인수명**은 실제 함수의 인수명과 대응할 필요는 없습니다.

```javascript
(인수명: 인수_타입) => 반환값_타입
```

- 다음 코드는 예시입니다. **singBirds**는 인수가 문자열이고 반환값이 배열(문자열 배열)인 함수를 인수로 받습니다.

```javascript
function genBirdsInfo(name: string): string[] {
  return name.split(',')
}

// 함수 타입을 사용
// (x: string) => string[]
function singBirds(birdInfo: (x: string) => string[]): string {
  return birdInfo('오리,비둘기')[0] + ' 꽥꽥'
}

console.log(singBirds(genBirdsInfo)) // "오리 꽥꽥"
console.log(singBirds('참새'))  // 타입이 맞지 않으므로 에러
```

