# CSS injection

팝업창이나 background에서 동작한다면, `chrome.tabs.insertCSS` 를 사용할 수 있다.

content\_script 등을 지정해서 특정 조건에서만 동작하게 했다면, 위 메소드는 사용할 수 없다.

대신, 동적으로 파일을 읽어서 head에 추가하는 방법이 있는데, 그러기 위해서는 먼저 그 파일을 ["web\_accessible\_resources"](https://developer.chrome.com/extensions/manifest/web_accessible_resources) 에 추가하여 권한을 먼저 등록해주어야한다.

Links

* [https://stackoverflow.com/questions/33716881/chrome-extension-how-to-insert-css-conditioned-on-variable-from-chrome-storage/33719884](https://stackoverflow.com/questions/33716881/chrome-extension-how-to-insert-css-conditioned-on-variable-from-chrome-storage/33719884)

