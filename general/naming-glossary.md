# Glossary of Naming Standards

You are an expert software engineer tasked with maintaining API and architectural clarity and developer ergonomics. The following glossary establishes a standardized verb taxonomy for function and method naming and standard noun and adjective taxonomies for naming objects and conditions. By adhering to these semantic signals, you create self-documenting code where the verb alone communicates intent, side effects, return types, and computational complexity. Use this as a definitive reference for naming decisions to ensure consistency across modules and teams.

---

## The Programmer's Verb Taxonomy

### 1. **Data Access & Retrieval**
Verbs that fetch data without side effects (ideally pure functions).

| Verb | Signal | Typical Pattern |
|------|--------|----------------|
| **get** | Immediate, synchronous retrieval from memory or cache | `getUser()`, `getConfig()` |
| **fetch** | Asynchronous retrieval over network or I/O boundary | `fetchData()`, `fetchRemoteConfig()` |
| **retrieve** | Complex lookup with potential processing overhead | `retrieveHistoricalData()` |
| **load** | Bulk retrieval into working memory/active state | `loadFile()`, `loadDataset()` |
| **read** | Consumption of stream or buffer content | `readStream()`, `readBuffer()` |
| **find** | Search returning single result or null/undefined | `findById()`, `findUser()` |
| **search** | Query returning collection of matches | `searchProducts()`, `searchKeywords()` |
| **query** | Structured data retrieval (often database/SQL) | `queryDatabase()`, `querySelector()` |
| **lookup** | Key-based retrieval from map/dictionary | `lookupCache()`, `lookupSymbol()` |
| **resolve** | Promise/settlement of async value or path | `resolvePath()`, `resolveDependency()` |

### 2. **Mutation & State Change**
Verbs indicating data modification or side effects.

| Verb | Signal | Typical Pattern |
|------|--------|----------------|
| **set** | Direct assignment or replacement | `setName()`, `setOptions()` |
| **update** | Partial modification preserving identity | `updateRecord()`, `updateState()` |
| **modify** | Alteration with potential structural change | `modifySchema()`, `modifyHeaders()` |
| **edit** | User-initiated content change | `editDocument()`, `editProfile()` |
| **change** | Toggle between predefined states | `changeStatus()`, `changeMode()` |
| **assign** | Allocation of reference or value | `assignRole()`, `assignId()` |
| **replace** | Complete substitution of entity | `replaceChild()`, `replaceToken()` |
| **swap** | Exchange between two entities | `swapElements()`, `swapBuffers()` |
| **reset** | Return to initial/default state | `resetForm()`, `resetCounter()` |
| **clear** | Removal of all contents/selections | `clearCache()`, `clearScreen()` |

### 3. **Creation & Construction**
Verbs that instantiate or generate new entities.

| Verb | Signal | Typical Pattern |
|------|--------|----------------|
| **create** | General instantiation with side effects | `createUser()`, `createFile()` |
| **make** | Factory pattern construction | `makeRequest()`, `makeObservable()` |
| **build** | Step-by-step assembly (often Builder pattern) | `buildQuery()`, `buildTree()` |
| **generate** | Algorithmic production of data/IDs | `generateUUID()`, `generateReport()` |
| **construct** | Complex object assembly with dependencies | `constructMatrix()`, `constructPayload()` |
| **initialize** / **init** | Setup of ready-to-use state | `initializeApp()`, `initModule()` |
| **instantiate** | Runtime creation from class/template | `instantiateClass()`, `instantiateComponent()` |
| **clone** | Deep copy of existing entity | `cloneObject()`, `cloneNode()` |
| **copy** | Shallow duplication | `copyArray()`, `copyFile()` |
| **derive** | Creation based on existing data transformation | `deriveKey()`, `deriveState()` |

### 4. **Deletion & Cleanup**
Verbs for removal and resource disposal.

| Verb | Signal | Typical Pattern |
|------|--------|----------------|
| **delete** | Permanent removal (often irreversible) | `deleteRecord()`, `deleteFile()` |
| **remove** | Extraction from collection without destruction | `removeItem()`, `removeClass()` |
| **destroy** | Complete teardown with resource cleanup | `destroySession()`, `destroyComponent()` |
| **dispose** | Release of managed resources (memory handles) | `disposeConnection()`, `disposeSubscription()` |
| **drop** | Database/structural removal | `dropTable()`, `dropIndex()` |
| **purge** | Aggressive cache/temporary cleanup | `purgeCache()`, `purgeOldLogs()` |
| **erase** | Secure/overwrite deletion | `eraseDisk()`, `eraseData()` |
| **unset** | Removal of property/variable binding | `unsetVariable()`, `unsetFlag()` |
| **close** | Termination of handle/stream | `closeFile()`, `closeSocket()` |
| **teardown** | Systematic reversal of setup | `teardownTests()`, `teardownEnvironment()` |

### 5. **Transformation & Conversion**
Verbs that input data and output new representation.

| Verb | Signal | Typical Pattern |
|------|--------|----------------|
| **convert** | Type or format change (A → B) | `convertCurrency()`, `convertToJSON()` |
| **transform** | Structural or semantic alteration | `transformData()`, `transformStream()` |
| **parse** | String → Structured data | `parseJSON()`, `parseDate()` |
| **stringify** | Structured data → String | `stringifyObject()`, `stringifyQuery()` |
| **serialize** | Object → Storable/transmittable format | `serializeState()`, `serializeForm()` |
| **deserialize** | Reverse of serialize | `deserializePayload()`, `deserializeConfig()` |
| **format** | Human-readable presentation | `formatDate()`, `formatNumber()` |
| **cast** | Coercive type conversion | `castToString()`, `castArray()` |
| **map** | Element-wise transformation | `mapValues()`, `mapResults()` |
| **flatten** | Nested → Flat structure | `flattenArray()`, `flattenTree()` |
| **normalize** | Standardization to canonical form | `normalizeData()`, `normalizeVector()` |
| **compress** / **decompress** | Size reduction/expansion | `compressImage()`, `decompressData()` |
| **encode** / **decode** | Representation translation | `encodeBase64()`, `decodeURIComponent()` |

### 6. **Validation & Assertion**
Verbs that check conditions and return boolean or throw.

| Verb | Signal | Typical Pattern |
|------|--------|----------------|
| **validate** | Comprehensive constraint checking (throws/errors) | `validateInput()`, `validateSchema()` |
| **verify** | Confirmation of correctness/truth | `verifyToken()`, `verifySignature()` |
| **check** | Lightweight condition inspection | `checkAvailability()`, `checkBounds()` |
| **ensure** | Assertion with auto-correction or throw | `ensureDirectory()`, `ensureArray()` |
| **assert** | Development-time invariant checking (throws if false) | `assertEqual()`, `assertNotNull()` |
| **is** / **has** / **can** / **should** | Boolean predicate prefixes | `isValid()`, `hasPermission()`, `canEdit()`, `shouldUpdate()` |
| **exists** | Presence verification | `existsSync()`, `fileExists()` |
| **matches** | Pattern/regression testing | `matchesRegex()`, `matchesPattern()` |
| **contains** / **includes** | Membership testing | `containsKey()`, `includesValue()` |
| **equals** | Equality comparison | `equalsIgnoreCase()`, `deepEquals()` |

### 7. **Execution & Invocation**
Verbs that trigger processes or callbacks.

| Verb | Signal | Typical Pattern |
|------|--------|----------------|
| **run** | General execution of task/process | `runTests()`, `runQuery()` |
| **execute** | Formal/structured command execution | `executeCommand()`, `executeTransaction()` |
| **perform** | Action with potential side effects | `performCleanup()`, `performSearch()` |
| **invoke** | Direct function/method calling | `invokeMethod()`, `invokeCallback()` |
| **call** | Immediate synchronous execution | `callFunction()`, `callHook()` |
| **apply** | Execution with context/argument binding | `applyMiddleware()`, `applyChanges()` |
| **dispatch** | Event/action routing to handlers | `dispatchEvent()`, `dispatchAction()` |
| **trigger** | Initiation of event/cascade | `triggerUpdate()`, `triggerAnimation()` |
| **emit** | Broadcasting event/signal | `emitEvent()`, `emitChange()` |
| **fire** | Immediate callback invocation | `fireCallback()`, `fireTimer()` |
| **handle** | Event processing entry point | `handleClick()`, `handleError()` |
| **process** | Systematic data handling pipeline | `processPayment()`, `processQueue()` |

### 8. **Calculation & Computation**
Verbs for deterministic mathematical/logical operations.

| Verb | Signal | Typical Pattern |
|------|--------|----------------|
| **calculate** | Mathematical derivation | `calculateSum()`, `calculateDistance()` |
| **compute** | Algorithmic processing | `computeHash()`, `computeLayout()` |
| **determine** | Logical deduction of state/value | `determineWinner()`, `determineOptimalPath()` |
| **evaluate** | Expression/condition resolution | `evaluateExpression()`, `evaluatePolicy()` |
| **derive** | Deduction from existing values | `deriveColor()`, `deriveKey()` |
| **estimate** | Approximation/prediction | `estimateTime()`, `estimateSize()` |
| **measure** | Physical quantity assessment | `measureText()`, `measurePerformance()` |
| **count** | Quantification | `countLines()`, `countOccurrences()` |
| **sum** | Aggregation | `sumValues()`, `sumTotal()` |
| **average** / **avg** | Mean calculation | `averageScores()`, `avgTemperature()` |
| **aggregate** | Collection summarization | `aggregateData()`, `aggregateStats()` |
| **reduce** | Fold operation (functional) | `reduceArray()`, `reduceState()` |

### 9. **Collection Operations**
Verbs specific to array/list/map manipulations.

| Verb | Signal | Typical Pattern |
|------|--------|----------------|
| **add** | General inclusion | `addItem()`, `addEventListener()` |
| **insert** | Placement at specific index/position | `insertRow()`, `insertBefore()` |
| **append** | Addition to end | `appendChild()`, `appendData()` |
| **prepend** | Addition to beginning | `prependHeader()`, `prependItem()` |
| **push** / **pop** | Stack operations | `pushState()`, `popElement()` |
| **shift** / **unshift** | Queue operations | `shiftQueue()`, `unshiftItem()` |
| **concat** | Concatenation | `concatArrays()`, `concatStrings()` |
| **merge** | Deep combination (often objects) | `mergeObjects()`, `mergeConfigs()` |
| **join** | Connection with separator | `joinPath()`, `joinQueryParams()` |
| **split** | Separation by delimiter | `splitString()`, `splitChunks()` |
| **slice** | Extraction of sub-range | `sliceArray()`, `sliceBuffer()` |
| **splice** | Removal/replacement at index | `spliceArray()` |
| **filter** | Conditional subset selection | `filterActive()`, `filterByType()` |
| **select** | Specific item picking | `selectFields()`, `selectOptions()` |
| **distinct** / **unique** | Duplicate elimination | `distinctValues()`, `uniqueIds()` |
| **group** | Categorization by key | `groupByCategory()`, `groupResults()` |
| **partition** | Binary splitting | `partitionArray()`, `partitionData()` |
| **sort** | Ordering arrangement | `sortByDate()`, `sortDescending()` |
| **reverse** | Order inversion | `reverseArray()`, `reverseString()` |
| **shuffle** | Random reordering | `shuffleDeck()`, `shuffleArray()` |
| **zip** | Interleaving of collections | `zipArrays()`, `zipFiles()` |
| **flatten** | Nested to flat | `flattenHierarchy()` |

### 10. **I/O & Network Operations**
Verbs for external system communication.

| Verb | Signal | Typical Pattern |
|------|--------|----------------|
| **read** | Input stream consumption | `readFile()`, `readStream()` |
| **write** | Output to destination | `writeFile()`, `writeResponse()` |
| **save** | Persistent storage | `saveDocument()`, `saveSettings()` |
| **download** | Network → Local storage | `downloadImage()`, `downloadBlob()` |
| **upload** | Local → Remote storage | `uploadFile()`, `uploadData()` |
| **send** | Transmission to recipient | `sendEmail()`, `sendRequest()` |
| **receive** | Incoming data acceptance | `receiveMessage()`, `receivePacket()` |
| **post** | HTTP POST / message posting | `postData()`, `postMessage()` |
| **put** | HTTP PUT / idempotent update | `putResource()`, `putRecord()` |
| **patch** | HTTP PATCH / partial update | `patchDocument()`, `patchEntity()` |
| **subscribe** / **unsubscribe** | Event stream registration | `subscribeTopic()`, `unsubscribeChannel()` |
| **connect** / **disconnect** | Connection lifecycle | `connectDatabase()`, `disconnectSocket()` |
| **listen** | Port/event waiting | `listenPort()`, `listenEvents()` |
| **accept** | Incoming connection handling | `acceptConnection()` |

### 11. **Lifecycle & State Management**
Verbs for component/system state transitions.

| Verb | Signal | Typical Pattern |
|------|--------|----------------|
| **setup** | Initial configuration | `setupServer()`, `setupListeners()` |
| **configure** | Parameter adjustment | `configureOptions()`, `configureStore()` |
| **prepare** | Readiness operations | `prepareData()`, `prepareEnvironment()` |
| **start** | Initiation of operation | `startEngine()`, `startTimer()` |
| **stop** | Cessation of operation | `stopProcess()`, `stopAnimation()` |
| **pause** / **resume** | Temporary suspension | `pauseVideo()`, `resumeStream()` |
| **enable** / **disable** | Capability toggling | `enableFeature()`, `disableButton()` |
| **activate** / **deactivate** | Focus/primary state toggling | `activateTab()`, `deactivateModule()` |
| **show** / **hide** | Visibility control | `showModal()`, `hideLoading()` |
| **open** / **close** | Access state control | `openConnection()`, `closeDialog()` |
| **lock** / **unlock** | Access restriction | `lockFile()`, `unlockMutex()` |
| **toggle** | Binary state switching | `toggleVisibility()`, `toggleSwitch()` |
| **mount** / **unmount** | DOM/Runtime attachment | `mountComponent()`, `unmountApp()` |
| **bootstrap** | System initialization | `bootstrapApp()`, `bootstrapModule()` |

### 12. **Asynchronous & Concurrency Control**
Verbs specific to timing and parallel execution.

| Verb | Signal | Typical Pattern |
|------|--------|----------------|
| **async** | Asynchronous operation marker | `asyncLoad()`, `asyncProcess()` |
| **await** | Promise resolution waiting | `awaitResult()`, `awaitCondition()` |
| **defer** | Delayed execution | `deferUpdate()`, `deferCalculation()` |
| **delay** | Time-based postponement | `delayExecution()`, `delayRetry()` |
| **schedule** | Timed execution planning | `scheduleTask()`, `scheduleJob()` |
| **queue** | FIFO ordering | `queueAction()`, `queueRequest()` |
| **throttle** | Rate limiting (time-based) | `throttleScroll()`, `throttleInput()` |
| **debounce** | Group rapid calls into one | `debounceSearch()`, `debounceResize()` |
| **retry** | Failure recovery attempt | `retryOperation()`, `retryFetch()` |
| **timeout** | Execution time limiting | `timeoutRequest()`, `timeoutPromise()` |
| **cancel** | Abortion of pending operation | `cancelRequest()`, `cancelAnimation()` |
| **abort** | Forceful termination | `abortController()`, `abortFetch()` |
| **wait** | Blocking/delaying | `waitFor()`, `waitUntil()` |
| **sync** / **synchronize** | State alignment | `syncData()`, `synchronizeTime()` |

### 13. **Testing & Quality Assurance**
Verbs for verification and test construction.

| Verb | Signal | Typical Pattern |
|------|--------|----------------|
| **test** | Execution of test case | `testFunctionality()`, `testEdgeCase()` |
| **mock** | Fake implementation creation | `mockApi()`, `mockFunction()` |
| **stub** | Placeholder method replacement | `stubMethod()`, `stubResponse()` |
| **spy** | Observation wrapper | `spyOnMethod()`, `spyConsole()` |
| **assert** | Expectation verification | `assertTrue()`, `assertThrows()` |
| **expect** | Assertion declaration (BDD style) | `expectValue()`, `expectError()` |
| **given** | Precondition setup (Gherkin) | `givenUser()`, `givenData()` |
| **when** | Action trigger (Gherkin) | `whenClicking()`, `whenSubmitting()` |
| **then** | Outcome verification (Gherkin) | `thenResult()`, `thenError()` |
| **arrange** / **act** / **assert** | AAA pattern | `arrangeData()`, `actOnTarget()` |
| **verify** | Mock interaction checking | `verifyCalled()`, `verifyParams()` |
| **snapshot** | Regression capture | `snapshotComponent()`, `snapshotState()` |
| **benchmark** | Performance measurement | `benchmarkSort()`, `benchmarkApi()` |
| **fuzz** | Randomized input testing | `fuzzInput()`, `fuzzParser()` |

---

## Composition Guidelines

**Prefix Modifiers** (use to refine core verbs):
- `try` (e.g., `tryParse`, `tryLock`) - Attempt without throwing
- `re` (e.g., `retry`, `reload`, `refresh`) - Repetition/re-execution
- `pre` / `post` (e.g., `preprocess`, `postprocess`) - Temporal positioning
- `un` (e.g., `undo`, `unwrap`) - Reversal/extraction

**Suffix Patterns**:
- `By` (e.g., `sortByDate`, `groupById`) - Specifies discriminator
- `From` (e.g., `loadFromCache`, `readFromFile`) - Specifies source
- `To` (e.g., `convertToString`, `saveToDisk`) - Specifies destination
- `With` (e.g., `mergeWithDefaults`, `compareWithPrevious`) - Specifies context
- `Async` (e.g., `loadAsync`, `validateAsync`) - Explicit async marker

---

## Anti-Patterns to Avoid

1. **Ambiguous Synonym Mixing**: Don't use both `remove` and `delete` interchangeably; establish distinct semantics (e.g., `remove` from collection vs `delete` from database).

2. **Getter Side Effects**: Never use `get` for operations that modify state. Use `fetch`, `load`, or `create` instead.

3. **Boolean without Prefix**: Functions returning booleans should use `is`, `has`, `can`, or `should` prefixes, not bare adjectives (use `isEmpty()` not `empty()`).

4. **Noun-Verbing**: Avoid turning nouns into verbs (e.g., `componentize`, `objectify`) when standard alternatives exist.

5. **Overloaded Context**: Avoid `process` when a more specific verb (transform, validate, parse) applies.

Apply these verbs as semantic contracts: once a verb signals a specific behavior in your codebase, maintain that contract consistently across all modules.

 I'll create a comprehensive naming standards document that establishes standardized vocabularies for adjectives and nouns across namespaces, classes, variables, and arguments. This will complement your verb taxonomy with precise semantic categories for data types, domains, architectural patterns, and state descriptors.

---

## Entity Naming Standards

---

### Part 1: The Programmer's Noun Taxonomy

#### 1. **Identity & Reference Concepts**
Nouns describing how entities are identified, addressed, or referenced.

