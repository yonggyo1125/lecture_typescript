# 실제 개발 시 중요한 타입

## Enum 타입

- Enum을 사용하면 이름이 붙은 상수 셋을 정의할 수 있습니다. Enum은 자바스크립트에는 없는 기능으로 타입스크립트에서 확장한 기능 중 하나입니다. 열거형이라고도 불립니다.
- 자바스크립트에서는 다음과 같은 정의를 볼 수 있습니다.

```javascript
const Direction = {
  Up: 0,
  Down: 1,
  Left: 2,
  Right: 3,
};
```

- 이 코드를 타입스크립트에선 다음과 같이 enum을 사용해 상수를 정의할 수 있습니다. Enum을 사용하면 열거한 값 이외에는 대입할 수 없는 타입을 정의할 수 있습니다. 예시와 같이 상하좌우 방향 등 , 특정한 값만 받고 싶을 때 사용합니다.

```javascript
enum Direction {
    Up,
    Down,
    Left,
    Right
}

// enum Direction을 참조
let direction: Direction = Direction.Left
// 2라는 숫자가 출력된다.
console.log(direction)

// enum을 대입한 변수에 다른 타입의 값을 대입하려고 하면 에러가 된다.
direction = 'Left' // string을 넣으러면 에러
```

- 콘솔에 출력된 Direction.Left의 값이 2가 됩니다. 특별히 지정하지 않는 경우 Enum은 정의된 순서대로 0부터 숫자가 자동으로 증가되면서 설정됩니다(수치 기반 Enum 타입).
- 타입스크립트에서는 수치 기반 이외에 문자열 기반의 Enum 타입을 사용할 수 있습니다. 문자열 기반 Enum 타입을 사용할 때는 각 멤버를 특정한 문자열의 상수로 초기화해야 합니다.

```javascript
enum Direction {
    Up = 'UP',
    Down = 'DOWN',
    Left = 'LEFT',
    Right = 'RIGHT'
}

// 예를 들어, API의 파라미터로 문자열이 전달된 경우를 가정한다.
const value = 'DOWN'
// 문자열에서 Enum 타입으로 변환한다.
const enumValue = value as Direction

if (enumValue === Direction.Down) {
    console.log('Down is selected')
}
```

- 문자열 기반(문자열 열거형)의 경우, 상수값은 자동 증가하지 않지만 문자열로 전달된 값과 Enum의 정수값을 비교할 때 편리합니다.
- Enum과 비슷한 기능으로 Union 타입이 있습니다. Union 타입으로도 거의 비슷한 기능을 구현할 수 있어, Union 타입을 선호하는 개발자도 있습니다.

## 제네릭 타입

- 제네릭(Generic)은 클래스와 함수에 대해, 그 안에서 사용하는 타입을 추상화해 외부로부터 구체적인 타입을 지정할 수 있는 기능입니다. 바깥쪽에서 지정된 타입이 달라도 작동하도록 할 수 있는 범용적인 클래스나 함수를 정의할 때 편리합니다.
- 다음 코드는 임의의 타입의 배열과 호출 시 배열을 순서대로 꺼내는 함수를 가진 클래스의 예시입니다. 제네릭을 사용한 코드 예시를 소개합니다.

```javascript
// T는 클래스 안에서 사용하는 임시 타입 이름이다.
class Queue<T> {
    // 내부에서 T 타입의 배열을 초기화한다
    private array: T[] = []

    // T 타입의 값을 배열에 추가한다
    push(item: T) {
        this.array.push(item)
    }

    // T 타입의 배열의 첫 번째 값을 꺼낸다
    pop(): T | undefined {
        return this.array.shift()
    }
}

const queue = new Queue<number>() // 숫자 타입을 다루는 큐를 생성한다
queue.push(111)
queue.push(112)
queue.push('foo') // number 타입이 아니므로 컴파일 시 에러가 된다.

let str = 'bar'
str = queue.pop() // str은 number 타입이 아니므로 컴파일 시 에러가 된다.
```

- 예시와 같이 제네릭 타입은 바깥쪽에서 지정해 작동하는 클래스를 기술할 때 편리합니다.

## Union 타입과 Intersection 타입

- 타입스크립트의 타입은 조합해서 사용할 수 있습니다. 다소 복잡한 타입을 표현하고 싶을 때, 지정한 여러 타입의 합집합을 의미하는 Union 타입의 교집합을 의미하는 Intersection 타입이 있습니다. 각각 | 와 &를 사용해 타입을 정의할 수 있습니다.
- Union 타입은 지정할 몇 가지 타입에 해당하면 좋은 타입을 생성합니다.

```javascript
// 변수나 인수 선언 시 Union 타입을 지정해, number 또는 string을 받을 수 있다
function printId(id: number | string) {
  console.log(id);
}

// number라도 정상 작동한다
printId(11);

// string이라도 정상 작동한다
printId("22");
```

- 타입 앨리어스로도 정의할 수 있습니다.

