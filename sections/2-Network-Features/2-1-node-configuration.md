# Chapter 2. Waves node and how it works, its configuration

I would recommend starting a direct technical acquaintance with the Waves platform with installing and configuring a node. Not necessary for the main network, it is possible for testnet (full copy of technical capabilities) or stagenet (experimental network). Why do I recommend starting by installing a node? Firstly, I myself began my acquaintance with the Waves blockchain with this, and secondly, the installation and configuration make you understand what settings the node has and what parameters the network has.

## What the Waves node consists of

As in almost all blockchains, a node is a software product that is responsible for accepting transactions, creating new blocks, synchronizing data between different nodes and reaching consensus between them. Each member of the network starts its own copy of the node and synchronizes with the rest. We'll talk about the rules of consensus a little later, now let's figure out what a node is from a software point of view.

By and large, a node is an executable file (a `jar` file for the Scala version and a binary for Go) that at the time of launch reads the configuration file in order to start communicating with other nodes in the network using the protocol over TCP based on these parameters. The node adds the received and generated data to LevelDB (key-value storage). By and large, that's all, but the devil is in the details. As you delve into the specifics of the work, you will realize that this is not as easy as it might seem. For now, let's talk about where the node starts its work at the time of launch - the configuration file.

## Installing a node

In this book, we will not analyze the process of installing a node, since this has already been described many times in different sources (documentation, videos on youtube, posts on the forum). No book will be enough for us if we want to plunge into this topic, because there are many ways to launch a node:

1. Running the `jar` file
2. Running a Docker container
3. Install and run from a `.deb` file
4. Installing from the `apt-` repository

Personally, I prefer running a Docker container as it makes it easier to maintain, configure, and configure. Another reason for my love for Docker may be that I made a Docker image hosted in the Docker Hub and I know perfectly well how what works there. Although unlikely, it's just more convenient! :)

## Node configuration

