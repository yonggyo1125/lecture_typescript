# 타입스크립트로 개발 시 설정

- 타입스크립트로 개발을 진행할 때, 편리한 설정 파일인 tsconfig.json과 개발 도구 prettier, ESLint에 관해 소개합니다.

## tsconfig.json

- 타입스크립트에서는 컴파일에 필요한 옵션이나 컴파일 대상이 되는 파일의 정보 등을 tsconfig.json에 기술합니다. IDE 편집기에서도 이 설정 파일을 기반으로 자동 완성이나 에러 감지가 가능하므로 활용하면 좋습니다. `tsc --init` 명령어를 실행하면 기본 `tsconfig.json` 파일이 생성됩니다. 프로젝트 루트에 배치합니다.
- 다음으로 샘플 코드로 사용하고 있는 tsconfig.json 파일에 관해 소개합니다.

```json
{
  "compilerOptions": {
    "target": "es5",
    "lib": ["dom", "dom.iterable", "esnext"],
    "allowjs": true,
    "skipLibCheck": true,
    "strict": false,
    "strictNullChecks": true,
    "forceConsistentCasingInFileNames": true,
    "noEmit": true,
    "esModuleInterop": true,
    "module": "esnext",
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "baseUrl": "src"
  },
  "include": ["next-env.d.ts", "src/**/*.ts", "src/**/*.tsx"],
  "exclude": ["node_modules"]
}
```

- tsconfig.json의 자세한 옵션에 관해서는 공식문서를 참조하기 바랍니다.

## Prettier

- Prettier를 사용하면 들여쓰기의 공백 수를 맞추거나, 큰따옴표와 작은따옴표를 정리하는 등 자동으로 코드를 포매팅할 수 있습니다. 특히 여러 사람이 동시에 파일을 변경할 때는 공통 코드 포맷을 사용해 Prettier를 실행함으로써 개발할 때 충돌을 줄일 수 있어 권장합니다. 여기서는 Prettier 도입 방법을 간단히 소개힙니다.
- 다음 명령어로 Prettier를 설치합니다.

```javascript
$ npm install prettier --save-dev
```

- 프로젝트 디렉토리 아래 .prettierrc라는 파일이 생성됩니다. 이 파일에는 다음과 같은 포맷 설정값들이 들어있습니다.

```json
{
  // 코드 끝에 세미콜론을 넣는가
  "semi": false,
  // 객체 정의의 마지막에 콤마를 빼는가
  "trainingComma": "none",
  // 문자열의 정의 등의 마침표에 작은 따옴표를 사용하는가
  "singleQuote": true,
  // 행을 줄바꿈할 문자 수
  "printWidth": 80
}
```

- package.json 안에 scripts 항목에 다음의 행을 작성합니다.

```json
{
    "scripts": {
        ...
        "prettier-format": "prettier --config .prettierrc 'src/**/*.ts' --write"
    }
}
```

- 다음 커맨드를 실행하면 src 아래의 모든 타입스크립트 소스 코드에 대해 포매팅이 실행됩니다.

```
$ npm run prettier-format
```

## ESLint

- ESLint는 자바스크립트나 타입스크립트 코드를 해석해 문제가 있는 위치를 지적해 코드 품질을 높이는 데 도움을 주는 도구입니다. Prettier는 코드를 포매팅(형태를 정리해서 출력)하는 것이 주목적입니다. 한편, ESLint는 코드를 해석해 문자를 감지하는 것(lint)이 주목적입니다. 많은 모든 프런트앤드 개발 프로젝트는 prettier와 ESLint를 함께 사용하고 있습니다.
- ESLint는 예를 들어 코드 안에 사용되지 않는 변수가 있을 때 등 에러를 출력합니다. 그 밖에도 if 문의 조건안에서 비교가 아니라 대입이 된 부분을 감지하거나, 함수에서 사용되지 않는 인수를 정의하는 경우 등에도 에러를 감지합니다. 100가지 이상의 다양한 규칙에 대해 코드를 해석할 수 있으며, 설정도 커스터마이즈할 수 있습니다. 또한, 직접 ESLint 규칙을 작성해서 추가할 수도 있습니다.
- .eslintrc.js라는 파일에 다음과 같이 설정을 작성합니다.