```javascript
type Id = number | string;

function printId(id: Id) {
  console.log(id);
}
```

- 또한, 타입 앨리어스 사이에 새로운 타입을 정의할 수 있습니다.

```javascript
type Identity = {
  id: number | string,
  name: string,
};

type Contact = {
  name: string,
  email: string,
  phone: string,
};

// 합집합을 토한 새로운 Union 타입을 정의한다.
// Identity 또는 Contact 타입을 받을 수 있다
type IdentityOrContact = Identity | Contact;

// OK
const id: IdentityOrContact = {
  id: "111",
  name: "Hana",
};

// OK
const contact: IdentityOrContact = {
  name: "Hana",
  email: "test@example.com",
  phone: "012345678",
};
```

- 한편, Intersection 타입은 여러 타입을 병합해서 하나로 만든 타입(모든 타입 정의의 내용을 합친 타입)을 생성합니다. 다음 코드는 2개의 타입을 조합한 예시입니다. 3개 이상의 타입도 조합할 수 있습니다.

```javascript
// 앞에서 설명한 Identity & Contact

// OK
const employee: Employee = {
  id: "111",
  name: "Hana",
  email: "test@example.com",
  phone: "012345678",
};

// 에러: Contact 정보만으로 변수를 정의할 수 없다. id가 필요하다.
const employeeContact: Employee = {
    name: "hana',
    email: 'test@example.com',
    phone: '012345678'
}
```

## 리터럴 타입

- |로 데이터를 구분하는 리터럴 타입을 사용하면 정해진 문자열이나 수치만 대입할 수 있는 타입으로 제어할 수 있습니다.

```javascript
변수: 허용하는_데이터_1 | 허용하는_데이터_2 | ...
```

- 예를 들어, 데이터의 상태를 나타내는 값 등에 사용할 수 있습니다. 이것도 타입스크립트로 제어할 수 있습니다.

```javascript
let postStatus: "draft" | "published" | "deleted";
postStatus = "draft"; // OK
postStatus = "drafts"; // 타입 선언에 없는 문자열이 할당돼 있으므로 컴파일 시 에러.
```

- 숫자에도 리터럴 타입을 쓸 수 있습니다. 다음 코드는 함수의 반환값을 타입 정보로 정의할 때 수치 리터럴 타입을 사용한 예시입니다.

```javascript
// -1, 0, 1 중 하나만 반환하는 타입 정보를 정의한다.
function compare(a: string, b: string): -1 | 0 | 1 {
  return a === b ? 0 : a > b ? 1 : -1;
}
```

### never 타입

- never 타입은 절대로 발생되지 않는 값의 종류를 나타냅니다. 예를 들어 항상 예외를 발생시키는 함수 등에서 절대로 값이 반환되지 않는 반환값 타입을 never로 정의할 수 있습니다.

```javascript
// 에러가 항상 반환되는 함수로 절대로 값이 정상으로 반환되지 않을 때 never 타입을 지정한다.
function error(message: string): never {
  throw new Error(message);
}

function foo(x: string | number | number[]): boolean {
  if (typeof x === "string") {
    return true;
  } else if (typeof x === "number") {
    return false;
  }

  // never를 사용해서 명시적으로 값이 반환되지 않은 것을 컴파일러에 전달할 수 있다.
  // never를 사용하지 않으면 타입스크립트는 컴파일 에러를 일으킨다.
  return error("Never happens");
}
```

- never와 같이 유효한 사용방법으로 if문이나 switch 문에서 타입스크립트의 타입의 조건 분기에 누락이 없는 것을 보증하는 경우가 있습니다. 다음 코드는 Enum으로 정의한 각 페이지의 타입과 해당 타입에 대응하는 제목을 반환하는 함수를 구현한 예시입니다. 함수 안의 switch 문으로 각각 Enum 타입의 확인을 수행한 후 명시적으로 never 타입을 사용함으로써, 미래 PageType이 새롭게 추가됐을 때 switch 문 구현에 누락이 있으면 컴파일 에러가 발생하게 할 수 있습니다.

```javascript
// 미래에 상수가 추가될 가능성이 있는 enum 타입을 정의한다.
enum PageType {
    ViewProfile,
    EditProfile,
    ChangePassword,
}

const getTitleTest = (type: PageType) => {
    switch (type) {
        case PageType.ViewProfile:
            return 'Setting'
        case PageType.EditProfile:
            return 'Edit Profile'
        case PageType.ChangePassword:
            return 'Change Password'
        default:
            // 결코 일어나지 않을 일을 컴파일러에 전달하는 never 타입에 대입한다.
            // 이로 인해 만약 미래 PageType의 enum 타입에 상수가 새롭게 추가됐을 때
            // 컴파일 시에 에러가 발생하기 때문에 버그를 미연에 방지해서 대응할 수 있다.
            const wrongType: never = type
            throw new Error(`${wrongType} is not in PageType`)
    }
}
```
