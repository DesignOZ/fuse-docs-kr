# API 목록

## Polyfills
FuseJS는 모든 플랫폼에서 (최소) EcmaScript 5.1 환경에서 실행됩니다. 브라우저가 포함되어있지는 않지만, 브라우저의 표준 라이브러리의 일부를 제공합니다. FuseJS는 몇가지 기능에 관해 주로 제공되는 서드파티 라이브러리에 polyfill을 제공합니다 (주로 브라우저 기능). 이러한 구현들은 아직 이 시점에선 완성되지 않았지만, ++[FuseJS 로드맵](https://www.fusetools.com/learn/fusejs#ref-fusejs-roadmap)++을 통해 개발이 얼마나 진행되고있는지 확인할 수 있습니다.

## fetch

다음은 HTTP 요청에 대한 대표적인 방법입니다.

다음은 `fetch`를 이용해 JavaScript 객체가 POST 메서드로 JSON 데이터를 보내고, 다시 JSON 데이터를 받아 JavaScript 객체로 반환하는 예제입니다.

```
var status = 0;
var response_ok = false;

fetch('http://example.com', {
    method: 'POST',
    headers: { "Content-type": "application/json"},
    body: JSON.stringify(requestObject)
}).then(function(response) {
    status = response.status;  // HTTP 상태 코드를 얻습니다.
    response_ok = response.ok; // Is response.status in the 200-range?
    return response.json();    // Promise를 반환합니다
}).then(function(responseObject) {
    // Do something with the result
}).catch(function(err) {
    // An error occured parsing Json
});
```
- ++[`fetch`](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)++에 대한 설명은 이곳에서 볼 수 있습니다.

<!-- '〉'는 '>'와는 다릅니다. ㄴ(니은) 특수문자 항목에 있습니다. -->

### XMLHttpRequest, Promise 〉
FuseJS는 `XMLHttpRequest`와 `Promise`를 지원하며, 각각의 자세한 정보는 아래의 주소를 참조해주세요:
- ++[https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)++
- ++[`Promise`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)++

<!-- 원래 XMLRequest 항목과 Promise 항목은 따로 있었지만 내용이 겹치기 때문에 합치게 되었습니다. -->

###setTimeout 〉
명령을 정해진 시간 뒤에 실행합니다:

```
setTimeout(function() { alert("Alert"); }, 2000);
```

`setTimeout`을 이용한 반복작업을 만들 수 있습니다.

```
function poller() {
    // 작업 내용 (...)

    // 1초 뒤에 다시 실행
    setTimeout(poller, 1000);
}
```

## 저장소
저장소 API를 이용해 다음과 같이 애플리케이션 경로에 파일을 저장할 수 있습니다.

```
var Storage = require('FuseJS/Storage');
```

### 쓰기
다음과 같이 문자열을 파일로 저장해봅시다.

```
Storage.write(filename, value)
```
- `filename` - 저장할 파일의 이름
- `value` - 파일에 쓰일 내용

아래와 같이 작성하였을 때 파일이 정상적으로 저장되면 `bool`값을 포함한 `Promise`가 반환됩니다.

```
storage.write("filename.txt", "filecontent");
```

### 읽기
```
Storage.read(filename)
```
- `filename` - 읽을 파일의 이름

아래와 같이 작성하였을 때 파일을 정상적으로 읽는다면 파일의 내용을 포함한 `문자열`을 포함한 `Promise`가 반환됩니다.

```
storage.read("filename.txt").then(function(content) {
    console.log(content);
}, function(error) {
    console.log(error);
});
```
플랫폼에 따라 실제 저장되는 경로는 다릅니다.

<!-- 
writeSync, readSync, deleteSync는 적절한 표현이 잘 생각나지 않아 동기적~, ~동기화를 생각하다 각각 동기적 쓰기, 읽기, 삭제로 작성하였습니다.
비슷하게나마 실시간(Realtime)이란 표현도 있지만 사용하지 않았습니다.
-->

### 동기적 파일 쓰기
애플리케이션 폴더에 다음과 같이 동기적 쓰기를 사용할 수 있습니다. 성공적으로 쓰기작업이 완료된다면 `true`값이 반환됩니다.

```
var wasWritten = storage.writeSync("filename.txt", "filecontent");
```

> 주의: 동기적 쓰기 호출이 되지 않는 경우, 자료의 양이 너무 많다는 의미이므로 ++[Storage.write](https://www.fusetools.com/learn/fusejs#ref-storage-write)++를 사용해주세요.

### 동기적 파일 읽기
다음과 같이 동기적 읽기를 사용할 수 있습니다.
```
var data = storage.readSync("filename.txt");
```

> 주의: 동기적 파일 쓰기와 마찬가지로 마찬가지로, 자료의 양이 너무 많아 호출할 수 없는 경우 ++[Storage.read](https://www.fusetools.com/learn/fusejs#ref-storage-read)++를 사욯해주세요.

###동기적 파일 삭제
다음과 같이 애플리케이션 폴더에서 파일을 삭제합니다. 성공적으로 삭제작업이 완료되면 `true`값이 반환됩니다:

```
var wasDeleted = storage.deleteSync("filename.txt");
```

<!-- 의역 함정 -->

## 라이프사이클
JavaScript를 이용해 라이프사이클 이벤트를 조정할 수 있습니다:

```
var Lifecycle = require('FuseJS/Lifecycle');
Lifecycle.onEnteringForeground = function() {
      initialize();
};
```
라이프사이클 이벤트는 다음을 따릅니다:

앱 시작 이벤트의 경우 JavaScript 코드의 첫 부분에 묵시적으로 명시되어있습니다.

- onEnteringForeground - 앱이 대기상태를 벗어나 실행되고있는 상태입니다. 앱을 시작할 때 이 이벤트를 얻습니다.
- onEnteringbackground - 앱이 대기상태에 진입한 상태입니다.
- onEnteringInteractive - 앱이 이벤트를 수신하는 상호작용상태로 진입합니다.
- onExitedInteractive - 앱이 상호작용상태에서 벗어납니다.
- onTerminating - 앱이 종료됩니다.

### 전화 API
다음과 같이 Phone-API를 호출하여 전화를 사용할 수 있습니다.

```
var Phone = require('FuseJS/Phone');
Phone.call("number");
```

### 카메라 API
사용할 기기의 카메라에 접근하여 사진을 찍을 수 있습니다.

```
var Camera = require('FuseJS/Camera');
```

아래와 같이 사용할 수 있습니다.
```
Camera.takePicture({ targetWidth: 640, targetHeight: 360}).then(function(file)
{
    // file is a standard JavaScript File object
    // file.path contains the path to the saved image on the device
}).catch(function(e) {
    console.log(e);
});
```

#### 촬영 기능
각각의 옵션은 다음을 따릅니다
- `targetWidth` - 촬영할 사진의 가로 해상도를 조절합니다.
- `targetHeight` - 촬영할 사진의 세로 해상도를 조절합니다.
>위의 두 옵션은 생략할 시 기본값으로 지정됩니다.
- `correctOrientation` - 사진의 방향을 지정합니다.

촬영한 `사진파일`이 JPG 포맷으로 저장소에 저장이 되면 `Promise`가 반환됩니다.

`targetWidth`와 `targetHeight`는 단지 힌트일 뿐이며, 반환된 이미지는 어느 크기나 가능합니다.


### 진동
기기의 진동 기능에 접근합니다.

```
var Vibration = require('FuseJS/Vibration');
```

다음과 같이 사용할 수 있습니다.

```
Vibration.vibrate(200);
```
200밀리초 동안 진동을 사용합니다.