| Noun | Signal | Typical Usage |
|------|--------|-------------|
| **id** / **identifier** | Primary unique key (scalar) | `userId`, `requestId`, `sessionIdentifier` |
| **key** | Lookup or cryptographic identifier | `apiKey`, `cacheKey`, `primaryKey` |
| **uuid** / **guid** | Globally unique identifier (128-bit) | `transactionUuid`, `jobGuid` |
| **index** | Position or ordinal reference | `rowIndex`, `pageIndex`, `arrayIndex` |
| **offset** | Relative displacement from origin | `byteOffset`, `timeOffset` |
| **position** | Spatial or sequential location | `cursorPosition`, `scrollPosition` |
| **pointer** / **ptr** | Memory or reference address | `headPointer`, `dataPtr` |
| **ref** / **reference** | Indirect handle or citation | `nodeRef`, `forwardRef` |
| **handle** | Abstract resource identifier | `fileHandle`, `connectionHandle` |
| **tag** | Categorical label or marker | `htmlTag`, `versionTag` |
| **label** | Human-readable identifier | `axisLabel`, `categoryLabel` |
| **slug** | URL-safe string identifier | `postSlug`, `productSlug` |
| **token** | Opaque credential or lexical unit | `authToken`, `accessToken` |
| **code** | Error or classification symbol | `statusCode`, `countryCode` |
| **hash** | Cryptographic digest or checksum | `passwordHash`, `contentHash` |
| **checksum** | Integrity verification value | `fileChecksum`, `packetChecksum` |

---

#### 2. **Data Containers & Structures**
Nouns describing storage entities and collection types.

| Noun | Signal | Typical Usage |
|------|--------|-------------|
| **data** | General payload or content (untyped/generic) | `userData`, `responseData` |
| **item** / **element** | Single discrete unit in collection | `menuItem`, `listElement` |
| **entry** | Key-value pair or record in map/log | `logEntry`, `dictionaryEntry` |
| **record** | Database row or structured datum | `customerRecord`, `medicalRecord` |
| **row** | Horizontal record in tabular structure | `dataRow`, `spreadsheetRow` |
| **column** / **col** | Vertical field in tabular structure | `dataColumn`, `matrixCol` |
| **cell** | Intersection of row and column | `tableCell`, `gridCell` |
| **node** | Graph or tree vertex with connections | `treeNode`, `domNode`, `graphNode` |
| **vertex** / **edge** | Graph theory primitives | `graphVertex`, `edgeWeight` |
| **leaf** | Terminal node without children | `treeLeaf`, `decisionLeaf` |
| **root** | Origin or top-level node | `rootNode`, `rootElement` |
| **parent** / **child** | Hierarchical relationship | `parentContainer`, `childProcess` |
| **sibling** | Peer at same hierarchy level | `siblingNode`, `siblingWindow` |
| **ancestor** / **descendant** | Multi-level lineage | `ancestorComponent`, `descendantRoute` |
| **bucket** | Coarse-grained storage partition | `hashBucket`, `rateLimitBucket` |
| **shard** | Database horizontal partition | `dataShard`, `indexShard` |
| **partition** | Logical division of dataset | `timePartition`, `topicPartition` |
| **segment** | Continuous subsection | `memorySegment`, `urlSegment` |
| **chunk** | Discrete fragment (often for streaming) | `fileChunk`, `dataChunk` |
| **block** | Fixed-size allocation unit | `memoryBlock`, `dataBlock` |
| **buffer** | Temporary holding area for I/O | `readBuffer`, `ringBuffer` |
| **queue** | FIFO ordered collection | `messageQueue`, `taskQueue` |
| **stack** | LIFO ordered collection | `callStack`, `undoStack` |
| **heap** | Dynamic memory region or priority structure | `minHeap`, `objectHeap` |
| **pool** | Managed reusable resource collection | `threadPool`, `connectionPool` |
| **cache** | Fast-access storage layer | `l1Cache`, `resultCache` |
| **store** | Persistent or centralized repository | `dataStore`, `keyValueStore` |
| **repository** / **repo** | Domain object collection with query logic | `userRepo`, `orderRepository` |
| **registry** | Lookup service for named instances | `serviceRegistry`, `pluginRegistry` |
| **catalog** | Enumerated listing or schema collection | `schemaCatalog`, `productCatalog` |
| **schema** | Structural definition or blueprint | `databaseSchema`, `jsonSchema` |
| **metadata** | Data describing other data | `fileMetadata`, `requestMetadata` |

---

#### 3. **Temporal & Scheduling Concepts**
Nouns describing time, duration, and sequence.

| Noun | Signal | Typical Usage |
|------|--------|-------------|
| **time** | Instant or temporal coordinate | `startTime`, `currentTime` |
| **date** | Calendar day without time component | `birthDate`, `createdDate` |
| **datetime** / **timestamp** | Combined date and time instant | `eventTimestamp`, `modifiedDatetime` |
| **instant** | Point on timeline (often UTC) | `requestInstant`, `snapshotInstant` |
| **duration** | Time span without anchor | `animationDuration`, `timeoutDuration` |
| **interval** | Recurring time span or gap | `pollInterval`, `retryInterval` |
| **period** | Named or regular time division | `billingPeriod`, `gracePeriod` |
| **span** | Bounded continuous range | `lifeSpan`, `attentionSpan` |
| **timeout** | Maximum allowed duration | `connectionTimeout`, `requestTimeout` |
| **deadline** | Non-negotiable end time | `submissionDeadline`, `hardDeadline` |
| **schedule** | Planned execution timing | `jobSchedule`, `maintenanceSchedule` |
| **timer** | Trigger mechanism for future event | `idleTimer`, `debounceTimer` |
| **tick** | Discrete time step or heartbeat | `clockTick`, `gameTick` |
| **epoch** | Reference starting point | `unixEpoch`, `trainingEpoch` |
| **cycle** | Complete iteration or sequence | `renderCycle`, `lifeCycle` |
| **phase** | Distinct stage in sequence | `startupPhase`, `moonPhase` |
| **step** | Discrete increment in process | `workflowStep`, `interpolationStep` |
| **sequence** | Ordered progression | `dnaSequence`, `executionSequence` |
| **order** | Sequence or command placement | `sortOrder`, `executionOrder` |
| **history** | Chronological log of changes | `revisionHistory`, `browserHistory` |
| **log** | Append-only chronological record | `errorLog`, `auditLog` |
| **backup** | Snapshot for recovery | `databaseBackup`, `configBackup` |
| **snapshot** | Point-in-time capture | `stateSnapshot`, `vmSnapshot` |
| **version** | Identifiable iteration of artifact | `apiVersion`, `schemaVersion` |

---

#### 4. **Quantitative & Mathematical Concepts**
Nouns describing measurements, calculations, and numeric properties.

| Noun | Signal | Typical Usage |
|------|--------|-------------|
| **count** | Discrete quantity (cardinality) | `itemCount`, `retryCount` |
| **total** | Aggregated sum | `grandTotal`, `runningTotal` |
| **sum** | Arithmetic addition result | `partialSum`, `checksum` |
| **number** / **num** / **no** | Generic numeric identifier | `orderNumber`, `serialNum` |
| **value** | Scalar datum or variable content | `returnValue`, `configValue` |
| **amount** | Monetary or continuous quantity | `transactionAmount`, `dosageAmount` |
| **quantity** / **qty** | Discrete countable units | `stockQuantity`, `qtyOrdered` |
| **metric** | Measured performance indicator | `latencyMetric`, `errorMetric` |
| **measurement** | Observation with units | `distanceMeasurement`, `temperatureMeasurement` |
| **size** | Spatial extent or magnitude | `fileSize`, `bufferSize` |
| **length** | One-dimensional extent | `arrayLength`, `stringLength` |
| **width** / **height** / **depth** | Dimensional extents | `canvasWidth`, `treeDepth` |
| **area** / **volume** | Multi-dimensional magnitude | `surfaceArea`, `dataVolume` |
| **capacity** | Maximum containment | `queueCapacity`, `batteryCapacity` |
| **limit** / **threshold** | Boundary or maximum allowed | `rateLimit`, `painThreshold` |
| **bound** / **boundary** | Constraint delimiter | `upperBound`, `arrayBounds` |
| **range** | Interval between min and max | `validRange`, `ageRange` |
| **minimum** / **min** | Lower extreme | `priceMinimum`, `minValue` |
| **maximum** / **max** | Upper extreme | `concurrencyMax`, `maxRetries` |
| **average** / **avg** | Central tendency | `movingAverage`, `classAvg` |
| **median** | Middle value in ordered set | `responseMedian`, `incomeMedian` |
| **mean** | Arithmetic average (statistical) | `populationMean`, `sampleMean` |
| **mode** | Most frequent value | `colorMode`, `transportMode` |
| **deviation** / **stddev** | Dispersion from center | `standardDeviation`, `meanDeviation` |
| **variance** | Squared dispersion | `statisticalVariance`, `budgetVariance` |
| **percentile** / **quartile** | Rank-based position | `95thPercentile`, `upperQuartile` |
| **ratio** | Proportional relationship | `aspectRatio`, `signalNoiseRatio` |
| **rate** | Frequency or velocity | `frameRate`, `successRate` |
| **velocity** / **speed** | Rate of position change | `scrollVelocity`, `windSpeed` |
| **acceleration** | Rate of velocity change | `gestureAcceleration` |
| **frequency** / **freq** | Occurrence rate per time | `samplingFrequency`, `cpuFreq` |
| **amplitude** | Signal magnitude | `waveAmplitude`, `vibrationAmplitude` |
| **magnitude** | Absolute size without direction | `vectorMagnitude`, `earthquakeMagnitude` |
| **factor** | Multiplicative coefficient | `scalingFactor`, `loadFactor` |
| **coefficient** / **coef** | Parameter in equation | `frictionCoefficient`, `correlationCoef` |
| **constant** / **const** | Immutable defined value | `PI_CONSTANT`, `GRAVITY_CONST` |
| **variable** / **var** | Mutable storage | `loopVar`, `instanceVariable` |
| **parameter** / **param** | Function input placeholder | `queryParam`, `pathParam` |
| **argument** / **arg** | Actual value passed to function | `cliArgument`, `functionArg` |
| **operand** | Value operated upon | `leftOperand`, `binaryOperand` |
| **result** / **res** | Operation output | `queryResult`, `calculationRes` |
| **remainder** / **mod** | Division residual | `divisionRemainder`, `modValue` |
| **quotient** | Division result | `integerQuotient` |
| **product** | Multiplication result | `scalarProduct`, `dotProduct` |
| **delta** / **diff** | Difference or change | `timeDelta`, `priceDiff` |
| **gradient** | Directional rate of change | `colorGradient`, `lossGradient` |
| **vector** | Directional magnitude array | `featureVector`, `velocityVector` |
| **matrix** | 2D numeric array | `transformationMatrix`, `confusionMatrix` |
| **tensor** | Multi-dimensional array | `weightTensor`, `inputTensor` |
| **scalar** | Single numeric value | `temperatureScalar`, `rewardScalar` |
| **point** | Zero-dimensional location | `coordinatePoint`, `dataPoint` |
| **coordinates** / **coords** | Position specification | `gpsCoordinates`, `textureCoords` |
| **angle** | Rotational measure | `rotationAngle`, `incidentAngle` |
| **radian** / **degree** | Angular units | `thetaRadians`, `headingDegrees` |
| **origin** | Zero-point reference | `coordinateOrigin`, `requestOrigin` |
| **axis** | Reference line/dimension | `xAxis`, `rotationAxis` |
| **curve** | Continuous function shape | `bezierCurve`, `learningCurve` |
| **slope** | Rate of incline | `regressionSlope`, `tangentSlope` |
| **intercept** | Axis crossing point | `yIntercept`, `xIntercept` |
| **threshold** | Activation or decision boundary | `confidenceThreshold`, `alertThreshold` |

---

#### 5. **State & Condition Concepts**
Nouns describing entity conditions and operational modes.

| Noun | Signal | Typical Usage |
|------|--------|-------------|
| **state** | Complete condition snapshot | `appState`, `componentState`, `finiteState` |
| **status** | High-level condition indicator | `httpStatus`, `paymentStatus`, `jobStatus` |
| **mode** | Operational behavioral configuration | `darkMode`, `editMode`, `safeMode` |
| **phase** | Stage in lifecycle or process | `initializationPhase`, `moonPhase` |
| **condition** | Boolean or environmental state | `weatherCondition`, `raceCondition` |
| **flag** | Boolean indicator or feature toggle | `featureFlag`, `errorFlag` |
| **toggle** | Binary switch state | `visibilityToggle`, `syncToggle` |
| **switch** | Control mechanism for state change | `featureSwitch`, `killSwitch` |
| **setting** / **config** | Configurable parameter value | `userSetting`, `appConfig` |
| **preference** / **pref** | User-defined choice | `localePreference`, `notificationPref` |
| **option** | Available choice or alternative | `dropdownOption`, `commandLineOption` |
| **property** / **prop** | Object attribute or characteristic | `cssProperty`, `componentProp` |
| **attribute** / **attr** | Metadata characteristic | `htmlAttribute`, `fileAttribute` |
| **characteristic** | Defining quality or trait | `productCharacteristic`, `deviceCharacteristic` |
| **quality** | Degree of excellence or property | `imageQuality`, `airQuality` |
| **level** | Graduated degree or layer | `logLevel`, `compressionLevel` |
| **grade** | Ranked classification | `securityGrade`, `tumorGrade` |
| **tier** | Hierarchical service/strata level | `pricingTier`, `memoryTier` |
| **class** | Taxonomic or type category | `objectClass`, `seatClass`, `socialClass` |
| **category** / **cat** | General classification | `productCategory`, `errorCategory` |
| **type** | Fundamental nature or kind | `dataType`, `mimeType`, `bloodType` |
| **kind** | General variety (softer than type) | `tokenKind`, `nodeKind` |
| **sort** | Colloquial variety or ordering | `sortOrder`, `bubbleSort` |
| **variant** | Alternative form of same base | `colorVariant`, `protocolVariant` |
| **flavor** | Slight implementation difference | `syntaxFlavor`, `linuxFlavor` |
| **style** | Aesthetic or idiomatic approach | `codingStyle`, `architecturalStyle` |
| **pattern** | Recurring design or structure | `designPattern`, `regexPattern` |
| **format** | Data arrangement specification | `dateFormat`, `imageFormat` |
| **encoding** | Character or binary representation | `textEncoding`, `videoEncoding` |
| **schema** | Structural validation blueprint | `jsonSchema`, `xmlSchema` |
| **structure** / **struct** | Organized composition | `dataStructure`, `cStruct` |
| **layout** | Spatial or visual arrangement | `memoryLayout`, `pageLayout` |
| **arrangement** | Ordered positioning | `pixelArrangement`, `furnitureArrangement` |
| **configuration** / **cfg** | Specific setup state | `systemConfiguration`, `networkCfg` |
| **context** | Environmental or execution setting | `requestContext`, `threadContext`, `authContext` |
| **scope** | Visibility or lifetime boundary | `variableScope`, `requestScope` |
| **environment** / **env** | External system conditions | `runtimeEnvironment`, `processEnv` |
| **situation** | Temporary composite circumstance | `emergencySituation`, `marketSituation` |
| **scenario** | Hypothetical or test case context | `testScenario`, `usageScenario` |
| **case** | Specific instance or condition | `edgeCase`, `useCase`, `switchCase` |
| **instance** | Concrete occurrence of type | `classInstance`, `serviceInstance` |
| **occurrence** | Event of happening | `rareOccurrence`, `patternOccurrence` |
| **event** | Discrete happening or signal | `clickEvent`, `domainEvent` |
| **incident** | Problem or notable occurrence | `securityIncident`, `serviceIncident` |
| **anomaly** / **outlier** | Deviation from normal | `statisticalAnomaly`, `dataOutlier` |
| **exception** | Error or special case | `runtimeException`, `businessException` |
| **error** | Failure or mistake condition | `syntaxError`, `networkError` |
| **fault** | Defect causing malfunction | `hardwareFault`, `singlePointOfFault` |
| **failure** | Unsuccessful operation outcome | `systemFailure`, `loginFailure` |
| **defect** / **bug** | Implementation flaw | `softwareDefect`, `regressionBug` |
| **issue** | Reported problem or concern | `githubIssue`, `complianceIssue` |
| **warning** / **alert** | Cautionary notification | `deprecationWarning`, `securityAlert` |
| **notice** | Informational notification | `maintenanceNotice`, `legalNotice` |
| **hint** | Suggestion or clue | `searchHint`, `optimizationHint` |
| **message** | Communication content | `errorMessage`, `welcomeMessage` |
| **notification** / **notice** | User-facing alert | `pushNotification`, `emailNotice` |
| **signal** | Mechanism for event communication | `unixSignal`, `tradingSignal` |
| **indicator** | Measurement display or marker | `progressIndicator`, `healthIndicator` |

---

#### 6. **User & Role Concepts**
Nouns describing actors, permissions, and organizational entities.

| Noun | Signal | Typical Usage |
|------|--------|-------------|
| **user** | Human or system actor | `currentUser`, `endUser`, `systemUser` |
| **account** | User-controlled container | `bankAccount`, `userAccount`, `awsAccount` |
| **profile** | Public or detailed user representation | `userProfile`, `companyProfile` |
| **persona** | Role-based user archetype | `adminPersona`, `buyerPersona` |
| **actor** | Entity performing actions (theater/UML) | `systemActor`, `threatActor` |
| **subject** | Security principal being authenticated | `accessSubject`, `jwtSubject` |
| **principal** | Authenticated security entity | `securityPrincipal`, `kerberosPrincipal` |
| **identity** / **id** | Verified who-ness | `digitalIdentity`, `corporateId` |
| **credential** / **creds** | Proof of identity | `loginCredentials`, `dbCreds` |
| **password** / **passwd** / **pwd** | Secret authentication string | `userPassword`, `dbPasswd` |
| **passphrase** | Longer password alternative | `walletPassphrase`, `encryptionPassphrase` |
| **token** | Opaque credential string | `bearerToken`, `refreshToken`, `csrfToken` |
| **ticket** | Session or access grant | `serviceTicket`, `lotteryTicket`, `supportTicket` |
| **session** | Bounded interaction period | `userSession`, `browserSession` |
| **role** | Permission grouping | `adminRole`, `editorRole`, `guestRole` |
| **group** | Collection of users/permissions | `securityGroup`, `userGroup`, `ageGroup` |
| **organization** / **org** | Business or structural unit | `nonprofitOrg`, `orgChart`, `orgId` |
| **team** | Collaborative work group | `engineeringTeam`, `salesTeam` |
| **department** / **dept** | Organizational division | `hrDepartment`, `itDept` |
| **division** | Major organizational segment | `easternDivision`, `researchDivision` |
| **unit** | Smallest organizational cell | `businessUnit`, `militaryUnit` |
| **member** | Belonging individual | `teamMember`, `classMember`, `setMember` |
| **owner** | Responsible party with full rights | `fileOwner`, `recordOwner`, `businessOwner` |
| **admin** / **administrator** | Superuser with elevated rights | `systemAdmin`, `databaseAdministrator` |
| **moderator** / **mod** | Content overseer | `forumModerator`, `chatMod` |
| **operator** | System or machine controller | `machineOperator`, `telecomOperator` |
| **guest** | Unauthenticated or temporary user | `guestUser`, `guestAccess` |
| **visitor** | Transient presence | `websiteVisitor`, `museumVisitor` |
| **customer** / **client** | Service consumer | `payingCustomer`, `apiClient`, `loyalClient` |
| **consumer** | Resource or data user | `resourceConsumer`, `eventConsumer` |
| **subscriber** | Event or service receiver | `newsletterSubscriber`, `topicSubscriber` |
| **publisher** | Content or event originator | `eventPublisher`, `contentPublisher` |
| **provider** | Service or data source | `cloudProvider`, `dataProvider`, `careProvider` |
| **vendor** | External supplier | `softwareVendor`, `hardwareVendor` |
| **supplier** | Goods or materials source | `partsSupplier`, `dataSupplier` |
| **partner** | Collaborative external entity | `businessPartner`, `channelPartner` |
| **stakeholder** | Interested party | `projectStakeholder`, `companyStakeholder` |
| **sponsor** | Funding or endorsing entity | `eventSponsor`, `projectSponsor` |
| **beneficiary** | Recipient of benefits | `policyBeneficiary`, `fundBeneficiary` |
| **authority** | Governing or certifying body | `certificateAuthority`, `portAuthority` |
| **issuer** | Credential or document creator | `passportIssuer`, `tokenIssuer` |
| **verifier** | Validation entity | `identityVerifier`, `proofVerifier` |
| **auditor** | Compliance examiner | `securityAuditor`, `financialAuditor` |
| **delegate** | Authorized representative | `meetingDelegate`, `apiDelegate` |
| **agent** | Acting on behalf of principal | `userAgent`, `travelAgent`, `aiAgent` |
| **bot** | Automated program actor | `chatBot`, `scrapingBot`, `twitterBot` |
| **serviceAccount** | Non-human system identity | `backupServiceAccount`, `cronServiceAccount` |

