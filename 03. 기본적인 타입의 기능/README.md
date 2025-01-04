# 기본적인 타입의 기능

- 타입스크립트의 타입 기능 중, 기본적인 것을 소개합니다.

## 타입 추론

- 타입스크립트에서는 명시적인 변수의 초기화를 수행하면 타입 추론을 통해 자동적으로 타입이 결정되는 기능이 있습니다.

```javascript
const age = 10
console.log(age.length) // 에러: age는 number 타입이므로 length 속성은 없다.

const user = {
  name: 'Hana',
  age: 36,
}
console.log(user.age.length) // 에러: age는 number 타입이므로 length 속성은 없다
```

- 함수의 반환값도 마찬가지 입니다.

```javascript
function getUser() {
  return {
    name: 'Hana',
    age: 36,
  }
}

const user = getUser()
console.log(user.age.length) // 에러: age는 number 타입이므로 length 속성은 없다
```

- 배열의 값도 추론되므로, 명시적으로 string 이라고 설정하지 않더라도 다음 for 루프에서는 string 타입으로 다루어집니다.

```javascript
const names = ['Hana', 'WonJoon', 'Eunjeong']

names.forEach((name) => {
  // string 타입으로 다뤄지므로, 함수명이 잘못된 호출은 컴파일 에러가 된다.
  // toUpperCase가 올바른 함수명이다
  console.log(name.toUpperCase())
})
```

- 그리고 타입스크립트 타입 추론은 대입할 대상 변수값의 타입이 결정되어 있을 때, 대입할 값과 타입이 일치하지 않는 경우 에러가 발생하는 추론 기능도 있습니다. 다음은 자바스크립트를 실행할 때 표준으로 갖는 window 객체 함수 예시입니다.

```javascript
// window.confirm 함수의 반환 타입은 boolean인 것을 타입스크립트가 알고 있으므로
// 대입하는 함수 타입이 일치하지 않으면 컴파일 에러가 된다.
window.confirm = () => {
  // boolean을 return하지 않는 한 에러가 된다.
  console.log('confirm 함수')
}
```

- 타입스크립트는 정적 타입 언어이지만 우수한 타입 추론이 있기 때문에 타입을 작성하는 복잡함을 크게 줄일 수 있습니다.

### 타입 어서션

- 타입스크립트가 구체적인 타입을 알 수 없는 경우도 있습니다. 예를 들어, document.getElementById()라는 자바스크립트(DOM) 내장 함수를 그대로 사용하면 타입스크립트는 HTMLElement(또는 null)가 반환된다는 것만 알 수 있습니다. HTMLElement라 하더라도 div인지, canvas인지에 따라 가능한 조작이 달라집니다. 단 타입스크립트에서는 document.getElementById()로 얻을 수 있는 대상의 타입에 관여할 수 없으므로 div, canvas 등을 자동으로 판정해서 처리하지 않습니다.
- 다음 코드는 자바스크립트에서는 에러, 타입스크립트에서는 컴파일 에러가 발생합니다. document.getElementById()는 HTMLElement를 반환하며, HTMLCanvasElement를 반환하지 않으므로 타입이 일치하지 않는다는 에러가 발생합니다.

```javascript
const myCanvas = document.getElementById('main_canvas')
console.log(myCanvas.width) // error TS2339: Property 'width' does not exist on type 'HTMLElement'
```

- 만약 개발자가 대상 ID를 가진 DOM 노드가 HTMLElement 중에서도 HTMLCanvasElement라는 것을 알고 있다면, 명시적으로 타입을 지정할 수 있습니다.

```javascript
변수 = 값 as 타입

const myCanvas = document.getElementById('main_canvas') as HTMLCanvasElement
```

