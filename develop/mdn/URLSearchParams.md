> 이 글은 chat gpt로 번역한 Next.js 공식문서 입니다. 오직 개인의 학습을 위함이 목적입니다.

아래는 원문의 포맷과 내용을 그대로 유지하면서, 초보 개발자도 이해하기 쉬운 자연스러운 한국어 번역 결과입니다.

---

# URLSearchParams

**참고:** 이 기능은 [Web Workers](https://developer.mozilla.org/en-US/docs/Web/API/Web_Workers_API)에서 사용할 수 있습니다.

**`URLSearchParams`** 인터페이스는 URL의 쿼리 문자열을 다루기 위한 유틸리티 메서드들을 정의합니다.

`URLSearchParams` 객체는 [반복 가능한(iterable)](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#the_iterable_protocol) 객체이므로, 쿼리 문자열에 나타나는 순서대로 키/값 쌍을 반복(iterate)하기 위해 [`for...of`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of) 구조에서 직접 사용할 수 있습니다. 예를 들어, 다음 두 줄은 동일하게 동작합니다:

```js
for (const [key, value] of mySearchParams) {
}
for (const [key, value] of mySearchParams.entries()) {
}
```

`URLSearchParams`는 기능적으로 [`Map`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map)과 유사하지만, 내부 구현 방식 때문에 반복(iterating)할 때 `Map`에서는 발생하지 않는 몇 가지 [문제점](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols#concurrent_modifications_when_iterating)이 있을 수 있습니다.

## [생성자(Constructor)](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams#constructor)

[`URLSearchParams()`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams/URLSearchParams "URLSearchParams()")

`URLSearchParams` 객체 인스턴스를 반환합니다.

## [인스턴스 속성](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams#instance_properties)

[`size`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams/size "size") 읽기 전용

검색 파라미터 항목의 총 개수를 나타냅니다.

## [인스턴스 메서드](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams#instance_methods)

[`URLSearchParams[Symbol.iterator]()`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams#urlsearchparamssymbol.iterator)

이 객체에 포함된 모든 키/값 쌍을 쿼리 문자열에 나타난 순서대로 반복(iterate)할 수 있는 [`iterator`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)를 반환합니다.

[`URLSearchParams.append()`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams/append)

지정된 키/값 쌍을 새로운 검색 파라미터로 추가합니다.

[`URLSearchParams.delete()`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams/delete)

모든 검색 파라미터 목록에서 이름과 (선택적으로) 값이 일치하는 검색 파라미터를 삭제합니다.

[`URLSearchParams.entries()`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams/entries)

이 객체에 포함된 모든 키/값 쌍을 쿼리 문자열에 나타난 순서대로 반복(iterate)할 수 있는 [`iterator`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)를 반환합니다.

[`URLSearchParams.forEach()`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams/forEach)

콜백 함수를 통해 이 객체에 포함된 모든 값을 반복(iterate)할 수 있게 합니다.

[`URLSearchParams.get()`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams/get)

주어진 검색 파라미터와 연결된 첫 번째 값을 반환합니다.

[`URLSearchParams.getAll()`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams/getAll)

주어진 검색 파라미터와 연결된 모든 값을 반환합니다.

[`URLSearchParams.has()`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams/has)

주어진 파라미터 또는 파라미터와 값 쌍이 존재하는지를 나타내는 불리언 값을 반환합니다.

[`URLSearchParams.keys()`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams/keys)

이 객체에 포함된 키/값 쌍의 모든 키를 반복(iterate)할 수 있는 [`iterator`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)를 반환합니다.

[`URLSearchParams.set()`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams/set)

주어진 검색 파라미터에 해당하는 값을 지정된 값으로 설정합니다. 만약 여러 값이 있다면, 나머지 값들은 삭제됩니다.

[`URLSearchParams.sort()`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams/sort)

있을 경우, 모든 키/값 쌍을 키를 기준으로 정렬합니다.

[`URLSearchParams.toString()`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams/toString)

URL에서 사용하기에 적합한 쿼리 문자열을 포함하는 문자열을 반환합니다.

[`URLSearchParams.values()`](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams/values)

이 객체에 포함된 키/값 쌍의 모든 값을 반복(iterate)할 수 있는 [`iterator`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Iteration_protocols)를 반환합니다.

## [예제](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams#examples)

```js
const paramsString = "q=URLUtils.searchParams&topic=api";
const searchParams = new URLSearchParams(paramsString);

// Iterating the search parameters
for (const p of searchParams) {
  console.log(p);
}

console.log(searchParams.has("topic")); // true
console.log(searchParams.has("topic", "fish")); // false
console.log(searchParams.get("topic") === "api"); // true
console.log(searchParams.getAll("topic")); // ["api"]
console.log(searchParams.get("foo") === null); // true
console.log(searchParams.append("topic", "webdev"));
console.log(searchParams.toString()); // "q=URLUtils.searchParams&topic=api&topic=webdev"
console.log(searchParams.set("topic", "More webdev"));
console.log(searchParams.toString()); // "q=URLUtils.searchParams&topic=More+webdev"
console.log(searchParams.delete("topic"));
console.log(searchParams.toString()); // "q=URLUtils.searchParams"
```

### [중복 검색 파라미터](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams#duplicate_search_parameters)

```js
const paramStr = "foo=bar&foo=baz";
const searchParams = new URLSearchParams(paramStr);

console.log(searchParams.toString()); // "foo=bar&foo=baz"
console.log(searchParams.has("foo")); // true
console.log(searchParams.get("foo")); // bar, only returns the first value
console.log(searchParams.getAll("foo")); // ["bar", "baz"]
```

### [URL 파싱 없음](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams#no_url_parsing)

`URLSearchParams` 생성자는 전체 URL을 파싱하지 않습니다. 그러나 문자열의 시작에 `?`가 있을 경우 이를 제거합니다.

```js
const paramsString1 = "http://example.com/search?query=%40";
const searchParams1 = new URLSearchParams(paramsString1);

console.log(searchParams1.has("query")); // false
console.log(searchParams1.has("http://example.com/search?query")); // true

console.log(searchParams1.get("query")); // null
console.log(searchParams1.get("http://example.com/search?query")); // "@" (equivalent to decodeURIComponent('%40'))

const paramsString2 = "?query=value";
const searchParams2 = new URLSearchParams(paramsString2);
console.log(searchParams2.has("query")); // true

const url = new URL("http://example.com/search?query=%40");
const searchParams3 = new URLSearchParams(url.search);
console.log(searchParams3.has("query")); // true
```

### [퍼센트 인코딩](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams#percent_encoding)

`URLSearchParams` 객체는 [`application/x-www-form-urlencoded` 퍼센트 인코딩 집합](https://url.spec.whatwg.org/#application-x-www-form-urlencoded-percent-encode-set) (ASCII 영문자와 숫자, `*`, `-`, `.`, `_`를 제외한 모든 코드 포인트를 포함)을 대상으로 [퍼센트 인코딩](https://developer.mozilla.org/en-US/docs/Glossary/Percent-encoding)을 수행하며, U+0020 (공백)을 `+`로 인코딩합니다. 단, 전체 URL 검색 파라미터 구문을 직렬화 및 역직렬화할 때만 퍼센트 인코딩을 처리합니다. 개별 키와 값을 다룰 때는 항상 인코딩되지 않은 버전을 사용합니다.

```js
// Creation from parsing a string: percent-encoding is decoded
const params = new URLSearchParams("%24%25%26=%28%29%2B");
// Retrieving all keys/values: only decoded values are returned
console.log([...params]); // [["$%&", "()+"]]
// Getting an individual value: use the decoded key and get the decoded value
console.log(params.get("$%&")); // "()+"
console.log(params.get("%24%25%26")); // null
// Setting an individual value: use the unencoded key and value
params.append("$%&$#@+", "$#&*@#()+");
// Serializing: percent-encoding is applied
console.log(params.toString());
// "%24%25%26=%28%29%2B&%24%25%26%24%23%40%2B=%24%23%26*%40%23%28%29%2B"
```

퍼센트 인코딩된 키로 키/값 쌍을 추가하면, 해당 키는 인코딩되지 않은 것으로 취급되어 다시 인코딩됩니다.

```js
const params = new URLSearchParams();

params.append("%24%26", "value");
params.toString(); // "%2524%2526=value"
```

### [플러스 기호 보존](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams#preserving_plus_signs)

`URLSearchParams` 생성자는 플러스 기호 (`+`)를 공백으로 해석하기 때문에 문제가 발생할 수 있습니다. 아래 예제에서는 [16진수 이스케이프 시퀀스](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Lexical_grammar#hexadecimal_escape_sequences)를 사용하여 URL 검색 파라미터에 저장되어야 하는 바이너리 데이터를 포함하는 문자열을 흉내냅니다 (여기서 각 바이트는 정보를 담고 있습니다). `btoa()`가 생성한 인코딩된 문자열에 `+`가 포함되어 있고, 이것이 `URLSearchParams`에 의해 보존되지 않는 것을 확인할 수 있습니다.

```js
const rawData = "\x13à\x17@\x1F\x80";
const base64Data = btoa(rawData); // 'E+AXQB+A'

const searchParams = new URLSearchParams(`bin=${base64Data}`); // 'bin=E+AXQB+A'
const binQuery = searchParams.get("bin"); // 'E AXQB A', '+' is replaced by spaces

console.log(atob(binQuery) === rawData); // false
```

동적으로 보간(interpolated)된 문자열을 사용하여 `URLSearchParams` 객체를 생성하지 마세요. 대신 위에서 언급한 대로 모든 문자를 그대로 해석하는 `append()` 메서드를 사용하세요.

```js
const rawData = "\x13à\x17@\x1F\x80";
const base64Data = btoa(rawData); // 'E+AXQB+A'

const searchParams = new URLSearchParams();
searchParams.append("bin", base64Data); // 'bin=E%2BAXQB%2BA'
const binQuery = searchParams.get("bin"); // 'E+AXQB+A'

console.log(atob(binQuery) === rawData); // true
```

### [URL.searchParams와의 상호작용](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams#interaction_with_url.searchparams)

[`URL.searchParams`](https://developer.mozilla.org/en-US/docs/Web/API/URL/searchParams) 속성은 URL의 [`search`](https://developer.mozilla.org/en-US/docs/Web/API/URL/search "search") 문자열을 `URLSearchParams` 객체로 노출합니다. 이 `URLSearchParams`를 업데이트하면, URL의 `search` 문자열이 그 직렬화 결과로 업데이트됩니다. 단, `URL.search`는 `URLSearchParams`가 인코딩하는 문자 집합의 일부만을 인코딩하며, 공백은 `+` 대신 `%20`으로 인코딩합니다. 이로 인해 놀라운 상호작용이 발생할 수 있는데, 동일한 값을 사용하여 `searchParams`를 업데이트하더라도 URL이 다르게 직렬화될 수 있습니다.

```js
const url = new URL("https://example.com/?a=b ~");
console.log(url.href); // "https://example.com/?a=b%20~"
console.log(url.searchParams.toString()); // "a=b+%7E"
// This should be a no-op, but it changes the URL's query to the
// serialization of its searchParams
url.searchParams.sort();
console.log(url.href); // "https://example.com/?a=b+%7E"

const url2 = new URL("https://example.com?search=1234&param=my%20param");
console.log(url2.search); // "?search=1234&param=my%20param"
url2.searchParams.delete("search");
console.log(url2.search); // "?param=my+param"
```

### [빈 값과 값이 없는 경우](https://developer.mozilla.org/en-US/docs/Web/API/URLSearchParams#empty_value_vs._no_value)

`URLSearchParams`는 `=` 뒤에 아무것도 없는 파라미터와, `=` 자체가 없는 파라미터를 구분하지 않습니다.

```js
const emptyVal = new URLSearchParams("foo=&bar=baz");
console.log(emptyVal.get("foo")); // returns ''
const noEquals = new URLSearchParams("foo&bar=baz");
console.log(noEquals.get("foo")); // also returns ''
console.log(noEquals.toString()); // 'foo=&bar=baz'
```
