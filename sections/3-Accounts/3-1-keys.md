# Ключи в блокчейне Waves

Первое, с чем сталкивается человек, когда начинает пользоваться блокчейном - работа с ключами. В отличие от классических веб приложений, где у нас есть логин и пароль, в блокчейнах есть только ключи, которые позволяют идентифицировать пользователя и валидность его действий.

У каждого аккаунта есть публичный ключ и соответствующий ему приватный. Публичный ключ является фактически идентификатором аккаунта, в то время как приватный позволяет сформировать подпись. В Waves используется подписи с использованием [кривой Curve25519-Ed25519](https://en.wikipedia.org/wiki/EdDSA#Ed25519) с ключами X25519 (что иногда является проблемой, потому что поддержка ключей X25519 есть далеко не во всех библиотеках).

Публичный и приватный ключи представляют из себя 32 байтовые значения, которые соответствуют другу по определенным правилам (подробнее можете найти в описании [EdDSA](https://blog.filippo.io/using-ed25519-keys-for-encryption/)). Важно понимать несколько вещей, которые отличаются по сравнению с другими блокченами:

- не любые 32 байта могут быть приватным ключом
- приватный ключ не содержит в себе публичный ключ (например, в Ethereum приватный ключ содержит приватный, поэтому имеет размер в 64 байта, в Waves публичный ключ вычисляется каждый раз для приватного ключа)
- подпись с помощью EdDSA является недетерменированной, то есть одни и те же данные можно подписать одним и тем же ключом и получать разные подписи, так как используются и случайные значения

## Путешествия ключа

Большинство пользователей все-таки сталкивается с ключами не в виде массива байт, а в виде сид-фразы, часто так же называемой мнемонической. Любая комбинация байт может быть сидом, но в клиентах Waves обычно используется 15 английских слов. На основе сид фразы вычисляется приватный ключ следующим образом:

1. строка переводится в массив байт
2. вычисляется хэш `blake2b256` для данного массива байт
3. вычисляется хэш `keccak256` для результата предыдущего шага
4. вычисляется приватный ключ на основе предыдущего шага, пример функции для этого шага представлен ниже

```go
func GenerateSecretKey(hash []byte) SecretKey {
    var sk SecretKey
    copy(sk[:], hash[:SecretKeySize])
    sk[0] &= 248
    sk[31] &= 127
    sk[31] |= 64
    return sk
}

```

Иными словами, а точнее кодом:
`privateKey = GenerateSecretKey(keccak256(blake2b256(accountSeedBytes)))`

Публичный и приватный ключи обычно представляют в виде `base58` строк вроде `3kMEhU5z3v8bmer1ERFUUhW58Dtuhyo9hE5vrhjqAWYT`.

При отправке транзакций (например, отправке токенов) пользователь имеет дело с адресом, а не публичным ключом получателя. Но адрес генерируется из публичного ключа получателя с некоторыми дополнительными параметрами: версия спецификации адреса, байт сети и чек-сумма. В данный момент в сети Waves есть только одна версия адресов, поэтому первым байтом в этой последоствальности является `1`, второй байт - уникальный идентификатор сети, который позволяет отличать адреса в разных сетях (mainnet, testnet, stagenet). Байты сети для перечисленных выше сетей `W`, `T`, `S` соответственно. Благодаря байту сети невозможно ошибиться и отправить токены на адрес, которого не может существовать в сети, в которой отправляется транзакция. После первых двух служебных байт идут 20 байт, полученных в результате функций хэширования blake2b256 и keccak256 над публичным ключом. Эта операция `keccak256(blake2b256(publicKey))` возвращает 32 байта, но последние 12 байт отбрасываются. Последние 4 байта в адресе являются чек-суммой, которая считается как `keccak256(blake2b256(data))`, где `data` это первые 3 параметра (версия, байт сети и 20 байт хэша публичного ключа). Полученная последовательность байт переводится в base58 представление, чтобы получилось похожее на это: `3PPbMwqLtwBGcJrTA5whqJfY95GqnNnFMDX`.

Опытные разработчики на Waves пользуются особенностями формирования адресов, чтобы по одному виду определять к какой сети относится адрес. Благодаря тому, что первые 2 байта в адресе похожи для всех адресов в одной сети, можно примерно понимать к какой сети относится адрес. Если адрес выглядит как `3P...`, то адрес с большой долей вероятности относится к mainnet, а если адрес начинается с `3M...` или `3N`, то перед вами скорее всего адрес из testnet или stagenet.

## Работа с ключами

Если по какой-то причине, приложение необходимо генерировать ключи для пользователя, то можно воспользоваться библиотеками для разных языков программирования. Например, в библиотеке `waves-transactions` для JavaScript/TypeScript сгенерировать seed фразу можно с помощью следующего кода:

```js
import {seedUtils} from '@waves/waves-transactions'

const seedPhrase = seedUtils.generateNewSeed(24);

console.log(seedPhrase);

// infant history cram push sight outer off light desert slow tape correct chuckle chat mechanic jacket camp guide need scale twelve else hard cement
```

В консоль выведется строка из 24 слов, которые являются seed фразой нового аккаунта. Эти слова являются случайным подмножеством из словаря, который есть в [коде библиотеки @waves/ts-lib-crypto](https://github.com/wavesplatform/ts-lib-crypto/blob/master/src/crypto/seed-words-list.ts) и в котором содержится 2048 слов.

В данном примере я сгенерировал 24 слова, но по умолчанию во многих приложениях Waves генерируется набор из 15 слов. Почему именно 15 и увеличивается ли безопасность, если сгенерировать больше слов?

15 слов из 2048 в любом порядке достаточно, для того, чтобы вероятность генерации двух одинаковых seed фраз была пренебрежительно мала. В то же время, 24 слова еще уменьшают такую вероятность, почему бы не использовать большие значения? Ответ прост - чем больше слов мы используем, тем больше надо записывать и/или запоминать пользователю и тем сложнее ему будет. Смысл использования seed фразы (а не приватного ключа) именно в упрощении опыта пользователя, а с 24 словами мы заметно ухудшаем user experience.

Имея seed фразу можно получить приватный ключ, публичный и адрес. Я снова покажу как это сделать на JS, но вы же помните, что есть библиотеки и для других языков?

```js

import {seedUtils} from '@waves/waves-transactions';
import {
  address,
  privateKey,
  publicKey
} from '@waves/ts-lib-crypto'


const seedPhrase = seedUtils.generateNewSeed(24);

console.log(privateKey(seedPhrase));   // 3kMEhU5z3v8bmer1ERFUUhW58Dtuhyo9hE5vrhjqAWYT
console.log(publicKey(seedPhrase));    // HBqhfdFASRQ5eBBpu2y6c6KKi1az6bMx8v1JxX4iW1Q8
console.log(address(seedPhrase, 'W')); // 3PPbMwqLtwBGcJrTA5whqJfY95GqnNnFMDX
```

Обратите внимание, что в функции `privateKey` и `publicKey` мы передаем только сид фразу, в то время как в `address` передаем еще один параметр `chainId` (он же байт сети). Как вы помните из объяснения выше, адрес в себе содержит такой дополнительный параметр.

## Как аккаунт появляется в блокчейне

Мы разобрали как работают ключи, как связаны сид фраза, приватный и публичный ключ, а также как к ним относится адрес, но я не упомянул один очень важный момент, о котором забывают некоторые начинающие разработчики. До момента совершения какого-либо действия с аккаунтом (отправка с него или на него транзакции), блокчейн ничего не знает об этом аккаунте. Если вы сгенерировали аккаунт (у себя локально или в любом клиенте), но в блокчейне не блоы транзакций, связанных с этим аккаунтом (входящих или исходящих), вы не сможете найти никакую информацию о вашем аккаунте в эксплорере или с помощью API. Это отличается от поведения в централизованных системах и API, поэтому может быть не так интуитивно понятным и простым, но об этом важно помнить.