- 타입스크립트에서 타입 어서션을 인정하는 것은 대상이 되는 타입보다 구체적이거나 범용적인 타입으로 변환하는 경우입니다. 이 규칙은 보수적이기 때문에 복잡한 어서션을 수행할 때는 잘 표현하기 어렵습니다. 이런 경우에는 먼저 any로 변환한 뒤, 원하는 타입으로 변환하는 2단계 어셔션으로 구현할 수 있습니다.

```javascript
const result = (response as any) as User
```

- 단, 타입 어서션은 실행 시에 에러를 일으킬 가능성이 있으므로 주의해야 합니다. 다음 코드 예시에서는 number 타입으로 타입 변환(casting)하는 것을 가정했습니다. 컴파일 시 에러는 발생하지 않지만, 실행 시 에러가 발생합니다.

```javascript
const foo: any = 'test'
const bar: number = foo as number
// 컴파일 시에는 number 타입으로서 다뤄져 에러가 발생하지 않지만, 실행 시 사실은 string 타입이 전달되므로 다음 에러가 발생한다
// TypeError: fuga.toFixed is not a function
console.log(bar.toFixed(2))
```

### 타입 앨리어스

- 지금까지 타입에 관해 변수나 인수의 정의 시에 직접 인라인으로 표기하는 방법을 소개했습니다. 단, 같은 타입을 여러 번 사용할 때 재사용하기 좋지 않은, 코드 기술이 복잡해지는 것이 문제입니다.
- 타입 앨리어스(type alias)는 타입 지정의 별명(alias)을 덧붙이는 기능입니다. 타입 앨리어스를 활용하면 타입 정의에 이름을 붙일 수 있습니다. 그 이름을 참조해서 같은 타입을 여러 차례 재사용할 수 있습니다.
- type 키워드를 사용해서 지정합니다.

```javascript
type 타입명 = 값
```

- 원시 타입에 별명을 붙여서 사용할 수 있습니다. 타입명은 일반적으로 대문자로 시작합니다.

```javascript
type Name = string
```

- 다음 코드는 x와 y 좌표 속성을 갖는 Point라는 타입 앨리어스를 정의한 예시입니다.

```javascript
type Point = {
  x: number,
  y: number,
}

function printPoint(point: Point) {
  console.log(`x 좌표는 ${point.x}입니다`)
  console.log(`y 좌표는 ${point.y}입니다`)
}

printPoint({ x: 100, y: 100 })
// 타입이 맞아도 속성명이 다르면 에러
// printPoint({ z: 100, t: 100 })
```

- 함수 자체의 타입도 타입 앨리어스로 정의할 수 있습니다. 다음 코드 string을 인수로 받고 string 타입을 반환하는, 주어진 문자열의 포맷을 변경하는 함수의 타입을 지정하는 예시입니다. 타입 앨리어스를 사용함으로써 코드 기술이 단순하게 되므로 가독성이 좋아집니다.

```javascript
type Formatter = (a: string) => string

function printName(firstName: string, formatter: Formatter) {
  console.log(formatter(firstName))
}
```

- 또한, 객체의 키 이름을 명시하지 않고 타입 앨리어스를 정의할 수도 있습니다. 이것은 인덱스 타입이라 불리는 타입 앨리어스입니다. 키 이름과 키 숫자가 미리 정해지지 않는 경우의 객체를 정의할 때 편리합니다.

```javascript
{ [] : 타입명 }
```

- 다음 코드는 key(객체의 키)에 문자열, 객체의 값에 문자열을 요청합니다.

```javascript
type Label = {
  [key: string]: string,
}
```

- 위 타입 앨리어스를 지정해 화면에 표시할 문자를 정의하는 객체의 키와 값을 다음과 같이 정의할 수 있습니다.

```javascript
const labels: Label = {
  topTitle: '탑 페이지의 제목입니다',
  topSubTitle: '탑 페이지의 하위 제목입니다.',
  topFeature1: '탑 페이지의 기능 1입니다.',
  topFeature2: '탑 페이지의 기능 2입니다.',
}

// 값 부분의 타입이 맞지 않으므로 에러

const foo: Label = {
  message: 100,
}
```

