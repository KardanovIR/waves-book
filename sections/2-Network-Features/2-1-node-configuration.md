# Глава 2. Нода Waves и как она работает, ее конфигурация

Непосредственное техническое знакомство с платформой Waves я бы рекомендовал начать с установки и настройки ноды. Не обязательно для основной сети, можно для testnet (полная копия по техническим возможностям) или stagenet (экспериментальная сеть). Почему я рекомендую начать с установки ноды? Во-первых, я сам свое знакомство с блокчейном Waves начинал именно с этого, а во-вторых, установка и настройка заставляют разобраться в том, какие есть настройки у ноды и какие параметры есть у сети.

## Из чего состоит нода Waves

Как и почти во всех блокчейнах, нода - программный продукт, который отвечает за прием транзакций, создание новых блоков, синхронизацию данных между разными узлами и достижение консенсуса между ними. Каждый участник сети запускает свою копию ноды и синхронизируется с остальными. Про правила консенсуса мы поговорим чуть позже, сейчас давайте разберемся, что из себя представляет нода с точки зрения ПО.

По большому счету, нода это исполняемый файл (`jar` файл для Sсala версии и бинарный для Go), который в момент запуска читает конфигурационный файл, чтобы на основе этих параметров начать общаться с другими узлами в сети по протоколу поверх TCP. Получаемые и генерируемые данные нода складывает в LevelDB (key-value хранилище). По большому то счету все, но дьявол кроется в деталях. По мере углубления в особенности работы вы поймете, что это далеко не так просто, как может показаться. А пока, давайте поговорим о том, с чего нода начинает свою работу в момент запуска - конфигурационного файла.

## Установка ноды

В данной книге мы не будем разбирать процесс установки ноды, так как это много раз описано уже в разных источниках (документация, видео на youtube, посты на форуме). Нам никакой книги не хватит, если мы захотим окунуться в эту тему, потому что существует много способов запуска ноды:

1. Запуск `jar` файла
2. Запуск Docker контейнера
3. Установка и запуск из `.deb` файла
4. Установка из `apt-` репозитория

Лично я предпочитаю запуск Docker контейнера, так как это упрощает и поддержку, и настройку, и конфигурирвание. Другой причиной моей любви к Docker может быть то, что я делал Docker образ, размещенный в Docker Hub и отлично знаю как там что работает. Хотя вряд ли, это просто удобнее! :)

## Конфигурация ноды

