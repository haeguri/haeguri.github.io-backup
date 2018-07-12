---
title: 자바스크립트 스코프
---

프로그래밍에서 변수의 스코프(Scope)는 변수에 접근할 수 있는 범위를 결정한다. 변수는 어떤 스코프에서 선언되었는가에 따라서 변수에 접근할 수 있는 범위가 달라진다. 프로그래밍 언어들은 저마다의 스코프 규칙을 갖고 있으며 자바스크립트도 역시 자신만의 규칙을 갖고 있다. 자바스크립트의 스코프는 크게 전역 스코프(Global Scope)와 지역 스코프(Local Scope)로 나뉜다.

## 전역 스코프

전역 스코프는 모든 자바스크립트 코드에서 접근할 수 있는 공간이다. 전역 스코프에 변수를 선언하면 그 전역 변수는 어디에서든 접근 가능할 수 있게 된다. 전역 변수를 선언하는 방법은 함수 밖에서 `var` 키워드로 변수를 선언하면 된다. 

#### 전역변수 선언 - `var`

```html
<script>
    var globalA = 1;

    function print() {
        console.log(globalA);
    }

    console.log(globalA);
    print();
</script>
<script>
    console.log(globalA);
</script>
```

```
[ 실행결과 ]
----------
1
1
1
```

#### 전역변수 선언 - `window`

전역변수를 선언하는 또 다른 방법이 있는데, 자바스크립트 내장 객체인 `window` 객체의 프로퍼티에 전역 변수로 사용할 데이터를 추가하는 방법이다.

```html
<script>
    window.globalA = 1;

    function print() { 
        console.log(globalA); 
    }

    console.log(globalA);
    print();
</script>
<script>
    console.log(globalA); 
</script>
```
```
[ 실행결과 ]
----------
1
1
1
```

`window` 객체의 프로퍼티로 `globalA`를 추가하면, 앞에서 `var`로 선언한 것과 동일하게 전역변수로서 사용할 수 있게 된다. 반대로 `var`로 전역 변수를 선언하더라도 `window` 객체의 프로퍼티로 추가되는데, 이것은 `hasOwnProperty` 메서드로 확인해볼 수 있다.

```html
<script>
    var globalA = 1;

    console.log(window.hasOwnProperty(‘globalA’));
    console.log(window.globalA === 1); 
</script>
```
```
[ 실행결과 ]
----------
true
true
```

#### 전역함수 선언

전역 스코프에는 변수 뿐만 아니라 **함수**도 선언할 수 있다. 아래의 코드를 보자.

```html
<script>
    function globalFunc() {
        console.log('global function');
    }
</script>
<script>
    globalFunc();
    console.log(window.hasOwnProperty(‘globalFunc’));
    console.log(window.globalFunc === globalFunc); 
</script>
```
```
[ 실행결과 ]
----------
'global function'
true
true
```

두 번째 `<script>`에서는 첫 번째 `<script>`에서 선언된 `globalFunc` 함수를 호출하고 있으며, 실행결과를 보면 정상적으로 함수를 호출하는 것을 확인할 수 있다. 또 전역변수의 경우와 마찬가지로 전역함수를 선언했을 때도 `window` 객체에 추가되는 것을 `hasOwnProperty` 메서드로 확인할 수 있다. 

#### 전역 스코프를 다룰 때 유의할 점

전역 스코프는 하나의 HTML 문서에서 동작하는 자바스크립트 코드들 사이에서 **공유된다**. 즉, 모든 자바스크립트에서 한 개의 전역 스코프를 바라보고 있다. 그렇기 떄문에 전역 스코프를 지나치게 오염시켰을 경우 다른 라이브러리와 충돌하여 예상하지 못한 동작을 불러일으킬 수 있다.

최근의 자바스크립트 개발에서는 위의 예제처럼 모듈화를 하지않는 경우가 거의 없어서 전역 변수를 선언할 일이 거의 없다. 그럼에도 불구하고 전역변수를 선언해야 할 일이 생긴다면 정말 필요한 것인지 신중하게 고려해야 한다.

최근에는 위의 예제처럼 모듈화 없이 자바스크립트를 사용하는 경우는 거의 없지만, 혹시라도 전역변수를 선언해야 할 경우에는 신중하게 고려하고 선언해야 한다.

## 참조

- Object.prototype.hasOwnProperty

<!--
## 지역 스코프

지역 스코프의 유효 범위는 전역 스코프와 달리 일부로 한정되어 있다. 지역 변수를 선언할 수 있는 지역 스코프는 **함수 스코프**와 ECMAScript 2015부터 추가된 **블록 스코프**가 있다.

#### 함수 스코프 (Function Scope)

함수 스코프로 변수를 선언하면 그 변수는 변수를 선언한 함수 안에서만 접근할 수 있다. 즉, 함수 외부에서는 변수에 접근할 수 없게 된다. 

```js
function f1(){
    var localA = 1; // 1
    console.log(localA); // 2
}

f1(); 
console.log(localA); // 3
```

```
[ 실행결과 ]
----------
1
Uncaught ReferenceError: localA is not defined
```

위의 코드를 살펴보면 `f1` 함수의 본문에서 `localA`를 선언하고, `localA`를 출력하고 있다. 그리고 `f1` 함수를 실행해보면 `localA`가 `f1` 함수의 스코프에서 선언되었으므로 로그는 정상적으로 출력된다. 하지만 `f1` 함수 외부에서 `localA` 변수의 값을 출력하려고 시도했을 때, 함수의 외부에서는 `localA`가 정의되지 않았기 때문에 에러가 발생했다.

#### 중첩된 함수 스코프

한 개의 함수가 아니라, 두 개의 함수가 중첩된 상황에서는 어떻게 될까? 

```js
function f1(){
    var localA = 1;

    function f2(){
        var localB = 2;
        console.log(localA);
    }

    f2();
    console.log(localB)
}

f1();
```

```
[ 실행결과 ]
----------
1
Uncaught ReferenceError: localB is not defined
```

실행 결과를 보면 내부 함수 `f2`(**내부 스코프**)에서는 외부 함수 `f1`(**외부 스코프**)에서 선언된 변수에 접근할 수 있는 것을 확인할 수 있다. 하지만 외부 스코프에서는 내부 스코프에서 선언된 변수에 접근할 수 없다.

#### 지역 스코프의 형성

#### 블록 스코프
-->