---

#### 7. **Content & Media Concepts**
Nouns describing information artifacts and creative works.

| Noun | Signal | Typical Usage |
|------|--------|-------------|
| **content** | General information payload | `pageContent`, `dynamicContent` |
| **document** / **doc** | Structured information container | `pdfDocument`, `wordDoc`, `legalDocument` |
| **file** | Named data storage unit | `configFile`, `uploadedFile`, `logFile` |
| **resource** / **res** | Addressable asset or capability | `webResource`, `systemResource` |
| **asset** | Valuable stored item | `digitalAsset`, `staticAsset`, `frozenAsset` |
| **media** | Communication content (plural sense) | `socialMedia`, `multimedia`, `newsMedia` |
| **artifact** | Produced or derived item | `buildArtifact`, `archaeologicalArtifact` |
| **object** / **obj** | Generic entity or OOP instance | `jsonObject`, `physicalObject`, `gameObj` |
| **entity** | Distinct existence (often database) | `businessEntity`, `jpaEntity`, `legalEntity` |
| **item** | Generic list member | `todoItem`, `menuItem`, `lineItem` |
| **article** | Textual content piece | `newsArticle`, `knowledgeBaseArticle` |
| **post** | Published user content | `blogPost`, `socialPost`, `forumPost` |
| **story** | Narrative content | `userStory`, `newsStory`, `instagramStory` |
| **thread** | Sequential conversation | `emailThread`, `forumThread`, `threadOfExecution` |
| **comment** | Response or annotation | `codeComment`, `socialComment` |
| **review** | Evaluative assessment | `codeReview`, `productReview`, `peerReview` |
| **rating** | Quantified evaluation | `starRating`, `creditRating`, `ageRating` |
| **feedback** | Response to experience | `userFeedback`, `systemFeedback` |
| **reply** / **response** | Answer to communication | `autoReply`, `apiResponse`, `immuneResponse` |
| **message** | Communication unit | `textMessage`, `errorMessage`, `commitMessage` |
| **email** / **e-mail** / **mail** | Electronic correspondence | `confirmationEmail`, `snailMail`, `voiceMail` |
| **letter** | Written character or correspondence | `coverLetter`, `greekLetter` |
| **note** | Brief annotation or record | `stickyNote`, `releaseNote`, `musicalNote` |
| **memo** | Internal organizational message | `companyMemo`, `meetingMemo` |
| **report** | Formal analysis or summary | `annualReport`, `crashReport`, `labReport` |
| **summary** / **abstract** | Condensed overview | `executiveSummary`, `paperAbstract` |
| **description** / **desc** | Textual explanation | `productDescription`, `jobDesc`, `metaDescription` |
| **title** | Identifying name or heading | `jobTitle`, `pageTitle`, `bookTitle` |
| **headline** | Prominent news title | `newspaperHeadline`, `emailSubjectHeadline` |
| **header** / **footer** | Page/document margin content | `httpHeader`, `pdfFooter`, `tableHeader` |
| **caption** | Image or table description | `photoCaption`, `tableCaption`, `closedCaption` |
| **label** | Identifying tag or sticker | `shippingLabel`, `axisLabel`, `recordLabel` |
| **tag** | Categorical marker | `hashtag`, `priceTag`, `gitTag` |
| **keyword** | Search-relevant term | `seoKeyword`, `searchKeyword` |
| **term** | Word or contractual condition | `medicalTerm`, `legalTerm`, `searchTerm` |
| **phrase** | Multi-word expression | `catchPhrase`, `nounPhrase`, `turnOfPhrase` |
| **sentence** | Complete grammatical unit | `prisonSentence`, `parsedSentence` |
| **paragraph** | Text block unit | `formattedParagraph`, `leadParagraph` |
| **section** | Document subdivision | `legalSection`, `codeSection`, `musicalSection` |
| **chapter** | Major book/document division | `bookChapter`, `organizationChapter` |
| **page** | Single document surface | `webPage`, `pdfPage`, `fanPage` |
| **sheet** | Single layer or paper unit | `spreadsheetSheet`, `balanceSheet`, `ghostSheet` |
| **slide** | Presentation unit | `powerPointSlide`, `carouselSlide` |
| **deck** | Presentation collection | `slideDeck`, `cardDeck`, `woodenDeck` |
| **presentation** | Visual display or speech | `salesPresentation`, `keynotePresentation` |
| **proposal** | Suggested plan or offer | `businessProposal`, `marriageProposal` |
| **draft** | Unfinished version | `emailDraft`, `contractDraft`, `nbaDraft` |
| **copy** | Duplicated instance | `hardCopy`, `softCopy`, `backupCopy` |
| **original** | First or authentic version | `originalDocument`, `originalArtwork` |
| **revision** | Modified version with history | `gitRevision`, `documentRevision` |
| **edition** | Published variant | `limitedEdition`, `morningEdition` |
| **version** | Identifiable iteration | `softwareVersion`, `apiVersion`, `schemaVersion` |
| **release** | Distributed version | `softwareRelease`, `pressRelease`, `movieRelease` |
| **build** | Compiled software version | `productionBuild`, `nightlyBuild`, `ciBuild` |
| **package** | Bundled distribution | `npmPackage`, `carePackage`, `dockerPackage` |
| **bundle** | Collection sold/distributed together | `softwareBundle`, `insuranceBundle`, `nerveBundle` |
| **archive** | Historical collection or compressed file | `zipArchive`, `nationalArchive`, `emailArchive` |
| **backup** | Safety copy | `databaseBackup`, `cloudBackup`, `backupPlan` |
| **image** / **img** | Visual representation or disk copy | `profileImage`, `dockerImage`, `diskImg` |
| **picture** / **pic** | Photographic or drawn representation | `profilePicture`, `satellitePic`, `mindPicture` |
| **photo** / **photograph** | Camera-captured image | `passportPhoto`, `aerialPhotograph` |
| **graphic** | Designed visual element | `vectorGraphic`, `infographic`, `computerGraphic` |
| **icon** | Small symbolic image | `appIcon`, `favicon`, `desktopIcon` |
| **logo** | Brand identifier | `companyLogo`, `productLogo` |
| **avatar** | User representation image | `userAvatar`, `gameAvatar`, `spiritualAvatar` |
| **thumbnail** | Small preview image | `videoThumbnail`, `galleryThumbnail` |
| **screenshot** / **snapshot** | Screen capture | `bugScreenshot`, `desktopSnapshot` |
| **frame** | Single image in sequence | `videoFrame`, `animationFrame`, `wireFrame` |
| **video** | Moving visual media | `trainingVideo`, `securityVideo`, `viralVideo` |
| **audio** | Sound content | `podcastAudio`, `backgroundAudio`, `audioBook` |
| **sound** | Audible vibration | `systemSound`, `soundEffect`, `soundWave` |
| **music** | Organized sound art | `streamingMusic`, `sheetMusic`, `copyrightMusic` |
| **song** | Musical composition with vocals | `hitSong`, `themeSong`, `protestSong` |
| **track** | Audio recording or path | `audioTrack`, `gpsTrack`, `racetrack` |
| **album** | Music collection or photo book | `debutAlbum`, `photoAlbum`, `stampAlbum` |
| **playlist** | Ordered audio collection | `spotifyPlaylist`, `workoutPlaylist` |
| **stream** | Continuous data flow | `liveStream`, `dataStream`, `videoStream` |
| **channel** | Distribution pathway | `tvChannel`, `slackChannel`, `marketingChannel` |
| **feed** | Regularly updated content source | `rssFeed`, `socialFeed`, `newsFeed` |
| **blog** | Web log/journal | `companyBlog`, `personalBlog`, `videoBlog` |
| **wiki** | Collaborative knowledge base | `corporateWiki`, `wikipediaClone` |
| **repository** / **repo** | Version-controlled storage | `gitRepository`, `mavenRepo`, `dockerRepo` |
| **library** / **lib** | Reusable code collection | `standardLibrary`, `musicLibrary`, `videoLibrary` |
| **collection** / **coll** | General grouping | `dataCollection`, `artCollection`, `garbageColl` |
| **gallery** | Display collection or photo group | `imageGallery`, `artGallery`, `photoGallery` |
| **portfolio** | Showcase collection | `investmentPortfolio`, `careerPortfolio` |
| **catalog** | Systematic listing | `productCatalog`, `starCatalog`, `libraryCatalog` |
| **inventory** | Detailed stock listing | `warehouseInventory`, `skillInventory` |
| **directory** / **dir** | Hierarchical listing or folder | `phoneDirectory`, `fileDirectory`, `workingDir` |
| **index** | Access structure or alphabetical list | `databaseIndex`, `bookIndex`, `stockIndex` |
| **registry** | Official record or lookup service | `domainRegistry`, `serviceRegistry`, `windowsRegistry` |
| **database** / **db** | Structured data system | `sqlDatabase`, `graphDb`, `firebaseDb` |
| **table** | Relational data structure | `databaseTable`, `lookupTable`, `periodicTable` |
| **record** | Single database entry | `medicalRecord`, `criminalRecord`, `worldRecord` |
| **row** | Horizontal data entry | `tableRow`, `matrixRow`, `deathRow` |
| **column** | Vertical data field | `spreadsheetColumn`, `newspaperColumn`, `militaryColumn` |
| **field** | Data attribute or form input | `formField`, `electromagneticField`, `sportsField` |
| **cell** | Data intersection or biological unit | `spreadsheetCell`, `prisonCell`, `solarCell` |
| **blob** | Binary large object | `imageBlob`, `dataBlob`, `azureBlob` |
| **clob** | Character large object | `textClob`, `xmlClob` |
| **json** | JavaScript Object Notation data | `configJson`, `responseJson`, `packageJson` |
| **xml** | Extensible Markup Language data | `configXml`, `sitemapXml`, `soapXml` |
| **yaml** / **yml** | YAML data format | `dockerComposeYml`, `configYaml` |
| **csv** | Comma-separated values | `exportCsv`, `dataCsv`, `contactsCsv` |
| **tsv** | Tab-separated values | `dataTsv`, `bedFileTsv` |
| **text** / **txt** | Plain text content | `readmeTxt`, `rawText`, `ocrText` |
| **string** / **str** | Character sequence | `queryString`, `connectionStr`, `stringUtils` |
| **html** | HyperText Markup Language | `templateHtml`, `emailHtml`, `rawHtml` |
| **markdown** / **md** | Lightweight markup | `readmeMd`, `documentationMd` |
| **css** | Cascading Style Sheets | `stylesCss`, `inlineCss`, `moduleCss` |
| **script** | Executable code or text | `javaScript`, `pythonScript`, `movieScript` |
| **stylesheet** / **style** | Presentation rules | `globalStylesheet`, `componentStyle` |
| **template** / **tpl** | Reusable pattern or mold | `emailTemplate`, `vueTpl`, `dnaTemplate` |
| **markup** | Annotation language | `xmlMarkup`, `markdownMarkup`, `priceMarkup` |
| **code** | Program instructions | `sourceCode`, `barcode`, `morseCode`, `geneticCode` |
| **source** | Origin or code origin | `dataSource`, `openSource`, `sourceFile` |
| **snippet** | Small code excerpt | `codeSnippet`, `searchSnippet`, `newsSnippet` |
| **fragment** | Broken or partial piece | `shaderFragment`, `dnaFragment`, `sentenceFragment` |
| **payload** | Transmitted data content | `requestPayload`, `virusPayload`, `rocketPayload` |
| **body** | Main content or HTTP entity | `emailBody`, `httpBody`, `celestialBody` |
| **attachment** | Appended file or emotional bond | `emailAttachment`, `emotionalAttachment` |
| **enclosure** | Contained or surrounded space | `mailEnclosure`, `cattleEnclosure`, `fenceEnclosure` |
| **wrapper** | Containment layer | `apiWrapper`, `componentWrapper`, `candyWrapper` |
| **container** / **cntr** | Holding structure | `dockerContainer`, `uiContainer`, `shippingCntr` |
| **package** | Wrapped bundle or software | `npmPackage`, `carePackage`, `holidayPackage` |
| **parcel** | Shipped package or land division | `deliveryParcel`, `landParcel`, `parcelOfLand` |
| **bundle** | Bound collection | `webpackBundle`, `softwareBundle`, `nerveBundle` |
| **batch** | Processed group | `batchJob`, `batchRequest`, `cookieBatch` |
| **set** | Mathematical or collection group | `dataset`, `settingsSet`, `jetSet` |
| **list** | Ordered sequence | `todoList`, `mailingList`, `linkedList` |
| **array** | Indexed collection | `byteArray`, `jsonArray`, `solarArray` |
| **map** / **dict** | Key-value association | `hashMap`, `urlParamsMap`, `pythonDict` |
| **dictionary** | Word reference or key-value store | `englishDictionary`, `configDictionary` |
| **hash** | Keyed data structure or digest | `hashMap`, `passwordHash`, `hashTable` |
| **tree** | Hierarchical structure | `domTree`, `decisionTree`, `familyTree` |
| **graph** | Node-edge network | `socialGraph`, `knowledgeGraph`, `barGraph` |
| **network** | Connected system | `neuralNetwork`, `computerNetwork`, `tvNetwork` |
| **web** | Network or World Wide Web | `spiderWeb`, `webApp`, `semanticWeb` |
| **grid** | Regular intersecting pattern | `dataGrid`, `powerGrid`, `cityGrid` |
| **matrix** | 2D array or complex system | `transformationMatrix`, `corporateMatrix` |
| **vector** | Directional array or carrier | `featureVector`, `diseaseVector`, `rowVector` |
| **tensor** | Multi-dimensional array | `stressTensor`, `tensorflowTensor` |
| **queue** | FIFO line or messaging | `messageQueue`, `printQueue`, `queueMusic` |
| **stack** | LIFO pile or technology | `callStack`, `technologyStack`, `hayStack` |
| **heap** | Priority structure or memory | `minHeap`, `garbageHeap`, `heapMemory` |
| **pool** | Resource collection | `threadPool`, `carPool`, `genePool` |
| **cache** | Fast storage layer | `cpuCache`, `browserCache`, `redisCache` |
| **buffer** | Temporary holding zone | `frameBuffer`, `inputBuffer`, `solutionBuffer` |
| **stream** | Continuous flow | `inputStream`, `liveStream`, `streamOfConsciousness` |
| **flow** | Movement sequence | `workflow`, `cashFlow`, `dataFlow`, `lavaFlow` |
| **pipeline** | Processing sequence | `ciPipeline`, `dataPipeline`, `oilPipeline` |
| **chain** | Linked sequence | `blockchain`, `supplyChain`, `mountainChain` |
| **sequence** | Ordered series | `dnaSequence`, `sequenceDiagram`, `randomSequence` |
| **series** | Numbered succession | `timeSeries`, `tvSeries`, `electricalSeries` |
| **progression** | Ordered advance | `arithmeticProgression`, `careerProgression` |
| **pattern** | Recurring design or regularity | `designPattern`, `behaviorPattern`, `sleepPattern` |
| **structure** | Organized construction | `dataStructure`, `molecularStructure`, `powerStructure` |
| **framework** | Supporting structure or code base | `reactFramework`, `legalFramework`, `conceptualFramework` |
| **architecture** / **arch** | Fundamental organization | `systemArchitecture`, `softwareArch`, `buildingArchitecture` |
| **platform** | Foundation technology or stage | `cloudPlatform`, `socialPlatform`, `oilPlatform` |
| **foundation** / **base** | Fundamental support | `codebase`, `knowledgeBase`, `foundationLayer` |
| **layer** | Level in stack | `networkLayer`, `abstractionLayer`, `ozoneLayer` |
| **stack** | Ordered technology layers | `techStack`, `protocolStack`, `callStack` |
| **tier** | Architectural level | `databaseTier`, `presentationTier`, `middleTier` |
| **level** | Height or difficulty degree | `experienceLevel`, `seaLevel`, `confidenceLevel` |
| **stage** | Phase or theater platform | `pipelineStage`, `lifeStage`, `theaterStage` |
| **step** | Incremental unit | `wizardStep`, `stepFunction`, `danceStep` |
| **phase** | Distinct period or state | `moonPhase`, `projectPhase`, `threePhasePower` |
| **epoch** | Notable period or ML iteration | `geologicalEpoch`, `trainingEpoch`, `unixEpoch` |
| **era** | Long historical period | `modernEra`, `commonEra`, `jurassicEra` |
| **age** | Life period or time span | `stoneAge`, `informationAge`, `votingAge` |
| **generation** / **gen** | Produced cohort or iteration | `nextGeneration`, `millennialGen`, `codeGen` |
| **iteration** / **iter** | Repetition or cycle | `loopIteration`, `designIteration`, `sprintIter` |
| **cycle** | Recurring sequence | `lifeCycle`, `washCycle`, `businessCycle` |
| **loop** | Repetitive control flow or circuit | `forLoop`, `feedbackLoop`, `inductionLoop` |
| **round** | Circular stage or session | `roundRobin`, `boxingRound`, `fundingRound` |
| **turn** | Change in direction or opportunity | `myTurn`, `turnOfEvents`, `turnSignal` |
| **rotation** | Circular movement or schedule | `cropRotation`, `jobRotation`, `earthRotation` |
| **revolution** | Overthrow or orbital movement | `industrialRevolution`, `planetaryRevolution` |

---

#### 8. **Computation & Process Concepts**
Nouns describing operations, workflows, and execution contexts.

