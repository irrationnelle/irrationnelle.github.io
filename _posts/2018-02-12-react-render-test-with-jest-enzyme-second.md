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

Foo 컴포넌트에 `state`가 추가되었고, 자식 컴포넌트로 Bar 를 가진다. Bar 컴포넌트에는 `props`로 number 를 전달한다.

그럼 다시 테스트를 해보자.

```javascript
import React from 'react';
import { shallow, configure } from 'enzyme';
import Adapter from 'enzyme-adapter-react-16';

import Foo from './Foo';
import Bar from './Bar';

configure({ adapter: new Adapter() });

describe('<Foo />', () => {
	it('should render', () => {
		const wrapper = shallow(<Foo />);
		expect(wrapper.length).toBe(1);
	});

	it('has number props', () => {
		const wrapper = shallow(<Foo number={20} />);
		expect(wrapper.props().number).toEqual(34);
	});

	it('has testValue state', () => {
		const wrapper = shallow(<Foo />);
		expect(wrapper.state().testValue).toEqual('luna');
	});

	it('has testValue state again', () => {
		const wrapper = shallow(<Foo />);
		expect(wrapper.state('testValue')).toEqual('luna');
	});

	it('does not have otherState state', () => {
		const wrapper = shallow(<Foo />);
		expect(wrapper.state('otherState')).toBeUndefined();
	});
});

describe('<Bar />', () => {
	it('has number props', () => {
		const wrapper = shallow(<Bar />);
		expect(wrapper.props().number).toEqual(34);
	});
});
```

`wrapper`의 메소드인 `state()`와 `props()`는 enzyme 에서 제공해주는 메소드다. 위의 코드를 보면 알겠지만 `state()` 메소드는 두가지 방식으로 사용이 가능한데 `props()` 메소드는 그렇지 않다.

이거 때문에 시간을 좀 잡아먹다가 [enzyme 공식 문서(.props() => Object)](http://airbnb.io/enzyme/docs/api/ShallowWrapper/props.html)를 보고 해결했다.

참고로 `state([key])` 방식처럼 `props` 테스트를 하고 싶다면 `prop([key])`를 이용해야 한다.

위 테스트를 돌리면 의아한 점이 `props` 테스트인데, ~~**받는 `props`**를 테스트하는 게 아니라 **전달하는 `props`**를 테스트한다.~~ 뭔가 이상하긴 한데 좀 더 알아봐야겠다.

그래서 **<Foo /> has number props** 는 테스트가 통과하는데 **<Bar /> has number props** 는 테스트가 실패한다. 기대값의 형태는 `number`인데 실제로 받은 값은 `undefined` 라고 나온다.
