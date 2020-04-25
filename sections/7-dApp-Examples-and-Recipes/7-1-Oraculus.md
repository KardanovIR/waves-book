# Oraculus

## Блокчейн и данные из реального мира

Блокчейн отлично работает с данными внутри себя, но не имеет никакого представления о том, что проиходжит за границами его консенсуса, а ведь там вся жизнь. Блокчейн не может сходить во внешний API и получить данные оттуда, потому что должна быть детерменированность операций. Если каждая нода будет ходить во внешний API в разное время, то какая-то может получить один результат, вторая - другой, третья - вообще получить ошибку, в итоге ноды блокчейна никогда не придут к консенсусу, ведь нельзя понять, какой ноде надо довериться. Чтобы решить эту проблему, вместо модели pull (когда ноды ходят за данными в реальный мир) принято применять модель push, когда есть какие-то поставщики данных, которые сохраняют данные в блокчейне, а дальше любые децентрализованные приложения в блокчейне используют эти данные. Сущности, которые сохраняют данные в блокчейне называются оракулами. Но с оракулами есть одна проблема - они централизованные, то есть мы доверяем одной сущности, которая поставляет нам какие-то данные. В общем случае, система является настолько централизованной, насколько централизованна самая "плохая" часть. То есть, децентрализованное приложение, которое использует для принятие критичных решений одного оракула на самом деле является централизованным приложением. Почему? Логика простая - повлияв на поведение одной сущности (оракула) можно добиться нужного поведения от такого приложения.

> Если децентрализованное приложение опирается на данные одного оракула, то такое приложение не является децентрализованным.

Идея сделать децентрализованных оракулов лежит на поверхности, но простого решения у этой проблемы нет, поэтому ее часто называют "проблемой оракулов". Давайте рассмотрим, что можно с этим сделать.

## Простейший вариант децентрализованных оракулов

Самым простым решением является мультиподпись, когда нескольько разных пользователей должны придти к консенсусу и подписать одни и те же данные. Например, мы хотим получать данные о курсе USD/EUR и у нас есть 5 оракулов, которые должны договориться о цене (каким-то образом за пределами блокчейна), подписать транзакцию и отправить ее в сеть на специальный аккаунт, который примет эту транзакцию только при наличии не менее 3 подписей из 5. Простейший контракт для такого случая выглядел бы так:

```scala
{-# STDLIB_VERSION 3 #-}
{-# CONTENT_TYPE EXPRESSION #-}
{-# SCRIPT_TYPE ACCOUNT #-}

# array of 5 public keys
let pks = [base58'', base58'', base58'', base58'', base58'']

# inner fold step for each signature
func signedBy(pk:ByteVector) = {
   # is signed by the public key or not
   func f(acc: Boolean, sig: ByteVector)
      = acc || sigVerify(tx.bodyBytes, sig, pk)
   FOLD<8>(tx.proofs, false, f)
}

# outer fold step for each public key
func signedFoldStep(acc:Int, pk:ByteVector)
   = acc + (if(signedBy(pk)) then 1 else 0)

# comparing total number of correct signatures
# to required number of correct signatures
FOLD<5>(pks, 0, signedFoldStep) >= 3
```

> Обратите внимание, эта функция является мультиподписью 3 из 5. В предыдущей главе мы рассматривали 2 из 3. В отличие от того примера, скрипт выше не делает допущений какая подпись где находится в массиве `proofs`, а проверяет каждое поле из `proofs` относительно каждого публичного ключа.

У такого решения вопроса есть несколько проблем:

- В случае отсутствия консенсуса у оракулов, данные просто не будут поставлены в блокчейн
- Отсутствует экономическая мотивация у поставщиков данных
- Заранее заданный и ограниченный список оракулов

Для решения этих проблем можно сделать полноценное децентрализованное приложение, которое будет из себя представлять маркетплейс, на котором встречаются 2 стороны:

1. Приложения, которым нужны данные
2. Оракулы, которые готовы поставлять данные в обмен на вознаграждение

Давайте сформулирует основные функциональные требования к такому приложению и реализуем его, используя Ride и блокчейн Waves.

## Децентрализованный оракул как dApp

