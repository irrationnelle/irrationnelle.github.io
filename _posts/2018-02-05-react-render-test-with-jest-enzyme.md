---
title: Jest와 Enzyme을 이용하여 React 테스트하기 - 1
categories:
 - react
tags:
 - react
 - es6
 - jest
 - enzyme
 - unit_test
---

# Jest 와 enzyme

`Jest`는 페이스북에서 만든 테스트러너이고, `enzyme`은 airbnb 에서 만든 React 컴포넌트를 테스트하기 위한 테스트 유틸리티이다.

`Jest`만 사용하는 경우, 켄트 백의 TDD 에서 볼 수 있는 유닛 테스트들은 가능하지만 React 컴포넌트가 잘 렌더링 되는지 props 값이 잘 전달되는지는 알 수 없다. 그래서 React 는 보통 `Jest`에 `enzyme`을 결합하여 테스트한다.

# create-react-app 로 컴포넌트 만들기

먼저 `create-react-app` 으로 간단하게 프로젝트를 만든다.

```bash
$ create-react-app react-jest-enzyme-test
$ cd react-jest-enzyme-test
```

그리고 테스트를 위한 npm 모듈들을 설치한다

```bash
$ npm i -D enzyme enzyme-adapter-react-16
```

[React 테스트(test, jest, enzyme)](https://www.zerocho.com/category/React/post/583231469a87ec001834a0ec)에서
`enzyme-adapter-react-16`은 `react@16`와 `react@15`가 서로 `enzyme`이 다르게 작동하기에 필요한 모듈이라고 한다.

`create-react-app`으로 만든 구조는 다음과 같다

```bash
.
├── .gitignore
├── package.json
├── public
│   ├── favicon.ico
│   ├── index.html
│   └── manifest.json
├── README.md
├── src
│   ├── App.css
│   ├── App.js
│   ├── App.test.js
│   ├── index.css
│   ├── index.js
│   ├── logo.svg
│   └── registerServiceWorker.js
└── yarn.lock
```

`./src/App.test.js`가 있어서 `npm run test`를 하면 알아서 테스트를 해주지만 우린 일단 `Jest`를 사용한다.

이처럼 유닛 테스트를 위한 파일은 `파일명.test.js`라고 해주면 된다.

일단 `src` 폴더에 `components` 폴더를 만든다.

```bash
$ mkdir ./src/components
```

테스트를 하기 위한 컴포넌트를 `components` 내부에 `Foo.js`라고 만든다. 테스트를 위한 컴포넌트이니 복잡한 건 다 빼겠다.

```javascript
import React, { Component } from 'react';

class Foo extends Component {
	render() {
		return <div>Foo</div>;
	}
}

export default Foo;
```

이제 `Foo` 컴포넌트를 테스트하기 위해 `Foo.test.js`를 작성하자. 드디어 본론이다.

# 유닛 테스트 만들기

```javascript
import React from 'react';
import { shallow, configure } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

import Foo from './Foo';

configure({ adapter: new Adapter() });

describe('<Foo />', () => {
	it('should render', () => {
		const wrapper = shallow(<Foo />);
		expect(wrapper.length).toBe(1);
	});
});
```

처음에 이 코드를 보고 많이 당황했다.

* 일단 많은 함수들이 특별히 `import` 되지 않았는데도 사용이 가능하다. i.e. `describe`, `it`, `expect`
* 그 함수들의 첫번째 매개변수가 하는 일이 뭔지 모르겠다. 이건 내가 켄트 백의 TDD 책에 익숙해져서, 즉 `JUnit`에 익숙해져서 그런 것인데 `JUnit`에서는 기대값과 결과값만 기대하는 코드를 넣는 반면 여기서는 코드가 어떻게 동작해야 한다고 기술할 수 있다.

`describe`는 테스트 하고자 하는 컴포넌트 단위로 보면 되고 `it`은 해당 컴포넌트가 기능하는 것을 단위로 삼는다고 보면 된다. 각 함수들의 첫번째 매개변수는 문자열로 기대하는 바를 자유롭게 적으면 된다. 나는 **`<Foo />`라는 컴포넌트가 렌더링 되어야 한다** 라고 기술했다.

`wrapper`는 `enzyme` 함수인 `shallow`에 인자로 `Foo`컴포넌트를 넣은 것인데 `shallow`는 `Foo`컴포넌트에 자식 컴포넌트가 있어도 해당 자식 컴포넌트까지 렌더링 해주지 않는다. 복잡한 이야긴 나중으로 미루고.

여기서 테스트하고 싶은 바는 `Foo` 컴포넌트가 렌더링 되는지 유무이기 때문에 위의 코드대로 해도 되고 `expect(wrapper).not.toEqual(null)` 라고 해도 상관없다.

그럼 이제 `Jest`로 테스트를 해보자

# Jest 로 테스트하기

`Jest`를 npm 모듈 전역으로 설치한다.

```bash
$ npm i -g jest
```

그리고 우리가 만든 디렉토리로 들어가자

```bash
$ cd ./src/components
```

테스트 실행!

```bash
$ jest Foo.test.js
```

그러면 에러가 난다. 테스트 실패가 아니라 에러가 난다. 왜???

왜냐면 위의 코드가 ES6 이상의 코드라서 그렇다. 그래서 `Babel` 모듈을 설치하고 `package.json`에 다음 설정을 추가해준다.

```bash
$ npm i -D babel-preset-env babel-preset-react-app babel-preset-stage-0
```

### package.json

    "babel": {
            "presets": [
                [
                    "env",
                    {
                        "modules": false
                    }
                ],
                "react-app",
                "stage-0"
            ],
            "env": {
                "production": {
                    "only": [
                        "app"
                    ],
                }
            }
        },

그리고 다시 테스트

```bash
$ jest Foo.test.js
```

아래처럼 나오면 성공이다.

```bash
 PASS  ./Foo.test.js
  <Foo />
    ✓ should render (3ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        1.05s
Ran all test suites matching /Foo.test.js/i.
```
