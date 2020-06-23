# Взаимодействия с пользователем

Во всех примерах до этого мы рассматривали либо статичные сид фразы прямо в коде, либо генерировали сид фразы для пользователей, однако в реальной разработке Web3 приложений нам чаще необходимо взаимодействовать с пользователем для получения подписи. При этом, запрашивать у него сид фразу или приватный ключ мы не можем, так как это совершенно не безопасно. Имея приватный ключ или сид фразу пользователя, можно делать любые действия с его аккаунтом, поэтому децентрализованные приложения запрашивают подпись для каждого конкретного действия.

Давайте сравним логику работы обычного приложения и Web3 приложения, чтобы нагляднее понять разницу в логике. Начнем с обычного веб-приложения, логика работы которого сводится к следующим шагам:

1. Веб браузер запрашивает некий URL
2. Веб сервер передает в виде ответа HTML, CSS, JavaScript и данные
3. Пользователь видит отрисованную страницу с данными
4. Пользователь совершает какие-либо действия (нажатия на кнопки, отправка форм), после которых логика приложения (JavaScript) делает XHR (AJAX) запросы на сервер
5. Веб сервер отвечает новой порцией HTML, CSS, JavaScript и данных

![Web2 app](../../assets/8-1-0-web2-app.png "Web2 app")

Логика работы Web3 приложения другая:

1. Веб браузер запрашивает некий URL
2. Веб сервер передает в виде ответа HTML, CSS, JavaScript и данные
3. JavaScript код запрашивает дополнительные данные у блокчейн ноды
4. Пользователь видит отрисованную страницу с данными
5. Пользователь совершает какие-либо действия для чтения (нажатия на кнопки, отправка форм), после которых логика приложения (JavaScript) делает XHR (AJAX) запросы на сервер **и/или к ноде блокчейна**
6. Веб сервер отвечает новой порцией HTML, CSS, JavaScript и данных
7. Для случаев **записи и обновления данных**, перед отправкой запроса к ноде блокчейна, клиентский код на JavaScript **запрашивает подпись пользователя**

![Web3 app](../../assets/8-1-0-web3-app.png "Web3 app")

Существует несколько способов работы с ключами пользователей, и самым распространенным является использование браузерных расширений. В экосистеме Waves принято использование расширения Waves Keeper, которое хранит сид-фразы и позволяет подписывать действия пользователя.

## Waves Keeper

Waves Keeper позволяет пользователям управлять своими аккаунтами:

1. Создавать новые аккаунты со случайной сид фразой или добавлять уже созданные
2. Отображать балансы токена Waves на каждом аккаунте
3. Подписывать транзакции и другие данные
4. Работать с разными сетями (mainnet, stagenet, testnet или приватная).

