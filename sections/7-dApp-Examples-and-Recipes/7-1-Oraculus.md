# Глава 7. Практикум: пишем Web3 приложения

## Oraculus

Лучший способ выучить язык - начать на нем писать. Заставить вас писать я не могу, поэтому предложу вам второй по крутизне вариант - читать как пишется код! Мы разберем несколько примеров контрактов, начнем с относительно простых, и закончим на достаточно сложных и разухабистых.

## Блокчейн и данные из реального мира

Блокчейн отлично работает с помещенными в него данными, но не имеет никакого представления о том, что происходит в реальном мире. Блокчейн не может, например, обратиться к внешнему API и получить из него данные, потому что нарушится детерменированность операций. Если каждая нода обратится к внешнему API в разное время, все они могут получить разные результаты, и в итоге ноды блокчейна никогда не придут к консенсусу, потому что неясно, какой из них надо довериться.
Чтобы решить эту проблему, вместо модели “pull” (когда обращаются данными в реальный мир) принято применять модель “push”, при которой поставщики сохраняют данные в блокчейне, а любые децентрализованные приложения могут их использовать.
Сущности, которые сохраняют данные в блокчейне, называются оракулами. Но проблема оракулов в том, что они - централизованные, то есть мы доверяем одной сущности, поставляющей данные. В общем случае система является настолько централизованной, насколько централизована самая "плохая" ее часть. То есть децентрализованное приложение, которое использует для принятия важных решений данные одного оракула, на самом деле является централизованным. Почему? Логика простая: повлияв на поведение одной сущности - оракула, - можно добиться нужного поведения от всего приложения.
Идея децентрализованных оракулов лежит на поверхности, но простого решения здесь нет, и поэтому часто говорят о "проблеме оракулов". Давайте рассмотрим, как ее можно решить.

> Если децентрализованное приложение опирается на данные одного оракула, то такое приложение не является децентрализованным.

## Простейший вариант децентрализованных оракулов

Самым простым решением является мультиподпись: несколько пользователей приходят к консенсусу и подписывают одни и те же данные. Например, мы хотим получать данные о курсе USD/EUR, и у нас есть пять оракулов, которые должны договориться о консенсусном значении (за пределами блокчейна), подписать транзакцию и отправить ее в сеть на специальный аккаунт, который примет ее только при наличии не менее трех подписей из пяти. Простейший контракт в этом случае выглядел бы так:

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

Но у такого подхода есть несколько проблем:

- При отсутствии у оракулов консенсуса данные просто не поступят в блокчейн
- У поставщиков данных нет экономической мотивации 
- Заранее заданный ограниченный список оракулов.

Для решения этих проблем можно сделать полноценное децентрализованное приложение, представляющее собой маркетплейс, на котором встречаются две стороны:

1. Приложения, которым нужны данные
2. Оракулы, готовые поставлять эти данные за вознаграждение

Давайте сформулируем основные функциональные требования к такому приложению и реализуем его, используя Ride и блокчейн Waves.

## Децентрализованный оракул как dApp

Основные принципы работы децентрализованных оракулов будут такими:

- Владелец любого децентрализованного приложения должен иметь возможность запросить данные в определенном формате и с определенным вознаграждением для оракулов
- Любой желающий должен быть в состоянии зарегистрировать свой оракул и отвечать на запросы, получая за это вознаграждение
- Все действия оракулов должны быть доступны для аудита.

### Запрос данных

Любой аккаунт в блокчейне может отправить вопрос оракулам. При отправке вопроса необходимо приложить награду для оракулов за предоставление корректных данных (в токенах WAVES). В вопросе необходимо указать следующие параметры:

- `id` - уникальный идентификатор каждого вопроса, генерируется его отправителем. Требования: отсутствие такого же ключа у dApp, не больше 64 символов.
- `question` - собственно вопрос. Он формируется в специальном формате для каждого типа данных. В начале вопроса нужно указать тип данных, после разделителя // идут метаданные в формате JSON. Например, для типа данных Temperature вопрос выглядит так: `Temperature//{"lat": "55.7558", "lon": "37.6173", "timestamp": 150000000000, "responseFormat": "NN.NN"}`
- `consensusType` - правило агрегации данных. Для строковых типов данных контракт предусматривает только консенсус (полное совпадение ответов), а для числовых возможны также `median` и `average`
- `minOraclesCount` - минимальное число оракулов, которые должны предоставить данные для получения итогового консенсус-результата. Значение не может быть меньше 3
- `maxOraclesCount` - максимальное число оракулов, которые могут ответить на вопрос. Не больше 6
- `oraclesWhiteList` - список оракулов (публичные ключи через запятые), которые должны предоставить данные. Если значение параметра равно пустой строке, то на запрос данных может ответить любой оракул
- `tillHeight` - дедлайн достижения консенсуса. Если до этого времени консенсус между оракулами не был достигнут (не набралось количество ответов > `minOraclesCount`), то отправитель запроса может забрать вознаграждение.