| Noun | Signal | Typical Usage |
|------|--------|-------------|
| **operation** / **op** | Single computational action | `arithmeticOp`, `databaseOperation` |
| **computation** / **comp** | Calculated processing | `heavyComputation`, `parallelComp` |
| **calculation** / **calc** | Mathematical derivation | `taxCalculation`, `routeCalc` |
| **evaluation** / **eval** | Assessment or expression resolution | `performanceEval`, `expressionEval` |
| **execution** / **exec** | Running of instructions | `queryExecution`, `codeExec`, `capitalPunishmentExec` |
| **processing** / **proc** | Systematic handling | `batchProcessing`, `imageProc`, `wordProc` |
| **computation** | Calculated result or act | `neuralComputation`, `distributedComputation` |
| **transaction** / **txn** | Atomic business operation | `databaseTransaction`, `financialTxn` |
| **job** | Scheduled or background task | `cronJob`, `batchJob`, `printJob` |
| **task** | Discrete unit of work | `asyncTask`, `buildTask`, `taskManager` |
| **work** | Labor or energy measure | `unitOfWork`, `asyncWork`, `mechanicalWork` |
| **worker** | Processing entity | `threadWorker`, `webWorker`, `factoryWorker` |
| **process** | Running program or method | `childProcess`, `manufacturingProcess` |
| **procedure** | Established sequence of actions | `storedProcedure`, `medicalProcedure`, `legalProcedure` |
| **routine** | Regular sequence or subroutine | `dailyRoutine`, `subroutine`, `skincareRoutine` |
| **function** / **func** | Callable code block | `utilityFunction`, `lambdaFunc`, `mathematicalFunction` |
| **method** | Object-oriented function | `instanceMethod`, `staticMethod`, `scientificMethod` |
| **procedure** | Step-by-step process | `surgicalProcedure`, `auditProcedure` |
| **algorithm** / **algo** | Problem-solving procedure | `sortingAlgorithm`, `mlAlgo`, `geneticAlgo` |
| **heuristic** | Practical problem-solving approach | `searchHeuristic`, `teachingHeuristic` |
| **strategy** / **strat** | Planned approach or pattern | `tradingStrategy`, `designStrat`, `militaryStrategy` |
| **tactic** | Immediate action technique | `negotiationTactic`, `chessTactic`, `marketingTactic` |
| **approach** | Way of handling or nearness | `algorithmicApproach`, `runwayApproach` |
| **technique** / **tech** | Specific method or skill | `renderingTechnique`, `medicalTech`, `singingTech` |
| **mechanism** | Working parts or process | `lockingMechanism`, `feedbackMechanism` |
| **machinery** | Complex mechanical system | `industrialMachinery`, `bureaucraticMachinery` |
| **engine** | Driving computational core | `searchEngine`, `gameEngine`, `databaseEngine` |
| **motor** | Movement creating device | `electricMotor`, `motorFunction`, `motorSkills` |
| **driver** | Hardware controller or factor | `deviceDriver`, `testDriver`, `busDriver` |
| **handler** | Event or request processor | `eventHandler`, `exceptionHandler`, `fileHandler` |
| **controller** | Directing component | `mvcController`, `gameController`, `airTrafficController` |
| **manager** / **mgr** | Coordinating overseer | `resourceManager`, `memoryMgr`, `productManager` |
| **supervisor** / **super** | Direct overseer | `processSupervisor`, `childcareSupervisor` |
| **monitor** | Observation or display device | `healthMonitor`, `systemMonitor`, `heartMonitor` |
| **observer** | Watching entity or pattern | `eventObserver`, `behavioralObserver` |
| **listener** | Event awaiting entity | `eventListener`, `musicListener`, `phoneListener` |
| **watcher** | Change monitoring entity | `fileWatcher`, `priceWatcher`, `birdWatcher` |
| **notifier** | Alert sending entity | `pushNotifier`, `eventNotifier` |
| **dispatcher** | Event or action router | `eventDispatcher`, `emergencyDispatcher` |
| **scheduler** | Timing coordinator | `taskScheduler`, `trainScheduler`, `cpuScheduler` |
| **coordinator** / **coord** | Synchronization manager | `threadCoordinator`, `projectCoord`, `weddingCoord` |
| **orchestrator** / **orch** | Complex workflow conductor | `containerOrchestrator`, `musicOrch`, `processOrch` |
| **conductor** | Director or electrical medium | `trainConductor`, `orchestraConductor`, `heatConductor` |
| **broker** | Intermediary or message handler | `messageBroker`, `insuranceBroker`, `stockBroker` |
| **proxy** | Stand-in or intermediary | `networkProxy`, `proxyPattern`, `votingProxy` |
| **delegate** | Authorized representative | `eventDelegate`, `politicalDelegate` |
| **agent** | Acting entity or software | `userAgent`, `travelAgent`, `intelligentAgent` |
| **facade** | Simplified interface | `apiFacade`, `buildingFacade`, `patternFacade` |
| **adapter** | Interface converter | `powerAdapter`, `apiAdapter`, `objectAdapter` |
| **bridge** | Connecting structure or pattern | `networkBridge`, `dentalBridge`, `patternBridge` |
| **gateway** | Entry/exit point | `paymentGateway`, `networkGateway`, `gatewayArch` |
| **router** | Path directing device | `networkRouter`, `expressRouter`, `cncRouter` |
| **switch** | Selection or circuit device | `networkSwitch`, `lightSwitch`, `contextSwitch` |
| **hub** | Central connection point | `networkHub`, `airlineHub`, `communityHub` |
| **node** | Network or tree vertex | `clusterNode`, `lymphNode`, `decisionNode` |
| **endpoint** / **ep** | Connection terminus | `apiEndpoint`, `networkEp`, `geometryEndpoint` |
| **origin** | Starting point or source | `corsOrigin`, `pointOfOrigin`, `countryOfOrigin` |
| **source** | Origin or supplier | `dataSource`, `openSource`, `energySource` |
| **destination** / **dest** | Target endpoint | `travelDestination`, `packetDest`, `finalDestination` |
| **target** | Aim or objective | `buildTarget`, `targetAudience`, `militaryTarget` |
| **goal** | Desired outcome | `projectGoal`, `fitnessGoal`, `soccerGoal` |
| **objective** | Specific measurable aim | `businessObjective`, `learningObjective`, `lensObjective` |
| **aim** | Direction of effort | `aimOfStudy`, `takeAim`, `trueAim` |
| **purpose** | Intended reason for existence | `purposeOfVisit`, `senseOfPurpose`, `dualPurpose` |
| **intent** | Planned meaning or goal | `userIntent`, `maliciousIntent`, `letterOfIntent` |
| **plan** | Designed course of action | `businessPlan`, `floorPlan`, `masterPlan` |
| **strategy** | Overall approach or game plan | `corporateStrategy`, `exitStrategy`, `learningStrategy` |
| **policy** | Guiding principle or rule | `privacyPolicy`, `insurancePolicy`, `companyPolicy` |
| **rule** | Guideline or regulation | `businessRule`, `grammarRule`, `goldenRule` |
| **regulation** / **reg** | Official rule or control | `governmentRegulation`, `geneRegulation`, `emissionReg` |
| **standard** | Established norm or flag | `codingStandard`, `goldStandard`, `moralStandard` |
| **specification** / **spec** | Detailed requirements | `technicalSpecification`, `jobSpec`, `productSpec` |
| **requirement** / **req** | Necessary condition | `systemRequirement`, `businessReq`, `prerequisite` |
| **constraint** / **constr** | Limiting condition | `databaseConstraint`, `budgetConstraint`, `timeConstr` |
| **limitation** / **limit** | Restricting boundary | `systemLimitation`, `statuteOfLimitations`, `speedLimit` |
| **restriction** | Imposed limitation | `travelRestriction`, `dietaryRestriction`, `ageRestriction` |
| **boundary** / **bound** | Dividing line or limit | `domainBoundary`, `personalBoundary`, `upperBound` |
| **threshold** | Entry point or limit | `painThreshold`, `taxThreshold`, `detectionThreshold` |
| **ceiling** | Upper limit | `debtCeiling`, `glassCeiling`, `priceCeiling` |
| **floor** | Lower limit or surface | `priceFloor`, `danceFloor`, `oceanFloor`, `groundFloor` |
| **cap** | Upper limit or hat | `spendingCap`, `salaryCap`, `kneeCap`, `bottleCap` |
| **quota** | Allocated share or limit | `salesQuota`, `immigrationQuota`, `productionQuota` |
| **allowance** | Permitted amount or payment | `errorAllowance`, `weeklyAllowance`, `taxAllowance` |
| **allocation** | Distributed share | `memoryAllocation`, `budgetAllocation`, `resourceAllocation` |
| **reservation** | Held or set-aside resource | `hotelReservation`, `nameReservation`, `indianReservation` |
| **booking** | Scheduled reservation | `flightBooking`, `appointmentBooking`, `doubleBooking` |
| **appointment** | Scheduled meeting | `doctorAppointment`, `jobAppointment`, `politicalAppointment` |
| **schedule** | Timetable or plan | `classSchedule`, `tvSchedule`, `productionSchedule` |
| **agenda** | List of items to address | `meetingAgenda`, `hiddenAgenda`, `politicalAgenda` |
| **itinerary** | Travel plan | `flightItinerary`, `tripItinerary`, `conferenceItinerary` |
| **program** | Scheduled events or code | `tvProgram`, `softwareProgram`, `trainingProgram` |
| **agenda** | Items for consideration | `hiddenAgenda`, `formalAgenda` |
| **timeline** | Chronological sequence | `projectTimeline`, `historicalTimeline`, `facebookTimeline` |
| **roadmap** | Strategic development plan | `productRoadmap`, `technologyRoadmap`, `careerRoadmap` |
| **blueprint** | Detailed construction plan | `architectureBlueprint`, `dnaBlueprint`, `businessBlueprint` |
| **schema** | Structural plan or database design | `databaseSchema`, `xmlSchema`, `cognitiveSchema` |
| **diagram** | Visual representation | `classDiagram`, `circuitDiagram`, `vennDiagram` |
| **chart** | Data visualization or nautical map | `pieChart`, `orgChart`, `nauticalChart`, `billboardChart` |
| **graph** | Visual data representation | `lineGraph`, `socialGraph`, `barGraph`, `graphTheory` |
| **plot** | Visual data points or storyline | `scatterPlot`, `moviePlot`, `landPlot` |
| **map** | Spatial representation or function | `worldMap`, `mindMap`, `hashMap`, `treasureMap` |
| **model** | Representation or fashion | `dataModel`, `machineLearningModel`, `roleModel`, `fashionModel` |
| **prototype** | Early sample or model | `softwarePrototype`, `carPrototype`, `prototypePattern` |
| **mockup** | Visual draft or model | `uiMockup`, `productMockup`, `architecturalMockup` |
| **wireframe** | Structural UI outline | `websiteWireframe`, `appWireframe`, `3dWireframe` |
| **sketch** | Rough drawing or outline | `designSketch`, `comedySketch`, `architecturalSketch` |
| **draft** | Preliminary version | `documentDraft`, `nbaDraft`, `militaryDraft` |
| **template** / **tpl** | Reusable pattern | `emailTemplate`, `vueTpl`, `jinjaTemplate` |
| **pattern** | Repeating design or regularity | `designPattern`, `sewingPattern`, `behavioralPattern` |
| **prototype** | Original model or early version | `functionPrototype`, `productPrototype`, `javascriptPrototype` |
| **instance** | Specific occurrence | `classInstance`, `databaseInstance`, `forInstance` |
| **example** / **ex** | Illustrative sample | `codeExample`, `forExample`, `caseInPointEx` |
| **sample** | Representative portion | `bloodSample`, `randomSample`, `freeSample`, `sampleRate` |
| **specimen** | Collected example or creature | `biologicalSpecimen`, `rockSpecimen`, `urineSpecimen` |
| **demo** / **demonstration** | Showing how something works | `productDemo`, `codeDemonstration`, `protestDemonstration` |
| **experiment** / **exp** | Controlled test | `scienceExperiment`, `abExperiment`, `thoughtExperiment` |
| **trial** | Test or legal proceeding | `clinicalTrial`, `freeTrial`, `courtTrial`, `trialAndError` |
| **test** | Examination or verification | `unitTest`, `bloodTest`, `drivingTest`, `testCase` |
| **pilot** | Trial or aircraft controller | `pilotProgram`, `pilotStudy`, `airlinePilot`, `tvPilot` |
| **beta** | Testing phase or Greek letter | `betaVersion`, `betaTesting`, `betaRadiation`, `betaMale` |
| **alpha** | First testing phase or Greek letter | `alphaVersion`, `alphaMale`, `alphaParticle`, `alphaChannel` |
| **release** | Distributed version or letting go | `softwareRelease`, `pressRelease`, `catchAndRelease` |
| **launch** | Market introduction or propulsion | `productLaunch`, `rocketLaunch`, `boatLaunch`, `launchWindow` |
| **deployment** / **deploy** | Installation or military operation | `softwareDeployment`, `troopDeployment`, `deployContract` |
| **rollout** | Gradual introduction | `featureRollout`, `vaccineRollout`, `nationalRollout` |
| **upgrade** | Improved version | `systemUpgrade`, `seatUpgrade`, `upgradePath` |
| **update** | Newer version or refresh | `softwareUpdate`, `statusUpdate`, `liveUpdate`, `newsUpdate` |
| **patch** | Small fix or fabric piece | `securityPatch`, `softwarePatch`, `eyePatch`, `nicotinePatch` |
| **fix** | Repair or position | `bugFix`, `quickFix`, `geographicFix`, `fixTheProblem` |
| **hotfix** | Urgent production repair | `emergencyHotfix`, `zerodayHotfix`, `serverHotfix` |
| **workaround** | Temporary solution | `temporaryWorkaround`, `knownWorkaround`, `hackyWorkaround` |
| **solution** | Answer or liquid mixture | `problemSolution`, `chemicalSolution`, `salineSolution` |
| **resolution** | Decision or image clarity | `conflictResolution`, `screenResolution`, `newYearResolution` |
| **settlement** | Agreement or populated place | `legalSettlement`, `financialSettlement`, `pioneerSettlement` |
| **agreement** | Mutual understanding or grammar | `serviceAgreement`, `subjectVerbAgreement`, `gentlemensAgreement` |
| **contract** | Binding agreement or verb contraction | `legalContract`, `employmentContract`, `psychologicalContract`, `muscleContraction` |
| **deal** | Agreement or transaction | `businessDeal`, `newDeal`, `rawDeal`, `bigDeal` |
| **transaction** | Business exchange or database operation | `financialTransaction`, `databaseTransaction`, `onlineTransaction` |
| **exchange** | Trade or communication platform | `currencyExchange`, `stockExchange`, `dataExchange`, `exchangeStudent` |
| **trade** | Commercial exchange or craft | `internationalTrade`, `tradeSkill`, `tradeSecret`, `tradeWind` |
| **transfer** | Movement or change of ownership | `dataTransfer`, `bankTransfer`, `heatTransfer`, `studentTransfer` |
| **movement** | Motion or social cause | `laborMovement`, `artMovement`, `bowelMovement`, `politicalMovement` |
| **migration** | Mass movement or database operation | `dataMigration`, `birdMigration`, `greatMigration`, `cloudMigration` |
| **transition** | Change from one state to another | `pageTransition`, `careerTransition`, `demographicTransition`, `energyTransition` |
| **transformation** | Major change in form or nature | `digitalTransformation`, `geometricTransformation`, `businessTransformation`, `laplaceTransformation` |
| **conversion** | Change in form or religion | `typeConversion`, `religiousConversion`, `currencyConversion`, `conversionRate` |
| **adaptation** | Adjustment or biological process | `softwareAdaptation`, `climateAdaptation`, `movieAdaptation`, `evolutionaryAdaptation` |
| **evolution** | Gradual development or biological process | `softwareEvolution`, `biologicalEvolution`, `stellarEvolution`, `culturalEvolution` |
| **revolution** | Major change or circular movement | `industrialRevolution`, `digitalRevolution`, `planetaryRevolution`, `colorRevolution` |
| **reform** | Improvement or restructuring | `educationReform`, `prisonReform`, `taxReform`, `healthcareReform` |
| **restructuring** | Reorganizing | `companyRestructuring`, `debtRestructuring`, `organizationalRestructuring` |
| **reorganization** | Systematic rearrangement | `departmentReorganization`, `corporateReorganization`, `atomicReorganization` |
| **realignment** | Adjustment to new position | `strategicRealignment`, `politicalRealignment`, `spinalRealignment` |
| **reconfiguration** | New arrangement | `systemReconfiguration`, `networkReconfiguration`, `molecularReconfiguration` |
| **reset** | Return to default | `factoryReset`, `passwordReset`, `hardReset`, `systemReset` |
| **restart** | Begin again | `computerRestart`, `careerRestart`, `freshRestart`, `serviceRestart` |
| **reboot** | System restart or footwear | `systemReboot`, `seriesReboot`, `fashionReboot`, ` rebootYourLife` |
| **refresh** | Update or revitalize | `pageRefresh`, `tokenRefresh`, `refreshRate`, `refreshYourMemory` |
| **renewal** | Making new again | `contractRenewal`, `licenseRenewal`, `urbanRenewal`, `spiritualRenewal` |
| **revival** | Bringing back to life | `economicRevival`, `religiousRevival`, `artisticRevival`, `speciesRevival` |
| **renaissance** | Rebirth or historical period | `digitalRenaissance`, `personalRenaissance`, `harlemRenaissance`, `italianRenaissance` |
| **resurrection** | Rising from dead or revival | `dataResurrection`, `projectResurrection`, `easterResurrection`, `brandResurrection` |
| **rebirth** | Being born again | `spiritualRebirth`, `careerRebirth`, `urbanRebirth`, `springRebirth` |
| **regeneration** | Growing again or renewal | `urbanRegeneration`, `tissueRegeneration`, `ecosystemRegeneration`, `spiritualRegeneration` |
| **restoration** | Returning to original state | `buildingRestoration`, `ecosystemRestoration`, `dataRestoration`, `monarchyRestoration` |
| **recovery** | Regaining lost state | `disasterRecovery`, `economicRecovery`, `dataRecovery`, `healthRecovery` |
| **rehabilitation** | Restoring to useful life | `criminalRehabilitation`, `physicalRehabilitation`, `drugRehabilitation`, `buildingRehabilitation` |
| **repair** | Fixing damage | `autoRepair`, `dnaRepair`, `relationshipRepair`, `softwareRepair` |
| **maintenance** | Upkeep or alimony | `preventiveMaintenance`, `softwareMaintenance`, `carMaintenance`, `spousalMaintenance` |
| **service** | Assistance or religious ceremony | `customerService`, `webService`, `militaryService`, `churchService` |
| **support** | Assistance or structural element | `technicalSupport`, `customerSupport`, `emotionalSupport`, `bridgeSupport` |
| **care** | Attention or medical treatment | `healthCare`, `customerCare`, `intensiveCare`, `takeCare` |
| **help** | Assistance or cry of distress | `onlineHelp`, `helpDesk`, `helpWanted`, `cryForHelp` |
| **aid** | Assistance or medical device | `financialAid`, `firstAid`, `hearingAid`, `humanitarianAid` |
| **assistance** | Help or support | `technicalAssistance`, `legalAssistance`, `roadsideAssistance`, `governmentAssistance` |
| **backup** | Support copy or substitute | `dataBackup`, `backupPlan`, `backupSinger`, `backupQuarterback` |
| **standby** | Ready reserve or waiting | `backupStandby`, `standbyMode`, `standbyFlight`, `onStandby` |
| **reserve** | Kept back or protected area | `cashReserve`, `natureReserve`, `militaryReserve`, `reserveSeat` |
| **buffer** | Protective zone or temporary storage | `inputBuffer`, `bufferZone`, `bufferStock`, `bufferState` |
| **cushion** | Protective padding or shock absorber | `financialCushion`, `seatCushion`, `airCushion`, `comfortCushion` |
| **safety** | Freedom from danger or football position | `safetyFirst`, `workplaceSafety`, `safetyNet`, `freeSafety` |
| **security** | Protection or financial instrument | `cyberSecurity`, `jobSecurity`, `socialSecurity`, `securityGuard` |
| **protection** | Defense or conservation | `environmentalProtection`, `consumerProtection`, `passwordProtection`, `witnessProtection` |
| **defense** | Protection or legal argument | `nationalDefense`, `selfDefense`, `legalDefense`, `defenseAttorney` |
| **guard** | Protector or watch | `securityGuard`, `borderGuard`, `coastGuard`, `onGuard` |
| **shield** | Protective barrier or emblem | `shieldFirewall`, `shieldOfFaith`, `policeShield`, `familyShield` |
| **barrier** | Obstacle or protective structure | `languageBarrier`, `tradeBarrier`, `soundBarrier`, `greatBarrierReef` |
| **wall** | Vertical structure or barrier | `firewall`, `greatWall`, `fourthWall`, `wallStreet` |
| **gate** | Entry point or logic device | `gateway`, `logicGate`, `boardingGate`, `watergate` |
| **filter** | Selection device or purifier | `spamFilter`, `airFilter`, `coffeeFilter`, `socialFilter` |
| **screen** | Display or protective mesh | `touchScreen`, `computerScreen`, `screenDoor`, `projectionScreen` |
| **cover** | Protective layer or concealment | `bookCover`, `insuranceCover`, `coverStory`, `underCover` |
| **wrapper** | Containing layer or cover | `candyWrapper`, `apiWrapper`, `componentWrapper`, `giftWrapper` |
| **shell** | Outer covering or command interface | `eggshell`, `turtleShell`, `unixShell`, `powerShell`, `shellCompany` |
| **case** | Container or legal proceeding | `phoneCase`, `useCase`, `legalCase`, `lowerCase`, `displayCase` |
| **casing** | Protective covering | `wellCasing`, `sausageCasing`, `windowCasing`, `bombCasing` |
| **housing** | Protective enclosure or residences | `electronicsHousing`, `studentHousing`, `publicHousing`, `gearHousing` |
| **enclosure** | Surrounded area or letter attachment | `zooEnclosure`, `mailEnclosure`, `pastureEnclosure`, `fenceEnclosure` |
| **capsule** | Small container or spacecraft | `timeCapsule`, `medicineCapsule`, `spaceCapsule`, `capsuleWardrobe` |
| **cocoon** | Protective covering or transformation | `silkwormCocoon`, `protectiveCocoon`, `digitalCocoon`, `cocooning` |
| **bubble** | Spherical enclosure or economic phenomenon | `soapBubble`, `filterBubble`, `economicBubble`, `housingBubble` |
| **cage** | Enclosed structure or sports equipment | `birdcage`, `ribCage`, `sharkCage`, `cageFighting` |
| **trap** | Capture device or situation | `mouseTrap`, `touristTrap`, `speedTrap`, `trapMusic`, `trapDoor` |
| **net** | Mesh fabric or internet | `fishingNet`, `safetyNet`, `internet`, `netProfit`, `netWeight` |
| **web** | Network or spider structure | `worldWideWeb`, `spiderWeb`, `webDesign`, `webOfLies` |
| **network** | Connected system or broadcasting | `socialNetwork`, `computerNetwork`, `neuralNetwork`, `televisionNetwork` |
| **grid** | Network of lines or electrical system | `powerGrid`, `cityGrid`, `offTheGrid`, `spreadsheetGrid` |
| **matrix** | Network or mathematical array | `theMatrix`, `boneMatrix`, `corporateMatrix`, `transformationMatrix` |
| **fabric** | Cloth or underlying structure | `cottonFabric`, `socialFabric`, `fabricOfSpace`, `dataFabric` |
| **texture** | Surface quality or feel | `wallTexture`, `textureMapping`, `musicalTexture`, `textureOfLife` |
| **structure** | Organized construction or building | `dataStructure`, `molecularStructure`, `powerStructure`, `steelStructure` |
| **framework** | Supporting structure or software | `softwareFramework`, `conceptualFramework`, `legalFramework`, `zurbFoundationFramework` |
| **skeleton** | Supporting framework or horror motif | `skeletonCrew`, `skeletonKey`, `appSkeleton`, `skeletonInTheCloset` |
| **scaffold** | Temporary structure or education | `constructionScaffold`, `scaffoldLearning`, `proteinScaffold`, `scaffoldLaw` |
| **infrastructure** | Basic underlying framework | `digitalInfrastructure`, `transportationInfrastructure`, `ITInfrastructure`, `crumblingInfrastructure` |

