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

# numberPadSelector

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

# numberPadSaga

```javascript
import { call, put, select, takeLatest } from 'redux-saga/effects';
import { delay } from 'redux-saga';
import { SUBMIT_MAGICSQUARE, SUBMIT_MAGICSQUARE_FAIL } from '../constants';
import { numberPad, system, magicSquare } from '../actions';
import { magicSquareSelector } from '../selectors';
import * as API from '../../service/API';

export function* submitMagicSquare() {
	try {
		const magicSquareMap = yield select(
			magicSquareSelector.makeMagicSquareMap()
		);
		yield call(API.checkAnswerPromise, magicSquareMap);
		yield put(system.endGame());
		yield put(system.showCircle());
		yield call(delay, 1500);
		yield put(system.showResultModal());
	} catch (err) {
		yield put(
			magicSquare.createRepeatedPixelValues(err.createRepeatedPixelValues)
		);
		yield put(numberPad.submitMagicSquareFail());
	}
}

export function* watcherSubmitMagicSquare() {
	yield takeLatest(SUBMIT_MAGICSQUARE, submitMagicSquare);
}

export function* submitMagicSquareFail() {
	yield put(system.FailEndGame());
	yield put(system.showCross());
	yield call(delay, 1500);
	yield put(system.showResultModal());
	yield put(system.resetGame());
}

export function* watcherSubmitMagicSquareFail() {
	yield takeLatest(SUBMIT_MAGICSQUARE_FAIL, submitMagicSquareFail);
}
```