Формат типа запросов данных оставим на усмотрение отправителей запросов и оракулов, но в качестве примера предлагаю следующие:

- `Temperature//{"lat": "55.7558", "lon": "37.6173", "timestamp": 150000000000, "responseFormat": "NN.NN"}`
- `Pricefeed//{"pair": "WAVES/USDN", "timestamp": 150000000000, "responseFormat": "NN.NNNN"}`
- `Sports//{"event": "WC2020", "timestamp": 150000000000, "responseFormat": "%s"}`
- `Random//{"round": 100, "responseFormat": "%s"}`

### Сбор ответов оракулов

Любой аккаунт Waves может зарегистрироваться в качестве оракула определенного типа данных. Для этого достаточно вызвать метод децентрализованного приложения и передать в качестве аргумента тип поставляемых данных. Пример вызова может выглядеть так - `registerAsOracle("Temperature")`. В этот момент в стейте dApp будет зафиксировано, в какой момент произошла регистрация оракула в качестве поставщика определенного типа данных, и записано следующее: `{oraclePublicKey}_Temperature={current_height}`.

Оракул отвечает с помощью метода `response(id: String, data: String)` и `responseNumber(id: String, data: Integer)`.

### Подсчет результатов

Для подсчета результатов необходимо вызвать метод `getResult(id: String)`. Подсчет результатов возможен только в том случае, если ответили больше оракулов, чем указано в minOraclesCount. При выборе правильного ответа используется не простое большинство, а рейтинги оракулов. Рейтинг формируется по следующей логике:

- при регистрации каждый оракул имеет рейтинг 100
- за каждый ответ, который в итоге стал результатом запроса, к рейтингу оракула прибавляется +1 рейтинга, за каждый неверный ответ - -1.

Давайте представим, что на запрос `Sports//{"event": "WorldCup2020", "timestamp": 150000000000, "responseFormat": "%s"}` ответили 5 оракулов со следующими рейтингами и ответами:

1. Oracle0, рейтинг = 102, ответ = "France"
2. Oracle1, рейтинг = 200, ответ = "Croatia
3. Oracle2, рейтинг = 63, ответ = "France"
4. Oracle3, рейтинг = 194, ответ = "France"
5. Oracle4, рейтинг = 94, ответ = "Croatia"

Итоговый результат будет `France`, так как суммарный рейтинг оракулов с таким ответом составляет 359, а рейтинг оракулов, ответивших Croatia, равен 294.

В результате процедуры подсчета голосов рейтинги оракулов `Oracle0`, `Oracle2` и `Oracle3` будут увеличены на 1, и они смогут забрать вознаграждение, в то время как рейтинги `Oracle1` и `Oracle4` будут уменьшены на единицу, и они не получат вознаграждения.

## Реализация

Рассмотрим пошаговую реализацию такого децентрализованного приложения. Логичнее всего начать с метода регистрации оракулов, который будет принимать в качестве аргумента тип предоставляемых оракулом данных. Если один оракул с одним публичным ключом предоставляет несколько типов данных, он должен регистрироваться несколько раз.