---

### Part 2: The Programmer's Adjective Taxonomy

#### 1. **Mutability & Lifetime**
Adjectives describing change characteristics and persistence.

| Adjective | Signal | Typical Usage |
|-----------|--------|-------------|
| **immutable** | Cannot be modified after creation | `immutableConfig`, `ImmutableList` |
| **mutable** | Can be modified after creation | `mutableState`, `MutableString` |
| **readonly** / **readOnly** | Readable but externally immutable | `readonlyField`, `ReadOnlyArray` |
| **writeonly** / **writeOnly** | Writable but not readable (rare) | `writeOnlyProperty`, `WriteOnlyStream` |
| **const** / **constant** | Compile-time fixed value | `constValue`, `ConstantPool` |
| **final** | Assigned exactly once | `finalVariable`, `FinalClass` (Java) |
| **static** | Belongs to class/type, not instance | `staticMethod`, `StaticAnalysis` |
| **dynamic** | Runtime-determined or changing | `dynamicType`, `DynamicArray` |
| **volatile** | May change unexpectedly (concurrency) | `volatileFlag`, `VolatileMemory` |
| **transient** | Not persisted (serialization) | `transientField`, `TransientError` |
| **persistent** | Survives program termination | `persistentStorage`, `PersistentConnection` |
| **ephemeral** | Short-lived, temporary | `ephemeralCache`, `EphemeralPort` |
| **temporary** / **temp** | Short-term existence | `tempFile`, `TemporaryVariable` |
| **permanent** | Long-lasting, irrevocable | `permanentStorage`, `PermanentRedirect` |
| **sticky** | Persistently attached or cached | `stickySession`, `StickyBit` |
| **weak** | Garbage collectible reference | `weakReference`, `WeakMap` |
| **strong** | Prevents garbage collection | `strongReference`, `StrongTyping` |
| **soft** | Memory-sensitive cache reference | `softReference`, `SoftCache` |
| **hard** | Firm, unchangeable, or physical | `hardLink`, `HardDrive`, `HardStop` |
| **lazy** | Deferred initialization | `lazyLoading`, `LazyEvaluation` |
| **eager** | Immediate initialization | `eagerLoading`, `EagerExecution` |
| **strict** | Enforces constraints strictly | `strictMode`, `StrictTyping` |
| **lenient** | Permissive, forgiving | `lenientParsing`, `LenientValidator` |

---

#### 2. **Visibility & Access Control**
Adjectives describing scope and accessibility.

| Adjective | Signal | Typical Usage |
|-----------|--------|-------------|
| **public** | Accessible everywhere | `publicApi`, `PublicClass` |
| **private** | Accessible only within defining scope | `privateField`, `PrivateKey` |
| **protected** | Accessible within hierarchy | `protectedMethod`, `ProtectedRoute` |
| **internal** | Accessible within assembly/package | `internalClass`, `InternalApi` |
| **external** | Outside the current system | `externalService`, `ExternalLink` |
| **exported** | Made available to other modules | `exportedFunction`, `ExportedVar` |
| **local** | Restricted to current scope | `localVariable`, `LocalStorage` |
| **global** | Accessible across entire program | `globalState`, `GlobalScope` |
| **universal** | Applies everywhere without exception | `universalConstant`, `UniversalSet` |
| **restricted** | Limited access permissions | `restrictedArea`, `RestrictedAccess` |
| **unrestricted** | No access limitations | `unrestrictedAccess`, `UnrestrictedMode` |
| **open** | Extensible or accessible | `openClass`, `OpenConnection` |
| **closed** | Sealed or inaccessible | `closedSet`, `ClosedSource` |
| **sealed** | Cannot be extended/overridden | `sealedClass`, `SealedMethod` |
| **hidden** | Concealed from normal view | `hiddenField`, `HiddenLayer` |
| **visible** | Currently renderable or detectable | `visibleElement`, `VisibleSpectrum` |
| **invisible** | Not renderable or detectable | `invisibleCharacter`, `InvisibleInk` |
| **transparent** | Passes through without obstruction | `transparentProxy`, `TransparentOverlay` |
| **opaque** | Cannot be seen through or inspected | `opaqueToken`, `OpaquePointer` |
| **isolated** | Separated from others | `isolatedScope`, `IsolatedProcess` |
| **sandboxed** | Restricted safe environment | `sandboxedCode`, `SandboxedIframe` |

---

#### 3. **Cardinality & Quantification**
Adjectives describing count and numerical relationships.

| Adjective | Signal | Typical Usage |
|-----------|--------|-------------|
| **single** | Exactly one instance | `singleInstance`, `SingleThreaded` |
| **multiple** | More than one | `multipleChoice`, `MultipleInheritance` |
| **many** | Large unspecified quantity | `manyToMany`, `ManyToOne` |
| **few** | Small unspecified quantity | `fewShotLearning`, `FewAttempts` |
| **all** | Complete set inclusion | `allUsers`, `AllClear` |
| **none** / **no** | Zero quantity | `noneValue`, `NoOp` |
| **any** | Existential quantifier (at least one) | `anyType`, `AnyMatch` |
| **some** | Partial subset (plural) | `someResults`, `SomeException` |
| **each** / **every** | Universal iteration | `eachItem`, `EveryNMinutes` |
| **either** | One of two alternatives | `eitherOr`, `EitherType` |
| **neither** | None of two alternatives | `neitherNor`, `NeitherOption` |
| **both** | Two together | `bothSides`, `BothConditions` |
| **first** | Ordinal position 1 | `firstChild`, `FirstClass` |
| **last** | Final ordinal position | `lastItem`, `LastModified` |
| **next** | Succeeding in sequence | `nextPage`, `NextSibling` |
| **previous** / **prev** | Preceding in sequence | `prevState`, `PreviousVersion` |
| **current** / **curr** | Active or present | `currentUser`, `CurrValue` |
| **old** | Superseded or aged | `oldValue`, `OldGeneration` |
| **new** | Recently created or different | `newValue`, `NewFolder` |
| **initial** | Starting or first | `initialState`, `InitialCapacity` |
| **final** | Ending or last (see also lifetime) | `finalResult`, `FinalDestination` |
| **intermediate** | Between start and end | `intermediateResult`, `IntermediateLanguage` |
| **partial** | Incomplete fragment | `partialMatch`, `PartialFunction` |
| **complete** / **full** | Whole without omission | `completeGraph`, `FullStack` |
| **total** | Sum or entirety | `totalCount`, `TotalOrder` |
| **whole** | Undivided complete unit | `wholeNumber`, `WholeSale` |
| **empty** | Zero contents | `emptySet`, `EmptyString` |
| **nonEmpty** | At least one element | `nonEmptyList`, `NonEmptyArray` |
| **null** / **nil** | Absent or undefined reference | `nullPointer`, `NilCoalescing` |
| **undefined** | Not yet defined | `undefinedBehavior`, `UndefinedVariable` |
| **defined** | Has a value or meaning | `definedTerm`, `WellDefined` |
| **infinite** | Without bound | `infiniteLoop`, `InfiniteScroll` |
| **finite** | Having definite bounds | `finiteState`, `FiniteResource` |
| **bounded** | Having limits | `boundedBuffer`, `BoundedContext` |
| **unbounded** | Without limits | `unboundedQueue`, `UnboundedWildcard` |
| **limited** | Restricted in quantity | `limitedEdition`, `LimitedAccess` |
| **unlimited** | Without restriction | `unlimitedPlan`, `UnlimitedBuffer` |
| **exclusive** | Restricted to specific set | `exclusiveLock`, `ExclusiveOr` |
| **inclusive** | Including boundary or all | `inclusiveRange`, `InclusiveLanguage` |

---

#### 4. **Qualitative Evaluation**
Adjectives describing goodness, validity, or quality.

| Adjective | Signal | Typical Usage |
|-----------|--------|-------------|
| **good** | Positive quality (prefer specific) | `goodPractice` → `bestPractice` |
| **bad** | Negative quality (prefer specific) | `badRequest` → `invalidRequest` |
| **best** | Optimal or supreme | `bestPractice`, `BestMatch` |
| **worst** | Least optimal | `worstCase`, `WorstFit` |
| **better** | Comparative improvement | `betterPerformance`, `BetterAlternative` |
| **worse** | Comparative degradation | `worseComplexity`, `WorseCase` |
| **optimal** | Best possible given constraints | `optimalSolution`, `OptimalPath` |
| **suboptimal** | Less than best | `suboptimalResult`, `SuboptimalChoice` |
| **ideal** | Perfect theoretical standard | `idealGas`, `IdealHash` |
| **perfect** | Without flaw or complete | `perfectHash`, `PerfectMatch` |
| **imperfect** | Having flaws | `imperfectInformation`, `ImperfectClone` |
| **valid** | Conforms to rules | `validInput`, `ValidState` |
| **invalid** | Breaks rules | `invalidToken`, `InvalidOperation` |
| **correct** | Accurate or true | `correctAnswer`, `CorrectnessProof` |
| **incorrect** | Wrong or false | `incorrectPassword`, `IncorrectSyntax` |
| **accurate** | Precisely correct | `accurateMeasurement`, `AccuratePrediction` |
| **inaccurate** | Lacking precision | `inaccurateData`, `InaccurateModel` |
| **precise** | Exactly specified | `preciseLocation`, `PreciseTiming` |
| **imprecise** | Vague or approximate | `impreciseLanguage`, `ImpreciseCalculation` |
| **exact** | Strictly accurate | `exactMatch`, `ExactArithmetic` |
| **approximate** / **approx** | Close but not exact | `approximateValue`, `ApproxSearch` |
| **true** | Boolean truth or aligned | `trueValue`, `TrueNorth` |
| **false** | Boolean falsity or deceptive | `falsePositive`, `FalseStart` |
| **positive** | Greater than zero or optimistic | `positiveNumber`, `PositiveFeedback` |
| **negative** | Less than zero or pessimistic | `negativeIndex`, `NegativeSpace` |
| **zero** | Exactly null value | `zeroBased`, `ZeroCopy` |
| **nonZero** | Not equal to zero | `nonZeroExit`, `NonZeroCheck` |
| **null** | Absence of value | `nullSafety`, `NullObject` |
| **nonNull** | Presence of value | `nonNullAssert`, `NonNullType` |
| **safe** | Without danger or risk | `safeMode`, `ThreadSafe` |
| **unsafe** | Potential danger or risk | `unsafePointer`, `UnsafeOperation` |
| **secure** | Protected from threats | `secureConnection`, `SecureBoot` |
| **insecure** | Vulnerable to threats | `insecureProtocol`, `InsecureContext` |
| **trusted** | Verified reliability | `trustedSource`, `TrustedDomain` |
| **untrusted** | Unverified or suspicious | `untrustedCode`, `UntrustedInput` |
| **verified** | Checked and confirmed | `verifiedAccount`, `VerifiedBuild` |
| **unverified** | Not yet checked | `unverifiedEmail`, `UnverifiedClaim` |
| **authentic** / **genuine** | True to origin | `authenticUser`, `GenuineCheck` |
| **fake** / **spoofed** | Fraudulent imitation | `fakeData`, `SpoofedIp` |
| **legitimate** / **legit** | Lawful and valid | `legitimateTraffic`, `LegitInterest` |
| **illegitimate** | Not lawful or valid | `illegitimateChild`, `IllegitimateAccess` |
| **legal** | Permitted by law | `legalEntity`, `LegalNotice` |
| **illegal** | Prohibited by law | `illegalArgument`, `IllegalState` |
| **authorized** | Permission granted | `authorizedUser`, `AuthorizedAccess` |
| **unauthorized** | Permission denied | `unauthorizedError`, `UnauthorizedRequest` |
| **permitted** | Explicitly allowed | `permittedAction`, `PermittedValue` |
| **forbidden** / **prohibited** | Explicitly disallowed | `forbiddenError`, `ProhibitedOperation` |
| **allowed** | Generally permissible | `allowedHost`, `AllowedMethod` |
| **disallowed** | Generally impermissible | `disallowedChar`, `DisallowedOperation` |
| **acceptable** | Meeting minimum standards | `acceptableUse`, `AcceptableQuality` |
| **unacceptable** | Below minimum standards | `unacceptableRisk`, `UnacceptableDelay` |
| **appropriate** | Suitable for context | `appropriateTechnology`, `AgeAppropriate` |
| **inappropriate** | Unsuitable for context | `inappropriateContent`, `InappropriateAccess` |
| **proper** | Correctly done | `properSubset`, `ProperNoun` |
| **improper** | Incorrectly done | `improperFraction`, `ImproperAccess` |
| **clean** | Without contamination or pure | `cleanCode`, `CleanArchitecture` |
| **dirty** | Modified or contaminated | `dirtyBit`, `DirtyRead`, `DirtyCache` |
| **pure** | Without side effects | `pureFunction`, `PureComponent` |
| **impure** | With side effects | `impureFunction`, `ImpureIO` |
| **fresh** | Newly created or unexpired | `freshData`, `FreshToken` |
| **stale** | Outdated or expired | `staleCache`, `StaleElement` |
| **raw** | Unprocessed or crude | `rawData`, `RawPointer`, `RawString` |
| **cooked** / **processed** | Transformed or refined | `cookedData`, `ProcessedFood` |
| **refined** | Purified or improved | `refinedSugar`, `RefinedAbstraction` |
| **coarse** / **crude** | Rough or unrefined | `coarseLock`, `CrudeOil`, `CrudeEstimate` |
| **fine** | Precise or of high quality | `fineGrained`, `FineTune`, `FinePrint` |
| **smooth** | Without irregularities | `smoothScroll`, `SmoothFunction` |
| **rough** | Approximate or uneven | `roughEstimate`, `RoughDraft` |

---

#### 5. **Completeness & Wholeness**
Adjectives describing integrity and saturation.