Waves Keeper доступен для пользователей Google Chrome, Firefox, Edge, Opera, Brave. Ссылки на скачивание вы можете найти [на странице продукта на сайте протокола](https://wavesprotocol.org/protocol/keeper).

В отличие от расширений для других протоколов, например, Metamask для Ethereum, Waves Keeper не является полноценным кошельком, так как не позволяет смотреть историю транзакций или создать транзакцию (только подписать ее).

<div style="text-align: center">

<img src="../../assets/8-1-0-keeper.png" style="max-width: 60%; max-height: 400px">
</div>

> Waves Keeper - безопасный способ управления ключами и взаимодействия с децентрализованными приложениями

Сид фразы в Waves Keeper хранятся в локальном хранилище расширения в зашифрованном с помощью пароля виде и не доступны для веб сайтов. Им доступен объект `WavesKeeper` в глобальной области видимости, который предоставляет API для работы с ключами. Давайте рассмотрим основные методы, которые могут быть полезны при интеграции Waves Keeper в ваш пользовательский интерфейс.

Объект `WavesKeeper` содержит следующие методы:

```js
WavesKeeper = {
  auth: function (){},
  publicState: function (){},
  signAndPublishCancelOrder: function (){},
  signAndPublishOrder: function (){},
  signAndPublishTransaction: function (){},
  signCancelOrder: function (){},
  signOrder: function (){},
  signTransaction: function (){},
  signRequest: function (){},
  signTransactionPackage: function (){},
  signCustomData: function (){},
  verifyCustomData: function (){},
  notification: function (){},
  encryptMessage: function (){},
  decryptMessage: function (){},
  resourceIsApproved: function (){},
  resourceIsBlocked: function (){},
  on: function (){}
}
```

Большинство методов являются асинхронными и возвращают `Promise`. Примеры использования некоторых методов доступны на демо-странице [в моем Github репозитории](https://kardanovir.github.io/WavesKeeper/).

Метод `auth` является одним из наиболее часто используемых и позволяет запросить у пользователя информацию о его аккаунте, активном в момент вызова. В функцию `auth` необходимо передать информацию о приложении и данные для подписи. Waves Keeper вернет нам не только информацию об аккаунте, но и подписанные данные из поля `data`, что позволит в коде нашего приложения валидировать подпись и убедиться, что у пользователя есть ключ от аккаунта с указанным публичным ключом и адресом, и он не пытается сломать нашу логику, например, заменяя глобальный объект `WavesKeeper`.

```js
WavesKeeper.auth({name: 'MyApp', data: 'Custom data to Sign', icon: 'https://docs.wavesplatform.com/_theme/brand-logo/waves-docs-logo.png'})
  .then(function (res) {
    // res будет содержать информацию об аккаунте
  })
  .catch(function(err){console.log(err)});

```

После вызова метода будет возвращен `Promise`, который успешно разрешится, если пользователь даст разрешение на предоставление доступа, или завершится ошибкой, если отказал в доступе.

В переменной `res` будет содержаться объект:

```json
{
    "data": "Custom data to Sign",
    "prefix": "WavesWalletAuthentication",
    "host": "localhost",
    "name": "MyApp",
    "icon": "https://docs.wavesplatform.com/_theme/brand-logo/waves-docs-logo.png",
    "timestamp": 1543175910353,
    "address": "3PKqkMWvjjwjqbVSu8eL48dNfzWc3ifaaWi",
    "publicKey": "4WLcUznGiQXCoy2TnCohGKzDR8c14LFUGezvLNu7CVPA",
    "signature": "4s2nz8RxT29UwbJoNjPWxYwjsXYeoaMWK4dDM5eQN5gRmeZWGrN1HbpsirhTzWMJFAGtzzw4U78RNRKeEtwficwR"
}
```

Вместе с адресом аккаунта Keeper возвращает публичный ключ аккаунта, префикс и подпись. Чтобы окончательно убедиться, что у пользователя есть этот аккаунт, необходимо проверить подпись от `префикс + данные` для этого публичного ключа и убедиться, что этот публичный ключ преобразуется именно в такой адрес.

```js

import { address, stringToBytes, verifySignature } from  '@waves/ts-lib-crypto'

const res = {
    "data":"Custom data to Sign",
    "prefix":"WavesWalletAuthentication",
    ...,
    "address":"3PKqkMWvjjwjqbVSu8eL48dNfzWc3ifaaWi",
    "publicKey":"4WLcUznGiQXCoy2TnCohGKzDR8c14LFUGezvLNu7CVPA",
    "signature":"4s2nz8RxT29UwbJoNjPWxYwjsXYeoaMWK4dDM5eQN5gRmeZWGrN1HbpsirhTzWMJFAGtzzw4U78RNRKeEtwficwR"
}

const signedBytes = stringToBytes(res.prefix + res.data)

verifySignature(res.publicKey, signedBytes, res.signature) && address({public: res.publicKey}) === res.address

```

Waves Keeper может так же подписывать транзакции, ордера для матчера, запросы для матчера (и сразу отправлять их в сеть, если необходимо) и случайный набор байт. Для подписания транзакции без отправки используется функция `sign`:

```js
WavesKeeper.sign({
  type: 4, // 4 - transfer transaction
  data: {
    amount: {
      assetId: 'WAVES',
      tokens: "0.00100000"
    },
    fee: {
      assetId: 'WAVES',
      tokens: "0.00100000"
    },
    recipient: '3P9E5QeGSF4As6kNtBi8j476gsM8mqnX12f'
  }
})
.then(function(res){
  // в переменной res будет содерджаться:
  //{
  //  "type" : 4,
  //  "id" : "2p8zC1riEZpC19PHuqndyaBnr9ndS6jGvFKyTbX2Qpyq",
  //  "sender" : "3PGiGn5K5zRgU7o3EfvqFeTR91shNAPyFaa",
  //  "senderPublicKey" : "DoQ87i3F9yAX21LrMijEszqMKAHuR867ZFfeXN7UCLe3",
  //  "fee" : 100000,
  //  "timestamp" : 1543228114324,
  //  "proofs" : [ "58U8fr9hUKir9WJkJtHV3eUNV7giCnFX42uDHwtdWW6Rq34P9BMXWEWuVLct1qgp1jhwvAJnvmPqGYZYknSQfW1o" ],
  //  "version" : 2,
  //  "recipient" : "3P9E5QeGSF4As6kNtBi8j476gsM8mqnX12f",
  //  "assetId" : null,
  //  "feeAssetId" : null,
  //  "feeAsset" : null,
  //  "amount" : 100000,
  //  "attachment" : ""
  //}
})
.catch(function(err){
    console.log(err);
});

```

Обратите внимание, что Waves Keeper принимает не такой же объект, как `waves-transactions`. Подробнее информацию об API Waves Keeper можете найти в документации на странице [Github репозитория проекта](https://github.com/wavesplatform/waveskeeper).

## Waves Signer

Пользовательский опыт - одна из самых сложных частей разработки децентрализованных приложений. На мой взгляд, одной из ключевых причин отсутствия массовой популярности технологии является довольно большое количество барьеров для пользователей. Порой очень сложно начать использовать блокчейн. Waves Keeper хоть и является крайне безопасным инструментом, но так же является барьером, который требует скачивания расширения. Waves Signer призван решить эту проблему.

Waves Signer является оберткой над разными провайдерами, которые непосредственно хранят ключи и подписывают транзакции. Сейчас доступен один провайдер для Waves Signer - провайдер Waves.Exchange, но в будущем будут доступны провайдеры для Waves Keeper, криптокошелька Ledger и десктопного клиента Waves.Exchange.

![Waves Signer scheme](../../assets/8-1-1-signer.png "Waves Signer scheme")

Существующий провайдер от Waves.Exchange предлагает совершенно новый пользовательский опыт. В отличие от Waves Keeper, где ключи хранятся в расширении, провайдер Waves.Exchange хранит зашифрованные ключи в `localStorage` сайта `https://waves.exchange` и предоставляет пользователю интерфейс в виде `ifаme` окна, где он может согласиться подписать транзакцию или отклонить ее.

![Waves Signer example](../../assets/8-1-2-signer-example.png "Waves Signer example")

Такой вариант подписи является менее безопасным, так как:

- Пользователь доверяет свои ключи странице `https://waves.exchange`. Важно понимать, что страница никуда ее не отправляет и хранит локально на устройстве пользователя, но в теории, в любой момент в будущем может начать отправлять на сервер
- В момент подтвеждения подписи транзакции, другие вредоносные скрипты могут перекрыть окно провайдера и дать пользователю подписать другие данные, не те, которые он видит на экране

Но данные риски являются компромиссными, чтобы не заставлять пользователей скачивать браузерное расширение.

Давайте рассмотрим, как интегрировать Waves Signer с провайдером от Waves.Exchange на вашу страницу. Для начала создадим кнопки, в которым мы привяжем действия пользователя и подключим скрипт, где будем описывать логику:

```js
<main>
    <button class="js-login">Authorization</button><br><br>
    <button class="js-invoke">Invoke Script</button><br>
</main>
<script src="../dist/example.js"></script>
```

В файле `example.js` подключим сам Signer и провайдер для Waves.exchange:

```js
import Waves from "@waves/signer";
import Provider from "@waves.exchange/provider-web";

// настройки для testnet
const waves = new Waves({NODE_URL: 'https://pool.testnet.wavesnodes.com'});
// Настройки для провайдера Waves.exchange
const provider = new Provider('https://testnet.waves.exchange/signer/');
waves.setProvider(provider);

```

Для того, чтобы в момент нажатия на кнопку появлялось окно Waves Signer, привяжем callback на событие нажатия:

```js
document.querySelector(".js-login").addEventListener("click", async function(event) {
    try {
        const userData = await waves.login();  // Вызываем Waves Signer 
        event.target.classList.add("clicked");
        event.target.innerHTML = `
            authorized as <br>
            ${userData.address}`;              // Получаем адрес пользователя
    } catch (e) {
        console.error('login rejected')        // пользователь отклонил запрос авторизации
    }
});
```

После нажатия кнопки и разрешения авторизации, адрес пользователя появится на самой кнопке.

Давайте рассмотрим пример создания и отправки транзакции по нажатию на кнопку:

```js
document.querySelector(".js-invoke").addEventListener("click", function() {
    waves.invoke({
        dApp: "3MuN7D8r19zdvSpAd1L91Gs88bcgwUFy2mn",
        call: {
            function: "faucet"
        }
    }).broadcast().then(console.log)
});
```

API для формирования транзакций совпадает с тем, что передается в `waves-transaction`, что может быть крайне удобно во время разработки.

Более подробную информацию о Waves Signer и пример интеграции вы сможете найти в статье [Владимира Журавлева на medium](https://medium.com/@izhur27/getting-started-with-waves-signer-893017c9b7ae).