```scala

@Callable(i)
func registerAsOracle(dataType: String) = {
    let neededKey = i.callerPublicKey.toBase58String() + "_" + dataType
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

Следующим логичным шагом будет реализация функциональности по отправке запросов на предоставление данных. Как описано выше, запрос на предоставление данных должен включать в себя следующие аргументы:

- `id` - уникальный идентификатор каждого вопроса
- `question` - собственно вопрос в заранее оговоренном формате
- `consensusType` - правило агрерации данных: `consensus`, `median` или `average`.
- `minOraclesCount` - минимальное количество оракулов.
- `maxOraclesCount` - максимальное количество оракулов.
- `oraclesWhiteList` - список оракулов (публичные ключи через запятые или пустая строка)
- `tillHeight` - дедлайн до достижения консенсуса.

Функция должна записывать в стейт контракта параметры запроса, сумму вознаграждения, а также публичный ключ отправителя запроса и ключи, по которым в дальнейшем мы будем записывать количество ответов, сами ответы, публичные ключи ответивших оракулов и флаг завершения запроса.

В момент запроса данных необходимо проверять аргументы на следующие условия:

- Если задан “белый список” оракулов, то длина строки с их публичными адресами не должна превышать 1000 символов (функция `checkOraclesWhiteListLengthLt1000`)
- Уникальный идентификатор запроса не должен превышать 32 символа (функция `checkRequestIdLt32`)
- Идентификатор запроса не должен быть использован ранее (функция `checkIdIsNotUsed`)
- У каждого запроса должно быть вознаграждение в токенах WAVES (функция `checkPaymentInWavesGt0`)
- Минимальное число оракулов - 3, а максимальное - 6 (функция `checkOraclesCountGt3Lt6`)
- Значение минимального числа должно быть меньше либо равно максимальному (функция `checkOraclesWhiteListCountGtMinCount`).

Все листингb кода ниже включают в себя вызовы вспомогательных функций, которые в этой книге не показываются, однако вы можете найти их в репозитории [Ride examples](https://github.com/wavesplatform/ride-examples)

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

Отлично! У нас уже есть функции регистрации оракулов и отправки запросов от пользователей. Теперь давайте реализуем функциональность отправки ответа от одного оракула.

### Ответ на запрос данных

Каждый оракул может ответить на запрос, если нет ограничений в виде белого списка и запрос не завершен (по числу ответов или длительности). В момент ответа на запрос публичный ключ оракула и его ответ записываются в хранилище децентрализованного приложения, для этого в момент отправки запроса были созданы ключи `{id}_responders` и `{id}_responses`. Данные в этих ключах хранятся в виде строк с разделителем `;`.

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

Также во время ответа мы увеличиваем счетчик числа ответивших на запрос и сумму баллов, набранных данным ответом (баллы равны сумме рейтингов оракулов).

### Сбор результатов

После отправки оракулами данных подводятся итоги в виде результата (консенсусного решения, среднего значения или медианы) и изменения рейтингов оракулов. В этой функции мы также могли бы выплатить сразу часть вознаграждения оракулам, ответившим правильно, но в виду ограничения по сложности контракта в 4000, сделать это в рамках одной функции не получится. Однако мы можем записать в хранилище аккаунта, кто имеет право забрать часть вознаграждения, и позволить самим оракулам вызвать специальную функцию получения вознаграждения. Напомню, что только оракулы, чей ответ совпал с итоговым (или все оракулы с ответом в пределах 10% от результата, если запрашивалось среднее значение или медиана) имеют право забрать часть вознаграждения.

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

Функция получения вознаграждения для оракула должна позволять забрать средства только один раз и после подтверждения, что этот оракул действительно отвечал на запрос и ответ его считается корректным.

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

На этом основная функциональность контракта для консенсуса оракулов готова. Примеры того, как работать с таким контрактом, можно найти в виде тестов в репозитории [Ride examples](https://github.com/wavesplatform/ride-examples/branch/refactor)

## Возможности для развития

Реализованная функциональность является простейшим вариантом работы децентрализованных оракулов. Мы решили проблемы, обозначенные в начале статьи:

- В блокчейне всегда будут данные, даже если не все оракулы достигнут консенсуса
- У участников процесса появляется экономическая и репутационная мотивация участвовать в предоставлении данных
- Список оракулов может быть максимально широким, но в то же время для своего запроса его можно ограничить, если, например, мы хотим получать данные не от любых оракулов, а только от тех, которым доверяем.

Благодаря тому, что форматы запросов являются типизированными, предоставление ответов можно автоматизировать, например, в виде браузерного расширения, которое следит за запросами на адресе децентрализованного приложения и отвечает на данные, если тип запрашиваемых данных поддерживается расширением. Возможен и  сценарий, когда пользователи с открытым браузером могут зарабатывать на предоставлении данных, не делая для этого ничего своими руками.

Во многих случаях данные необходимы не разово, а в виде постоянного потока. В нашем децентрализованном приложении функциональность подписки на данные не реализована, но мы будем рады участию сообщества в доработке этого примера.
