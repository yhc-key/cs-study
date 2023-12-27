# Revealing Module Pattern

- [Revealing Module Pattern](#revealing-module-pattern)
- [설명](#설명)
  - [예시](#예시)
  - [장점](#장점)
  - [단점](#단점)
- [참고](#참고)

# 설명

노출모듈 패턴으로 불리며, `Javascript` 에서 사용되는 디자인 패턴이다. JS는 `private`, `public` 와 같은 접근 제어자가 없고 전역 범위에서 스크립트가 실행되기 때문에 노출모듈 패턴으로 접근 제어자를 구현한다. 명시적으로 노출하고 싶은 부분만 정해서 노출하는 방식이다. 일반적으로 객체 리터럴(`{…}`) 형태로 반환한다.

## 예시

```jsx
const pukuba = (() => {
	const a = 1;
	const b = () => 2;
	const public = {
		c: 2,
		d: () => 3,
	};
	return public;
})();
console.log(pukuba); // { c: 2, d: [Function: d] }
console.log(pukuba.a); // undefined
```

- **a, b** : **private** 범위. 다른 모듈에서 사용할 수 없는 변수나 함수이다.
- **c, d** : **public** 범위. 다른 모듈에서 사용할 수 있는 변수나 함수이다.

<aside>
💡 노출모듈 패턴을 기반으로 만든 JS 모듈 방식으로 **CJS(CommonJS)** 모듈 방식이 있다.
**CJS :** nodejs에서 자바스크립트 패키지를 불러올 때 사용하는 방식. 더 궁금한 사람은 [여기](https://yceffort.kr/2023/05/what-is-commonjs)를 클릭하세용

</aside>

```jsx
var myModule = (function (window, undefined) {
	// private
	function myMethod() {
		console.log("myMethod");
	}

	function myOtherMethod() {
		console.log("myOtherMethod");
	}

	return {
		//public
		someMethod: myMethod,
		someOtherMethod: myOtherMethod,
	};
})(window);

myModule.myMethod(); // Uncaught TypeError: myModule.myMethod is not a function
myModule.myOtherMethod(); // Uncaught TypeError: myModule.myOtherMethod is not a function
myModule.someMethod(); // console.log('myMethod');
myModule.someOtherMethod(); // console.log('myOtherMethod');
```

## 장점

- 개발자에게 깔끔한 접근 방법을 제공
- `private` **데이터 제공**
- 전역 변수를 덜 더럽힘
- 클로저를 통해 함수와 변수의 지역화
- 스크립트 문법이 더 일관성 있음
- 명시적으로 `public` 메소드와 변수를 제공해 명시성을 높임

## 단점

- **Test를 할 때 `private` 메소드에 접근할 방법이 없음**
  - 그래도 함수 무결성을 고려하면 공개된 메소드만 테스트하는 게 맞다고 함
- `private` 메소드에 대해 함수 확장하는데 어려움이 있음
- `private` 메소드를 참조하는 `public` 메소드를 수정하기 어려움

# 참고

[[Design Pattern] 노출모듈 패턴(revealing module pattern)](https://velog.io/@juijeong8324/Design-Pattern-8)

[노출식 모듈 패턴 Revealing Module Pattern](https://edykim.com/ko/post/revealing-module-pattern/)