| Adjective | Signal | Typical Usage |
|-----------|--------|-------------|
| **full** | Complete to capacity | `fullStack`, `FullScreen`, `FullNode` |
| **empty** | Without contents | `emptySet`, `EmptyString`, `EmptyState` |
| **filled** | Occupied to some degree | `filledRectangle`, `FilledForm` |
| **unfilled** | Not yet occupied | `unfilledOrder`, `UnfilledSlot` |
| **complete** | Whole with all parts | `completeGraph`, `CompleteBinaryTree` |
| **incomplete** | Missing parts | `incompleteType`, `IncompleteData` |
| **whole** | Entire undivided | `wholeNumber`, `WholeSale`, `WholePart` |
| **partial** | Fragment or subset | `partialFunction`, `PartialUpdate` |
| **total** | Complete aggregate | `totalOrder`, `TotalRecall` |
| **absolute** | Unconditional or complete | `absolutePath`, `AbsoluteZero`, `AbsoluteValue` |
| **relative** | Dependent on context | `relativePath`, `RelativeHumidity` |
| **comprehensive** | All-inclusive | `comprehensiveTest`, `ComprehensiveCoverage` |
| **selective** | Choosing specific elements | `selectiveSync`, `SelectiveHearing` |
| **exclusive** | Excluding others | `exclusiveAccess`, `ExclusiveOr` |
| **inclusive** | Including all relevant | `inclusiveDesign`, `InclusiveRange` |
| **exhaustive** | Covering all possibilities | `exhaustiveSearch`, `ExhaustiveTest` |
| **minimal** / **min** | Smallest sufficient | `minimalPair`, `MinHeap`, `MinimalApi` |
| **maximal** / **max** | Largest possible | `maximalElement`, `MaxFlow`, `MaximalMatch` |
| **sufficient** | Enough for purpose | `sufficientCondition`, `SufficientStat` |
| **insufficient** | Not enough | `insufficientFunds`, `InsufficientMemory` |
| **necessary** | Required for purpose | `necessaryCondition`, `NecessaryEvil` |
| **unnecessary** | Not required | `unnecessaryCopy`, `UnnecessaryComplexity` |
| **redundant** | Unnecessarily duplicated | `redundantArray`, `RedundantCode` |
| **sparse** | Mostly empty/scattered | `sparseMatrix`, `SparseArray`, `SparseData` |
| **dense** | Compactly filled | `denseVector`, `DenseHash`, `DenseForest` |
| **rich** | Abundant in features | `richText`, `RichClient`, `RichDomain` |
| **lean** | Efficiently minimal | `leanStartup`, `LeanManufacturing`, `LeanCode` |
| **fat** / **thick** | Resource-heavy | `fatClient`, `ThickClient`, `FatBinary` |
| **thin** / **light** | Resource-light | `thinClient`, `LightApp`, `ThinWrapper` |
| **heavy** | Resource-intensive | `heavyLoad`, `HeavyComputation`, `HeavyMetal` |
| **light** / **lite** | Easy or simplified | `lightMode`, `LiteVersion`, `LightWeight` |
| **deep** | Many layers or intense | `deepCopy`, `DeepLearning`, `DeepThought` |
| **shallow** | Few layers or superficial | `shallowCopy`, `ShallowRendering`, `ShallowWater` |
| **flat** | Without hierarchy | `flatMap`, `FlatFile`, `FlatOrganization` |
| **nested** | Hierarchical containment | `nestedFunction`, `NestedLoop`, `NestedComment` |
| **hierarchical** | Tree-structured levels | `hierarchicalData`, `HierarchicalNav` |
| **linear** | Sequential without branching | `linearSearch`, `LinearHistory`, `LinearScale` |
| **nonlinear** | With branching or curves | `nonlinearDynamics`, `NonlinearScale` |
| **cyclic** / **circular** | Loops back to start | `cyclicDependency`, `CircularBuffer`, `CyclicGraph` |
| **acyclic** | Without cycles | `acyclicGraph`, `AcyclicDependency` |

---

#### 6. **Temporal Characteristics**
Adjectives describing time relationships and freshness.

| Adjective | Signal | Typical Usage |
|-----------|--------|-------------|
| **early** | Near beginning of timeline | `earlyReturn`, `EarlyBinding`, `EarlyAdopter` |
| **late** | Near end of timeline | `lateBinding`, `LateNight`, `LateArrival` |
| **prompt** | Without delay | `promptResponse`, `PromptPayment` |
| **delayed** / **deferred** | Postponed in time | `delayedJob`, `DeferredPayment`, `DeferredShading` |
| **immediate** / **instant** | Without intervening time | `immediateMode`, `InstantMessage`, `InstantPayment` |
| **eventual** | At some future time | `eventualConsistency`, `EventualSuccess` |
| **pending** | Awaiting resolution | `pendingRequest`, `PendingPayment`, `PendingApproval` |
| **ongoing** / **inProgress** | Currently happening | `ongoingProcess`, `InProgressBar` |
| **upcoming** | Approaching in future | `upcomingEvent`, `UpcomingFeature` |
| **past** / **previous** | Already occurred | `pastTense`, `PreviousVersion` |
| **future** / **upcoming** | Not yet occurred | `futureValue`, `FutureTask` |
| **recent** | Shortly before present | `recentActivity`, `RecentChanges` |
| **ancient** / **archaic** | Very old, outdated | `ancientCode`, `ArchaicSystem` |
| **modern** / **contemporary** | Current era | `modernCpp`, `ContemporaryDesign` |
| **new** | Recently created | `newFeature`, `NewInstance` |
| **old** | Previously existing | `oldVersion`, `OldGeneration` (GC) |
| **current** / **present** | Existing now | `currentTime`, `PresentValue` |
| **former** | Previously held position | `formerValue`, `FormerEmployee` |
| **latter** | Second of two mentioned | `latterOption`, `LatterHalf` |
| **prior** / **previous** | Preceding in sequence | `priorArt`, `PreviousState` |
| **subsequent** / **next** | Following in sequence | `subsequentRequest`, `NextPage` |
| **initial** | First or starting | `initialLoad`, `InitialState` |
| **final** | Last or ending | `finalOutput`, `FinalClass` (Java) |
| **interim** / **temporary** | Provisional | `interimSolution`, `TemporaryFile` |
| **permanent** | Lasting indefinitely | `permanentStorage`, `PermanentRedirect` |
| **transient** / **ephemeral** | Brief existence | `transientError`, `EphemeralPort` |
| **persistent** / **durable** | Long-lasting | `persistentVolume`, `DurableMessage` |
| **synchronous** / **sync** | Same-time coordinated | `synchronousCall`, `SyncOperation` |
| **asynchronous** / **async** | Different-time coordinated | `asyncFunction`, `AsyncAwait`, `AsyncIO` |
| **concurrent** | Happening simultaneously | `concurrentUsers`, `ConcurrentModification` |
| **sequential** / **serial** | One after another | `sequentialAccess`, `SerialPort`, `SerialProcessing` |
| **parallel** | Simultaneous processing | `parallelStream`, `ParallelComputing`, `ParallelUniverse` |
| **periodic** | Recurring at intervals | `periodicTask`, `PeriodicTable`, `PeriodicBoundary` |
| **aperiodic** | Not recurring regularly | `aperiodicTask`, `AperiodicCrystal` |
| **interval** / **periodic** | Between time points | `intervalTraining`, `PeriodicTimer` |
| **continuous** | Without interruption | `continuousIntegration`, `ContinuousDeployment` |
| **discrete** | Separate distinct units | `discreteMathematics`, `DiscreteEvent` |
| **batch** | Processed in groups | `batchJob`, `BatchProcessing`, `BatchNorm` |
| **realtime** / **real-time** | Immediate processing | `realtimeData`, `RealTimeClock`, `RealtimeOS` |
| **live** | Currently happening | `liveStream`, `LiveReload`, `LiveSite` |
| **archived** | Stored for history | `archivedData`, `ArchivedProject` |
| **expired** / **obsolete** | No longer valid | `expiredToken`, `ObsoleteCode` |
| **stale** | Outdated cache/data | `staleCache`, `StaleWhileRevalidate` |
| **fresh** | Recently updated | `freshData`, `FreshInstall` |
| **upToDate** / **current** | Recently synchronized | `upToDateCheck`, `CurrentVersion` |
| **outdated** / **deprecated** | Superseded by newer | `outdatedLib`, `DeprecatedApi` |
| **legacy** | Old but still used | `legacyCode`, `LegacySystem`, `LegacySupport` |

---

#### 7. **Location & Spatial Relationships**
Adjectives describing position and direction.

| Adjective | Signal | Typical Usage |
|-----------|--------|-------------|
| **local** | Nearby or restricted scope | `localVariable`, `LocalHost`, `LocalStorage` |
| **remote** | Distant or external | `remoteServer`, `RemoteProcedure`, `RemoteWork` |
| **central** | Middle or controlling | `centralServer`, `CentralProcessing`, `CentralBank` |
| **peripheral** | Outer or secondary | `peripheralDevice`, `PeripheralVision` |
| **internal** | Inside boundary | `internalState`, `InternalApi`, `InternalMedicine` |
| **external** | Outside boundary | `externalService`, `ExternalLink`, `ExternalHardDrive` |
| **inner** / **inside** | Within (closer to center) | `innerClass`, `InnerJoin`, `InsideOut` |
| **outer** / **outside** | Beyond (farther from center) | `outerJoin`, `OuterBanks`, `OutsideIn` |
| **upper** | Higher position | `upperBound`, `UpperCase`, `UpperManagement` |
| **lower** | Lower position | `lowerBound`, `LowerCase`, `LowerClass` |
| **higher** | Greater elevation/rank | `higherOrder`, `HigherEducation` |
| **high** | Great elevation or intensity | `highAvailability`, `HighPerformance`, `HighLevel` |
| **low** | Small elevation or intensity | `lowMemory`, `LowLevel`, `LowPassFilter` |
| **top** | Uppermost | `topLevel`, `TopDown`, `TopRated` |
| **bottom** | Lowermost | `bottomUp`, `BottomLine`, `BottomSheet` |
| **up** / **upper** | Ascending direction | `upStream`, `Upload`, `UpTime` |
| **down** / **lower** | Descending direction | `downStream`, `Download`, `DownTime` |
| **left** / **right** | Horizontal lateral | `leftJoin`, `RightAligned`, `LeftShift` |
| **horizontal** | Parallel to horizon | `horizontalRule`, `HorizontalScaling` |
| **vertical** | Perpendicular to horizon | `verticalAlign`, `VerticalSlice` |
| **forward** / **ahead** | Front direction | `forwardReference`, `ForwardProxy`, `LookAhead` |
| **backward** / **behind** | Rear direction | `backwardCompat`, `BackwardChaining`, `BehindTheScenes` |
| **front** / **fore** | Leading position | `frontEnd`, `Foreground`, `ForeWord` |
| **back** / **rear** | Trailing position | `backEnd`, `Background`, `RearAdmiral` |
| **head** / **heading** | Beginning or direction | `headPointer`, `HeadingLevel`, `HeadOfState` |
| **tail** | Ending or trailing | `tailCall`, `TailRecursion`, `TailWind` |
| **source** / **origin** | Starting point | `sourceCode`, `OriginServer`, `SourceOfTruth` |
| **destination** / **target** | Ending point | `destAddr`, `TargetAudience`, `DestinationUnknown` |
| **native** | Original or indigenous | `nativeCode`, `NativeApp`, `NativeSpeaker` |
| **foreign** | External or different origin | `foreignKey`, `ForeignExchange`, `ForeignObject` |
| **domestic** | Internal to country/org | `domesticMarket`, `DomesticPolicy`, `DomesticFlight` |
| **global** | Worldwide or universal | `globalScope`, `GlobalVariable`, `GlobalWarming` |
| **universal** | Applicable everywhere | `universalGrammar`, `UniversalRemote`, `UniversalSet` |
| **worldwide** / **international** | Across countries | `worldwideWeb`, `Internationalization` (i18n) |
| **national** | Country-wide | `nationalId`, `NationalGuard`, `NationalDebt` |
| **regional** | Area-specific | `regionalSettings`, `RegionalDialect` |
| **provincial** | State/territory level | `provincialTax`, `ProvincialAttitude` |
| **municipal** | City/town level | `municipalCode`, `MunicipalBond` |
| **urban** | City characteristic | `urbanPlanning`, `UrbanLegend` |
| **rural** | Countryside characteristic | `ruralRoute`, `RuralElectrification` |
| **domestic** | Home/internal | `domesticFlight`, `DomesticViolence` |
| **adjacent** / **neighboring** | Next to | `adjacentSibling`, `NeighboringCell`, `AdjacentPossible` |
| **proximal** / **proximate** | Near in space/time | `proximalCause`, `ProximateAnalysis` |
| **distal** / **distant** | Far in space/time | `distalPhalanx`, `DistantFuture`, `DistantReading` |
| **near** / **close** | Small distance | `nearField`, `CloseButton`, `NearMiss` |
| **far** / **distant** | Large distance | `farPointer`, `FarAway`, `FarField` |
| **absolute** | Fixed non-relative | `absolutePosition`, `AbsoluteZero`, `AbsoluteValue` |
| **relative** | Dependent on context | `relativePosition`, `RelativeHumidity`, `RelativePath` |
| **fixed** | Immobile | `fixedHeader`, `FixedPoint`, `FixedWidth` |
| **floating** | Movable or variable | `floatingPoint`, `FloatingActionButton`, `FloatingIP` |
| **static** | Unmoving or unchanged | `staticIp`, `StaticAnalysis`, `StaticElectricity` |
| **dynamic** | Moving or changing | `dynamicIp`, `DynamicProgramming`, `DynamicRange` |
| **mobile** | Capable of movement | `mobileApp`, `MobileFirst`, `MobileHome` |
| **portable** / **relocatable** | Easily moved | `portableCode`, `PortableDocument`, `RelocatableBinary` |
| **stationary** | Fixed in place | `stationaryPoint`, `StationaryBike`, `StationaryWave` |
| **spatial** | Related to space | `spatialIndex`, `SpatialAudio`, `SpatialReasoning` |
| **temporal** | Related to time | `temporalLogic`, `TemporalDatabase`, `TemporalLobe` |
| **virtual** | Simulated, not physical | `virtualMemory`, `VirtualMachine`, `VirtualReality` |
| **physical** | Material, not simulated | `physicalMemory`, `PhysicalServer`, `PhysicalEducation` |
| **concrete** | Specific, not abstract | `concreteClass`, `ConcreteSyntax`, `ConcretePoetry` |
| **abstract** | Conceptual, not concrete | `abstractClass`, `AbstractSyntax`, `AbstractArt` |
| **concrete** / **specific** | Particular instance | `specificGravity`, `ConcreteInstance` |
| **general** / **generic** | Universal or type-parameterized | `genericType`, `GeneralPurpose`, `GenericProgramming` |
| **abstract** / **theoretical** | Conceptual model | `abstractFactory`, `TheoreticalPhysics`, `AbstractAlgebra` |

---

#### 8. **Structural & Architectural**
Adjectives describing composition and design patterns.

| Adjective | Signal | Typical Usage |
|-----------|--------|-------------|
| **structural** | Related to organization | `structuralTyping`, `StructuralEngineering`, `StructuralPattern` |
| **architectural** | Related to system design | `architecturalPattern`, `ArchitecturalDecision` |
| **procedural** | Step-by-step process | `proceduralProgramming`, `ProceduralGeneration`, `ProceduralMemory` |
| **objectOriented** / **oo** | Based on objects | `objectOrientedDesign`, `OOP`, `OOD` |
| **functional** | Based on pure functions | `functionalProgramming`, `FunctionalInterface`, `FunctionalFood` |
| **declarative** | What not how | `declarativeUI`, `DeclarativeLanguage`, `DeclarativeConfig` |
| **imperative** | Step-by-step commands | `imperativeProgramming`, `ImperativeMood`, `ImperativeSentence` |
| **reactive** | Event-driven response | `reactiveProgramming`, `ReactiveExtensions`, `ReactiveAirway` |
| **eventDriven** | Triggered by events | `eventDrivenArchitecture`, `EventDrivenProgramming` |
| **dataDriven** | Determined by data | `dataDrivenDecision`, `DataDrivenTesting`, `DataDrivenDesign` |
| **testDriven** / **tdd** | Tests before implementation | `testDrivenDevelopment`, `TDD` |
| **behaviorDriven** / **bdd** | Tests as behavior specs | `behaviorDrivenDevelopment`, `BDD` |
| **domainDriven** / **ddd** | Organized by business domain | `domainDrivenDesign`, `DDD` |
| **modelDriven** | Based on abstract models | `modelDrivenEngineering`, `ModelDrivenArchitecture` |
| **contractDriven** | Based on formal contracts | `contractDrivenDesign`, `DesignByContract` |
| **apiFirst** | API design precedes implementation | `apiFirstDevelopment`, `ApiFirstDesign` |
| **mobileFirst** | Mobile precedes desktop | `mobileFirstDesign`, `MobileFirstStrategy` |
| **databaseFirst** | Schema precedes code | `databaseFirstApproach`, `DbFirstEfCore` |
| **codeFirst** | Code precedes schema | `codeFirstMigration`, `CodeFirstEntityFramework` |
| **schemaFirst** | Schema definition precedes code | `schemaFirstApi`, `SchemaFirstGraphQL` |
| **clientFirst** / **serverFirst** | Priority orientation | `clientFirstArchitecture`, `ServerFirstRendering` |
| **modular** | Composed of modules | `modularArchitecture`, `ModularDesign`, `ModularArithmetic` |
| **monolithic** | Single unified structure | `monolithicApp`, `MonolithicKernel`, `MonolithicArchitecture` |
| **microservice** | Small independent services | `microserviceArchitecture`, `MicroservicePattern` |
| **serviceOriented** / **soa** | Organized around services | `serviceOrientedArchitecture`, `SOA` |
| **layered** | Organized in levels | `layeredArchitecture`, `LayeredCake`, `LayerProtocol` |
| **tiered** | Organized in tiers | `threeTierArchitecture`, `TieredPricing` |
| **componentBased** | Built from components | `componentBasedArchitecture`, `ComponentBasedUI` |
| **pluginBased** | Extended via plugins | `pluginBasedSystem`, `PluginArchitecture` |
| **eventBased** | Organized around events | `eventBasedSystem`, `EventBasedCommunication` |
| **messageBased** | Communication via messages | `messageBasedArchitecture`, `MessageBasedAsync` |
| **queueBased** | Work managed via queues | `queueBasedProcessing`, `QueueBasedArchitecture` |
| **streamBased** | Data as continuous flow | `streamBasedProcessing`, `StreamBasedAnalytics` |
| **batchBased** | Processing in groups | `batchBasedSystem`, `BatchBasedETL` |
| **ruleBased** | Determined by explicit rules | `ruleBasedSystem`, `RuleBasedEngine`, `RuleBasedValidation` |
| **workflowBased** | Managed via workflows | `workflowBasedRouting`, `WorkflowBasedApproval` |
| **stateBased** | Managed via state machines | `stateBasedRouting`, `StateBasedUI`, `StateBasedTesting` |
| **configBased** | Behavior via configuration | `configBasedRouting`, `ConfigBasedFeatureFlags` |
| **annotationBased** / **decoratorBased** | Metadata-driven | `annotationBasedConfig`, `DecoratorBasedDI` |
| **conventionBased** | Follows established patterns | `conventionBasedRouting`, `ConventionOverConfiguration` |
| **inheritanceBased** | Uses class hierarchy | `inheritanceBasedDesign`, `InheritanceBasedPolymorphism` |
| **compositionBased** | Uses object composition | `compositionBasedDesign`, `FavorComposition` |
| **interfaceBased** | Uses contracts/interfaces | `interfaceBasedProgramming`, `InterfaceBasedDI` |
| **aspectOriented** / **aop** | Cross-cutting concerns | `aspectOrientedProgramming`, `AOP`, `AspectJ` |
| **featureOriented** / **fosd** | Organized by features | `featureOrientedSoftwareDevelopment`, `FOSD` |
| **productLine** / **spl** | Variants from common base | `softwareProductLine`, `SPL`, `ProductLineEngineering` |
| **generative** | Creates other artifacts | `generativeAI`, `GenerativeAdversarialNetwork`, `GenerativeArt` |
| **reflective** | Self-examining | `reflectiveProgramming`, `ReflectiveSurface`, `ReflectivePractice` |
| **introspective** | Self-analyzing | `introspectiveApi`, `IntrospectivePersonality` |
| **extensible** | Can be extended | `extensibleMarkup`, `ExtensibleSystem`, `ExtensibleLanguage` |
| **flexible** | Adaptable to change | `flexibleLayout`, `FlexibleWorking`, `FlexibleSchema` |
| **rigid** / **inflexible** | Resistant to change | `rigidSchema`, `RigidBody`, `InflexibleRule` |
| **pluggable** | Modular attachment | `pluggableArchitecture`, `PluggableAuth`, `PluggableDatabase` |
| **interchangeable** | Easily swapped | `interchangeableParts`, `InterchangeableLens` |
| **replaceable** | Can be substituted | `replaceableBattery`, `ReplaceableComponent` |
| **swappable** | Hot-replaceable | `swappableDrive`, `SwappableTheme`, `HotSwappable` |
| **scalable** | Handles growth | `scalableArchitecture`, `HorizontallyScalable` |
| **elastic** | Automatic scaling | `elasticCompute`, `ElasticSearch`, `ElasticBandwidth` |
| **resilient** / **faultTolerant** | Recovers from failures | `resilientSystem`, `FaultTolerantDesign`, `ResilientDistributedDataset` |
| **robust** / **rugged** | Handles edge cases well | `robustCode`, `RuggedSoftware`, `RobustStatistics` |
| **fragile** / **brittle** | Breaks easily | `fragileBaseClass`, `BrittleTest`, `FragileState` |
| **decoupled** | Loosely connected | `decoupledArchitecture`, `LooselyCoupled`, `DecoupledComponents` |
| **coupled** / **tightlyCoupled** | Strongly connected | `tightlyCoupled`, `CoupledOscillator`, `LooselyCoupled` |
| **cohesive** / **coherent** | Internally consistent | `cohesiveModule`, `CoherentDesign`, `CohesiveTeam` |
| **orthogonal** | Independent, non-overlapping | `orthogonalDesign`, `OrthogonalArray`, `OrthogonalProjection` |
| **normalized** | Standardized form | `normalizedData`, `NormalizedDatabase`, `NormalizedVector` |
| **denormalized** | Optimized for read | `denormalizedData`, `DenormalizedCache` |
| **composable** | Builds from smaller parts | `composableFunction`, `ComposableArchitecture`, `ComposableUI` |
| **aggregate** | Collection summary | `aggregateRoot`, `AggregateFunction`, `AggregateData` |
| **composite** | Composed of parts | `compositePattern`, `CompositeKey`, `CompositeMaterial` |
| **atomic** | Indivisible unit | `atomicOperation`, `AtomicDesign`, `AtomicMass` |
| **molecular** | Made of atoms/units | `molecularStructure`, `MolecularBiology`, `MolecularGastronomy` |
| **granular** / **fineGrained** | Detailed small units | `granularControl`, `FineGrainedLock`, `GranularData` |
| **coarseGrained** | Large undetailed units | `coarseGrainedLock`, `CoarseGrainedService` |
| **lowLevel** / **highLevel** | Abstraction distance from hardware | `lowLevelApi`, `HighLevelLanguage`, `LowLevelFormat` |
| **closeToMetal** | Very low level | `closeToMetalProgramming`, `BareMetal` |