Конфигурационный файл ноды Waves описан в формате [HOCON](https://github.com/lightbend/config/blob/master/HOCON.md) (это как JSON, только с комментариями, возможностью композиции нескольких файлов и, что не менее важно, с меньшим количеством кавычек). Полный файл с конфигурацией выглядит громоздко, но я все-таки приведу его здесь, версия на момент написания этих строк (файл постоянно меняется, но актульную версию можно найти в [репозитории Waves на Github](https://github.com/wavesplatform/Waves/blob/master/node/src/main/resources/application.conf)).

Файл содержит большое количество комментариев, поясняющих каждый параметр, поэтому подробно разбирать все параметры мы не будем. Поговорим только об основных моментах.
В конфигурации содержатся следующие разделы:

- waves
- kamon
- metrics
- akka

Последние 3 раздела являются служебными, которые отвечают за параметры логгирования, отправку метрик и фреймворка akka. Нас интересует только первый раздел, который касается непосредственно протокола и содержит на первом уровне следующие подразделы:

- **db** - параметры для работы с LevelDB и настройки какие данные сохранять. Например, результаты некоторых транзакций можно сжимать (экономить до 10 ГБ), но это вредит удобству работу с API. Поэтому будьте осторожны с тем, что включать, а что выключать и экономить место на диске.
- **network** - параметры общения с другими нодами в сети. В этом разделе много важных параметров, которые мы разберем чуть ниже.
- **wallet** - параметры файла для сохранения ключей. Каждая нода, которая хочет генерировать блоки (чтобы получать за них вознаграждение), должна подписывать свои блоки. Для этого у ноды должен быть доступ к ключу аккаунта. В этой секции задается приватный ключ (а если быть точнее, то seed фраза в кодировке [base58](https://en.wikipedia.org/wiki/Base58)), пароль для того, чтобы эту фразу зашифровать, и путь, по которому хранить файл с этим зашифрованным ключом.
- **blockchain** - параметры блокчейна, в котором будет работать нода. В данном разделе есть настройка `type`, которая позволяет задать одну из заранее определенных типов блокчейнов (stagenet, testnet или mainnet). При указании значения `custom` можно менять все параметры блокчейна, в том числе первоначальное количество токенов, их распределение, байт сети (уникальный идентификатор каждой сети), поддерживаемую функциональность и т.д.
- **miner** - параметры генерации новых блоков. По умолчанию, генерация включена (надо понимать, что это будет работать только при наличии генерирующего баланса больше 1000 Waves на аккаунте), но ее можно отключить с помощью параметра `enable`. Может пригодиться, если, например, нужна нода, которая будет только валидировать блоки, но не генерировать их. Другой полезный параметр - `quorum`, который определяет сколько соединений с другими нодами необходимо, чтобы нода пыталась генерировать блоки. Если задать данный параметр, равный 0, то можно запустить блокчейн, состоящий из 1 узла. Зачем вам такой блокчейн, если этот один узел может переписывать всю историю и делать все, что захочет? Для тестирования! Идеальная песочница для игр с блокчейном.
- **rest-api** - в ноде есть встроенный REST API, который по умолчанию отключен, но после включения (за это отвечает параметр `enable`) позволяет делать HTTP запросы для получения данных из блокчейна или записи в нее новых транзакций. В данном API есть как открытые для всех методы, так и те, которые требуют API ключ, который задается в этой же секции настроек. Параметр `port` задает на каком порту будет слушать нода входящие HTTP запросы, на этом же порту будет доступен Swagger UI с описанием всех методов API. `api-key-hash` позволяет указать хэш от API-ключа, с которым будут доступны приватные методы. То есть в конфигурационном файле мы указываем не сам ключ, а хэш от него. А какой хэш надо взять? SHA-1? SHA-512? Или, прости господи, MD5? Ничто из перечисленного, потому что в Waves используется `secure hash`, который является последовательным вычислением хешей `Blake2b256` и `Keccak256` - `keccak256(blake2b256(data))`. В REST API есть метод [`/utils/hash/secure`](https://docs.wavesprotocol.org/en/waves-node/node-api/utils), который позволяет передать значение и получить готовый хэш.
- **synchronization** - параметры синхронизации в сети, в том числе максимальная длина форка и параметр `max-rollback`, который задает сколько блоков может быть откачено и по умолчанию равно 100. Фактически можно сказать, что время финализации транзакции, после которого точно можно быть уверенным, что транзакция не пропадет из сети, составляет 100 минут (среднее время блока составляет 1 минуту).
- **utx** задает параметры пула неподтвержденных транзакций. Каждая нода настраивает эти параметры в зависимости от объема доступной оперативной памяти, мощности и количества CPU. Параметр `max-size`, задающий максимальное количество транзакций в списке ожидания, составляет 100 000 по умолчанию, а `max-bytes-size` имеет значение `52428800`. При достижении любого из этих лимитов, нода перестанет принимать транзакции в свой список ожидания. Про UTX мы поговорим отдельно в главе 5 "Транзакции".
- **features** - каждый новый функционал и изменения консенсуса (именно правил консенсуса, не изменения API или внутренностей ноды!) должны проходить через процедуру голосования. Принцип работы голосования мы разберем позже в этом разделе. Сейчас просто скажем, что каждая нода голосует используя массив `supported` в этой части конфигурации. Так же нода может автоматически выключиться, если вся сеть приняла какое-то обновление, которое не поддерживается этой версией, используя флаг `auto-shutdown-on-unsupported-feature`.
- **rewards** позволяет установить размер вознаграждения для майнера блока. Как и в случае с обновлениями протокола, проводится голосование, но голосование за размер вознаграждения работает по другому принципу.
- **extensions** описывает какие расширения вместе с этой нодой необходимо запускать.

В разделе `waves` на первом уровне так же лежат некоторые параметры:

- `directory` - путь к директории, в которой нода будет сохранять все файлы, которые относятся к ней, к том числе файлы LevelDB с данными
- `ntp-server` - сервер синхронизации времени
- `extensions-shutdown-timeout` - это время, которое дается расширенниям, подключенным к ноде, корректно завершиться при выключении самой ноды

**В следующим разделах мы будем подробнее разбирать какие параметры влияют на поведение ноды и каким образом.**

## Особенности конфигурации

Вы уже увидели, что конфигурация ноды осуществляется с помощью HOCON файлов, которые поддерживают возможность композиции. Другими словами, в файле конфигурации можно использовать инструкции `include filename.conf`, который может загружать разные разделы конфигурации из другого файла. Некоторые разделы могут повторяться в разных файлах, поэтому там так же есть механизм разрешения конфликтов (чем ниже подключен файл, тем больший приоритет он имеет). Если у вас есть опыт работы с CSS, то принцип такой же. В некоторых местах документации Waves приводится нотация вроде `waves.network.port`, как нетрудно догадаться, это обозначает параметр в конфигурации вместе с путем:

```hocon
waves {
  network {
    port = 6868
  }
}
```

### Безопасность

При конфигурировании ноды имеет значение уделить особое внимание тем параметрам, которые напрямую влияют на безопасность - разделам `wallet` и `rest-api`. Хорошей практикой считается указать в конфигурационном файле base58 представление seed фразы и пароль, запустить ноду, а затем удалить фразу из файла (не перезапуская ноду). Таким образом, запущенная нода будет знать приватный ключ и пароль (это есть в оперативной памяти), но в файле конфигурации ничего не останется. Если вдруг кто-то получит доступ к вашей конфигурации или даже `wallet` файлу, он не сможет расшифровать ключ.

API ключ ноды не менее важен, потому что он позволяет отправить с ноды транзакции, подписанные ключами, хранящимися в ноде. В отличие от данных аккаунта, в конфигурации хранится только хэш, поэтому удалять оттуда после запуска смысла нет, но есть смысл использовать максимально сложный ключ и никак не тот, который стоит по умолчанию (в старых версиях был ключ по-умолчанию, в последних такого уже нет).

### Оптимальные настройки блокчейна

Очень часто задаваемый вопрос - какие же настройки оптимальные? В первую очередь зависит от того, о какой сети мы говорим - `stagenet`, `testnet`, `mainnet` или `custom`? Например, для `custom` не существует оптимальных, так как различаются требования к сети. Но надо понимать, что нода Waves не всемогущая, эмпирически показано, что при времени блока меньше 12 секунд (`waves.blockchain.genesis.average-block-delay`), времени микроблока меньше 3 секунд (`waves.miner.micro-block-interval`) и относительно большом количестве узлов в сети (10+), сеть может быстро разделяться на несколько. Такое поведение вызвано задержками в передаче сетевых сообщений.

Важным параметром, который надо настраивать с учетом особенностей окружения, является максимальное количество транзакций в UTX. 100 000 транзаций является оптимальным для ноды, удовлетворяющей минимальным системным требованиям.

Я описал выше только самые основные параметры, многие другие мы будем рассматривать в следующих разделах, по мере погружения в протокол и его особенности. Сейчас же приведу полный файл конфигурации с комментариями:

```hocon
# Waves node settings in HOCON
# HOCON specification: https://github.com/lightbend/config/blob/master/HOCON.md
waves {
  # Node base directory
  directory = ""

  db {
    directory = ${waves.directory}"/data"
    store-transactions-by-address = true
    store-invoke-script-results = true
    # Limits the size of caches which are used during block validation. Lower values slightly decrease memory consummption,
    # while higher values might increase node performance. Setting ghis value to 0 disables caching alltogether.
    max-cache-size = 100000

    max-rollback-depth = 2000
    remember-blocks = 3h
  }

  # NTP server
  ntp-server = "pool.ntp.org"

  # P2P Network settings
  network {
    # Peers and blacklist storage file
    file = ${waves.directory}"/peers.dat"

    # String with IP address and port to send as external address during handshake. Could be set automatically if UPnP
    # is enabled.
    #
    # If `declared-address` is set, which is the common scenario for nodes running in the cloud, the node will just
    # listen to incoming connections on `bind-address:port` and broadcast its `declared-address` to its peers. UPnP
    # is supposed to be disabled in this scenario.
    #
    # If declared address is not set and UPnP is not enabled, the node will not listen to incoming connections at all.
    #
    # If declared address is not set and UPnP is enabled, the node will attempt to connect to an IGD, retrieve its
    # external IP address and configure the gateway to allow traffic through. If the node succeeds, the IGD's external
    # IP address becomes the node's declared address.
    #
    # In some cases, you may both set `decalred-address` and enable UPnP (e.g. when IGD can't reliably determine its
    # external IP address). In such cases the node will attempt to configure an IGD to pass traffic from external port
    # to `bind-address:port`. Please note, however, that this setup is not recommended.
    # declared-address = "1.2.3.4:6863"

    # Network address
    bind-address = "0.0.0.0"

    # Port number
    port = 6863

    # Node name to send during handshake. Comment this string out to set random node name.
    # node-name = "default-node-name"

    # Node nonce to send during handshake. Should be different if few nodes runs on the same external IP address. Comment this out to set random nonce.
    # nonce = 0

    # List of IP addresses of well known nodes.
    known-peers = ["52.30.47.67:6863", "52.28.66.217:6863", "52.77.111.219:6863", "52.51.92.182:6863"]

    # How long the information about peer stays in database after the last communication with it
    peers-data-residence-time = 1d

    # How long peer stays in blacklist after getting in it
    black-list-residence-time = 15m

    # Breaks a connection if there is no message from the peer during this timeout
    break-idle-connections-timeout = 5m

    # How many network inbound network connections can be made
    max-inbound-connections = 30

    # Number of outbound network connections
    max-outbound-connections = 30

    # Number of connections from single host
    max-single-host-connections = 3

    # Timeout on network communication with other peers
    connection-timeout = 30s

    # Size of circular buffer to store unverified (not properly handshaked) peers
    max-unverified-peers = 100

    # If yes the node requests peers and sends known peers
    enable-peers-exchange = yes

    # If yes the node can blacklist others
    enable-blacklisting = yes

    # How often connected peers list should be broadcasted
    peers-broadcast-interval = 2m

    # When accepting connection from remote peer, this node will wait for handshake for no longer than this value. If
    # remote peer fails to send handshake within this interval, it gets blacklisted. Likewise, when connecting to a
    # remote peer, this node will wait for handshake response for no longer than this value. If remote peer does not
    # respond in a timely manner, it gets blacklisted.
    handshake-timeout = 30s

    suspension-residence-time = 1m

    # When a new treansaction comes from the network, we cache it and doesn't push this transaction again when it comes
    # from another peer.
    # This setting setups a timeout to remove an expired transaction in the elimination cache.
    received-txs-cache-timeout = 3m

    upnp {
      # Enable UPnP tunnel creation only if you router/gateway supports it. Useful if your node is runnin in home
      # network. Completely useless if you node is in cloud.
      enable = no

      # UPnP timeouts
      gateway-timeout = 7s
      discover-timeout = 3s
    }

    # Logs incoming and outgoing messages
    traffic-logger {
      # Codes of transmitted messages to ignore. See MessageSpec.messageCode
      ignore-tx-messages = [23, 25] # BlockMessageSpec, TransactionMessageSpec

      # Codes of received messages to ignore. See MessageSpec.messageCode
      ignore-rx-messages = [25] # TransactionMessageSpec
    }
  }

  # Wallet settings
  wallet {
    # Path to wallet file
    file = ${waves.directory}"/wallet/wallet.dat"

    # Password to protect wallet file
    # password = "some string as password"

    # The base seed, not an account one!
    # By default, the node will attempt to generate a new seed. To use a specific seed, uncomment the following line and
    # specify your base58-encoded seed.
    # seed = "BASE58SEED"
  }

  # Blockchain settings
  blockchain {
    # Blockchain type. Could be TESTNET | MAINNET | CUSTOM. Default value is TESTNET.
    type = TESTNET

    # 'custom' section present only if CUSTOM blockchain type is set. It's impossible to overwrite predefined 'testnet' and 'mainnet' configurations.
    #    custom {
    #      # Address feature character. Used to prevent mixing up addresses from different networks.
    #      address-scheme-character = "C"
    #
    #      # Timestamps/heights of activation/deactivation of different functions.
    #      functionality {
    #
    #        # Blocks period for feature checking and activation
    #        feature-check-blocks-period = 10000
    #
    #        # Blocks required to accept feature
    #        blocks-for-feature-activation = 9000
    #
    #        reset-effective-balances-at-height = 0
    #        generation-balance-depth-from-50-to-1000-after-height = 0
    #        block-version-3-after-height = 0
    #        max-transaction-time-back-offset = 120m
    #        max-transaction-time-forward-offset = 90m
    #        pre-activated-features {
    #          1 = 100
    #          2 = 200
    #        }
    #        lease-expiration = 1000000
    #      }
    #      # Block rewards settings
    #      rewards {
    #        term = 100000
    #        initial = 600000000
    #        min-increment = 50000000
    #        voting-interval = 10000
    #      }
    #      # List of genesis transactions
    #      genesis {
    #        # Timestamp of genesis block and transactions in it
    #        timestamp = 1460678400000
    #
    #        # Genesis block signature
    #        signature = "BASE58BLOCKSIGNATURE"
    #
    #        # Initial balance in smallest units
    #        initial-balance = 100000000000000
    #
    #        # Initial base target
    #        initial-base-target =153722867
    #
    #        # Average delay between blocks
    #        average-block-delay = 60s
    #
    #        # List of genesis transactions
    #        transactions = [
    #          {recipient = "BASE58ADDRESS1", amount = 50000000000000},
    #          {recipient = "BASE58ADDRESS2", amount = 50000000000000}
    #        ]
    #      }
    #    }
  }

  # New blocks generator settings
  miner {
    # Enable/disable block generation
    enable = yes

    # Required number of connections (both incoming and outgoing) to attempt block generation. Setting this value to 0
    # enables "off-line generation".
    quorum = 1

    # Enable block generation only in the last block is not older the given period of time
    interval-after-last-block-then-generation-is-allowed = 1d

    # Mining attempts delay, if no quorum available
    no-quorum-mining-delay = 5s

    # Interval between microblocks
    micro-block-interval = 5s

    # Max amount of transactions in key block
    max-transactions-in-key-block = 0

    # Max amount of transactions in micro block
    max-transactions-in-micro-block = 255

    # Miner references the best microblock which is at least this age
    min-micro-block-age = 4s

    # Minimal block generation offset
    minimal-block-generation-offset = 0

    # Max packUnconfirmed time
    max-pack-time = ${waves.miner.micro-block-interval}
  }

  # Node's REST API settings
  rest-api {
    # Enable/disable REST API
    enable = yes

    # Network address to bind to
    bind-address = "127.0.0.1"

    # Port to listen to REST API requests
    port = 6869

    # Hash of API key string
    api-key-hash = ""

    # Enable/disable CORS support
    cors = yes

    # Enable/disable X-API-Key from different host
    api-key-different-host = no

    # Max number of transactions
    # returned by /transactions/address/{address}/limit/{limit}
    transactions-by-address-limit = 1000
    distribution-address-limit = 1000
  }

  # Nodes synchronization settings
  synchronization {

    # How many blocks could be rolled back if fork is detected. If fork is longer than this rollback is impossible.
    max-rollback = 100

    # Max length of requested extension signatures
    max-chain-length = 101

    # Timeout to receive all requested blocks
    synchronization-timeout = 60s

    # Time to live for broadcasted score
    score-ttl = 90s

    # Max baseTarget value. Stop node when baseTraget greater than this param. No limit if it is not defined.
    # max-base-target = 200

    # Settings for invalid blocks cache
    invalid-blocks-storage {
      # Maximum elements in cache
      max-size = 30000

      # Time to store invalid blocks and blacklist their owners in advance
      timeout = 5m
    }

    # History replier caching settings
    history-replier {
      # Max microblocks to cache
      max-micro-block-cache-size = 50

      # Max blocks to cache
      max-block-cache-size = 20
    }

    # Utx synchronizer caching settings
    utx-synchronizer {
      # Max microblocks to cache
      network-tx-cache-size = 1000000

      # Max scheduler threads
      max-threads = 8

      # Max pending queue size
      max-queue-size = 5000

      # Send transaction to peers on broadcast request even if it's already in utx-pool
      allow-tx-rebroadcasting = yes
    }

    # MicroBlock synchronizer settings
    micro-block-synchronizer {
      # How much time to wait before a new request of a microblock will be done
      wait-response-timeout = 2s

      # How much time to remember processed microblock signatures
      processed-micro-blocks-cache-timeout = 3m

      # How much time to remember microblocks and their nodes to prevent same processing
      inv-cache-timeout = 45s
    }
  }

  # Unconfirmed transactions pool settings
  utx {
    # Pool size
    max-size = 100000
    # Pool size in bytes
    max-bytes-size = 52428800 // 50 MB
    # Pool size for scripted transactions
    max-scripted-size = 5000
    # Blacklist transactions from these addresses (Base58 strings)
    blacklist-sender-addresses = []
    # Allow transfer transactions from the blacklisted addresses to these recipients (Base58 strings)
    allow-blacklisted-transfer-to = []
    # Allow transactions from smart accounts
    allow-transactions-from-smart-accounts = true
    # Allow skipping checks with highest fee
    allow-skip-checks = true
  }

  features {
    auto-shutdown-on-unsupported-feature = yes
    supported = []
  }

  rewards {
    # desired = 0
  }

  extensions = [
    # com.wavesplatform.matcher.Matcher
    # com.wavesplatform.api.grpc.GRPCServerExtension
  ]

  # How much time to wait for extensions' shutdown
  extensions-shutdown-timeout = 5 minutes
}

# Performance metrics
kamon {
  # Set to "yes", if you want to report metrics
  enable = no

  # A node identification
  environment {
    service = "waves-node"

    # An unique id of your node to distinguish it from others
    # host = ""
  }

  metric {
    # An interval within metrics are aggregated. After it, them will be sent to the server
    tick-interval = 10 seconds

    instrument-factory.default-settings.histogram {
      lowest-discernible-value = 100000 # 100 microseconds
      highest-trackable-value = 2000000000000 # 200 seconds
      significant-value-digits = 0
    }
  }

  # Reporter settings
  influxdb {
    hostname = "127.0.0.1"
    port = 8086
    database = "mydb"

    # authentication {
    #   user = ""
    #   password = ""
    # }
  }
}

# Non-aggregated data (information about blocks, transactions, ...)
metrics {
  enable = no
  node-id = -1 # ${kamon.environment.host}

  influx-db {
    uri = "http://"${kamon.influxdb.hostname}":"${kamon.influxdb.port}
    db = ${kamon.influxdb.database}

    # username = ${kamon.influxdb.authentication.user}
    # password = ${kamon.influxdb.authentication.password}

    batch-actions = 100
    batch-flash-duration = 5s
  }
}

# WARNING: No user-configurable settings below this line.

akka {
  loglevel = "INFO"
  loggers = ["akka.event.slf4j.Slf4jLogger"]
  logging-filter = "akka.event.slf4j.Slf4jLoggingFilter"
  log-dead-letters-during-shutdown = false

  http.server {
    max-connections = 128
    request-timeout = 20s
    parsing {
      max-method-length = 64
      max-content-length = 1m
    }
  }

  io.tcp {
    direct-buffer-size = 1536 KiB
    trace-logging = off
  }
}

include "deprecated-settings.conf"
```
