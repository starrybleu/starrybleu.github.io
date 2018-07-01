---
layout: post
title: 자바스크립트 동적인 import() & export
category: Development
comments: true
---

리액트 등 자바스크립트로 화면을 개발하다보면 import, export 를 거의 대부분의 파일에 쓰게 되는데, 전체 앱을 감싸는 컴포넌트 또는 스토어에 특정한 객체(예로, 로그인 정보, 유저 권한 등)가 있는지 없는지에 대한 보장을 받고 싶었다. Vue에서는 잘 모르겠으나, 리액트 컴포넌트의 라이프 사이클 메소드인 `componentDidMount`나 `componentWillMount` 등에서 ajax 요청을 하여 원하는 정보를 가져오고 `state` 를 변경하는 등의 작업이 일반적인 방법으로 쓰여지고 있는 것 같다.

그 상태가 각 컴포넌트에서만 쓰인다면 상관이 없지만, 다른 컴포넌트에서도 그 상태를 `props` 또는 `store`로부터 받아 사용하려고 보면 아직 비동기 요청이 끝나지 않았을 가능성이 많다.

(이를 디버깅해보며 자바스크립트 모듈의 import, export 와 더불어 리액트 라이프 사이클 메소드들의 실행 순서 등에 대해 이전보다는 이해력이 생겼다.)

이를 해결하기 위해 방법을 찾던 중, 아래에 언급하는 글을 보게 되었고, 내용이 알아두면 좋을 것 같고 좀 더 잘 이해하기 위해 번역을 하게 되었다.