### 인터페이스

- 타입스크립트의 인터페이스(interface)는 타입 앨리어스와 비슷한 기능이지만, 보다 확장성이 높은 열린 기능을 갖고 있습니다. 클래스와 함께 많이 사용합니다. 인터페이스로 객체 타입을 지정할 때는 다음과 같이 사용합니다. type과 비슷하지만 =가 필요하지 않고, 반드시 {로 타입 정의가 시작되어야 하는 등 몇 가지 차이가 있습니다.

```javascript
interface 타입명 {
  속성_1: 타입_1;
  속성_2: 타입_2;
}
```

- 실제로 인터페이스를 사용해 봅니다. 다음 코드는 좌표 x와 y를 갖는 Point 인터페이스를 작성하고, 나중에 좌표 z를 추가하는 예시입니다.

```javascript
interace Point {
  x: number;
  y: number;
}

function printPoint(point: Point) {
  console.log(`x 좌표는 ${point.x}입니다`)
  console.log(`y 좌표는 ${point.y}입니다`)
  console.log(`z 좌표는 ${point.z}입니다`)
}

interface Point {
  z: number;
}

// 인수의 객체에 z가 존재하지 않으므로 컴파일 시 에러가 된다

printPoint({x: 100, y: 100})

// 문제없이 작동한다.
printPoint({ x: 100, y: 100, z: 200 })
```

- Point에 나중에 z를 추가한 것처럼 인터페이스를 확장할 수 있습니다. 타입 앨리어스를 사용할 때는 나중에 같은 이름으로 타입을 정의할 수 없습니다.
- 인터페이스에서는 클래스의 작동 타입을 정의하고, implements를 사용해 클래스에 구현을 위임할 수 있습니다.

```javascript
interface Point {
  x: number;
  y: number;
  z: number;
}

// 클래스가 인터페이스를 implements했을 때, z가 존재하지 않으므로 컴파일 시 에러가 된다.
class MyPoint implements를 Point {
  x: number;
  y: number;
}
```

- 속성 정의에 ?를 사용하면 옵셔널(생략 가능) 속성이 됩니다.

```javascript
interface Point {
  x: number;
  y: number;
  z?: number;
}

// 에러는 발생하지 않는다.
class MyPoint implements Point {
  x: number
  y: number
}
```

- 또한 인터페이스에서는 extends를 사용해 다른 인터페이스를 확장할 수 있습니다.

```javascript
interface Colorful {
  color: string;
}

interface Circle {
  radius: number;
}

// 여러 인터페이스를 상속해서 새로운 인터페이스를 정의할 수 있다.
interface ColorfulCircle extends Colorful, Circle {}

const cc: ColorfulCircle = {
  color: '빨강',
  radius: 10,
}
```

- 객체 타입을 정의할 떄 인터페이스와 앨리어스 모두 사용할 수 있으며, 상속에 관한 세세한 기능의 큰 차이는 없이 거의 비슷한 기능을 갖습니다.
- 단, 타입스크립트의 설계 사상을 고려했을 때 이 2가지 기능은 다소 다른 점이 있습니다.
- 인터페이스는 클래스나 데이터의 한쪽 측면을 정의한 타입, 즉 인터페이스에 매치하는 타입이라도 그 값 이외에 다른 필드나 메서드가 있음을 전제로 한 것입니다. 한편 타입 앨리어스는 객체의 타입 자체를 의미합니다.
- 객체 그 자체가 아니라 클래스나 객체의 일부 속성이나 함수를 포함하는 일부 작동을 정의할 때는 인터페이스를 사용하는 것이 적합할 것입니다.

### 클래스
- 타입스크립트는 ES2015에서 자바스크립트에 도입된 클래스 표기법에 타입을 붙일 수 있습니다. 
