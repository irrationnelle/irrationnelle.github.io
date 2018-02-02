---
title: Code Syntax Highlight
categories:
 - react
tags:
 - react
 - javascript
 - es6
---

This is a highlight test.

# Normal block

```javascript
import { createSelector } from 'reselect';

const selectNumber = state => state.numberPad.number;
const selectSubmitButtonStatus = state =>
	state.numberPad.isActivateSubmitButton;
/*
    NOTE: makeGameProcess에 함수 대입
*/
const makeNumber = () => createSelector(selectNumber, number => number);
const makeSubmitButtonStatus = () =>
	createSelector(
		selectSubmitButtonStatus,
		isActivateSubmitButton => !isActivateSubmitButton
	);

export { makeNumber, makeSubmitButtonStatus };
```