이 글은 [Javascript dynamic import() & export](https://medium.com/@WebReflection/javascript-dynamic-import-export-b0e8775a59d4)를 번역한 글입니다. 

읽는 이에게: 직역 위주라 의미가 자연스럽게 이해가 안 되는 게 많을 수 있음을 감안하고 봐주시길 바랍니다.

![][imgae1]
*정적인 imports와 동적인 imports 의 조합*

[stage3](https://github.com/tc39/proposal-dynamic-import#import) 인 현재, ECMAScript의 다음 버전은 non-block 하는 방식으로 비동기 모듈들을 동적으로 import하는 능력을 가져올 것 같다.

이것에 대한 근거는, 특히 웹에 대해서, 모든 스크립트들을 먼저 번들링 하는 필수적인 기법을 요구하는 것과는 정반대로 당신은 아마도 모듈을 요구에 따라(on demand) import 하고 싶을 것이다.

[AMD](http://requirejs.org/docs/whyamd.html) 라는 알려진 비슷한 접근법이 역사상 [CommonJS bundles](http://browserify.org/)와의 전투에서 패배함에 따라, 오늘날 우리는 `async`와 `await` 뿐만 아니라 [Promise](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Promise)등의 더 나은 기초 요소와 같은 언어의 기능을 갖게 되었다. 그래서 비동기 import 는 이렇게 쉽게 가능하다. : `const module = await import('./module.js');`

### 그러면 비동기 exports는 어떠한가?
우리는 모듈들을 정의하기 위해 `import`와 `export` 메커니즘을 갖고 있는 같은 이유로, 또한 기능을 비동기적으로 export 할 수 있는 조건부의 모듈들에 의존하는 모듈을 아마도 만들고 싶을것이다.

이 시나리오에 대한 몇가지 use case를 살펴보자.

* 기능 탐지에 기반한 **polyfills**는 기능을 export 하기 전에 임의의 수의 모듈들을 import 한다.
* 다른 사용자 정의 요소들에 의존한 **사용자 정의 요소들**. 왜냐하면 한꺼번에 그것들을 번들링 하는 것은 재사용을 불가능하게 만들기 때문이다.
* `customElements.whenDefined('comp-name').then(useIt);` 과 같은 `Promise`에 기반한 메커니즘을 이미 갖고 있는 사용자 정의 요소들. 이것을 컴포넌트들을 한번에 해결(resolve)하고 등록(register)하게 하는 동적인 import와 조합하는 것은 가장 자연스러운 발전이다.
* 실제로 DB 연결이 이뤄진 기능을 export 하는 것은 일단 원격 파일이 구문분석(parsed)되고, exported 된 모듈을 사용가능하게 만들기 위해 반드시 필요한 비동기 동작이 이행된 후에 수행된다.

마지막 use case는 심지어 비동기 import 가 유용할 필요조차 없다. 이미 서버 사이드에서는 매우 일반적인 use case이다.

### 지난번까지는...
나는 이미 비동기 import/export에 대하여 글을 쓴 적이 있고, "아무도 원하지 않는" 비동기 import에 반대하는 많은 개발자들과 함께 [CommonJS and NodeJS](https://github.com/nodejs/node-eps/pull/50)의 사람들에게 제안을 했다.

아무 가치가 없다면 나는 정말로 그것을 원한다. 그리고 HTTP/2 와 더 나은 캐시 메커니즘을 따르는 웹으로 나아가기 위해, Service Workers가 제공하는 메커니즘처럼 비동기 export를 가지지 않을 이유를 찾을 수 없다.

나는 정말로 지금이 [번들러들이 없어질](https://www.contentful.com/blog/2017/04/04/es6-modules-support-lands-in-browsers-is-it-time-to-rethink-bundling/) 떄라고 생각한다. 하지만 우리는 모듈을 비동기적으로 export하는 간단하며 범용적이고 경쟁력 있는 접근법에 확실하게 동의할 필요가 있다.

### An import(...) polyfill as playground
`import()` 함수는 polyfill하는 것이 불가능하다고 [내가 스스로 말했을 지라도](https://medium.com/@WebReflection/asynchronous-module-import-path-b9f56675e109), 나는 실제로 네이티브 모듈과 트랜스파일된 모듈을 브라우저로 가져와서 런타임에 주입할 수 있는 `import()` 기능을  1KB 이하의 부트스트랩 스크립트를 만들기 위한 관리를 했다.

* 상대경로, 절대경로, 원격경로와 호환
* 정적인 imports, 동적인 imports와 호환
* 네이티브 JS 또는 Babel 트랜스파일된 코드와 호환. 이는 모든 이전과 현대의 ES5 호환가능한 브라우저와 호환됨으로도 알려져있다.

```html
<!doctype html>
<!-- all it takes, you could also use unpkg:
https://unpkg.com/import.js@latest -->
<script src="import.js"
  data-main="js/main.js"
></script>
```

모든 것은 [GitHub](https://github.com/WebReflection/import.js)에 있다. 이걸 가지고 놀기 시작하기 위해 필요한것은 단지 `import.js` 스크립트를 include 하는 것과 당신의 `main/index` 자바스크립트 파일을 가리키는 `data-main` 속성(attribute)를 정의하는 것 뿐이다.

거기엔 생생한 몇가지 예제도 함께 있다. 트랜스파일된것은 모든 브라우저에서 잘 동작해야하고, ES2015 네이티브 import/export 기능을 기반한 예제는 맥OS의 Safari 또는 리눅스의 GNOME Web 브라우저와 같이 호환가능한 브라우저가 필요하다.

거기엔 또한 다음 절에서 설명할 비동기 export 패턴을 유지하면서 네이티브와 동기적 imports 대 동적인 imports를 비교하는 복잡한 테스트가 포함된 테스트 폴더도 있다.

### 일단 비동기면, 모두 비동기
나는 이 패턴에 대해 이미 [ES-Discuss ML](https://esdiscuss.org/topic/dynamic-import-polyfill-question) 논의한 바가 있다. 그리고 동적으로 또는 부분적으로 export하는 패턴에 대해서는 영원히 자전거방 만큼의(많은) 여지가 있지만, 나는 항상 KISS와 YAGNI 접근법을 지지한다. (KISS : Keep it short and simple, YAGNI: You are'nt gonna need it.)

#### CommonJS가 우리에게 가르쳐 준 것
CommonJS 안에서 모듈들을 export하는 대부분의 패턴은 `module.exports={...}` 이다.

함수들, 클래스들, 객체들, 이것들은 모두 모듈 객체의 하나의 진입점으로서 정의되는데 ES2015에서 간을 맞춘("re-sugared") 모듈들은 `export default {...};` 를 통한다.

그러면 우리가 하나의 기본 진입점(entry point)을 통해 비동기적으로 한 번 export하는 것은 어떨까?

### JS 비동기 export 요약

```javascript
export default new Promise(async $export => {
  // await anything that needs to be imported
  // await anything that asynchronous
  // finally export the module resolving the Promise
  // as object, function, class, ... anything
  $export(
    {module: 'object'} ||
    function () {}     ||
    class Anything {}
  );
});
```

축하한다. 넌 비동기 모듈을 export 하도록 하는 모든 것을 배웠다.

이제, 빠르게 이 패턴이 가능하게 하는 기능들을 살펴보자.

* 당신은 여전히 동기적인 의존성을 갖는 한 언제라도 최상단에서 정적으로 `import mod from './mod.js';`를 할 수 있다.
* 당신은 동적인 `import(...)`, DB 연결, 원격 URL 호출 등을 포함하는 promise 콜백 안에서 `await anyting;`을 할 수 있다.
* 필요에 따라 어느 모듈 Consumer는 이 패턴을 개발할 수 있다.
* 비동기적으로 import 또는 export 하는 것을 필요로 하든 말든 언제나 이패턴은 작동한다.

```javascript
// ES2017 Asynchronous Export
// module.js
export default new Promise(async $export => {
  const module = await Promise.resolve(
    {my: 'module'}
  );
  $export(module);
});

// - - - - - - - - - - - - - - - - - - - - - - - - - - - -

// ES2015 consumer
import module from './module.js';

module.then(exports => {
  // will log "module"
  console.log(exports.my);
});

// - - - - - - - - - - - - - - - - - - - - - - - - - - - -

// ES2017 consumer
(async () => {
  const module = await (
    await import('./module.js')
  ).default;
})();


// - - - - - - - - - - - - - - - - - - - - - - - - - - - -

// ES2017 consumer and exporter
export default new Promise(async $export => {
  const module = await (
    await import('./module.js')
  ).default;
  $export({module, method(){}});
});
```

이 패턴은 또한 CommonJS와도 쉽게 상호작용할 수 있다.

```javascript
// CommonJS consumer and/or importer
module.exports = new Promise(async $export => {
  const module = await require('./module');
  $export({module, method(){}});
});
```

이는 다시 심지어 번들된 파일들과도 호환되고, 더 일반적으로는 이 패턴은 가장 일반적이고, 실제의 모듈들에 호환된다는 것을 의미한다.

### "await (await...)" 을 더 간단히
재밌게도 CommonJS에서 이 패턴을 사용하는 것은 ES2015 모듈들보다 쉽고 더 직관적으로 보여진다. 그 이유는 CommonJS에서의 `default` export는 항상 암묵적이기 때문이다.

만약 우리가 모듈들을 위해 트랜스파일러를 사용하지 않는다면, 우리는 모듈 시스템으로서 우리가 할 수 있는게 없는 CommonJS를 목표로 삼았을 것이다. 모든 번들러들은 그냥 작동했을것이고, 어떤이들은 심지어 모든 모듈을 번들링하는 것 대신에 브라우저가 좀 더 똑똑하게 비동기적으로 모듈들을 요청하고 로드하도록 만들기로 결정했을지도 모른다.

그러나 우리는 트랜스파일러를 통해 코드를 작성한다면, 아마도 한번에 정적/동적인 모듈들을 import 하는 것을 포함하여 import하는 것을 간단히하고 싶을 것이다. 다음은 그런 기능을 갖기 위한 필요한 모든 것이다.

```javascript
// ES2017 version
export default (...modules) => Promise.all(
  // flatten arguments to enable both
  // imports([m1, m2]) or imports(m1, m2)
  modules.concat.apply([], modules)
  // asynchronously await each module
  // and return its default
  .map(async m => (await m).default)
);

// ES2015 simplified version
export default (...modules) => Promise.all(
  // flatten arguments to enable both
  // imports([m1, m2]) or imports(m1, m2)
  modules.concat.apply([], modules)
  // and per each resolved module
  // return its default
  .map(m => Promise.resolve(m).then(m => m.default))
);
```

작아보이는 만큼 위 모듈은 많은 동적인 imports들을 간소화할 수 있다.

```javascript
// grab the importer
import imports from './imports.js';

// export through the same pattern
export default new Promise(async $export => {

  // grab many modules at once
  const [a, b] = await imports(
    import('./a.js'),
    import('./b.js')
  );

  // export this module
  $export({name: 'c', a, b});

});
```

### 미래 증명과 미래 친화적
내가 이 패턴에 대해 의문들을 제기한 메일링 리스트(ML)에서, 어떤이는 이미 `default`을 포함한 모든 형태의 `export await`을 소개하는 가능성을 언급했다.

이것은 미래에는 정적인 imports 는 이 포스트에서 언급한 패턴과 호환 가능해짐을 의미한다. 그리고 차이점은 오직 `default`와 `new Promise` 사이에서의 `await` 키워드가 될 것이다.

```javascript
// keeping the same pattern
export default await new Promise(async $export => {
  // export the module
  $export({my: 'module'});
});

// dropping the Promise all together
export default await (async () => {
  // await async dependencies
  // export the module
  return {my: 'module'};
})();

// both static and dynamic importers will work
import module from './module.js';

// or
import('./module.js').then(m => {
  const module = m.default;
});
```

### ... 그러면 사용자 정의 요소는 어떡해?
당신이 맞아. 난 그것들을 언급하긴 했지만 아직 구체적인 예를 들지 않았고, 그래서 여기왔지. (갑자기 말투가...)

```javascript
// ./components/form-secure.js
const components = [
  'input-credit-card',
  'input-human'
];

export default new Promise(async $export => {

  // import all required sub components
  await Promise.all(components.map(
    c => import('./components/' + c + '.j')
  ));
  // await all definitions
  await Promise.all(components.map(
    c => customElements.whenDefined(c)
  ));

  // the Custom Element class
  class FormSecure extends HTMLElement {
    constructor() {
      const shadow = this.attachShadow({mode: 'close'});
      shadow.innerHTML = `
      <form>
        <input-credit-card>
        <input-human>
        <input type="submit">
      </form>`;
    }
  }

  // custom element definition
  customElements.define('form-secure', FormSecure);

  // export the class as default
  $export(FormSecure);

});
```

여기까지입니다.

역시나 직역 위주라 물 흐르듯이 읽히지는 않지만 개발자들은 글 보다는 코드가 눈에 더 잘 띌 것이고, 코드를 보면 이 글이 무엇을 말하고 싶은 지 알 수 있을 거라 생각이 되네요.


[imgae1]:https://cdn-images-1.medium.com/max/2000/1*ibrCviRleI9URWBbNPkDOA.png