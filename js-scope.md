---
title: 자바스크립트 스코프
---

프로그래밍에서 변수의 스코프(Scope)는 변수의 유효 범위를 결정한다. 유효 범위란 변수에 접근할 수 있는 범위를 말한다. 프로그래밍 언어들은 저마다의 스코프 규칙을 갖고 있으며 자바스크립트도 역시 자신만의 규칙을 갖고 있다. 자바스크립트의 스코프는 크게 전역 스코프(Global Scope)와 지역 스코프(Local Scope)로 나뉜다.

## 전역 스코프

전역 스코프에 선언된 전역 변수는 모든 자바스크립트 코드에서 접근할 수 있다. 전역 변수를 선언하는 방법은 함수밖에서 `var` 키워드로 변수를 선언하면 된다.

#### 1. 전역변수 선언 - `var`

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

#### 2. 전역변수 선언 - `window`

전역변수를 선언하는 또 다른 방법으로는 자바스크립트 내장 객체인 `window` 객체의 프로퍼티에 데이터를 추가하는 방법이 있다.

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

`window` 객체의 프로퍼티로 `globalA`를 추가하면, 앞에서 `var`로 선언한 것과 동일하게 전역변수로서 사용할 수 있게 된다. 반대로 `var`로 전역 변수를 선언하더라도 `window` 객체의 프로퍼티로 추가되는데, 이것은 `Object.prototyep.hasOwnProperty` 메서드로 확인해볼 수 있다.

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

#### 3. 전역함수 선언

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

#### 4. 전역 스코프를 다룰 때 유의할 점

전역 스코프는 어디서든 접근할 수 있기 때문에 내가 선언한 전역변수를 다른 사람이 작성한 코드에서 값을 바꿔버릴 수도 있어 위험하다. 또 전역변수를 무분별하게 선언해서 쓰다가 전역 변수가 필요없어져 전역 변수 선언을 지우려고 할 경우에 그 변수가 어디서 사용되는지 다 확인해야 하기 때문에 프로그램을 수정하기 어렵게 만든다. 그렇기 때문에 전역 스코프에 변수나 함수를 선언하는 것은 최대한 피해야 한다.

## 지역 스코프

지역 스코프에 변수를 선언하면 변수의 유효 범위를 어디서 어디까지로 제한할 수 있다. 예전의 자바스크립트에서는 **함수** 단위로만 지역 변수를 선언할 수 있었지만, 최신 자바스크립트(ECMAScript 6이상)에서는 **블록(`{ }`)** 단위로 지역 변수를 선언할 수 있게 됐다. 먼저 알아볼 것은 함수 단위로 스코프를 형성하는 함수 스코프이다. 

#### 1. 함수 스코프

함수 스코프로 선언된 지역 변수는 자신이 속한 함수 안에서만 접근할 수 있다. 

```js
if(true){
    let a =2;
    const b = 3;
}
var globalA = 1;

function f1(){
    var localA = 2; 
    console.log('globalA = ', globalA);  // ------ "1"
    console.log('localA = ', localA);    // ------ "2"
}

f1();
console.log('localA = ', localA);        // ------ "3"
```

```
[ 실행결과 ]
----------
globalA = 1
localA = 2
Uncaught ReferenceError: localA is not defined
```

"1" - `f1` 함수에서는 `globalA`가 선언되지 않았다. 자바스크립트는 현재 코드가 실행되고 있는 스코프에서 `globalA`를 찾을 수 없다면, **한 단계 위의 스코프**로 올라가서 변수를 찾기 시작한다. `f1` 함수 스코프의 **한 단계 위의 스코프**는 **전역 스코프**이다. 전역 스코프에는 `globalA`가 선언되어 있으므로 `globalA`의 값을 출력할 수 있다.

"2" - `localA`를 출력하는 코드가 속한 `f1` 함수 스코프에는 `localA`가 선언되어 있다. 따라서 상위 스코프로 올라갈 필요도 없이 `f1` 함수 스코프에서 `localA`를 찾아 출력할 수 있다.

"3" - 이번에는 `localA`를 출력하는 코드가 전역 스코프에 실행된다. 전역 스코프에는 `localA`가 선언된 적이 없으므로 상위 스코프로 올라가서 찾아야 하는데, 자바스크립트에서는 전역 스코프가 최상위 스코프이므로 더 이상 위로 올라갈 스코프가 없다. 따라서 `localA`를 찾을 수 없다는 에러가 발생한다. 

정리하면, 변수에 접근하기 위해 스코프에서 변수를 탐색하는 것은 현재 스코프에서부터 시작된다. 현재 스코프에서도 찾을 수 없다면, 상위 스코프로 올라가면서 찾다가 마지막에는 전역 스코프에서 변수를 찾는다. 하지만 상위 스코프에서 하위 스코프의 변수에는 접근할 수 없다. 이러한 규칙은 함수가 중첩되었을 때도 적용된다.

#### 2. 중첩된 함수 스코프

```js
var globalA = 1

function f1(){
    var localA = 2;

    function f2(){
        var localB = 3;
        console.log('globalA = ', globalA);  // ----- "1"
        console.log('localA = ', localA);    // ----- "2"
    }

    f2();
    console.log('localB = ', localB);        // ----- "3"
}

f1();
console.log("localA = ", localA);            // ----- "4"
```

```
실행결과
----------
globalA = 1
localA = 2
localB = 3
Uncaught ReferenceError: localA is not defined
```

"1" - `f1` 함수에서는 `globalA`가 선언되지 않았다. 현재 스코프에서 `globalA`를 찾을 수 없으므로 상위 스코프로 올라가면서 `globalA`를 찾는다. 상위 스코프인 `f2`에서도 `globalA`를 찾을 수 없으므로 더 상위 스코프인 전역 스코프까지 올라간다. 전역 스코프에는 `globalA`가 선언되어 있으므로 `globalA`의 값은 `1`로 출력된다.

"2" - `f1` 함수에서는 `localA`가 선언되지 않았다. 현재 스코프에서 찾을 수 없으므로 상위 스코프로 올라가 보는데, 상위 스코프인 `f2`에서는 `localA`가 선언되어 있으므로 `localA`의 값인 `2`가 출력된다.

"3" - `f2` 함수에서 `localB`가 선언됐으므로 `localB`의 값인 `3`이 출력된다.

"4" - 전역 스코프에는 `localA`가 선언되지 않았으므로 상위 스코프로 올라가서 찾아야 하는데, 전역 스코프가 최상위 스코프이므로 더 찾아볼 스코프가 없어서 에러를 발생시킨다.

#### 2. 블록 스코프

ECMAScript 5(ES 5)까지의 자바스크립트에서는 블록 단위의 스코프를 지원하지 않았다. 지역 변수를 선언하고 싶으면 **함수**를 선언해야 했었다.

```js
if(true) {
    var a = 1;

    console.log(a);
}

console.log(a);
```

```
실행결과
----------
1
1
```

ECMAScript 6(ES 6)부터는 새로 추가된 변수 선언 키워드인 `let`, `const`를 이용하면 블록 단위의 스코프를 형성할 수 있게 됐다.

```js
if(true){
    let a = 2;
}
```

test