Основные принципы работы децентрализованных оракулов должны быть следующими:

- Каждый желающий владелец любого децентрализованного приложения должен быть в состоянии запросить данные в определенном формате и с определенным вознаграждением для оракулов
- Каждый желающий должен быть в состоянии зарегистрировать своего оракула и отвечать на запросы, зарабатывая таким образом
- Все действия оракулов должны быть доступны для аудита

### Запрос данных

Любой аккаунт в блокчейне может "отправить запрос" оракулам. При отправке запроса необходимо прикладывать награду для оракулов за предоставление корректных данных (в токенах Waves). При задании вопросы обходимо указывать следуюшие параметры:

- `id` - уникальный идентификатор каждого вопроса, генерируется задающим вопрос. Требование - отсутствие такого же ключа у dApp, не больше 64 символов.
- `question` - непосредственно задаваемый вопрос. Вопрос формируется в специальном формате для каждого типа данных, в начале вопроса должен стоять тип данных, после разделителя `//` идут мета данные в формате JSON. Например, для типа данных `Temperature` вопрос выглядит следующим образом: `Temperature//{"lat": "55.7558", "lon": "37.6173", "timestamp": 150000000000, "responseFormat": "NN.NN"}`.
- `consensusType` - правило агрерации данных. Для строковых типов данных контракт предусматривает возможность только консенсуса (полное совпадение ответов), в то время как для числовых может быть `median` или `average`.
- `minOraclesCount` - минимальное количество оракулов, которые должны предоставить данные, чтобы можно было получить итоговый консенсус-результат. Значение не может быть меньше 3.
- `maxOraclesCount` - максимальное количество оракулов, которые могут ответить на вопрос. Не больше 6.
- `oraclesWhiteList` - список оракулов (публичные ключи через запятые), которые должны предоставить данные. Если значение параметра равно пустой строке, то любой оракул может ответить на запрос данных
- `tillHeight` - дедлайн до достижения консенсуса. Если до этого времени консенсус между оракулами не был достигнут (не набралось количество ответов > `minOraclesCount`), то отправитель запроса может забрать награду.

Формат типа запросов данных оставим на усмотрение отправителей запросов и оракулов, но в качестве примера предлагают следующие:

- `Temperature//{"lat": "55.7558", "lon": "37.6173", "timestamp": 150000000000, "responseFormat": "NN.NN"}`
- `Pricefeed//{"pair": "WAVES/USDN", "timestamp": 150000000000, "responseFormat": "NN.NNNN"}`
- `Sports//{"event": "WC2020", "timestamp": 150000000000, "responseFormat": "%s"}`
- `Random//{"round": 100, "responseFormat": "%s"}`

### Сбор ответов оракулов

Любой аккаунт Waves может зарегистрироваться как оракул определенного типа данных. Для этого будет достаточно вызвать метод децентрализованного приложения и передать в качестве аргумента тип поставляемых данных. Пример вызова может выглядеть следующим образом - `registerAsOracle("Temperature")`. В этот момент в стейте dApp будет зафиксировано в какой момент произошла регистрация оракула в качестве поставщика определенного типа данных и записано следующее: `{oraclePublicKey}_Temperature={current_height}`.

Ответ оракулов осуществляется с помощью метода `response(id: String, data: String)` и `responseNumber(id: String, data: Integer)` .

### Подсчет результатов

Для подсчета результатов необходимо вызвать метод `getResult(id: String)`. Подсчет результатов возможен только в том случае, когда ответили больше оракулов, чем указано в `minOraclesCount`. При выборе правильного ответа используется не простое большинство, а рейтинги оракулов. Рейтинг формируется по следующей логике:

- каждый оракул имеет рейтинг 100 при регистрации
- за каждый ответ, который в итоге стал результатом запроса, оракул получает +1 рейтинга, за каждый неверный -1

Давайте представим, что на запрос `Sports//{"event": "WorldCup2020", "timestamp": 150000000000, "responseFormat": "%s"}` ответили 5 оракулов со следующими рейтингами и ответами:

