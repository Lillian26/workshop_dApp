# Understanding roundtable.

## What is Roundtable
Check dApp online, tinker with testnet dApp; set up nami for testnet, 2/3 addresses. \
Be able to create & fund treasury, create a transaction, sign and submit it.

## Project structure:
Ignoring other project files, below is a list of the files and structure for the main functionality of the dApp.
<pre>
.
├── src
│   ├── cardano
│   │   ├── config.ts
│   │   ├── multiplatform-lib.ts
│   │   ├── query-api.ts
│   │   └── utils.ts  
│   ├── components
│   │   ├── currency.tsx
│   │   ├── layout.tsx
│   │   ├── modal.tsx
│   │   ├── native-script.tsx
│   │   ├── notification.tsx
│   │   ├── status.tsx
│   │   ├── time.tsx
│   │   ├── transaction.tsx
│   │   └── user-data.tsx
│   ├── db.ts
│   ├── pages
│   │   ├── api
│   │   │   └── hello.ts
│   │   ├── _app.tsx
│   │   ├── config.tsx
│   │   ├── index.tsx
│   │   ├── transactions
│   │   │   └── [base64CBOR].tsx
│   │   └── treasuries
│   │       ├── [base64CBOR]
│   │       │   ├── edit.tsx
│   │       │   ├── index.tsx
│   │       │   └── new.tsx
│   │       └── new.tsx
│   ├── route.ts
│   └── styles
│       ├── ...
├── tapes
    └── graphql
        ├── query-1.json5
        └── query-2.json5

</pre>

### Cardano
The cardano folder contains files for functions to set blockchain network and db configurations, functions for grapql querying and processing data on the blockchain, and functions for interacting with the blockchain.
- config.ts is the file for settin network and db parameters, exports context and config variables for the entire dApp.
- multiplatform-lib.ts exports functions for; verify network, add recipient, build transactions, get signatures, verify signatures, sign transactions.
- query-api.ts exports type of value and all functions to be used to return wallet data for creating transactions.
- utils.ts exports functions that return transaction parameters.

### Components
This folder contains files of various parts of the UI. Using own UI would require one to change the layout and create own pages in the pages folder. \
A few interesting files: 
- transaction.tsx is 
- user-data.tsx contains
- native-script.tsx - is

### db.ts
In this file, set and export the type of treasury, and the local database for your treasuries created. \
Why need a local db? \
Need to define own dApp data types and db schema specific to own dApp. 

### pages
- api \
This folder contains API routes, serverless functions e.g. hello.ts. 
- app.tsx
- config.tsx
- index.tsx
- transactions
    - [base64CBOR].tsx
- treasuries
    - [base64CBOR]
        - edit.tsx
        - index.tsx
        - new.tsx
    - new.tsx

### route.ts

### styles

### tapes 
This folder contains examples

## List of libraries.
The libraries to know and follow updates on. Here is a list of vital libraries for the dApp technology.
 - "@apollo/client": "^3.5.10",
 - "@cardano-graphql/client-ts": "^6.2.0",
 - "@dcspark/cardano-multiplatform-lib-browser": "^0.3.0",
 - "buffer": "^6.0.3",
 - "dexie": "^3.2.1",
 - "dexie-react-hooks": "^1.1.1",
 - "graphql": "^16.3.0",
 - "gun": "^0.2020.1236",
 - "nanoid": "^3.3.2",

## Setting up.

### Requirements:
- RoundTable: almost zero. Any modern computer with a modern browser can run it;
- Auto Sync: almost free on Heroku and Glitch;
- Submit API: needs a cardano-node and its submit api, with a reverse proxy. non-trivial but should calculate together with GraphQL stack;
- GraphQL stack: significant usage. 4 CPU cores; 32GB RAM; 80GB SSD disk storage. https://github.com/input-output-hk/cardano-graphql/releases/tag/6.2.0


### Test environment:
Minimal set up includes the first two items listed below: 

1. set up node
https://tutorials.cardanoacademy.io/cardano-cli-testnet/setting-up-cardano-node

2. Alternatives Ogmios instead of Cardano-db-sync: https://ogmios.dev/faq/

3. Auto-sync: This is another set up needed to be able to check if the other addresses of your treasury have already "signed" the proposed transaction.
Generally check the status of a given transaction.

How to use GUN for auto-sync ? \
For now we might not need to have auto sync for first trial set up.

If hosted Gun servers used go offline you can still just share the url and import signatures, copy and send the signatures via other channel like Discord or Email.