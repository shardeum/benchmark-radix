## Radix DLT TPS Test for Coin Transfer

##### Hardware: dedicated server at nocix.net

- Processor 2x E5-2660 @ 2.2GHz / 3GHz Turbo 16 Cores / 32 thread
- Ram 96 GB DDR3
- Disk 960 GB SSD
- Bandwidth 1Gbit Port: 200TB Transfer
- Operating System Ubuntu 18.04 (Bionic)

##### Issue found when running the network

- We were able to run nodes locally, but ran into error when testing the network-gateway.

##### Instructions to recreate this test

1.  Install required tools and dependencies.

    1. Java 17
    2. Docker
    3. https://github.com/radixdlt/radixdlt/blob/main/radixdlt-core/README.md
    4. https://github.com/radixdlt/radixdlt.git
    5. https://github.com/radixdlt/radixdlt-network-gateway

2.  Create a 5 local nodes network.

    1.  Go to the _[radixdlt](https://github.com/radixdlt/radixdlt.git)_ installed path.
    2.  Checkout the required branch: e.g.
        - cd radixdlt && git checkout release/1.0-beta.34
    3.  Before starting the network, make sure it is working well by
        - ./gradlew test integrationTest
    4.  cd radixdlt-core
    5.  ./docker/scripts/rundocker.sh 5
    6.  Some endpoints to test if the network is running okay.
        - curl 'localhost:3333/system/version'
        - curl --request POST 'localhost:3333/network/configuration' --data-raw '{}'

3.  Temporary script to interact with the network.

    1.  [https://github.com/radixdlt/radixdlt-javascript](https://github.com/radixdlt/radixdlt-javascript)
    2.  This JS lib is customed to interacts with `radixdlt-network-gateway`
    3.  At the time writing this, the version of `npm i @radixdlt/application` is 4.0.15.
    4.  The testing script to interact with the network.

        ```
        import application from '@radixdlt/application';
        const { Radix } = application;
        import { firstValueFrom } from 'rxjs'
        const radix = Radix.create();

        const testing = async () => {

            try {
                await radix.connect('http://localhost:8080')
                // const address1 = await firstValueFrom(radix.targetApiVersion)
                // console.log(address1)

                // await radix.connect('https://mainnet-gateway.radixdlt.com')

                const networkId = await firstValueFrom(radix.ledger.networkId())
                console.log(networkId)
            } catch (e) {
                console.log(e)
            }
        }

        testing()

        ```

4.  Testing `radixdlt-network-gateway`.

    1. https://github.com/radixdlt/radixdlt-network-gateway/tree/main/deployment
    2. Configuring the steps as described above.
    3. https://github.com/radixdlt/radixdlt-network-gateway/tree/main/deployment#starting-the-services

       - At the time of writing this, the release tag of the repo is `1.1.1`
       - Running the scripts as above gives the error and we weren't able to test it.

       ```
       fullnode_1               | # 2022-02-21T09:43:13,952 [INFO/MemoryLeakDetector/main] (MemoryLeakDetector.java:101) - Starting memory leak detector...
       fullnode_1               | # 2022-02-21T09:43:13,958 [OFF/Radix/main] (:) - Radix distributed ledger 'HEAD-a464be9' from branch 'HEAD' commit 'a464be9d5faa5f2d251b505caf5b6b4b494df371'
       fullnode_1               | # 2022-02-21T09:43:14,148 [INFO/PersistedProperties/main] (PersistedProperties.java:114) - Loaded properties from default.config
       fullnode_1               | Feb 21, 2022 9:43:14 AM com.google.inject.internal.MessageProcessor visit
       fullnode_1               | INFO: An exception was caught and reported. Message: java.lang.IllegalStateException: No genesis txn specified.
       fullnode_1               | java.lang.IllegalStateException: No genesis txn specified.
       fullnode_1               | 	at com.radixdlt.RadixNodeModule.loadGenesis(RadixNodeModule.java:178)
       fullnode_1               | 	at com.radixdlt.RadixNodeModule.configure(RadixNodeModule.java:195)
       fullnode_1               | 	at com.google.inject.AbstractModule.configure(AbstractModule.java:64)
       fullnode_1               | 	at com.google.inject.spi.Elements$RecordingBinder.install(Elements.java:409)
       fullnode_1               | 	at com.google.inject.spi.Elements.getElements(Elements.java:108)
       fullnode_1               | 	at com.google.inject.internal.InjectorShell$Builder.build(InjectorShell.java:160)
       fullnode_1               | 	at com.google.inject.internal.InternalInjectorCreator.build(InternalInjectorCreator.java:107)
       fullnode_1               | 	at com.google.inject.Guice.createInjector(Guice.java:87)
       fullnode_1               | 	at com.google.inject.Guice.createInjector(Guice.java:69)
       fullnode_1               | 	at com.google.inject.Guice.createInjector(Guice.java:59)
       fullnode_1               | 	at org.radix.Radix.start(Radix.java:194)
       fullnode_1               | 	at org.radix.Radix.main(Radix.java:171)
       fullnode_1               |
       fullnode_1               | # 2022-02-21T09:43:14,405 [FATAL/Radix/main] (Radix.java:173) - Unable to start
       fullnode_1               | com.google.inject.CreationException: Unable to create injector, see the following errors:
       fullnode_1               |
       fullnode_1               | 1) [Guice/MissingImplementation]: No implementation for Txn annotated with @Genesis() was bound.
       fullnode_1               |
       fullnode_1               | Requested by:
       fullnode_1               | 1  : RadixNodeModule.genesis(RadixNodeModule.java:138)
       fullnode_1               |       \_ for 1st parameter
       fullnode_1               |      at RadixNodeModule.genesis(RadixNodeModule.java:138)
       fullnode_1               |
       fullnode_1               | Learn more:
       fullnode_1               |   https://github.com/google/guice/wiki/MISSING_IMPLEMENTATION
       fullnode_1               |
       fullnode_1               | 2) [Guice/MissingImplementation]: No implementation for LedgerAccumulator was bound.
       fullnode_1               |
       fullnode_1               | Requested by:
       fullnode_1               | 1  : GenesisBuilder.<init>(GenesisBuilder.java:97)
       fullnode_1               |       \_ for 2nd parameter
       fullnode_1               |      at RadixNodeModule.genesis(RadixNodeModule.java:138)
       fullnode_1               |       \_ for 2nd parameter
       fullnode_1               |      at RadixNodeModule.genesis(RadixNodeModule.java:138)
       fullnode_1               |
       fullnode_1               | Learn more:
       fullnode_1               |   https://github.com/google/guice/wiki/MISSING_IMPLEMENTATION
       fullnode_1               |
       fullnode_1               | 3) An exception was caught and reported. Message: No genesis txn specified.
       fullnode_1               |   at [unknown source]
       fullnode_1               |
       fullnode_1               | 4) [Guice/MissingConstructor]: No injectable constructor for type RERules.
       fullnode_1               |
       fullnode_1               | class RERules does not have a @Inject annotated constructor or a no-arg constructor.
       fullnode_1               |
       fullnode_1               | Requested by:
       fullnode_1               | 1  : RERules.class(RERules.java:92)
       fullnode_1               |      at GenesisBuilder.<init>(GenesisBuilder.java:97)
       fullnode_1               |       \_ for 1st parameter
       fullnode_1               |      at RadixNodeModule.genesis(RadixNodeModule.java:138)
       fullnode_1               |       \_ for 2nd parameter
       fullnode_1               |      at RadixNodeModule.genesis(RadixNodeModule.java:138)
       fullnode_1               |
       fullnode_1               | Learn more:
       fullnode_1               |   https://github.com/google/guice/wiki/MISSING_CONSTRUCTOR
       fullnode_1               |
       fullnode_1               | 4 errors
       fullnode_1               |
       fullnode_1               | ======================
       fullnode_1               | Full classname legend:
       fullnode_1               | ======================
       fullnode_1               | Genesis:           "com.radixdlt.statecomputer.checkpoint.Genesis"
       fullnode_1               | GenesisBuilder:    "com.radixdlt.statecomputer.checkpoint.GenesisBuilder"
       fullnode_1               | LedgerAccumulator: "com.radixdlt.ledger.LedgerAccumulator"
       fullnode_1               | RERules:           "com.radixdlt.statecomputer.forks.RERules"
       fullnode_1               | RadixNodeModule:   "com.radixdlt.RadixNodeModule"
       fullnode_1               | Txn:               "com.radixdlt.atom.Txn"
       fullnode_1               | ========================
       ```