---

#### 9. **Operational & Runtime**
Adjectives describing execution characteristics.

| Adjective | Signal | Typical Usage |
|-----------|--------|-------------|
| **active** | Currently running or enabled | `activeUser`, `ActiveRecord`, `ActiveDirectory` |
| **inactive** / **idle** | Not currently running | `inactiveSession`, `IdleTimeout`, `IdleConnection` |
| **enabled** | Turned on or allowed | `enabledFeature`, `EnabledFlag` |
| **disabled** | Turned off or blocked | `disabledButton`, `DisabledState` |
| **running** | Currently executing | `runningProcess`, `RunningTotal`, `RunningShoes` |
| **stopped** / **halted** | Execution ceased | `stoppedState`, `HaltedException` |
| **paused** | Temporarily suspended | `pausedVideo`, `PausedThread` |
| **suspended** | Hanging temporarily | `suspendedAccount`, `SuspendedAnimation` |
| **resumed** | Continued after pause | `resumedOperation`, `ResumedThread` |
| **terminated** / **killed** | Forcefully ended | `terminatedProcess`, `KilledJob` |
| **aborted** | Prematurely ended | `abortedTransaction`, `AbortedLaunch` |
| **completed** / **finished** | Successfully ended | `completedTask`, `FinishedGood` |
| **failed** / **error** | Unsuccessfully ended | `failedRequest`, `ErrorState` |
| **successful** / **success** | Achieved intended result | `successfulLogin`, `SuccessRate` |
| **unsuccessful** / **failure** | Did not achieve result | `unsuccessfulAttempt`, `FailureRate` |
| **ready** | Prepared to execute | `readyState`, `ReadyQueue`, `ReadyPlayerOne` |
| **waiting** / **pending** | Awaiting condition | `waitingThread`, `PendingPromise`, `WaitingRoom` |
| **blocked** | Prevented from proceeding | `blockedThread`, `BlockedPort`, `BlockedUser` |
| **unblocked** | Free to proceed | `unblockedThread`, `UnblockedSignal` |
| **locked** / **locking** | Exclusive access held | `lockedFile`, `LockingPolicy` |
| **unlocked** / **unlocking** | Access available | `unlockedPhone`, `UnlockingAchievement` |
| **busy** / **occupied** | Currently in use | `busySignal`, `OccupiedFlag` |
| **available** / **free** | Ready for use | `availableMemory`, `FreeSpace`, `AvailableForHire` |
| **allocated** | Reserved for use | `allocatedMemory`, `AllocatedBudget` |
| **deallocated** / **freed** | Released from use | `deallocatedMemory`, `FreedPointer` |
| **reserved** | Held for future use | `reservedWord`, `ReservedSeat`, `ReservedMemory` |
| **committed** | Definitely allocated | `committedMemory`, `CommittedTransaction` |
| **tentative** / **speculative** | Provisional | `tentativePlan`, `SpeculativeExecution` |
| **speculative** | Optimistic prediction | `speculativeLoad`, `SpeculativeInvestment` |
| **optimistic** | Assumes success | `optimisticLock`, `OptimisticConcurrency`, `OptimisticUI` |
| **pessimistic** | Assumes failure/collision | `pessimisticLock`, `PessimisticConcurrency` |
| **lazy** / **deferred** | Delayed until needed | `lazyLoading`, `DeferredExecution`, `LazyEvaluation` |
| **eager** | Immediate execution | `eagerLoading`, `EagerInitialization` |
| **justInTime** / **jit** | Compiled at runtime | `jitCompiler`, `JustInTimeManufacturing`, `JIT` |
| **aheadOfTime** / **aot** | Compiled before runtime | `aotCompilation`, `AheadOfTimeOptimization` |
| **interpreted** | Executed line-by-line | `interpretedLanguage`, `InterpretedCode` |
| **compiled** | Translated before execution | `compiledLanguage`, `CompiledBinary`, `CompiledQuery` |
| **transpiled** | Source-to-source compiled | `transpiledCode`, `TranspiledToJs` |
| **native** | Machine code directly | `nativeCode`, `NativeCompilation`, `NativeApp` |
| **managed** | Runtime-controlled (GC, etc.) | `managedCode`, `ManagedMemory`, `ManagedService` |
| **unmanaged** | Manual control required | `unmanagedMemory`, `UnmanagedResource`, `UnmanagedCode` |
| **sandboxed** | Restricted environment | `sandboxedCode`, `SandboxedProcess`, `SandboxedIframe` |
| **containerized** | Isolated in container | `containerizedApp`, `ContainerizedService` |
| **virtualized** | Running on virtual hardware | `virtualizedServer`, `VirtualizedEnvironment` |
| **distributed** | Spread across nodes | `distributedSystem`, `DistributedDatabase`, `DistributedLedger` |
| **clustered** | Grouped for availability | `clusteredIndex`, `ClusteredServer`, `ClusteredEnvironment` |
| **replicated** | Copied across locations | `replicatedData`, `ReplicatedState`, `ReplicatedLog` |
| **sharded** | Horizontally partitioned | `shardedDatabase`, `ShardedCollection` |
| **partitioned** | Divided into parts | `partitionedTable`, `PartitionedDrive` |
| **federated** | United while autonomous | `federatedIdentity`, `FederatedLearning`, `FederatedSearch` |
| **isolated** | Separate from others | `isolatedScope`, `IsolatedStorage`, `IsolatedProcess` |
| **standalone** | Independent operation | `standaloneApp`, `StandaloneServer`, `StandaloneMode` |
| **embedded** | Contained within another | `embeddedSystem`, `EmbeddedDatabase`, `EmbeddedVideo` |
| **headless** | Without UI | `headlessBrowser`, `HeadlessCMS`, `HeadlessServer` |
| **serverless** | Abstracted infrastructure | `serverlessFunction`, `ServerlessArchitecture`, `ServerlessComputing` |
| **stateless** | No session memory | `statelessProtocol`, `StatelessComponent`, `StatelessWidget` |
| **stateful** | Maintains session memory | `statefulSession`, `StatefulWidget`, `StatefulFirewall` |
| **persistent** / **durable** | Survives restart | `persistentConnection`, `DurableMessage`, `PersistentVolume` |
| **ephemeral** / **transient** | Temporary existence | `ephemeralStorage`, `TransientFault`, `EphemeralPort` |
| **sticky** | Persistently associated | `stickySession`, `StickyBit`, `StickyFooter` |
| **idempotent** | Same result on retry | `idempotentOperation`, `IdempotentAPI` |
| **safe** / **harmless** | Without side effects | `safeMode`, `HarmlessFunction` |
| **pure** | No side effects, deterministic | `pureFunction`, `PureComponent` |
| **impure** | Has side effects | `impureFunction`, `ImpureReducer` |
| **deterministic** | Predictable output | `deterministicAlgorithm`, `DeterministicFiniteAutomaton` |
| **nondeterministic** / **stochastic** | Random/probabilistic | `nondeterministicAutomaton`, `StochasticProcess`, `StochasticGradientDescent` |
| **probabilistic** | Based on probability | `probabilisticModel`, `ProbabilisticDataStructure`, `ProbabilisticAlgorithm` |
| **heuristic** | Rule-of-thumb approach | `heuristicAlgorithm`, `HeuristicSearch`, `HeuristicEvaluation` |
| **meta** | Self-referential | `metaData`, `MetaProgramming`, `MetaAnalysis`, `MetaCognition` |
| **recursive** | Self-calling | `recursiveFunction`, `RecursiveDescent`, `RecursiveCommonTableExpression` |
| **iterative** | Loop-based repetition | `iterativeApproach`, `IterativeDevelopment`, `IterativeDeepening` |
| **parallel** | Simultaneous execution | `parallelProcessing`, `ParallelAlgorithm`, `ParallelUniverse` |
| **concurrent** | Overlapping execution | `concurrentModification`, `ConcurrentAccess`, `ConcurrentUser` |
| **sequential** | One-at-a-time execution | `sequentialAccess`, `SequentialConsistency`, `SequentialCircuit` |
| **async** / **asynchronous** | Non-blocking | `asyncOperation`, `AsynchronousIO`, `AsyncAwait` |
| **sync** / **synchronous** | Blocking/waiting | `syncCall`, `SynchronousCommunication` |
| **blocking** | Waits for completion | `blockingQueue`, `BlockingCall`, `BlockingIO` |
| **nonblocking** / **nonBlocking** | Continues without waiting | `nonblockingAlgorithm`, `NonBlockingIO`, `NonBlockingSocket` |
| **lockFree** | No mutual exclusion | `lockFreeQueue`, `LockFreeDataStructure`, `LockFreeProgramming` |
| **waitFree** | Guaranteed progress | `waitFreeAlgorithm`, `WaitFreeConsensus` |
| **obstructionFree** | Progress if solo | `obstructionFreeAlgorithm`, `ObstructionFreePrinciple` |
| **threadSafe** | Safe concurrent access | `threadSafeQueue`, `ThreadSafeSingleton`, `ThreadSafeCollection` |
| **reentrant** | Safe recursive entry | `reentrantLock`, `ReentrantFunction`, `ReentrantReadWriteLock` |
| ** preemptive** | Can be interrupted | `preemptiveMultitasking`, `PreemptiveScheduling` |
| **cooperative** / **coop** | Yields control voluntarily | `cooperativeMultitasking`, `CooperativeScheduling` |
| **realtime** / **real-time** | Time-constrained | `realtimeSystem`, `HardRealTime`, `SoftRealTime`, `RealTimeOS` |
| **softRealtime** | Occasional deadline miss OK | `softRealtimeSystem`, `SoftRealtimeVideo` |
| **hardRealtime** | No deadline miss allowed | `hardRealtimeSystem`, `HardRealtimeControl` |
| **firmRealtime** | Missed deadline = bad result | `firmRealtimeSystem`, `FirmRealtimeProcessing` |
| **timeCritical** | Time is crucial | `timeCriticalSection`, `TimeCriticalPath` |
| **latencySensitive** | Low delay required | `latencySensitiveApp`, `LatencySensitivePath` |
| **throughputOptimized** | High volume focused | `throughputOptimizedSystem`, `ThroughputOptimizedStorage` |
| **bandwidthOptimized** | Data transfer focused | `bandwidthOptimizedProtocol`, `BandwidthOptimizedCompression` |
| **memoryOptimized** | Low RAM usage | `memoryOptimizedAlgorithm`, `MemoryOptimizedDataStructure` |
| **cpuOptimized** | Low processor usage | `cpuOptimizedCode`, `CpuOptimizedRendering` |
| **spaceOptimized** | Low storage usage | `spaceOptimizedFormat`, `SpaceOptimizedTree` |
| **timeOptimized** | Fast execution | `timeOptimizedAlgorithm`, `TimeOptimizedPath` |
| **cacheOptimized** | Efficient cache usage | `cacheOptimizedLayout`, `CacheOptimizedAccess` |
| **ioOptimized** | Efficient input/output | `ioOptimizedDatabase`, `IoOptimizedStorage` |
| **networkOptimized** | Efficient data transmission | `networkOptimizedProtocol`, `NetworkOptimizedSync` |
| **batteryOptimized** | Low power consumption | `batteryOptimizedApp`, `BatteryOptimizedMode` |
| **energyEfficient** / **green** | Low environmental impact | `energyEfficientCode`, `GreenComputing` |
| **highPerformance** / **hp** | Optimized for speed | `highPerformanceComputing`, `HPC`, `HighPerformanceMode` |
| **lowLatency** | Minimal delay | `lowLatencyNetwork`, `LowLatencyAudio`, `LowLatencyStorage` |
| **highThroughput** | Maximum processing rate | `highThroughputSystem`, `HighThroughputScreening` |
| **highAvailability** / **ha** | Minimal downtime | `highAvailabilityCluster`, `HAProxy`, `HighAvailabilityArchitecture` |
| **faultTolerant** / **ft** | Continues despite errors | `faultTolerantSystem`, `FaultTolerantDesign`, `FTCluster` |
| **selfHealing** | Auto-recovery from failure | `selfHealingSystem`, `SelfHealingNetwork`, `SelfHealingCode` |
| **autoScaling** | Automatic capacity adjustment | `autoScalingGroup`, `AutoScalingPolicy`, `HorizontalAutoScaling` |
| **loadBalanced** | Distributed workload | `loadBalancedCluster`, `LoadBalancedArchitecture` |
| **geoDistributed** | Spread geographically | `geoDistributedDatabase`, `GeoDistributedCDN` |
| **multiTenant** | Shared by many users/orgs | `multiTenantApp`, `MultiTenantArchitecture`, `MultiTenantDatabase` |
| **singleTenant** | Dedicated to one user/org | `singleTenantDeployment`, `SingleTenantInstance` |
| **zeroDowntime** | Continuous availability | `zeroDowntimeDeployment`, `ZeroDowntimeMigration` |
| **blueGreen** | Parallel environment switch | `blueGreenDeployment`, `BlueGreenStrategy` |
| **canary** | Gradual rollout testing | `canaryDeployment`, `CanaryRelease`, `CanaryAnalysis` |
| **rolling** | Sequential replacement | `rollingUpdate`, `RollingDeployment`, `RollingRestart` |
| **hot** | Active without restart | `hotReload`, `HotSwap`, `HotPatch`, `HotStandby` |
| **cold** | Requires initialization | `coldStart`, `ColdStorage`, `ColdFusion`, `ColdBackup` |
| **warm** | Partially initialized | `warmCache`, `WarmStandby`, `WarmStart` |
| **live** | Real-time active | `liveMigration`, `LiveBackup`, `LiveSite`, `LiveStream` |
| **batch** | Grouped processing | `batchJob`, `BatchProcessing`, `BatchNormalization` |
| **streaming** | Continuous flow processing | `streamingData`, `StreamingAnalytics`, `StreamingPlatform` |

---

#### 10. **Semantic & Categorical**
Adjectives describing meaning and classification.