The configuration file of the Waves node is described in the [HOCON](https://github.com/lightbend/config/blob/master/HOCON.md) format  (it's like JSON, only with comments, the ability to compose several files and, no less important , with fewer quotes). The complete configuration file looks cumbersome, but I will still provide it here, the version at the time of this writing (the file is constantly changing, but the current version can be found in the [Waves Github repository](https://github.com/wavesplatform/Waves/blob/master/node/src/main/resources/application.conf).

The file contains a large number of comments explaining each parameter, so we will not analyze all the parameters in detail. Let's talk only about the main points.
The configuration contains the following sections:

- waves
- kamon
- metrics
- akka

The last 3 sections are service ones, which are responsible for logging parameters, sending metrics and the akka framework. We are only interested in the first section, which relates directly to the protocol and contains the following subsections at the first level:

- **db** - parameters for working with LevelDB and setting what data to save. For example, the results of some transactions can be compressed (save up to 10 GB), but this hurts the usability of the API. Therefore, be careful about what to enable and what to disable and save disk space.
- **network** - parameters of communication with other nodes in the network. There are many important parameters in this section, which we will discuss below.
- **wallet** - file parameters for saving keys. Each node that wants to generate blocks (in order to receive rewards for them) must sign their blocks. To do this, the node must have access to the account key. In this section, the private key is set (or, to be more precise, the seed phrase encoded in [base58](https://en.wikipedia.org/wiki/Base58), the password in order to encrypt this phrase, and the path by which to store the file with this encrypted key.
- **blockchain** - parameters of the blockchain in which the node will operate. In this section, there is a `type` setting that allows you to set one of the predefined blockchain types (stagenet, testnet or mainnet). When specifying the value `custom`, you can change all the parameters of the blockchain, including the initial number of tokens, their distribution, network bytes (unique identifier of each network), supported functionality, etc.
- **miner** - parameters for generating new blocks. By default, generation is enabled (you need to understand that this will only work if there is a generating balance of more than 1000 Waves on your account), but it can be disabled using the `enable` parameter. It can be useful if, for example, you need a node that will only validate blocks, but not generate them. Another useful parameter is `quorum`, which determines how many connections to other nodes are needed for a node to try to generate blocks. If you set this parameter to 0, then you can start a blockchain consisting of 1 node. Why do you need such a blockchain, if this one node can rewrite the whole history and do whatever it wants? For testing! The perfect sandbox for blockchain games.
- **rest-api** - the node has a built-in REST API, which is disabled by default, but after enabling (the `enable` parameter is responsible for this) allows you to make HTTP requests to receive data from the blockchain or write new transactions to it. This API has both methods open to all and those that require an API key, which is specified in the same settings section. The `port` parameter specifies on which port the node will listen for incoming HTTP requests, on the same port the Swagger UI will be available with a description of all API methods. `api-key-hash` allows you to specify a hash from the API key with which private methods will be available. That is, in the configuration file we specify not the key itself, but the hash from it. What hash should you take? SHA-1? SHA-512? Or, God forgive me, MD5? None of the above, because Waves uses a `secure hash`, which is a sequential computation of the` Blake2b256` and `Keccak256` -` keccak256 (blake2b256 (data)) `hashes. The REST API has a method [`/utils/hash/secure`](https://docs.wavesprotocol.org/en/waves-node/node-api/utils) that allows you to pass a value and get a ready-made hash.
- **synchronization** - synchronization parameters in the network, including the maximum fork length and the `max-rollback` parameter, which specifies how many blocks can be rolled back and by default equals 100. In fact, we can say that the transaction finalization time, after which exactly you can be sure that the transaction will not disappear from the network, is 100 minutes (average block time is 1 minute).
- **utx** specifies the uncommitted transaction pool parameters. Each node configures these parameters depending on the amount of available RAM, power, and the number of CPUs. The `max-size` parameter, which specifies the maximum number of transactions in the pending list, is 100,000 by default, and` max-bytes-size` has the value `52428800`. Upon reaching any of these limits, the node will stop accepting transactions on its waiting list. We'll talk about UTX separately in Chapter 5 "Transactions".
- **features** - each new functionality and consensus changes (namely the consensus rules, not changes to the API or the internals of the node!) must go through the voting procedure. We'll discuss how voting works later in this section. For now, let's just say that each node votes using the `supported` array in this part of the configuration. Also, a node can automatically shutdown if the entire network has accepted some update that is not supported by this version using the `auto-shutdown-on-unsupported-feature` flag.
- **rewards** allows you to set the size of the block miner reward As with protocol updates, a vote is taken, but the reward vote works differently.
- **extensions** describes which extensions should be run with this node.

In the section `waves`, at the first level, there are also some parameters:

- `directory` - path to the directory in which the node will save all the files that refer to it, including LevelDB files with data
- `ntp-server` - time synchronization server
- `extensions-shutdown-timeout` is the time that is given to extensions connected to the node to exit correctly when the node itself is shut down

**In the next sections, we will analyze in more detail which parameters affect the behavior of the node and how.**

## Configuration Features

You have already seen that the configuration of a node is done using HOCON files, which support the ability to compose. In other words, you can use the `include filename.conf` statements in the configuration file, which can load different configuration sections from another file. Some sections can be repeated in different files, so there is also a conflict resolution mechanism there (the lower the file is connected, the higher priority it has). If you have experience with CSS, the principle is the same. In some places in the Waves documentation, a notation like `waves.network.port` is given, as you might guess, this means a parameter in the configuration along with the path:

hocon
waves {
  network {
    port = 6868
  }
}
``

### Safety

When configuring a node, it is important to pay special attention to those parameters that directly affect security - the `wallet` and` rest-api` sections. It is considered good practice to specify the seed phrase and password in the base58 configuration file, start the node, and then delete the phrase from the file (without restarting the node). Thus, the launched node will know the private key and password (this is in RAM), but nothing will remain in the configuration file. If suddenly someone gets access to your configuration or even the `wallet` file, he will not be able to decrypt the key.

The node's API key is equally important, because it allows you to send transactions from the node, signed by the keys stored in the node. Unlike account data, only the hash is stored in the configuration, so there is no point in deleting from there after launch, but it makes sense to use the most complex key and not the default one (in older versions there was a key by default, in the latter it is already not).

### Optimal blockchain settings

A very frequently asked question - what are the optimal settings? First of all depends on what kind of network we are talking about - `stagenet`,` testnet`, `mainnet` or` custom`? For example, there are no optimal ones for `custom` because of the different network requirements. But you need to understand that the Waves node is not omnipotent, it is empirically shown that when the block time is less than 12 seconds (`waves.blockchain.genesis.average-block-delay`), the microblock time is less than 3 seconds (` waves.miner.micro-block -interval`) and a relatively large number of nodes in the network (10+), the network can quickly split into several. This behavior is caused by delays in the transmission of network messages.

An important parameter that needs to be adjusted taking into account the peculiarities of the environment is the maximum number of transactions in UTX. 100,000 transactions is optimal for a node that meets the minimum system requirements.

I described above only the most basic parameters, we will consider many others in the following sections, as we dive into the protocol and its features. Now I will give a complete configuration file with comments:

```hocon
# Waves node settings in HOCON
# HOCON specification: https://github.com/lightbend/config/blob/master/HOCON.md
waves {
  # Node base directory
  directory = ""

  db {
    directory = $ {waves.directory} "/ data"
    store-transactions-by-address = true
    store-invoke-script-results = true
    # Limits the size of caches which are used during block validation. Lower values ​​slightly decrease memory consummption,
    # while higher values ​​might increase node performance. Setting ghis value to 0 disables caching alltogether.
    max-cache-size = 100000

    max-rollback-depth = 2000
    remember-blocks = 3h
  }

  # NTP server
  ntp-server = "pool.ntp.org"

  # P2P Network settings
  network {
    # Peers and blacklist storage file
    file = $ {waves.directory} "/ peers.dat"

    # String with IP address and port to send as external address during handshake. Could be set automatically if UPnP
    # is enabled.
    #
    # If `declared-address` is set, which is the common scenario for nodes running in the cloud, the node will just
    # listen to incoming connections on `bind-address: port` and broadcast its` declared-address` to its peers. UPnP
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
    # to `bind-address: port`. Please note, however, that this setup is not recommended.
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
      # Enable UPnP tunnel creation only if you router / gateway supports it. Useful if your node is runnin in home
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
    file = $ {waves.directory} "/ wallet / wallet.dat"

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
    # custom {
    # # Address feature character. Used to prevent mixing up addresses from different networks.
    # address-scheme-character = "C"
    #
    # # Timestamps / heights of activation / deactivation of different functions.
    # functionality {
    #
    # # Blocks period for feature checking and activation
    # feature-check-blocks-period = 10000
    #
    # # Blocks required to accept feature
    # blocks-for-feature-activation = 9000
    #
    # reset-effective-balances-at-height = 0
    # generation-balance-depth-from-50-to-1000-after-height = 0
    # block-version-3-after-height = 0
    # max-transaction-time-back-offset = 120m
    # max-transaction-time-forward-offset = 90m
    # pre-activated-features {
    # 1 = 100
    # 2 = 200
    #}
    # lease-expiration = 1000000
    #}
    # # Block rewards settings
    # rewards {
    # term = 100000
    # initial = 600000000
    # min-increment = 50,000,000
    # voting-interval = 10000
    #}
    # # List of genesis transactions
    # genesis {
    # # Timestamp of genesis block and transactions in it
    # timestamp = 1460678400000
    #
    # # Genesis block signature
    # signature = "BASE58BLOCKSIGNATURE"
    #
    # # Initial balance in smallest units
    # initial-balance = 100000000000000
    #
    # # Initial base target
    # initial-base-target = 153722867
    #
    # # Average delay between blocks
    # average-block-delay = 60s
    #
    # # List of genesis transactions
    # transactions = [
    # {recipient = "BASE58ADDRESS1", amount = 50000000000000},
    # {recipient = "BASE58ADDRESS2", amount = 50000000000000}
    #       ]
    #   }
    # }
  }

  # New blocks generator settings
  miner {
    # Enable / disable block generation
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
    max-pack-time = $ {waves.miner.micro-block-interval}
  }

  # Node's REST API settings
  rest-api {
    # Enable / disable REST API
    enable = yes

    # Network address to bind to
    bind-address = "127.0.0.1"

    # Port to listen to REST API requests
    port = 6869

    # Hash of API key string
    api-key-hash = ""

    # Enable / disable CORS support
    cors = yes

    # Enable / disable X-API-Key from different host
    api-key-different-host = no

    # Max number of transactions
    # returned by / transactions / address / {address} / limit / {limit}
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