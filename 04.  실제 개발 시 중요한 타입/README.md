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
