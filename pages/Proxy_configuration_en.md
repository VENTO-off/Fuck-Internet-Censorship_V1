# Proxy configuration guide

## 1. Google Chrome configuration
### 1.1 Install browser extension
Install [Proxy Switcher and Manager](https://chrome.google.com/webstore/detail/proxy-switcher-and-manage/onnfghpihccifgojkpnnncpagjcdbjod) in Google Chrome.

### 1.2 Configure proxy connection
Switch `PAC Script` tab and paste the next code in `Inline` section:
```javascript
var proxy = "IP:PORT";
//proxy = "";

var proxy_all = false;
//proxy_all = true;

var hosts = [
    'userapi.com',
    'vk.cc',
    'vk.com',
    'vk.me',
    'vk.link',
    'vk-cdn.net',
    'vkuservideo.net',
    'vkuserlive.com',
    'vkuseraudio.net',
    'vkforms.ru',
    'vkuserdocs.net',
    'vk-apps.com',
    'vkpay.io',
    'vk-portal.net',
    'vkontakte.ru',
    'vkuser.net',
    'mycdn.me',
    'ok.ru',
    'mail.ru',
    'attachmail.ru',
    'datacloudmail.ru',
    'imgsmail.ru',
    'ya.ru',
    'yadi.sk',
    'yandex.ru',
    'yandex.com',
    'yandex.ua',
    'yastatic.net',
    'yandex.net',
    'clstorage.net',
    'narod.ru',
    'dzen.ru',
    'webmoney.ru',
    'wmtransfer.com',
    'kinopoisk.ru',
    'lovina.app',
    'mradx.net',
    'tns-counter.ru',
    'cdnland.in',
    'svetacdn.in',
    'yoomoney.ru',
    'beget.com',
    'beget.ru',
    'beget.email',
    'reg.ru',
    'habr.com',
    'more.tv'
];

var domains = [
    'ru',
    'su'
];

function FindProxyForURL(url, host) {
    let uri = host.split('.').reverse();
    let domain = uri[1] + '.' + uri[0];
    if (proxy_all || hosts.includes(domain) || domains.includes(uri[0])) {
        return "PROXY " + proxy;
    }

    return "DIRECT";
}
```