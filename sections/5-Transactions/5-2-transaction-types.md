# Типы транзакций



В Waves есть два типа транзакций, которые сейчас не используются и которые вам точно не пригодятся при работе в основной сети - `Genesis` и `Payment`. Долго останавливаться на этих типах транзакций не будем.

## Genesis транзакция (type = 1) [deprecated]

`Genesis` транзакции были только в [самом первом блоке блокчейна](http://nodes.wavesplatform.com/blocks/at/1) и отвечали за распределение предвыпущенных токенов. Их сразу было 100 миллионов. Давайте посмотрим как выглядел `genesis` блок.

**Примечание:** Многие путают `genesis` блок и `genesis` транзакции. `Genesis` блок - самый первый блок в блокчейн сети (во всех блокчейнах принято так называть), который отличается от остальных блоков только отсутствие ссылки на предыдущий блок, ведь его попросту не было. `Genesis` блок содержит `genesis` транзакции, которые отвечают за первоначальное распределение выпущенных токенов Waves.

```json
{
  "reference": "67rpwLCuS5DGA8KGZXKsVQ7dnPb9goRLoKfgGbLfQg9WoLUgNY77E2jT11fem3coV9nAkguBACzrU1iyZM4B8roQ",
  "blocksize": 500,
  "signature": "FSH8eAAzZNqnG8xgTZtz5xuLqXySsXgAjmFEC25hXMbEufiGjqWPnGCZFt6gLiVLJny16ipxRNAkkzjjhqTjBE2",
  "totalFee": 0,
  "nxt-consensus": {
    "base-target": 153722867,
    "generation-signature": "11111111111111111111111111111111"
  },
  "fee": 0,
  "generator": "3P274YB5qseSE9DTTL3bpSjosZrYBPDpJ8k",
  "transactionCount": 6,
  "transactions": [
    {
      "type": 1,
      "id": "2DVtfgXjpMeFf2PQCqvwxAiaGbiDsxDjSdNQkc5JQ74eWxjWFYgwvqzC4dn7iB1AhuM32WxEiVi1SGijsBtYQwn8",
      "fee": 0,
      "timestamp": 1465742577614,
      "signature": "2DVtfgXjpMeFf2PQCqvwxAiaGbiDsxDjSdNQkc5JQ74eWxjWFYgwvqzC4dn7iB1AhuM32WxEiVi1SGijsBtYQwn8",
      "recipient": "3PAWwWa6GbwcJaFzwqXQN5KQm7H96Y7SHTQ",
      "amount": 9999999500000000
    },
    {
      "type": 1,
      "id": "2TsxPS216SsZJAiep7HrjZ3stHERVkeZWjMPFcvMotrdGpFa6UCCmoFiBGNizx83Ks8DnP3qdwtJ8WFcN9J4exa3",
      "fee": 0,
      "timestamp": 1465742577614,
      "signature": "2TsxPS216SsZJAiep7HrjZ3stHERVkeZWjMPFcvMotrdGpFa6UCCmoFiBGNizx83Ks8DnP3qdwtJ8WFcN9J4exa3",
      "recipient": "3P8JdJGYc7vaLu4UXUZc1iRLdzrkGtdCyJM",
      "amount": 100000000
    },
    {
      "type": 1,
      "id": "3gF8LFjhnZdgEVjP7P6o1rvwapqdgxn7GCykCo8boEQRwxCufhrgqXwdYKEg29jyPWthLF5cFyYcKbAeFvhtRNTc",
      "fee": 0,
      "timestamp": 1465742577614,
      "signature": "3gF8LFjhnZdgEVjP7P6o1rvwapqdgxn7GCykCo8boEQRwxCufhrgqXwdYKEg29jyPWthLF5cFyYcKbAeFvhtRNTc",
      "recipient": "3PAGPDPqnGkyhcihyjMHe9v36Y4hkAh9yDy",
      "amount": 100000000
    },
    {
      "type": 1,
      "id": "5hjSPLDyqic7otvtTJgVv73H3o6GxgTBqFMTY2PqAFzw2GHAnoQddC4EgWWFrAiYrtPadMBUkoepnwFHV1yR6u6g",
      "fee": 0,
      "timestamp": 1465742577614,
      "signature": "5hjSPLDyqic7otvtTJgVv73H3o6GxgTBqFMTY2PqAFzw2GHAnoQddC4EgWWFrAiYrtPadMBUkoepnwFHV1yR6u6g",
      "recipient": "3P9o3ZYwtHkaU1KxsKkFjJqJKS3dLHLC9oF",
      "amount": 100000000
    },
    {
      "type": 1,
      "id": "ivP1MzTd28yuhJPkJsiurn2rH2hovXqxr7ybHZWoRGUYKazkfaL9MYoTUym4sFgwW7WB5V252QfeFTsM6Uiz3DM",
      "fee": 0,
      "timestamp": 1465742577614,
      "signature": "ivP1MzTd28yuhJPkJsiurn2rH2hovXqxr7ybHZWoRGUYKazkfaL9MYoTUym4sFgwW7WB5V252QfeFTsM6Uiz3DM",
      "recipient": "3PJaDyprvekvPXPuAtxrapacuDJopgJRaU3",
      "amount": 100000000
    },
    {
      "type": 1,
      "id": "29gnRjk8urzqc9kvqaxAfr6niQTuTZnq7LXDAbd77nydHkvrTA4oepoMLsiPkJ8wj2SeFB5KXASSPmbScvBbfLiV",
      "fee": 0,
      "timestamp": 1465742577614,
      "signature": "29gnRjk8urzqc9kvqaxAfr6niQTuTZnq7LXDAbd77nydHkvrTA4oepoMLsiPkJ8wj2SeFB5KXASSPmbScvBbfLiV",
      "recipient": "3PBWXDFUc86N2EQxKJmW8eFco65xTyMZx6J",
      "amount": 100000000
    }
  ],
  "version": 1,
  "timestamp": 1460678400000,
  "height": 1
}
```

Можно заметить, что было 6 публичных ключей-получателей свеже выпущенных токенов Waves. У всех транзакций одинаковый timestamp и они все были бесплатными (`fee` равен нулю), потому что нечем еще было платить `fee` на момент создания этих транзакций.

Эти транзакции созданы не вручную, они генерируются автоматически специальной утилитой `genesis-generator`, который есть в репозитории ноды. Вам это может понадобится сделать, если вы захотите запустить свой приватный блокчейн. Как это сделать (и зачем) мы рассмотрим в одной из следующих глав.

Внимательные читатели могут спросить, почему в самой первой транзакции отправляется `9999999500000000` токенов, если было выпущено всего 100 миллионов? В Waves во всех транзакциях счет идет минимальными неделимыми единицами токена (fraction). У токена Waves количество знаков после запятой (decimals) равно 8, поэтому минимальная единица - одна сто миллионная. Если в поле `amount` любой транзакции стоит значение `100000000` (10^8), это обозначает на самом деле один целый токен Waves. В случае с `genesis` транзакцией, `9999999500000000` означает 99 999 995 токенов или 9999999500000000 минимальных единиц. Минимальные единицы Waves часто называют WAVELET.

## Payment транзакция (type = 2) [deprecated]

В момент запуска блокчейна Waves было реализовано всего 2 типа транзакций - уже рассмотренный тип `genesis` и `payment`, который позволял переводить токены Waves c одного аккаунта на другой. Примеры транзакции `payment` в JSON представлении можно посмотреть в [блоке под номером 2000](http://nodes.wavesplatform.com/blocks/at/2000).

```json
    {
      "senderPublicKey": "6q5VhGeTanU5T8vWx6Jka3wsptPKSSHA9uXHwdvBMTMC",
      "amount": 10000000000,
      "sender": "3PGj6P4Mfzgo24i8cG3nhLU6uktF6s5LVCT",
      "feeAssetId": null,
      "signature": "3gzk9QyfqQGvsU8A4zMMorpKTcFpdG7UtC4c5E7ds9MGMCMSyp6JZymQJoCjUSJQ8AaSWQDQwNmQ5F46ud4ofA5o",
      "proofs": [
        "3gzk9QyfqQGvsU8A4zMMorpKTcFpdG7UtC4c5E7ds9MGMCMSyp6JZymQJoCjUSJQ8AaSWQDQwNmQ5F46ud4ofA5o"
      ],
      "fee": 1,
      "recipient": "3P59ixWkqiEnL7RJoXtZewgbatKBZo8bG15",
      "id": "3gzk9QyfqQGvsU8A4zMMorpKTcFpdG7UtC4c5E7ds9MGMCMSyp6JZymQJoCjUSJQ8AaSWQDQwNmQ5F46ud4ofA5o",
      "type": 2,
      "timestamp": 1465865163143
    }
```

`Payment` транзакция умеет отправлять только Waves токены (не кастомные ассеты) с одного адреса на другой. Она стала устаревшей с появлением `Transfer` транзакций, которые умеют отправлять как токены Waves, так и кастомные токены, поэтому сейчас `Payment` уже нигде не используется.

## Issue транзакция (type = 3)

D разделе про токены мы уже подробно рассматривали как выпустить свой ассет с помощью `Issue` транзакции, поэтому сейчас не буду останавливаться на том, как его использовать. Стоит только сказать, что отличительная особенность `Issue` транзакции в наличии 2 принципиально разных вариантов выпуска токена:

- выпуск уникального токена (он же не взаимозаменяемый токен, non-fungible token, NFT)
- выпуск обычного токена

Выпуск уникального токена отличается тем, что параметры `amount`, `reissuable`, `decimals` должны иметь заранее известные значения - `1`, `false` и `0` соответственно. При соблюдении этого условия минимальная комиссия составит 0.001 Waves. Если данные параметры отличаются (хотя бы один из параметров), то токен считается обычным и минимальная комиссия выпуска составит 1 Waves.

Пример JSON представления `Issue` транзакции представлен ниже:
```json
{
    senderPublicKey: "7nSKRN4XZiD3TGYsMRQGQejzP7x8EgiKoG2HcY7oYv6r",
    quantity: 210000000,
    signature: "3Vj8M9tkVZmnjdYAKKN3GzAtV9uQDX5hhgUfXQDdvZsk2AmvqQum3oGBJqdjALVHXX2ibLAZHeruwjNXR46WgBnm",
    fee: 100000000,
    description: "",
    type: 3,
    version: 1,
    reissuable: true,
    sender: "3PAJ6bw7kvSPf6Q9kAgfSLzmpFspZmsi1ki",
    feeAssetId: null,
    proofs: [
"3Vj8M9tkVZmnjdYAKKN3GzAtV9uQDX5hhgUfXQDdvZsk2AmvqQum3oGBJqdjALVHXX2ibLAZHeruwjNXR46WgBnm"
],
    assetId: "oWgJN6YGZFtZrV8BWQ1PGktZikgg7jzGmtm16Ktyvjd",
    decimals: 1,
    name: "ihodl",
    id: "oWgJN6YGZFtZrV8BWQ1PGktZikgg7jzGmtm16Ktyvjd",
    timestamp: 1528867061493,
    height: 1039500
}
```

## Tranfer транзакция (type = 4)

`Tranfser` транзакция пришла на замену `Payment` транзакции, потому что `Payment` не позволял отправлять токены, созданные с помощью `Issue` транзакции. В данный момент `Transfer` транзакция является наиболее часто встречающейся по данным [dev.pywaves.org](http://dev.pywaves.org/txs/) и составляет порядка 70% транзакций в сети. Отправка `Transfer` транзакции похожа на отправку большинства транзакций, связанных с токенами:

```js

const { transfer } = require('@waves/waves-transactions');

const seed = 'example seed phrase';

//Transfering 3 WAVES
const params = {
  amount: 300000000,
  recipient: '3P23fi1qfVw6RVDn4CH2a5nNouEtWNQ4THs',
  feeAssetId: null,
  assetId: null,
  attachment: 'TcgsE5ehTSPUftEquDt',
  fee: 100000,
}

const signedTransferTx = transfer(params, seed);
broadcast(signedTransferTx);

```

Пример выше сгенерирует транзакцию от аккаунта с сид фразой `example seed phrase`, автоматически подставит в созданную транзакцию дополнительные поля (`timestamp`,`senderPublicKey`, `proofs`), подпишет приватным ключом от указанной сид-фразы и добавит подпись транзакции в массив `proofs`.

Получаетелем транзакции является адрес `3P23fi1qfVw6RVDn4CH2a5nNouEtWNQ4THs`, а отправляем мы токены Waves. Чтобы вычислить сколько отправляется токенов нам надо вспомнить, что в транзакции указывается значение `amount` в минимальных фракциях этого токена. Чтобы получить в целых единицах, надо `300000000` разделить на `10^decimals`. `300000000 / (10^8) = 3`.

У `Transfer` транзакции есть несколько интересных особенностей:

- Она поддерживает спонсирование транзакций, поэтому в поле `feeAssetId` можно указать assetId какого-либо токена, который есть у вас и спонсируется создателем, тогда вы заплатите комиссию в этом токене. В нашем случае указано `null`, поэтому комиссия будет уплачиваться в Waves.
- У транзакции есть поле `attachment`, который может содержать до 140 байт информации. В библиотеке `waves-transactions` значение `attachment` надо передавать в формaте `base58`, поэтому вы видите `TcgsE5ehTSPUftEquDt`, хотя в "человеческом" представлении можно прочитать как `HelloWavesBook`.

`Transfer` транзакция позволяет указать в поле `amount` 0, то есть отправить 0 токенов получателю. Некоторые пользователи используют такую особенность для отправки `Transfer` транзакций как "сообщений" или событий, которые могут вызывать другие действия уже не в рамках блокчейна.

Пример `Transfer` транзакции представлен ниже:

```json
{
    senderPublicKey: "CXpZvRkJqBfnAw3wgaRbeNjtLJcithoyQQQSzGQZRF3x",
    amount: 32800000000,
    signature: "4cR2NAor9WjeTbysg2QMerkgymc5RLrX8PPjdXkUkWEc7BFBKMCCj8RKF7X1UchbvtEGoqGyQh62MDq5KoXsnCzg",
    fee: 100000,
    type: 4,
    version: 1,
    attachment: "",
    sender: "3P4FoAakEyk78TxUBcXH4uZXLaSE5BiDgjz",
    feeAssetId: null,
    proofs: [
"4cR2NAor9WjeTbysg2QMerkgymc5RLrX8PPjdXkUkWEc7BFBKMCCj8RKF7X1UchbvtEGoqGyQh62MDq5KoXsnCzg"
],
    assetId: null,
    recipient: "3PNX6XwMeEXaaP1rf5MCk8weYeF7z2vJZBg",
    feeAsset: null,
    id: "JAutkv1Nk4xVrkb4fkacS4451VvyHC3iJtEDfBRD7rwr",
    timestamp: 1528867058828,
    height: 1039500
}
```

## Reissue транзакция (type = 5)

Если при выпуске транзакции указать флаг `reissuable` в значение `true`, то создатель токена получает возможность перевыпускать токен. История `reissuable` транзакций в Waves немного странная, так как вы можете найти в блокчейне токены, которые в момент создания имели флаг `reissuable` равный `false`, но были перевыпущены. Таких токенов было всего 4, вот их assetId: `6SGeUizNdhLx8jEVcAtEsE7MGPHGYyvL2chdmPxDh51K`, `UUwsxTvvG7LiN7yaAKvNU48JHcSwQ3q1HvsXyAgc9fL`, `3DhpxLxUrotfXHcWKr4ivvLNVQUueJTSJL5AG4qB2E7U`, `CH1LNr9ASLVqSHDb482ZzSA5rBVLDtF5QbfECGgwE8bh`. Такое стало возможным благодаря багу в коде ноды, он позволял перевыпускать неперевыпускаемые токены. Данный был просуществовал непродолжительное время. Не удивляйтесь, если найдете перевыпущенные `non-reissuable` токены в истории mainnet Waves.

Пример `Reissue` транзакции представлен ниже:

```json
{
    senderPublicKey: "4X2Fv5XaDwBj2hjRghfqmsQDvBHqSa2zBUgZPDgySSJG",
    quantity: 10000000000000000,
    signature: "5nNrLV46rVzQzeScz3RmZF4rzaV2XaSjT9kjtHoyrBzAj3iVZM9Gy6t5Paho7xRx9dyqzj1AKyWYQsgL2nFa7jYU",
    fee: 100000000,
    type: 5,
    version: 1,
    reissuable: true,
    sender: "3P6ms9EotRX8JwSrebeTXYVnzpsGCrKWLv4",
    feeAssetId: null,
    chainId: null,
    proofs: [
"5nNrLV46rVzQzeScz3RmZF4rzaV2XaSjT9kjtHoyrBzAj3iVZM9Gy6t5Paho7xRx9dyqzj1AKyWYQsgL2nFa7jYU"
],
    assetId: "AC3KZWmywTEYrcQwpjg4sQiWxkZ2TZmv81JAvDmsoQvy",
    id: "6qd8QbnFrKEibTr26JyNh1hc4KaafGQYStyShtXdNk3v",
    timestamp: 1528733511933,
    height: 1037381
}
```

## Burn транзакция (type = 6)

Транзакция сжигания токенов позволяет сжечь любое количество токенов одного вида. Единственное условие - эти токены должны быть на вашем аккаунте. 

Пример `Burn` транзакции представлен ниже:

```json
{
    senderPublicKey: "EhuzuzEWHhZGo1th6YGy34AecoRP4sVi863xXCQUmgUT",
    amount: 10000000000,
    signature: "5HdfqY47Pm4G6h67K9ZpN7jQ4NKr9hsNsmTAtyFD5FhBPr3J9kNxodhYn6hMSieKE7UmYZvSohv7XJpyjKvGCfTC",
    fee: 100000,
    type: 6,
    version: 1,
    sender: "3PAjApsrjJWGmRDbGo65gGgrN2hFJroAZDC",
    feeAssetId: null,
    proofs: [
"5HdfqY47Pm4G6h67K9ZpN7jQ4NKr9hsNsmTAtyFD5FhBPr3J9kNxodhYn6hMSieKE7UmYZvSohv7XJpyjKvGCfTC"
],
    assetId: "56w2Jbj8MGKwSWyTXvCzkqKKHiyX7C2zrgCQb2CEwM52",
    id: "EzeiYzYPwyJNEgofQrE23rpqaYERjUSnCaXZ84vUDoec",
    timestamp: 1528814759445,
    height: 1038647
}
```

## Exchange транзакция (type = 7)

В предыдущей главе мы достаточно много говорили про процедуру обмена токенов, работу матчера и `Exchange` ордера. В том числе затронули тему, что транзакция содежит в себе ордера, и именно поэтому данная транзация является наиболее сложной в JSON представлении:

```json
{
    senderPublicKey: "7kPFrHDiGw1rCm7LPszuECwWYL3dMf6iMifLRDJQZMzy",
    amount: 74,
    signature: "2p1BS5BPkMW4C3C6vL8MsrQ8CBQRQqDoYieaZcxeMAq5zvAsm6T4N5DDN6MfPx8emVmbHfibZRsok2v2Ss45e1mj",
    fee: 300000,
    type: 7,
    version: 1,
    sellMatcherFee: 63610,
    sender: "3PJaDyprvekvPXPuAtxrapacuDJopgJRaU3",
    feeAssetId: null,
    proofs: [
"2p1BS5BPkMW4C3C6vL8MsrQ8CBQRQqDoYieaZcxeMAq5zvAsm6T4N5DDN6MfPx8emVmbHfibZRsok2v2Ss45e1mj"
],
    price: 103526336,
    id: "GHKhG3CWNfXAPWprk9bHSE4rxN6QfNDe3d3rZGaDLWhm",
    order2: {
    version: 1,
    id: "5C8qLi2eK92CJtBqXbL9pMuQ2R9VpRMaJ6NGACfxMBCn",
    sender: "3P7DsCo8TN5t1PNz45exhLe6vKFkTQJYrNb",
    senderPublicKey: "6mYVd69bZsLYW9gpxu3Vjneaf4xpZPnKYiLFuGXJQKQw",
    matcherPublicKey: "7kPFrHDiGw1rCm7LPszuECwWYL3dMf6iMifLRDJQZMzy",
    assetPair: {
    amountAsset: "725Yv9oceWsB4GsYwyy4A52kEwyVrL5avubkeChSnL46",
    priceAsset: null
},
    orderType: "sell",
    amount: 349,
    price: 103526336,
    timestamp: 1528814695617,
    expiration: 1528814995617,
    matcherFee: 300000,
    signature: "4DSQvXBLA4U4mtTRzjz62Ci757TZsys8phWbfnCmwvrKDhYFfB8kEknJ9fknAfWkJua7wN4EPbdrSLPgRShaxTsj",
    proofs: [
"4DSQvXBLA4U4mtTRzjz62Ci757TZsys8phWbfnCmwvrKDhYFfB8kEknJ9fknAfWkJua7wN4EPbdrSLPgRShaxTsj"
]
},
    order1: {
    version: 1,
    id: "Eiy6wSzu3aZu3V5Mi7VN54Vmu5KQE18nEQ3j5bJU2WYK",
    sender: "3PMFLMN9GG1coCXRn26vUmF2vtCCd4RDWRR",
    senderPublicKey: "Dk3r1HwVK1Ktp3MJCoAspNyyRpLFcs2h5SKsoV5F3Rvd",
    matcherPublicKey: "7kPFrHDiGw1rCm7LPszuECwWYL3dMf6iMifLRDJQZMzy",
    assetPair: {
    amountAsset: "725Yv9oceWsB4GsYwyy4A52kEwyVrL5avubkeChSnL46",
    priceAsset: null
},
    orderType: "buy",
    amount: 74,
    price: 103526336,
    timestamp: 1528814695596,
    expiration: 1528814995596,
    matcherFee: 300000,
    signature: "5kM8NRVxu4xtDUwz7GCVqyHbeszjXheJn1f7Q5Kpa4zdkeXe8k1kNENAU1YVNXyxNjMHCwtY9mwUkBpZWPo2CHWf",
    proofs: [
"5kM8NRVxu4xtDUwz7GCVqyHbeszjXheJn1f7Q5Kpa4zdkeXe8k1kNENAU1YVNXyxNjMHCwtY9mwUkBpZWPo2CHWf"
]
},
    buyMatcherFee: 300000,
    timestamp: 1528814695635,
    height: 1038644
}
```

Как вы можете заметить, транзакция содержит поля `order1` (ордер типа `buy`) и `order2` (транзакция типа `sell`). Так же присутствует подпись в массиве `proofs`, которая явлется подписью матчера (не отправителей ордеров), размер комиссии для матчера (`sellMatcherFee`), комиссия для ноды, которая смайнит блок (`fee`).

Значения полей `matcherPublicKey` в ордерах должно совпадать с полем `senderPublicKey` для `Exchange` транзации, что гарантирует невозможность совершения операции обмена с помощью этих ордеров другим матчером.

Формирование `Exchange` транзакции в большинстве случаев не нужно пользователям и разработчикам, поэтому не поддерживается во многих библиотеках для разных языков программирования. Другое дело - ордера, формирование которых необходимо для ботов и многих пользовательских интерфейсов. Формирование ордера с помощью `waves-tranasctions` принципиально не отличается от формирования транзакции:

```js
const { order } = require('@waves/waves-transactions')

const seed = 'b716885e9ba64442b4f1263c8e2d8671e98b800c60ec4dc2a27c83e5f9002b18'

const params = {
  amount: 100000000, //1 waves
  price: 10, //for 0.00000010 BTC
  priceAsset: '8LQW8f7P5d5PZM7GtZEBgaqRPGSzS3DfPuiXrURJ4AJS',
  matcherPublicKey: '7kPFrHDiGw1rCm7LPszuECwWYL3dMf6iMifLRDJQZMzy',
  orderType: 'buy'
}


const signedOrder = order(params, seed)
```

Обратите внимание, что в отличие от примеров с транзакциями, в примере не используется функция `broadcast` для отправки транзакции, потому что `broadcast` отправляет транзакцию в ноду, а нам необходимо отправлять в матчер. Информацию про API матчера можете найти в [документации waves.exchange](https://docs.waves.exchange/en/waves-matcher/matcher-api).

## Lease и Lease Cancel транзакции (type 8 и 9)

В самом начале этой книги мы немного затронули тему лизинга, который позволяет сдавать свои токены другим нодам "в аренду" для генерации блоков. Чтобы сделать это необходимо отправить транзакцию типа `Lease`.

```js
const { lease } = require('@waves/waves-transactions')

const seed = 'example seed phrase'

const params = {
  amount: 100,
  recipient: '3P23fi1qfVw6RVDn4CH2a5nNouEtWNQ4THs',
  fee: 100000
}

const signedLeaseTx = lease(params, seed)
broadcast(signedLeaseTx);
```

Как видите, транзакция предельно простая, указываем получателя в поле `recipient` в виде адреса или алиаса (про них поговорим ниже) и сумму, которую ходим отдать в лизинг. Необходимо учитывать, что участвовать в майнинге эти токены будут только спустя 1000 блоков после того, как они будут отправлены в лизинг.

Отправитель лизинга может в любой момент отменить лизинг, снова получая к ним доступ для торговли, переводов или майнинга на своем адресе. Для этого необходимо отправить транзакцию `LeaseCancel`:

```js
const { cancelLease } = require('@waves/waves-transactions')

const seed = 'example seed phrase'

const params = {
  leaseId: '2fYhSNrXpyKgbtHzh5tnpvnQYuL7JpBFMBthPSGFrqqg',
  senderPublicKey: '3SU7zKraQF8tQAF8Ho75MSVCBfirgaQviFXnseEw4PYg', //optional, by default derived from seed
  timestamp: Date.now(), // optional
  fee: 100000, //minimal value
  chainId: 'W' // optional
}

const signedCancelLeaseTx = cancelLease(params, seed)
broadcast(signedCancelLeaseTx);
```

Транзакция отмены лизинга требует передавать id транзакции отправки в лизинг. Отменять можно только всю транзакцию лизинга целиком. Например, если вы отправите в лизинг 1000 Waves любой ноде одной транзакций, вы не сможете забрать часть этой сумму - отмена может быть только целиком.

Обратите так же внимание, что в данной транзакции указывается `chainId`, в то время как в транзакции отправки лизинга, такого не требуется. Попробуйте угадать почему.

Ответ прост: в транзакции отправки лизинга есть поле `recipient`, куда указывается адрес и который и так содержит `chainId` в себе, а в транзакции отмены такого поля нет, поэтому, чтобы сделать невозможным отправку одной и той же транзакции в разных сетях, приходится указывать байт сети. Но если вы используете библиотеку `waves-transactions`, то она сама подставит байт сети для Mainnet, так что не переживайте.

Другое отличие отмены лизинга от отправки в лизинг в том, что, отмена начинает действовать сразу же, как попадает в блокчейн, без ожидания 1000 блоков.

## Alias транзакция (type = 10)

В Waves есть уникальная особенность, которой нет во многих других блокчейнах - наличие алиасов. Использовать адреса для совершения операций порой крайне неудобно, они длинные и их невозможно запомнить, поэтому каждый аккаунт может сделать себе алиас. Он может быть простым и легкозапоминаемым. В любой транзакции в сети Waves в поле `recipient` можно указывать не только адрес, но и алиас.

В Ethereum есть немного похожая концепция ENS, которая построена по принципам DNS, с разными уровнями (namespace) и управлением через смарт-контракты. В Waves алиасы являются частью протокола и все находятся в глобальном пространстве имен, не имея разделения на домены и поддомены. Один аккаунт может создавать неограниченное количество алиасов с помощью отправки специального типа транзакции:

```js
const { alias } = require('@waves/waves-transactions')

const seed = 'example seed phrase'

const params = {
  alias: 'new_alias',
  chainId: 'W'
}

const signedAliasTx = alias(params, seed)
broadcast(signedAliasTx)
```

Алиас может состоять из:

- буквы латинского алфавита в нижнем регистре
- цифры
- точка
- нижнее подчеркивание
- знак дефиса
- знак @

Алиас должен быть длиной от 4 до 30 символов. Проблема алиасов в сети Waves в том, что они все находятся в глобальном пространстве и не могут повторяться, поэтому есть аккаунтами с более чем 2000 алиасов - своебразная форма киберсквоттинга в блокчейне.

## Mass transfer транзакция (type = 11)

На заре своей истории Waves был известен как блокчейн с очень легким выпуском токенов, и закономерным желанием сообщества стало упрощение следующего шага многих кампаний по выпуску токенов - распределение токенов среди получателей. Для удовлетворения этого спроса была создана транзакция, которая позволяет отправить токены с одного адреса на множество. Есть только 2 ограничения - получаетелей может быть до 100, а отправляется им всем только 1 вид токена (нельзя сделать `MassTransfer` и отправить первой половине адресов токен `A`, а второй `B`).

```js
const { massTransfer } = require('@waves/waves-transactions')

const seed = 'example seed phrase'

const params = {
  transfers: [
    {
      amount: 100,
      recipient: '3P23fi1qfVw6RVDn4CH2a5nNouEtWNQ4THs',
    },
    {
      amount: 200,
      recipient: '3PPnqZznWJbPG2Z1Y35w8tZzskiq5AMfUXr',
    },
  ],
  //senderPublicKey: 'by default derived from seed',
  //timestamp: Date.now(),
  // fee: 100000 + transfers.length * 50000,
}

const signedMassTransferTx = massTransfer(params, seed);
broadcast(signedMassTransferTx);
```

Кроме удобства работы с такой транзакцией, по сравнению с отправкой 100 транзаций типа `Transfer`, такая транзакция получается еще и дешевле. Если минимальная комиссия для `Transfer` составляет 0.001 Waves (100000 Wavelet), то размер минимальной комиссии для `MassTransfer` вычисляется по формуле:

`100000 + transfers.length * 50000`

То есть, отправка 100 `Transfer` транзакций нам обойдется в 0.1 Waves, в то время как отправка одной `MassTransfer` со 100 получателями всего лишь в 0.051 Waves - почти в 2 раза дешевле.

## Data транзакция (type = 12)

Особенность Waves, которая делает его крайне удобным блокчейном для работы с данными, является наличие `Data` транзакций, которые появились в апреле 2018 года и позволили записывать данные в блокчейн в очень удобном формате.

С введением `Data` транзакций, у каждого аккаунта появилось key-value хранилище, в которое можно записывать данные четырех типов: строки, числа, булевые значения и массивы байт.

Хранилище аккаунта не имеет ограничения по общему размеру данных, которое можно туда записывать, но есть ограничения на:

- размер одной транзакции записи данных в хранилище не более 140 килобайт. Комиссия за транзакцию зависит от размера транзакции и считается по формуле `100000 + bytes.length * 100000`.
- размер данных на один ключ не более 32 килобайт
- размер ключа не более 100 символов. Ключами в хранилище могут быть только строки в формате UTF-8.

Давайте посмотрим как записать данные с помощью JavaScript библиотеки:

```js
const { data } = require('@waves/waves-transactions')

const seed = 'example seed phrase'

const params = {
  data: [
    { key: 'integerVal', value: 1 },
    { key: 'booleanVal', value: true },
    { key: 'stringVal', value: 'hello' },
    { key: 'binaryVal', value: [1, 2, 3, 4] },
  ],
  //senderPublicKey: 'by default derived from seed',
  //timestamp: Date.now(),
  //fee: 100000 + bytes.length * 100000
}

const signedDataTx = data(params, seed);
broadcast(signedDataTx);

```

Надо понимать, что состояние хранилища со всеми ключами и значениями может прочитать любой пользователь, более того, значение по любому ключу доступно всем смарт-контрактам в сети, будь то dapp, смарт ассет или смарт аккаунт.

Данные по ключу могут перезаписываться неограниченное количество раз, если обратное не указано в контракте аккаунта. В дальнейшем мы рассмотрим, как реализовать на аккаунте read-only пары, которые могут быть записаны только один раз и не могут быть изменены или удалены.

Многие пользователи ожидают, что у ассетов тоже есть свои key-value хранилища, однако это не так. Только аккаунт имеет такое хранилище, поэтому если вам необходимо записывать данные для использования ассетом - записывайте в аккаунт, который выпустил токен, или любой другой аккаунт, все равно можно читать любые ключи любых аккаунтов в коде вашего смарт-ассета.

Другой частый вопрос, можно ли удалить из хранилища ключ. До недавнего времени такое было невозможно, но с релизом Ride v4 это становится возможно. Чтобы сейчас не смешивать и Ride и транзакции, давайте отложим рассмотрение кода Ride до следующего раздела, но сейчас поговорим по получение данных их хранилища аккаунта. Это можно сделать с помощью REST запроса к API ноды:

1. Эндпоинт `/addresses/data/{address}?matches={regexp}` позволяет получить все данные из хранилища, при необходимости фильтрую ключи по регулярному выражению, передаваемому как параметр `matches`. Фильтрация по значениям пока не поддерживается в ноде.
2. Эндпоинт `/addresses/data/{address}/{key}` позволяет получить значение одного ключа в хранилище одного аккаунта.

В библиотеке `waves-transactions` есть дополнительные методы, которые позволяют делать это без необходимости писать самому запрос к API. Ниже пример получения всего состояния хранилища и значения по одному ключу:

```js
const { nodeInteractions } = require('@waves/waves-transactions')

const address = '3P23fi1qfVw6RVDn4CH2a5nNouEtWNQ4THs'

const wholeStorage = await accountData(address);
const oneKeyValue = await accountDataByKey(address, "demoKey");

console.log(wholeStorage, oneKeyValue);

```

Как видите, все достаточно несложно. У API ноды Waves есть несколько особенностей, некоторые из которых хорошо бы знать до начала работы, чтобы в самый неподходящий момент не получить ошибку в рантайме. К таким особенностям работы я бы отнес следующее:

1. Нода предназначена в первую очередь для поддержания работы блокчейна, а не оптимальной работы с API, поэтому запросы всего хранилища для аккаунтов с большим количеством данных могут приводить к проблемам. Я бы никогда и никому не рекомендовал запрашивать весь стейт аккаунта никогда, потому что если вы это делаете - вы что-то делаете не так.
2. Нода возвращает результаты в JSON, но в JSON нет возможности передавать массив байт как есть, поэтому в отличие от других типов данных (строк, чисел и булевых значений), они кодируются в `base64` представление. На самом деле, при записи данных типа массив байт в блокчейн с помощью `waves-transactions` он так же конвертирует байты в `base64` строку и отправляет это, а не массив байт в виде чиел. Вот, например, как выглядит сформированная транзация для отправки в API c помощью `POST` запроса:

```json

```

Будучи DevRel компании Waves я получал много вопросов относительно потенциально некотролируемого роста размера блокчейна. Многих людей, особенно у которых есть опыт работы с другими блокчейнами, смущает факт возможности записывать много данных по фиксированной и достаточно низкой цене и масштабируемость такого решения. В некоторых случаях (особенно долгосрочного хранения) блокчейн Waves может быть экономически эффективнее, чем хранение в Amazon S3, что потенциально опасно для масштабирования сети. Простого ответа на этот вопрос действительно нет, пока размер блокчейна Waves составляет порядка 40 гигабайт (не ~2.8 ТБ как в Ethereum), так что проблема не актуальна, зато простота записи позволяет делать "блокчейн для людей", о чем мы говорили в самом начале книги. Проблема станет актуальной только в случае быстрого роста популярности блокчейна Waves, но в таком случае будет расти и цена токенов, соответственно, стоимость хранилища тоже, что будет приводить к меньшему количеству желающих писать в блокчейн большие объемы данных. Там, где технология не могут полностью решить проблему, приходит на помощь экономика, что и будет происходить в случае роста популярности.

## SetScript транзакция (type = 13)

Транзакции типа `SetSсript` мы косвенно затрагивали, когда говорили про смарт-аккаунты. Логику поведения смарт-аккаунта и децентрализованных приложений мы описываем с помощью языка Ride, который компилируется в `base64` представление одним из доступных способов (JS бибиотека `ride-js`, API ноды, Java пакет в Maven, online IDE или плагин для Visual Studio Code) и отправляется в составе `SetScript` транзакции:

```js

const { setScript } = require('@waves/waves-transactions')
const seed = 'example seed phrase'
const params = {
  script: 'AQa3b8tH', // TRUE в base64 представлении
  //senderPublicKey: 'by default derived from seed',
  //timestamp: Date.now(),
  //fee: 100000,
  //chainId: 'W'
}

const signedSetScriptTx = setScript(params, seed)
broadcast(signedSetScriptTx);
```

`SetScript` транзакция используется только для аккаунтов, чтобы сделать из них Smart Account или dApp, но не для токенов.

## SetSponsorship транзакция (type = 14)

## SetAssetScript транзакция (type = 15)

## Invoke транзакция (type = 16)

## UpdateAssetInfo транзакция (type = 17) [stagenet]