1. Oracle0, рейтинг = 102, ответ = "France"
2. Oracle1, рейтинг = 200, ответ = "Croatia
3. Oracle2, рейтинг = 63, ответ = "France"
4. Oracle3, рейтинг = 194, ответ = "France"
5. Oracle4, рейтинг = 94, ответ = "Croatia"

Итоговый результат будет `France`, ведь суммарный рейтинг оракулов с таким ответом составляет 359, в то время как рейтинг оракулов, ответивших `Croatia` 294.

В результате процедуры подсчета голосов рейтинги оракулов `Oracle0`, `Oracle2` и `Oracle3` будут увеличены на 1 и они смогут забрать вознаграждение, в то время как рейтинги `Oracle1` и `Oracle4` уменьшены на единицу и они не получат вознаграждения.

## Реализация

Давайте шаг за шагом начнем реализацию такого децентрализованного приложения. Логичнее всего начать с метода регистрации оракулов, который будет принимать в качестве аргумента тип предоставляемых оракулом данных. Если один оракул с одним публичным ключом предоставляет несколько типов данных, то он должен регистрироваться несколько раз.

```scala

@Callable(i)
func registerAsOracle(dataType: String) = {
    let neededKey = inv.callerPublicKey.toBase58String() + "_" + dataType
    let ratingKey = i.callerPublicKey.toBase58String() + "_rating"

    let currentRating = match getInteger(this, ratingKey) {
        case v: Int => v
        case _ => 100
    }
    match (getString(i.caller, neededKey)) {
        case data:String => throw("This oracle is registered already")
        case _ => {
            [
                StringEntry(neededKey, toString(height)),
                IntegerEntry(ratingKey, currentRating)
            ]
        }
    }
}
```

Следующим логичным шагом будет реализация функционала по отправке запросов на предоставление данных. Как мы уже описывали ранее, запрос на предоставление данных должен включать в себя следующие аргументы:

- `id` - уникальный идентификатор каждого вопроса.
- `question` - непосредственно задаваемый вопрос в заранее оговоренном формате.
- `consensusType` - правило агрерации данных, `consensus`, `median` или `average`. 
- `minOraclesCount` - минимальное количество оракулов.
- `maxOraclesCount` - максимальное количество оракулов.
- `oraclesWhiteList` - список оракулов (публичные ключи через запятые или пустая строка)
- `tillHeight` - дедлайн до достижения консенсуса.

Функция должна записывать в стейт контракта параметры запроса, сумму вознаграждения, а так же публичный ключ отправителя запроса и ключи, по которым в дальнейшем будем записывать количество ответов, сами ответы, публичные ключи ответивших оракулов и флаг завершения запроса.

В момент запроса данных необходимо проверять аргументы на следующие условия:

- Если задан white-list оракулов, то длина строки с их публичными адресами не должна быть больше 1000 символов (функция `checkOraclesWhiteListLengthLt1000`)
- Уникальный идентификатор запроса должен быть не более 32 символов (функция `checkRequestIdLt32`)
- Идентификатор запроса не должен быть раньше использован (функция `checkIdIsNotUsed`)
- У каждого запроса должно быть вознаграждение в токенах Waves (функция `checkPaymentInWavesGt0`)
- Минимальное количество оракулов должно быть не менее 3, а максимальное не более 6 (функция `checkOraclesCountGt3Lt6`)
- Значение минимального количества должно быть меньше либо равно, чем значение максимального количества (функция `checkOraclesWhiteListCountGtMinCount`)