```json
{
  "rule": {
    "semi": ["error", "always"],
    "quotes": ["error", "double"]
  }
}
```

- 위 설정 파일의 semi와 quotes는 각각 세미콜론과 따옴표를 어떻게 취급하는지 나타내는 규칙을 설정한 것입니다. 배열의 첫 번째 값은 에러 수준을 나타냅니다.
  - off 또는 0 규칙 비활성화
  - warn 또는 1, 규칙을 경고로 취급
  - error 또는 3, 규칙을 에러로 취급
- 두 번째 값은 각각 규칙에 전달하는 설정값입니다. 자세한 규칙의 설명에 관해서는 공식 문서의 규칙 페이지를 참조하기 바랍니다.
- ESLint의 규칙은 플러그인으로 풍부하게 제공되며 npm 명령어를 통해 설치할 수 있어 프로젝트에 간단하게 삽입할 수 있습니다. ESLint 설치 방법이나 좀 더 자세한 ESLint 사용법에 관해서는 공식 문서를 참조하기 바랍니다.

## 컴파일 옵션

- 타입스크립트의 컴파일 명령이나 tsconfig에 지정한 옵션에 관한 몇 가지 주의사항을 소개합니다. 타입스크립트 CLI의 보다 자세한 사용 방법에 관해서는 공식 문서를 참조하기 바랍니다.

### noImplicitAny

- 타입이 지정되지 않고 타입스크립트가 콘텍스트에 따라 타입을 추측할 수 없는 경우, 컴파일러는 일반적으로 기본 any를 사용합니다. 이것을 암묵적인 any라 부릅니다. 하지만 any는 타입 체크를 수행하지 않도록 사용을 자제해야 합니다. 그래서 컴파일 옵션인 noImplicitAny를 사용한다면 암묵적인 any를 사용한 경우 에러를 발생하도록 설정할 수 있습니다.
- 예를 들어, 다음 코드와 같이 인수 타입을 정의하지 않는 함수가 있을 때, noImplicitAny 옵션을 활성화해서 실행하면 에러가 발생합니다.

```javascript
// noImplicitAny 옵션을 지정해 컴파일을 실행하면 아래 인수의 타입 정의가 없는 경우 에러가 된다.
// error TS7006: Parameter 'word' implicity has an 'any' type.
function hello(word) {
  console.log(`Hello, ${name}`);
}

hello("Hana");
strictNullChecks;
```

- 컴파일 옵션 strictNullChecks을 사용하면 null, undefined를 엄격하게 다룰 수 있습니다. 옵션을 활성화하지 않으면, 변수에 null이나 undefined 등을 명시하지 않아도 허용됩니다. 한편, 옵션을 활성화해서 null이나 undefined를 사용하는 경우에는, Union 타입이나 생략 가능한 인수를 사용해 명시적으로 null이나 undefined를 허용하는 작성법도 필요합니다.

```javascript
let date: Date;
date = new Date();
// strictNullChecks를 활성화한 경우, null을 대입하려고 할 때 컴파일 에러가 발생한다.
// error TS2322: Type 'null' is not assignable to type 'Date'.
date = null;
```

- null 대입을 사용할 때는 다음과 같이 기술합니다.

```javascript
// null을 명시적으로 허용하도록 타입을 정의한다.
let date: Date | null;
date = new Date();
// 컴파일 에러는 발생하지 안는다.
date = null;
```

- 앞서 설명한 !를 사용하는 논-널 어서션이라는 기능도 이 옵션이 활성화돼 있을 때 사용할 수 있습니다. 타입스크립트의 타입 체크를 통한 혜택을 얻을 수 있습니다.

### target

- 컴파일 옵션 target을 사용하면 타입스크립트가 컴파일을 수행할 때 어떤 버전의 ECMAScript로 출력할 것 인지 지정할 수 있습니다. 예를 들어 Internet Explorer 11 등 조금 오래된 브라우저를 지원하는 경우에는 다음과 가티 es5를 지정해 ECMAScript5 표준의 자바스크립트 변환됩니다.

```
$ tsc --target es5 sayHello.ts
```