| Adjective | Signal | Typical Usage |
|-----------|--------|-------------|
| **semantic** | Related to meaning | `semanticVersioning`, `SemanticHTML`, `SemanticWeb`, `SemanticAnalysis` |
| **syntactic** / **syntactical** | Related to structure | `syntacticSugar`, `SyntacticAnalysis`, `SyntacticallyValid` |
| **lexical** | Related to words/tokens | `lexicalScope`, `LexicalAnalysis`, `LexicalEnvironment` |
| **pragmatic** | Related to practical use | `pragmaticProgrammer`, `PragmaticMeaning`, `PragmaticApproach` |
| **semantic** | Meaning-based | `semanticError`, `SemanticGap`, `SemanticVersioning` |
| **formal** | According to established rules | `formalLanguage`, `FormalSpecification`, `FormalWear` |
| **informal** | Not strictly regulated | `informalLanguage`, `InformalMeeting`, `InformalSpecification` |
| **concrete** | Specific, not abstract | `concreteClass`, `ConcreteInstance`, `ConcreteSyntax` |
| **abstract** | Conceptual, general | `abstractClass`, `AbstractMethod`, `AbstractAlgebra`, `AbstractArt` |
| **generic** | Type-parameterized | `genericClass`, `GenericProgramming`, `GenericDrug`, `GenericBrand` |
| **specific** | Definite, particular | `specificImplementation`, `SpecificHeat`, `SpecificGravity` |
| **concrete** | Real, not abstract | `concreteImplementation`, `ConcretePoetry`, `ConcreteJungle` |
| **nominal** | In name only | `nominalValue`, `NominalTypeSystem`, `NominalGDP`, `NominalLeader` |
| **structural** | Based on structure | `structuralTyping`, `StructuralEngineering`, `StructuralEquation` |
| **duck** | If it walks like duck... | `duckTyping`, `DuckTest`, `RubberDuckDebugging` |
| **static** | Compile-time determined | `staticType`, `StaticTyping`, `StaticAnalysis`, `StaticElectricity` |
| **dynamic** | Runtime determined | `dynamicType`, `DynamicTyping`, `DynamicLanguage`, `DynamicPricing` |
| **strong** | Strict type enforcement | `strongTyping`, `StrongPassword`, `StrongAI`, `StrongForce` |
| **weak** | Loose type enforcement | `weakTyping`, `WeakReference`, `WeakAI`, `WeakForce` |
| **strict** | Enforces rules rigorously | `strictMode`, `StrictTyping`, `StrictEquality`, `StrictParent` |
| **loose** / **lenient** | Permissive, flexible | `looseCoupling`, `LenientParsing`, `LooseLeaf` |
| **nominal** | Name-based typing | `nominalTypeSystem`, `NominalTyping` (Java, C#) |
| **structural** | Shape-based typing | `structuralTypeSystem`, `StructuralTyping` (TypeScript, Go) |
| **behavioral** | Based on behavior/contracts | `behavioralSubtype`, `BehavioralPattern`, `BehavioralEconomics` |
| **invariant** | Unchangeable or mathematical | `invariantProperty`, `LoopInvariant`, `InvariantMass` |
| **covariant** | Varying in same direction | `covariantReturn`, `CovariantArray`, `CovariantDerivative` |
| **contravariant** | Varying in opposite direction | `contravariantParameter`, `ContravariantFunctor` |
| **bivariant** | Both co and contra | `bivariantType`, `BivariantFunction` |
| **variant** | Varying, or virus strain | `variantType`, `CovidVariant`, `ProductVariant` |
| **invariant** | Unchanging, or mathematical constant | `invariantCheck`, `InvariantCulture`, `InvariantSection` |
| **immutable** | Unchangeable after creation | `immutableData`, `ImmutableObject`, `ImmutableCollection` |
| **mutable** | Changeable after creation | `mutableState`, `MutableVariable`, `MutableCollection` |
| **final** | Cannot be overridden/extended | `finalClass`, `FinalVariable`, `FinalAnswer` |
| **sealed** | Closed for extension | `sealedClass`, `SealedMethod`, `SealedWithAKiss` |
| **open** | Extensible | `openClass`, `OpenSource`, `OpenRelationship`, `OpenConnection` |
| **closed** | Not extensible | `closedClass`, `ClosedSource`, `ClosedBook`, `ClosedSet` |
| **primitive** | Basic built-in type | `primitiveType`, `PrimitiveValue`, `PrimitiveObsession` |
| **composite** / **compound** | Made of multiple parts | `compositeType`, `CompositePattern`, `CompoundInterest`, `CompoundWord` |
| **complex** | Made of real and imaginary | `complexNumber`, `ComplexSystem`, `ComplexProblem`, `OedipusComplex` |
| **simple** | Not compound or easy | `simpleType`, `SimpleObject`, `SimpleInterest`, `KeepItSimple` |
| **basic** / **fundamental** | Essential, elementary | `basicType`, `FundamentalTheorem`, `BasicTraining`, `BasicInstinct` |
| **derived** / **inherited** | Coming from base | `derivedClass`, `InheritedProperty`, `DerivedValue`, `DerivedDemand` |
| **base** / **parent** | Foundation for others | `baseClass`, `ParentClass`, `BaseCase`, `Baseball` |
| **child** / **sub** / **derived** | Inherits from parent | `childClass`, `Subclass`, `DerivedTable`, `SubQuery` |
| **super** / **ancestor** | Above in hierarchy | `superClass`, `SuperType`, `AncestorQuery`, `SuperUser` |
| **root** | Origin of hierarchy | `rootClass`, `RootCause`, `RootDirectory`, `SquareRoot` |
| **leaf** | End of hierarchy | `leafNode`, `LeafPage`, `LeafLet`, `LeafBlower` |
| **intermediate** | Middle of hierarchy | `intermediateNode`, `IntermediateLanguage`, `IntermediateResult` |
| **terminal** | End point or final | `terminalNode`, `TerminalWindow`, `TerminalIllness`, `AirportTerminal` |
| **auxiliary** / **helper** | Supporting role | `auxiliaryFunction`, `HelperClass`, `AuxiliaryVerb` |
| **utility** | General-purpose tools | `utilityClass`, `UtilityFunction`, `UtilityBelt`, `PublicUtility` |
| **helper** / **auxiliary** | Assisting function | `helperMethod`, `AuxiliaryConstructor` |
| **wrapper** | Containing layer | `wrapperClass`, `WrapperFunction`, `WrapperPattern`, `WrapperLibrary` |
| **adapter** / **adaptor** | Interface converter | `adapterPattern`, `PowerAdapter`, `DatabaseAdapter` |
| **bridge** | Connecting abstraction | `bridgePattern`, `BridgeMethod`, `DentalBridge`, `NetworkBridge` |
| **proxy** | Stand-in representative | `proxyPattern`, `ProxyServer`, `ProxyWar`, `ProxyVote` |
| **decorator** | Adding responsibilities | `decoratorPattern`, `DecoratorFunction`, `PropertyDecorator` |
| **facade** | Simplified interface | `facadePattern`, `BuildingFacade`, `ApiFacade` |
| **composite** | Tree structure component | `compositePattern`, `CompositeKey`, `CompositeMaterial` |
| **flyweight** | Shared state optimization | `flyweightPattern`, `FlyweightFactory`, `FlyweightObject` |
| **singleton** | One instance only | `singletonPattern`, `SingletonClass`, `SingletonInstance` |
| **observer** / **observable** | Publish-subscribe | `observerPattern`, `ObservableStream`, `ObserverEffect` |
| **strategy** | Interchangeable algorithm | `strategyPattern`, `PricingStrategy`, `ExitStrategy` |
| **state** | State-dependent behavior | `statePattern`, `StateMachine`, `StateManagement`, `StateOfMind` |
| **command** | Encapsulated request | `commandPattern`, `CommandLine`, `CommandCenter`, `VoiceCommand` |
| **iterator** | Sequential access | `iteratorPattern`, `IteratorProtocol`, `ForEachIterator` |
| **memento** | State capture | `mementoPattern`, `MementoMori`, `ScreenMemento` |
| **template** / **skeleton** | Method algorithm steps | `templateMethod`, `SkeletonMethod`, `EmailTemplate` |
| **factory** | Object creation | `factoryMethod`, `AbstractFactory`, `FactoryPattern`, `CarFactory` |
| **builder** | Step-by-step construction | `builderPattern`, `StringBuilder`, `QueryBuilder`, `HomeBuilder` |
| **prototype** | Clone-based creation | `prototypePattern`, `ObjectPrototype`, `PrototypeCar` |
| **pool** | Reusable object collection | `objectPool`, `ThreadPool`, `ConnectionPool`, `GenePool` |
| **chain** / **pipeline** | Linked processing | `chainOfResponsibility`, `PipelinePattern`, `SupplyChain` |
| **mediator** | Central coordinator | `mediatorPattern`, `MediatorObject`, `PeaceMediator` |
| **memento** / **caretaker** | State preservation | `caretakerPattern`, `MementoSnapshot`, `CaretakerGovernment` |
| **visitor** | External operation | `visitorPattern`, `VisitorFunction`, `TouristVisitor` |
| **interpreter** | Language parsing | `interpreterPattern`, `PythonInterpreter`, `InterpreterOfMaladies` |
| **null** / **nil** / **nothing** | Absence pattern | `nullObjectPattern`, `NilPointer`, `NothingType`, `NullHypothesis` |
| **optional** / **maybe** | Possible absence | `optionalType`, `MaybeMonad`, `OptionalChaining`, `OptionalParameter` |
| **default** | Fallback value | `defaultValue`, `DefaultCase`, `DefaultConstructor`, `DefaultRoute` |
| **required** / **mandatory** | Must be present | `requiredField`, `MandatoryArgument`, `RequiredCourse` |
| **optional** / **facultative** | May be absent | `optionalParam`, `FacultativeCourse`, `OptionalFeature` |
| **nullable** / **nonNull** | Can/cannot be null | `nullableType`, `NonNullAssert`, `NullableReference` |
| **mandatory** / **compulsory** | Required by rule | `mandatoryField`, `CompulsoryEducation`, `MandatoryMinimum` |
| **conditional** | Depends on condition | `conditionalRendering`, `ConditionalProbability`, `ConditionalJump` |
| **unconditional** | No conditions apply | `unconditionalJump`, `UnconditionalLove`, `UnconditionalOffer` |
| **contextual** | Depends on context | `contextualHelp`, `ContextualAdvertising`, `ContextualTab` |
| **contextFree** | Independent of context | `contextFreeGrammar`, `ContextFreeLanguage` |

---

### Part 3: Composition Guidelines

#### Prefix Patterns

| Prefix | Meaning | Examples |
|--------|---------|----------|
| **pre** / **before** | Temporal precedence | `preprocess`, `prefetch`, `beforeMount` |
| **post** / **after** | Temporal succession | `postprocess`, `postcondition`, `afterRender` |
| **re** | Repetition or reversal | `retry`, `reload`, `revert`, `reinitialize` |
| **un** / **non** / **anti** | Negation or opposite | `undo`, `nonNull`, `antiPattern`, `unsafe` |
| **sub** / **mini** / **micro** | Smaller scope | `subset`, `subroutine`, `microservice`, `minified` |
| **super** / **hyper** / **meta** | Greater scope or self-reference | `superset`, `superclass`, `metadata`, `metaprogramming` |
| **multi** / **poly** | Many | `multithreaded`, `multicast`, `polyglot`, `polymorphism` |
| **uni** / **mono** / **single** | One | `unidirectional`, `monolithic`, `singleton`, `singlePage` |
| **bi** / **di** / **dual** | Two | `bidirectional`, `binary`, `dualCore`, `digraph` |
| **tri** | Three | `trinary`, `tripleStore`, `trigram` |
| **auto** / **self** | Automatic or reflexive | `autocomplete`, `autoscaling`, `selfHealing`, `selfReference` |
| **pseudo** | False or resembling | `pseudocode`, `pseudorandom`, `pseudoclass` |
| **quasi** | Partially or seemingly | `quasiExperiment`, `quasistatic`, `quasiparticle` |
| **semi** / **hemi** / **demi** | Half or partial | `semicolon`, `semistructured`, `hemisphere`, `demigod` |
| **demi** / **semi** | Partial | `demilitarized`, `demigray`, `demiurge` |
| **ultra** / **extra** / **super** | Beyond normal | `ultrafast`, `extralarge`, `superuser` |
| **infra** / **sub** | Below | `infrastructure`, `infrared`, `subroutine`, `subzero` |
| **over** / **hyper** | Above or excessive | `overflow`, `override`, `hypervisor`, `hyperlink` |
| **under** / **hypo** | Below or insufficient | `underflow`, `underscore`, `hypothermia`, `hypothesis` |
| **inter** / **intra** | Between / within | `internet`, `interface`, `intranet`, `intramural` |
| **trans** | Across or beyond | `transport`, `transaction`, `transform`, `transgender` |
| **pro** / **pre** | Forward / before | `progress`, `proactive`, `preview`, `prefix` |
| **retro** | Backward | `retrofit`, `retrospective`, `retrograde`, `retrocomputing` |
| **co** / **con** / **com** / **col** / **cor** | Together | `cooperate`, `connect`, `combine`, `collect`, `correlate` |
| **counter** / **contra** | Against | `counterexample`, `countermeasure`, `contradict`, `contraflow` |
| **anti** / **against** | Opposition | `antivirus`, `antipattern`, `antithesis`, `antihero` |
| **de** / **dis** / **dif** | Removal or reversal | `decode`, `disconnect`, `different`, `disable` |
| **a** / **an** / **ab** / **abs** | Away from | `away`, `anon`, `abstract`, `abnormal`, `absent` |
| **ex** / **e** / **ef** | Out of | `exit`, `extract`, `emit`, `effect`, `efficient` |
| **in** / **im** / **il** / **ir** / **en** / **em** | Into or not | `input`, `import`, `illegal`, `irregular`, `enable`, `embed` |
| **ob** / **oc** / **of** / **op** | Against or toward | `object`, `occur`, `offer`, `oppose` |

#### Suffix Patterns

| Suffix | Meaning | Examples |
|--------|---------|----------|
| **able** / **ible** | Capability or possibility | `portable`, `visible`, `scalable`, `flexible` |
| **al** / **ial** / **ical** | Relating to | `logical`, `functional`, `hierarchical`, `structural` |
| **an** / **ian** | Belonging to | `republican`, `guardian`, `librarian`, `artificial` |
| **ance** / **ence** / **ancy** / **ency** | State or quality | `importance`, `existence`, `buoyancy`, `efficiency` |
| **ant** / **ent** / **er** / **or** / **ar** / **ist** / **ster** | Agent/doer | `servant`, `student`, `teacher`, `actor`, `beggar`, `scientist`, `gangster` |
| **ary** / **ery** / **ory** / **ry** | Place or collection | `library`, `bakery`, `directory`, `factory`, `refinery` |
| **ate** / **ify** / **ize** / **ise** | To make or become | `activate`, `simplify`, `organize`, `modernise` |
| **ation** / **ition** / **ion** / **sion** / **tion** | Action or process | `creation`, `addition`, `connection`, `decision`, `action` |
| **dom** / **hood** / **ship** | State or condition | `freedom`, `neighborhood`, `ownership`, `relationship` |
| **ed** / **d** / **t** | Past tense or adjective | `processed`, `fixed`, `built`, `sent` |
| **ee** / **er** | Object or subject of action | `employee`, `trainee`, `employer`, `trainer` |
| **en** | Made of or to become | `wooden`, `golden`, `strengthen`, `harden` |
| **er** / **or** | Comparative or agent | `faster`, `bigger`, `creator`, `director` |
| **est** / **most** | Superlative | `fastest`, `biggest`, `utmost`, `innermost` |
| **ful** / **full** | Full of | `beautiful`, `powerful`, `handful`, `useful` |
| **hood** | State or condition | `childhood`, `neighborhood`, `falsehood`, `likelihood` |
| **ic** / **ical** | Relating to | `historic`, `musical`, `atomic`, `comic` |
| **ing** | Present participle/adjective | `running`, `interesting`, `pending`, `processing` |
| **ion** / **ation** / **ition** / **tion** / **sion** | Action or result | `decision`, `information`, `condition`, `action`, `version` |
| **ish** | Somewhat like | `greenish`, `childish`, `selfish`, `foolish` |
| **ism** / **asm** | Doctrine or condition | `capitalism`, `tourism`, `enthusiasm`, `criticism` |
| **ist** | Believer or practitioner | `scientist`, `artist`, `optimist`, `specialist` |
| **ity** / **ty** / **ety** | State or quality | `reality`, `cruelty`, `safety`, `honesty`, `variety` |
| **ive** / **ative** / **itive** | Tending to | `active`, `creative`, `sensitive`, `quantitative` |
| **ize** / **ise** | To make or become | `organize`, `modernize`, `computerize`, `apologise` |
| **less** | Without | `homeless`, `careless`, `useless`, `endless` |
| **let** / **et** / **ette** | Small | `booklet`, `leaflet`, `hamlet`, `kitchenette`, `cigarette` |
| **like** / **ly** / **y** | Resembling | `childlike`, `friendly`, `sunny`, `hairy` |
| **logy** / **ology** | Study of | `biology`, `technology`, `psychology`, `methodology` |
| **ment** | Result of action | `movement`, `development`, `government`, `equipment` |
| **ness** | State or quality | `happiness`, `darkness`, `kindness`, `effectiveness` |
| **oid** / **ode** / **pod** | Resembling | `android`, `asteroid`, `method`, `tripod`, `nematode` |
| **or** / **er** / **ar** | Agent or comparator | `actor`, `bigger`, `cellular`, `regular` |
| **ory** / **ary** / **ery** | Place or relating to | `directory`, `necessary`, `bakery`, `refinery` |
| **ous** / **ious** / **eous** / **uous** | Full of | `dangerous`, `curious`, `courageous`, `continuous` |
| **ship** | State or skill | `friendship`, `leadership`, `workmanship`, `ownership` |
| **sion** / **tion** / **ion** | Action or state | `decision`, `action`, `information`, `version` |
| **some** | Tending to or group | `handsome`, `troublesome`, `twosome`, `threesome` |
| **th** / **eth** | Ordinal or noun | `fourth`, `fifth`, `warmth`, `depth`, `breadth` |
| **ure** / **ture** / **sure** | Result or action | `pressure`, `structure`, `nature`, `culture`, `measure` |
| **ward** / **wards** | Direction | `forward`, `backward`, `toward`, `homeward`, `afterwards` |
| **wise** | Manner or direction | `clockwise`, `likewise`, `otherwise`, `crabwise` |
| **y** / **ly** / **ty** / **ery** | Characterized by | `dirty`, `friendly`, `cruelty`, `bakery` |

---

### Part 4: Anti-Patterns to Avoid

#### 1. **Vague Generality**
Avoid nouns that communicate nothing specific:
- ❌ `data`, `info`, `stuff`, `thing`, `object` (when used as generic suffixes without context)
- ✅ `userProfileData`, `paymentInfo`, `sessionStuff` → `sessionContext`, `unknownObject` → `unclassifiedEntity`

#### 2. **Hungarian Notation Remnants**
Avoid encoding type in name (modern IDEs make this redundant):
- ❌ `strName`, `intCount`, `bIsValid`, `arrItems`
- ✅ `name`, `count`, `isValid`, `items`

#### 3. **Abbreviation Inconsistency**
Be consistent with abbreviation depth:
- ❌ `cust` vs `customer` vs `cstmr` in same codebase
- ✅ Establish: `cust` (internal), `customer` (external API), never `cstmr`

#### 4. **Grammatical Number Confusion**
- ❌ `userList` containing a single user (should be `selectedUser` or `currentUser`)
- ❌ `users` for a boolean flag (should be `hasMultipleUsers` or `isMultiUser`)
- ✅ Use plural **only** for collections: `users` = collection, `user` = single entity

#### 5. **False Specificity**
Avoid implying precision where none exists:
- ❌ `exactAmount` when value is approximate
- ❌ `permanentStorage` for cache that expires
- ✅ `estimatedAmount`, `durableCache` (accurate honesty)

#### 6. **Domain Leakage**
Avoid technology terms in domain models:
- ❌ `sqlUser`, `mongoOrder`, `redisCache` (unless infrastructure-specific)
- ✅ `persistentUser`, `documentOrder`, `fastCache` (capability-based)

#### 7. **Negative Naming**
Prefer positive assertions when possible:
- ❌ `isNotFound`, `disableInvalid`, `nonEmptyCheck`
- ✅ `isMissing`, `validateOnly`, `hasContent`

#### 8. **Temporal Confusion**
Be precise about when state applies:
- ❌ `updatedData` (when? by whom?)
- ✅ `lastModifiedData`, `clientUpdatedData`, `syncedAtData`

#### 9. **Scale Ambiguity**
Avoid size adjectives without context:
- ❌ `bigFile`, `smallCache`, `largeObject`
- ✅ `fileSizeMB`, `cacheEntryLimit`, `objectHeapSize`

#### 10. **Boolean Blunders**
Boolean variables must be predicative adjectives:
- ❌ `status` (boolean), `complete` (boolean - confusing with verb)
- ✅ `isComplete`, `hasStatus`, `shouldUpdate`

---

### Semantic Contracts

Apply these adjectives and nouns as **semantic contracts**: once an identifier establishes a specific meaning in your codebase, maintain that meaning consistently across all modules. A `Cache` is always a fast-access storage layer; an `Immutable` object never mutates after creation; a `Service` always represents a business capability boundary.

By standardizing this vocabulary, you enable **semantic grep**—the ability to search for patterns by meaning rather than by string matching—and create a **ubiquitous language** that bridges technical implementation and domain expertise.

---

*This standard works in conjunction with The Programmer's Verb Taxonomy to provide complete naming coverage for identifiers, parameters, types, and architectural components.*