Все листинга кода ниже включают в себя вызовы вспомогательных функций, которые в этой статье не показываются, однако вы модете найти их в репозитории [Ride examples](https://github.com/wavesplatform/ride-examples)

```scala
@Callable(i)
func request(id: String, question: String, minResponsesCount: Int, maxResponsesCount: Int, oraclesWhiteList: String, tillHeight: Int) = {
        let whiteList = checkOraclesWhiteListLengthLt1000(oraclesWhiteList)
        let checkedRequestIdLt64 = checkRequestIdLt32(id)
        let requestId = checkIdIsNotUsed(checkedRequestIdLt64)
        let paymentAmount = checkPaymentInWavesGt0(i.payments[0].extract())
        let minCount = checkOraclesCountGt3Lt6(minResponsesCount, maxResponsesCount)
        let maxCount = checkOraclesWhiteListCountGtMinCount(oraclesWhiteList, minCount, maxResponsesCount)
        let callerPubKey = toBase58String(i.callerPublicKey)
        [
            StringEntry(keyQuestion(requestId), question),
            StringEntry(keyOraclesWhiteList(requestId), whiteList),
            StringEntry(keyRequesterPk(requestId), callerPubKey),
            StringEntry(keyResponders(requestId), ""),
            StringEntry(requestId, question),
            IntegerEntry(keyMinResponsesCount(requestId), minCount),
            IntegerEntry(keyMaxResponsesCount(requestId), maxCount),
            IntegerEntry(keyResponsesCount(requestId), 0),
            IntegerEntry(keyTillHeight(requestId), tillHeight),
            IntegerEntry(keyRequestHeight(requestId), height),
            IntegerEntry(keyPayment(requestId), paymentAmount),
            BooleanEntry(keyRequestIsDone(id), false)
        ]
}

```

Отлично! Теперь у нас есть функции регистрации оракулов и отправки запросов от пользователей. Давайте теперь реализуем функционал отправки ответа от одного оракула.

### Ответ на запрос данных

Каждый оракул может ответить на запрос, если нет ограничений в виде white-list и запрос уже не закончился (по количеству ответов или длительности). В момент ответа на запрос, публичный ключ оракула и его ответ записываются в хранилище децентрализованного приложения, для этого в момент отправки запроса были созданы ключи `{id}_responders` и `{id}_responses`. Данный в этих ключах хранятся в виде строк с разделителем `;`.

```scala

@Callable(i)
func response(id: String, data: String) = {

    # Шаг 0 - проверка валидности предоставленных даннх
    let requestId = checkIdExists(id)
    let checkedData = checkDataIllegalCharacters(data)

    # Шаг 1 - проверка состояния запроса (количества ответов уже)
    let currentResponsesCount = getResponsesCount(id)
    let newResponsesCount = checkNewResponsesCount(currentResponsesCount, id)

    # Шаг 2 - проверка на вхождение оракула в white list и не ответил ли он уже на запрос
    let oraclePubKey = i.callerPublicKey.toBase58String()
    let oracleIsAllowed = checkOracleInWhiteList(oraclePubKey, id) == true || checkOracleResponded(oraclePubKey, id) == false
    let maxHeight = getIntegerValue(this, keyTillHeight(id))
    let isDone = getBooleanValue(this, keyRequestIsDone(id)) == true
    let requestIsActive = maxHeight > height || isDone

    if (oracleIsAllowed == false) then throw("Oracle is not in the white list or already responded") else
    if (requestIsActive == false) then throw("Request is not active anymore due to max height (" + maxHeight.toString() + "/" + height.toString() + ") or it is just done (" + isDone.toString() + ")")
    else {

        let currentResponders = getResponders(id)
        let currentResponses = getResponses(id)

        let newResponders = if currentResponders == "" then oraclePubKey
            else currentResponders + ";" + oraclePubKey
        let newResponses = if currentResponses == "" then checkedData
            else currentResponses + ";" + checkedData


        let currentResponsePoints = getCurrentResponsePoints(id, checkedData)

        let oracleRating = getOracleRating(oraclePubKey)

        let newResponsePoint = currentResponsePoints + if oracleRating < 200 then oracleRating / 3 else log(oracleRating, 0, 8, 0, 5, HALFEVEN)


        [
            IntegerEntry(keyCurrentResponsePoints(requestId, checkedData), newResponsePoint),
            IntegerEntry(keyResponsesCount(requestId), newResponsesCount),
            StringEntry(keyResponseFromOracle(requestId, oraclePubKey), checkedData),
            StringEntry(keyResponders(requestId), newResponders),
            StringEntry(keyResponses(requestId), newResponses),
            BooleanEntry(keyTookPayment(requestId, oraclePubKey), false),
            # StringEntry(keyOneResponse(requestId, i, checkedData), newResponders),newResponsePoint)
        ]
    }
}
```

Так же во время ответа мы увеличиваем счетчик количества ответивших на запрос и какое количество баллов набирает данный ответ (баллы равны сумме рейтингов оракулов).

### Сбор результатов

После отправки своих данных оракулами, должно быть подведение итогов, вырадающееся в выборе итогового результата (консенсусного решения, среднего или медианы) и изменении рейтингов оракулов. В этой функции мы так же могли бы выплатить сразу часть вознагрждения ответившим правильно, но в виду ограничения по сложности контракта в 4000, сделать в рамках одной функции не получится. Однако мы можем записать в хранилище аккаунта, кто имеет право забрать часть вознаграждения и позволить самим оракулам вызвать специальную функцию получения вознаграждения. Напомню, что только оракулы, чей ответ совпал с итоговым (или все оракулы с ответом в пределах 10% от результата если запрашивалось среднее или медиана) имеют право забрать часть ознаграждения.

```scala
@Callable(i)
func getResult(id: String) = {
    if (keyIsDefined(id) == false) then throwIdError(id) else {
        let responsesCount = getResponsesCount(id)
        let minResponsesCount = getMinResponsesCount(id)
        if (responsesCount < minResponsesCount) then throw("Minimum oracles count not reached yet") else {
            let result = calculateResult(id)
            let ratingsDiff = getOracleRatingsDiff(id, result)
            let resultKey = keyResult(id)
            let resultDataEntry = StringEntry(resultKey, result)
            let dataToWrite = cons(resultDataEntry, ratingsDiff)

            dataToWrite
        }
    }
}
```

### Получение вознаграждения

Функция получения вознаграждения для оракула должна позволять забрать средства только один раз и проверять, что этот оракул действительно отвечал на запрос и ответ его считается корректным.

```scala
@Callable(i)
func takeReward(id: String) = {
    if (keyIsDefined(id) == false) then throwIdError(id) else {
        let paymentValue = getIntegerValue(this, keyPayment(id))
        let oraclePubKey = i.callerPublicKey.toBase58String()
        let oracleResponseKey = keyResponseFromOracle(id, oraclePubKey)
        let oracleResponse = getStringValue(this, oracleResponseKey)

        let resultKey = keyResult(id)
        let resultDataEntry = getStringValue(this, resultKey)

        let alreadyTookKey = keyTookPayment(id, oraclePubKey)
        let alreadyTookPayment = getBooleanValue(this, alreadyTookKey)

        let responsesCount = getResponsesCount(id)

        if (oracleResponse == resultDataEntry && alreadyTookPayment == false) then {
            let paymentAmount = paymentValue / responsesCount
            [
              BooleanEntry(alreadyTookKey, true),
              ScriptTransfer(i.caller, paymentAmount, unit)
          ]
        }else {
            throw("Already took payment or provided data was not valid")
        }

    }
}
```

На этом основной функционал контракта для консенсуса оракулов готов. Примеры в виде тестов как работать с таким контрактом вы можете найти в репозитории [Ride examples](https://github.com/wavesplatform/ride-examples/branch/refactor)

## Возможности для развития

Релизованный функционал является простейшим вариантом работы децентрализованных оракулов. Мы решили проблемы, которые были обозначены в начале:

- В блокчейне всегда будут данные, даже если не все оракулы достигнут консенсуса
- У участников процесса появляется экономическая и репутационная мотивация участвовать в предоставлении данных
- Список оракулов может быть максимально широким, но в то же время его можно ограничить для своего запроса, если мы не хотим получать данные от любых оракулов, а только тех, которым мы доверяем.

Благодаря тому, что форматы запросов являются типизированными, предоставление ответов можно автоматизировать, например, в виде браузерного расширения, которое следит за запросами на адресе децентрализованного приложения и отвечает на данные, если тип запращиваемых данных поддерживается расширением. Можно представить сценарий, когда пользователи с открытым браузером могут зарабатывать на предоставлении данных, при этом не делая ничего для этого своими руками.

Во многих случаях данные необходимы не разово, а в виде постоянного потока. В нашем децентрализованном приложении функциональность подписки на данные не реализована, но мы будем рады участию нашего сообщества в доработке этого примера. Pull requests are welcome!
