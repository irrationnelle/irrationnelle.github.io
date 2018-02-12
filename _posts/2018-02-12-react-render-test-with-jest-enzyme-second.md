---
title: Jest와 Enzyme을 이용하여 React 테스트하기 - 2
categories:
 - react
tags:
 - react
 - es6
 - jest
 - enzyme
 - unit_test
---

[Jest 와 Enzyme 을 이용하여 React 테스트하기 - 1](https://irrationnelle.github.io/react/2018/02/05/react-render-test-with-jest-enzyme/)

지난 테스트는 `render` 여부만을 판정했는데 이번에는 컴포넌트 데이터인 `state`와 `props` 값에 대해 테스트한다.

```javascript
import React, { Component } from 'react';

import Bar from './Bar';

class Foo extends Component {
	state = {
		testValue: 'luna',
	};

	render() {
		return <Bar number={34} />;
	}
}

export default Foo;
```

Foo 컴포넌트를 위외 같이 수정했다.
