# Глава 7. Практикум: пишем Web3 приложения

Лучший способ выучить язык - начать на нем писать. Заставить вас писать я не могу, поэтому предложу вам второй по крутизне вариант - читать как пишется код! Мы разберем несколько примеров контрактов, начнем с относительно простых, и закончим на достаточно сложных и разухабистых. Первым проектом, который будем писать является приложение Billy.

## Описание Billy

Billy - децентрализованное приложение на базе бота для корпоративного мессенджера Slack. Вы можете найти подробную информацию о том, как работает Billy, на официальном сайте проекта - [https://iambilly.app](https://iambilly.app). Но давайте я коротко расскажу о том из каких частей он состоит и как именно в нем используется блокчейн.

Billy является проектом для мотивации сотрудников компании. В ходе работы у нас возникает много ситуаций, когда мы помогаем коллегам или они помогают нам. И далеко не всегда такая помощь входит в рабочие обязанности коллеги. Чтобы стимулировать помощь вы можете добавить Billy в Slack одной кнопкой на официальном сайте проекта. Billy генерирует уникальный адрес для каждого сотрудника компании и сохраняет их в базе данных. Каждый месяц бот начисляет на сгенерированные адреса 500 "Спасибо"-токенов, которые могут быть потрачены с помощью этого бота. Для этого сотрудники компании могут отправлять специальные команды боту (`10 спасибо @username`) или реагировать на сообщения с использованием специальных emoji.

![Emoji reaction](../../assets/7-2-1-emoji.png "Emoji reaction")

Неиспользованный остаток из 500 спасибо-токенов за месяц сгорают в конце этого месяца. Заработанные спасибо-токены не сгорают и могут быть использованы для 3 целей:

- **Перевод другим пользователям в благодарность.** Полученные токены можно переводить в любое время своим коллегам.
- **Покупка товаров во внутреннем магазине.** Внутренний магазин позволяет сотрудникам компании (или уполномоченным лицам) предлагать товары и услуги в обмен на спасибо-токены.
- **Для участия в голосованиях и crowdfunding компаниях.** Каждый пользователь может указать цель, для которой собирает токены (например, проведение внутренного митапа) или руководство компании может инициировать голосования, где каждый токен будет считаться одним голосом, то есть более "полезный" и активный сотрудник может больше влиять на итоги голосования. Кроме необходимого количества токенов у каждой кампании есть срок окончания сбора и срок реализации. Механика работы голосования схожа с DAO - децентрализованной автономной организацией.

Видео-демонстрацию работы бота вы можете найти на официальном сайте проекта - [https://iambilly.app](https://iambilly.app). Система максимально скрывает для пользователей все, что касается блокчейна. При необходимости пользователь может запросить свою `seed` фразу и использовать токены вне Slack, однако по умолчанию все детали реализации для него скрыты, чтобы не создавать лишних барьеров.

> **Billy предоставляется бесплатно для всех компаний, поэтому, лучше всего понять его механику можно очень быстро и просто - добавьте в Slack и начните использовать.**

## Реализация

Преждем чем перейти к реализации идеи, давайте опишем все требования к сущностям в блокчейне в более формальном виде:

1. Для каждой команды в Slack выпускается уникальный токен, который будет является внутренней валютой команды
2. Для каждого участника команды создается отдельный аккаунт, на котором хранятся только токены этой команды
3. Токены команды могут переводиться только между членами команды, поэтому список адресов членов команды нам необходимо где-то хранить
4. Любой член команды может сжечь свои токены
5. Участие в голосовании осуществляется через вызовы функций децентрализованного приложения с приложенными токенами команды
6. За каждое голосование, пользователь получает уникальный NFT токен, который подтверждает голос

Система является комплексной и должна включать в себя несколько скриптов. Первый логичной шаг - выпуск токена для команды, является достаточной простой операцией отправки `Issue` транзакции (мы рассматривали пример в главе 5). Мы не хотим, чтобы пользователи системы должны были оплачивать комиссии за переводы в Waves и покупать для этого Waves, поэтому мы используем функционал спонсирования. Это позволит пользователям платить за транзакции в токенах "Спасибо", в то время как настоящие Waves комиссии будут списываться с аккаунта администратора команды.

```js
    const adminSeed = '...';
    const issueTx = issue({
        name: `Thanks`,
        description: 'Say thank you to all of your teammates in Slack. By Billy.',
        decimals: 0,
        quantity: 100000000,
        reissuable: false
    }, adminSeed);

    await broadcast(issueTx);
    await waitForTx(issueTx.id);
    const sponsorshipTx = sponsorship({
        assetId: issueTx.id,
        minSponsoredAssetFee: 1
    }, adminSeed);
    await broadcast(sponsorshipTx);
```

Мы используем функцию `waitForTx` из библиотеки `waves-transactions`, чтобы убедиться, что транзакция выпуска токена попала в блокчейн и только потом отправить транзакцию спонсирования.

Использование спонсирования некоторым образом ограничивает наши возможности по остальным требованиям, так как спонсирование не может быть использовано вместе со смарт-ассетами. То есть, на уровне токена мы не можем заложить ограничение на перевод токенов только другим членам команды. В дальнейшем, с реализацией [WEP-2 Customizable sponsorship](https://forum.wavesplatform.com/t/wep-2-customizable-sponsorship/15880) станет возможно включать спонсирование для смарт-ассетов, но в данный момент мы можем найти и другое решение. Например, мы можем на аккаунт каждого члена команды поставить скрипт (и сделать смарт-аккаунт), который будет проверять есть ли получатель токенов в списке коллег и позволять или запрещать транзакцию.

Список адресов членов команды необходимо так же хранить где-то в блокчейне. Так как у нас будет децентрализованное приложение для совершения покупок и голосования, мы туда же можем добавить функционал управления списком членов команды. Давайте сделаем децентрализованное приложение, которое позволяет добавить или удалить человека из списка. Функция добавления в список будет принимать адрес в качесте аргумента и добавлять в хранилище пару ключ-значение, где ключом будет адрес, а значением - `true`. Функция удаления будет обновлять запись в хранилище и переводить значение для указанного адреса в `false`.

```scala

{-# STDLIB_VERSION 4 #-}
{-# CONTENT_TYPE DAPP #-}
{-# SCRIPT_TYPE ACCOUNT #-}

let adminPublicKey = base58'...'

func addToWhiteList(address: String) = {
    let userInTheList = getBoolean(this, address)
    let newValue = match userInTheList {
        case b: Boolean => {
            if b then throw("User is already in the list and enabled")
            else true
        }
        case _ => true
    }
    if i.callerPublicKey != adminPublicKey then throw("Only admin can call this function") else
    [
        [BooleanEntry(address, newValue)]
    ]
}

@Callable(i)
func removeFromWhiteList(address: String) = {
    if i.callerPublicKey != adminPublicKey then throw("Only admin can call this function")
    else [BooleanEntry(address, false)]
}
```

Давайте напишем код смарт-аккаунта, который будет устанавливаться для каждого члена команды:

```scala
{-# STDLIB_VERSION 4 #-}
{-# CONTENT_TYPE EXPRESSION #-}
{-# SCRIPT_TYPE ACCOUNT #-}

# указываем id токена, который мы выпустили для данной команды
let assetId = base58'...'
# адрес аккаунта, на котором будет децентрализованное приложение и список аккаунтов членов команды
let whiteListAddress = "..."

match tx {
    # Любой член команды может сжечь свои токены
    case b: BurnTransaction => {
        sigVerify(tx.bodyBytes, tx.proofs[0], tx.senderPublicKey)
    }

    # Обновление этого скрипта возможно при наличии 2 подписей - члена команды
    # и администратора команды, с аккаунта которого выпускается токен для команды
    case s: SetScriptTransaction => {
        let assetIssuerPublicKey = assetInfo(assetId).extract().issuerPublicKey
        sigVerify(tx.bodyBytes, tx.proofs[0], tx.senderPublicKey) &&
        sigVerify(tx.bodyBytes, tx.proofs[1], assetIssuerPublicKey)
    }

    # Токены команды могут переводиться только между членами команды, другие токены могут
    # переводиться при наличии подписи аккаунта
    case t: TransferTransaction => {
        # Если переводимый токен НЕ токен команды, то проверяем подпись
        if t.assetId != assetId then sigVerify(t.bodyBytes, t.proofs[0], t.senderPublicKey) else
        {
            # получаем адрес получателя токенов в виде строки
            let recipientAddress = addressFromRecipient(t.recipient).toString()

            # переводим адрес децентрализованного приложения команды в тип Address
            let whiteListAddressValue = addressFromStringValue(whiteListAddress)

            # читаем значение из хранилища децентрализованного приложения команды по ключу
            # равному текстовому представлению адреса получателя текущей транзакции
            let addressIsAllowed = getBoolean(whiteListAddressValue, recipientAddress)

            # проверяем, что полученное из хранилища значение == true
            # иначе (если false или Unit) то записываем false в переменную
            let addressInWhiteList = match addressIsAllowed {
                case b: Boolean => b == true
                case _ => false
            }

            # если адрес получателя есть в списке сотрудников
            # или токены переводятся на децентрализованное приложение команды, то
            # проверяем подпись транзакции (должна быть подписана ключом аккаунта)
            if ((addressInWhiteList || recipientAddress == whiteListAddress)) then {
                if (sigVerify(tx.bodyBytes, tx.proofs[0], tx.senderPublicKey))
                then true
                 else
                throw("Signature is required")
            }else {
                throw("You can send this token only to white-listed addresses")
            }
        }
    }

    # Если вызывается функция децентрализованного приложения
    # то позволяем вызывать только приложение команды
    case i: InvokeScriptTransaction => {
        # получаем адрес вызываемого приложения в виде строки
        let dappCalledAddress = addressFromRecipient(i.dApp).toString()

        # если адрес вызываемого приложения равен адресу приложения команды, то 
        # требуем подпись пользователя
        # иначе запрещаем вызов
        if (dappCalledAddress != whiteListAddress) then throw("You can call only dApp with address " + whiteListAddress + ", but you're trying to call " + dappCalledAddress) else
        if (sigVerify(tx.bodyBytes, tx.proofs[0], tx.senderPublicKey) == false) then throw("Transaction should be signed with users key") else
        true
    }

    # Запрещаем все остальные типы транзакций, в том числе обмен, выпуск токена и т.д.
    case _ => throw("Bad transaction type")
}

```

Описанные выше скрипты позволят удовлетворить следующие требования:

1. Для каждой команды в Slack выпускается уникальный токен, который будет является внутренней валютой команды
2. Для каждого участника команды создается отдельный аккаунт, на котором хранятся токены этой команды
3. Токены команды могут переводиться только между членами команды, поэтому список адресов членов команды нам необходимо где-то хранить
4. Любой член команды может сжечь свои токены

Однако, есть еще 2 требования, которые нам необходимо выполнить:

1. Участие в голосовании осуществляется через вызовы функций децентрализованного приложения с приложенными токенами команды
2. За каждое голосование, пользователь получает уникальный NFT токен, который подтверждает голос

Голосование и сбор средств на цель (далее будем называть кампанией по сбору средств) может инициировать любой член команды. Для этого необходимо вызвать функцию децентрализованного приложения, указав:

- уникальный идентификатор
- время окончания сбора средств/голосования
- срок реализации проекта
- сумма сбора, при которой кампания считается успешной

Мы не будем указывать название или описание в децентрализованном приложении, чтобы не раскрывать внутреннюю информацию. Данные о кампании (название, описание) могут храниться в централизованной базе данных, доступной сотрудникам компании.

Если сбор средств был успешным, то есть собранная сумма больше указанной в момент создания кампании, то токены должны быть заблокированы на контракты до момента наступления срока исполнения. После наступления срока исполнения, инвесторы начинают голосование по факту реализации проекта. Если проект был реализован по мнению большинства инвесторов, то токены разблокируются на контракте и создатель кампании получает их, иначе токены навсегда остаются на контракте. Такая схема позволяет избегать случаев, что кто-то собирает токены и не реализует обещанное. Давайте по шагам будем реализовывать наше приложение.

Функция начала кампании сбора средств и голосования может быть следующей:

```scala
@Callable(i)
func startFunding(id: Int, fundraisingEndTimestamp: Int, implmenetationEndTimestamp: Int, targetSum: Int) = {
    # текущее время
    let lastBlockTimestamp = lastBlock.timestamp

    # Время окончания кампании не может быть меньше, чем 60 секунд
    if (fundraisingEndTimestamp - lastBlockTimestamp - 60 < 0) then throw("End time should at least 60 seconds more than the last block time") else

    # Время окончания сбора средств не может быть меньше срока релизации проекта
    if (implmenetationEndTimestamp < fundraisingEndTimestamp) then throw("Implementation end time should more or equal to endTimestamp") else

    # Идентификатор кампании должен быть уникальным
    if (isDefined(getInteger(this, keyFunding(id)))) then throw("Funding with the same ID already exists") else

    # Минимальная сумма токенов для сбора равна 1000 
    if (targetSum < 1000) then throw("You cannot fundraise less than 1000 tokens")
    else {
        let fundingPrefix = "funding_" + id.toString()
        [
            # сохраняем на какой высоте была начата кампания
            IntegerEntry(fundingPrefix, height),
            # сохраняем время окончания кампании
            IntegerEntry(fundingPrefix + "_timestamp", fundraisingEndTimestamp),
            # сохраняем дату исполнения проекта
            IntegerEntry(fundingPrefix + "_impl_timestamp", implmenetationEndTimestamp),
            # сохраняем значение цели (количество собираемых токенов), при которых кампания считается успешной
            IntegerEntry(fundingPrefix + "_targetSum", targetSum),
            # создаем ключ, в котором постоянно будем хранить актуальное количество поступивших на цель токенов, и записываем 0 как начальное значение
            IntegerEntry(fundingPrefix + "_raised", 0),
            # создаем ключ, в котором будем хранить количество голосов за то, что проект был реализован, и записываем 0 как начальное значение
            IntegerEntry(fundingPrefix + "_release_votes", 0),
            # сохраняем публичный ключ создателя кампании (в виде строки для удобства чтения в обозревателе)
            StringEntry(fundingPrefix + "_owner", i.callerPublicKey.toBase58String(),
            # создаем ключ, в котором будем хранить получил ли создатель цели свои токены после голосования, и записываем false как начальное значение
            BooleanEntry(keyReleasedTokens(id), false)
        ]
    }
}

```

Мы создаем много ключей, которые будем использовать в других функциях тоже, поэтому имеет смысл веделить их в отдельные функции, чтобы не повторяться и не допускать опечаток:

```scala
func keyFunding (id: Int) = "funding_" + id.toString()
func keyEndTimestamp(id: Int) = keyFunding(id) + "_timestamp"
func keyImplEndTimestamp(id: Int) = keyFunding(id) + "_impl_timestamp"
func keyTargetSum(id: Int) = keyFunding(id) + "_targetSum"
func keyOwner(id: Int) = keyFunding(id) + "_owner"
func keyRaised(id: Int) = keyFunding(id) + "_raised"
func keyReleaseVotes(id: Int) = keyFunding(id) + "_release_votes"
func keyReleasedTokens(id: Int) = keyFunding(id) + "_released"
```

Все члены команды, кроме создателя кампании, могут отдавать свои токены на реализацию проекта или как голоса, однако вклад одного пользователя не может быть больше 33% от необходимой суммы. Для этого они должны вызвать метод приложения, передать уникальный идентификатор кампании и прикрепить токены в качестве платежа

```scala

@Callable(i)
func fund(id: Int) = {
    # получаем приклепленный платеж, если его нет, будет выброшено исключение
    let pmt = i.payments[0].extract()

    # получаем параметры кампании
    let targetSum = getIntegerValue(this, keyTargetSum(id))
    let endTimestamp = getIntegerValue(this, keyEndTimestamp(id))
    let owner = getStringValue(this, keyOwner(id))

    # получаем количество собранных токенов на текущий момент
    let raised = getIntegerValue(this, keyRaised(id))

    # получаем количество токенов, которые текущий пользователь уже отправлял на эту цель
    # в случае отсутствия, получаем 0
    let alreadyFundedByUser = match getInteger(this, keyUserFunded(id, i.callerPublicKey.toBase58String())){
        case v: Int => v
        case _ => 0
    }

    # проверяем, что прикрепленные токены действительно являются токенами организации
    if (pmt.assetId != thanksTokenId) then throw("You have to attach proper tokens with id: " + thanksTokenId.toBase58String()) else

    # проверяем, что создатель кампании не пытается сам профинансировать кампанию
    if (owner == i.callerPublicKey.toBase58String()) then throw("You cannot fund your own target") else

    # проверяем, что время окончания сбора средств еще не наступило
    if (endTimestamp > lastBlock.timestamp) then throw("This target is finished already") else

    # проверяем, что вклад пользователя в цель менее 33% после этой транзакции
    if (((alreadyFundedByUser + pmt.amount) > targetSum / 3 + 1)) then throw("You cannot fund more than 33% of the whole sum which is " + ((targetSum / 3 + 1) - alreadyFundedByUser).toString())
    else {
        [
            # сохраняем новое количество токенов, которые получены от текущего вызывабющего аккаунта на эту кампанию
            IntegerEntry(keyUserFunded(id, i.callerPublicKey.toBase58String()), alreadyFundedByUser + pmt.amount),
            # обновляем сумму собранных токенов на текущий момент
            IntegerEntry(keyRaised(id), raised + pmt.amount)
        ]
    }
}
```

Мы уже реализовали функционал создания кампании и его финансирования, однако если сбор средств был успешным, то есть собранная сумма больше указанной в момент создания кампании, то токены должны быть заблокированы на контракты до момента наступления срока исполнения. После наступления срока исполнения, инвесторы начинают голосование по факту реализации проекта. Если проект был реализован по мнению большинства инвесторов, то токены разблокируются на контракте и создатель кампании получает их, иначе токены навсегда остаются на контракте. Давайте реализуем функцию, которую могут вызвать инвесторы кампании и подтвердить, что проект был реализован. Отсутствие голоса будем считать голосом "Против".

```scala
@Callable(i)
func voteForRelease(id: Int) = {

    # получаем параметры кампании
    let endTimestamp = getIntegerValue(this, keyEndTimestamp(id))
    let implEndTimestamp = getIntegerValue(this, keyImplEndTimestamp(id))
    let votePower = getIntegerValue(this, keyUserFunded(id, i.callerPublicKey.toBase58String()))
    let targetSum = getIntegerValue(this, keyTargetSum(id))
    let raised = getIntegerValue(this, keyRaised(id))
    let released = getBooleanValue(this, keyReleasedTokens(id))
    let owner = keyOwner(id)

    # получаем количество токенов инвесторов, которые уже проголосовали "За"
    let votedPower = getIntegerValue(this, keyReleaseVotes(id))


    # проверяем, что срок реализации кампании уже прошел
    if (implEndTimestamp > lastBlock.timestamp) then throw("This target is not finished yet") else

    # проверяем, что кампания была успешной и собрала необходимую сумму
    if (raised < targetSum) then throw("This target didn't reach target") else

    # проверяем, что голосование по этой кампании уже не завершено
    if (released) then throw("This target was released already") else

    # проверяем, что текущий инвестор уже не голосовал за кампанию
    if (isDefined(getBoolean(this, keyUserVoted(id, i.callerPublicKey.toBase58String())))) then throw("You already voted for the target") else

    # если количество уже проголосовавших "За" и голос текущего инвестора в сумме
    # больше 50%, передаем токены создателю кампании и фиксируем факт окончания
    # иначе увеличиваем количество токенов инвесторов, которые уже проголосовали "За"
    if ((votedPower + votePower) > (raised / 2)) then {
        # создатель кампании получает 80% собранной суммы
        # остальные 20% удерживаются приложением в качестве комиссии
        let ownerPrize = raised - (raised / 1 / 5)
        [
            IntegerEntry(keyReleaseVotes(id), votedPower + votePower),
            BooleanEntry(keyReleasedTokens(id), true),
            ScriptTransfer(addressFromPublicKey(owner.toBytes()), ownerPrize, thanksTokenId)
        ]
    }else {
        [
            IntegerEntry(keyReleaseVotes(id), votedPower + votePower)
        ]
    }
}
```

Если все-таки кампания не была успешной и не собрала необходимое количество токенов, то необходимо дать инвесторам возможность забрать свои средства:

```scala
@Callable(i)
func refundUser(id: Int) = {
    # получаем параметры кампании
    let endTimestamp = getIntegerValue(this, keyEndTimestamp(id))
    let targetSum = getIntegerValue(this, keyTargetSum(id))
    let raised = getIntegerValue(this, keyRaised(id))

    # получаем количество токенов, которые были отправлены текущим инвестором
    let fundedAmount = getIntegerValue(this, keyUserFunded(id, i.callerPublicKey.toBase58String()))

    # проверяем, что срок сбора средств кампании уже завершился
    if (endTimestamp > lastBlock.timestamp) then throw("This target time is not finished yet") else

    # проверяем, что кампания НЕ собрала необходимое количество средств
    if (raised > targetSum) then throw("This target raised enough tokens") else

    # проверяем, что текущий инвестор действительно поддерживал кампанию
    if (fundedAmount == 0) then throw("You have no power here") else
        [
            # обнуляем количество токенов от текущего инвестора
            IntegerEntry(keyUserFunded(id, i.callerPublicKey.toBase58String()), 0)
            # возвращаем инвестору токены
            ScriptTransfer(i.caller, fundedAmount, thanksTokenId)
        ]
}

```

## Пользовательский интерфейс

Мы реализовали основной код на Ride, однако, чтобы это работало, необходимо большое количество работы с точки зрения взаимодействия с пользователей. Чтобы понять как работает приложение для коненчного пользователя, посмотрите видео-демонстрацию на сайте проекта - [https://iambilly.app](https://iambilly.app). Или установите Billy для своего Slack и начните использовать - это бесплатно.
