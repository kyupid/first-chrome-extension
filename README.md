### Note

![2021-10-23_21-58-42](https://user-images.githubusercontent.com/59721293/138557377-a7e212bf-81a2-4aff-ac9b-efc93480a6d0.jpg)

When you click a link "background page" the console page would pop up and execute a file "background.js" immediately.

And when the refresh button is pressed, it is refreshed literally.

foreground.js는 background.js와 달리 자동으로 실행되지않는다.

---

### Note 2

```js
chrome.tabs.executeScript(null, { file: "./foreground.js" }, () =>
  console.log("test")
);
```

MDN에 따르면 JavaScrpt APIs 에서 tabs API가 있고 그안에 excuteScript 함수가 있다.
용도는 js코드를 페이지로 inject 하는 것이다
inject 한다는 말은 말그대로 그 자바스크립트를 열린 해당 사이트에서 사용가능하도록 주입한다는 이야기이다.
추가적으로 자바스크립트 js 주입 공격 테스트 및 방지에 대해서 [이 링크](https://ko.myservername.com/javascript-injection-tutorial)에서 자세히 볼수있다.

어쨋든 저 코드를 넣고 다시 로드해본다.
그럼 Error 라는 빨갛게 된게 버튼이 뜬다.
이버튼을 클릭하면 콘솔에 나왔던 에러가 그대로 나온다.
로드하면 `chrome://extensions`에 뭐 주입할수없다 이런 이야기인듯 하다.

---

### Note 3

```js
chrome.tabs.onActivated.addListener((tab) => {
  console.log(tab);
});
```

background.js에 위 코드를 넣고 실행하고 background 콘솔을 보면
탭을 클릭할때마다 background가 뭔가 계속 listening 하는것을 볼수있다.
이걸로 어떤 도메인에 있는지 알 수가 있음.

---

### Note 4

```js
chrome.tabs.onActivated.addListener((tab) => {
  chrome.tabs.get(tab.tabId, (current_tab_info) => {
    if (/^https:\/\/www\.google/.test(current_tab_info.url)) {
      chrome.tabs.executeScript(null, { file: "./foreground.js" }, () =>
        console.log("i injected")
      );
    }
  });
});
```

코드 해석: 구글 홈페이지이면 foreground.js 를 실행한다. `i injected`는 background.js에서 나타남. 

아래 이미지 참고

![2021-10-23_22-56-35](https://user-images.githubusercontent.com/59721293/138559484-230b88a3-f0e6-4fa2-a7f2-716bc3f78d99.jpg)

---

### Note 5
```js
// background.js

chrome.tabs.onActivated.addListener((tab) => {
  chrome.tabs.get(tab.tabId, (current_tab_info) => {
    if (/^https:\/\/www\.google/.test(current_tab_info.url)) {
      chrome.tabs.insertCSS(null, { file: "./mystyle.css" });
      chrome.tabs.executeScript(null, { file: "./foreground.js" }, () =>
        console.log("i injected")
      );
    }
  });
});

```

```js
// foreground.js

document.querySelector(".rSk4se img").classList.add("spinspinspin");
```
이미지 돌리는 css 만들고 `querySelecter.classList.add`해서 이미지 돌리는 클래스를 넣는다.

![Oct-23-2021 23-17-30](https://user-images.githubusercontent.com/59721293/138560245-ee1d4240-ff1a-4ca6-82da-704d536f00e4.gif)

---

### Note 6

![2021-10-23_23-28-29](https://user-images.githubusercontent.com/59721293/138560571-b570b32f-c19a-4c10-bf53-54419fbb44a9.jpg)

option이랑 popup은 위 이미지에 있는거 클릭하면나온다

extension 아이콘을 누르면 popup이고 option은 deatils 항목에 들어가서 저걸 누르면 된다

---

### Note 7

백엔드와 프론트엔드.

설명:

1. foregrounds.js 에서 first button을 클릭했을때 local storage에 `password: "123"`을 저장한다.
2. second button을 클릭했을 때 runtime에서 Message를 보낸다
3. background.js 에서 request.message가 second button에서 보낸 message가 같으면 local storage에서 "password"를 get 한다. 

![2021-10-23_23-53-35](https://user-images.githubusercontent.com/59721293/138561363-7fd56881-68d4-46b4-9ecb-e700363c20e3.jpg)
![2021-10-23_23-52-47](https://user-images.githubusercontent.com/59721293/138561371-64c2e59b-a65b-4c2b-b66f-d0b7eaa0fba7.jpg)

