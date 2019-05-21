One-command private test Tangle
================================

This repository allows you to set up your own IOTA network by using a single [Docker](https://www.docker.com/why-docker) command. When you run this command, you'll have your own IOTA test network and 2.7Pi of test IOTA tokens to use at the first address of this seed: `SEED99999999999999999999999999999999999999999999999999999999999999999999999999999`.

You can use this network to test your ideas and applications (without risking any monetary value) until it's ready for the IOTA Mainnet.

The test network consists of one [IRI node](https://docs.iota.works/docs/iri/0.1/introduction/overview) and an instance of [Compass](https://docs.iota.works/docs/compass/0.1/introduction/overview). The IRI node waits to receive transactions, validates them, and keeps an up-to-date record of users' balances. Compass sends the IRI node zero-value transactions called milestones that reference other transactions. Any transaction that's referenced by a milestone is considered confirmed. At this point, the node updates any balances that were affected by the confirmed transaction.

Compass uses a pre-built [Merkle tree](https://docs.iota.works/docs/the-tangle/0.1/concepts/the-coordinator#milestones) (in the `layers` directory) with a depth of 20. This Merkle tree is large enough for Compass to send milestones for over a year at 30-second intervals. 

**Warning:** The purpose of this repository is to allow you to quickly set up a test IOTA network. To do so, this repository uses a public Merkle tree. As a result, you should use this repository only for testing. Do not expose this network to the Internet!

## Get started

You need at least 4GB RAM to run this code.

1. Install [`docker`, `docker-compose`](https://docs.docker.com/compose/install/) and [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
2. Clone this repository
3. In the `one-command-tangle` directory, execute the `docker-compose up` command. If you're using a Linux operating system, you may need to add `sudo` before this command.
 **Note:** Before running any Docker command, you need to run the Docker daemon on the host machine.
 **Note:** If you want to start Compass again after stopping it, remove the `-bootstrap` flag from the `docker-compose.yml` file before running the command again.
4. Interact with your IRI node at http://localhost:14265
 For example, using Node.js, you can do the following to see your balance for the `SEED99999999999999999999999999999999999999999999999999999999999999999999999999999` seed. If you've never used the IOTA client libraries before, we recommend completing [this tutorial](https://docs.iota.works/docs/getting-started/0.1/tutorials/send-a-zero-value-transaction-with-nodejs).
 ```js
 var request = require('request');

 const iota = require('@iota/core');

 Iota = iota.composeAPI({
     provider: 'http://localhost:14265'
 });

 var address = iota.generateAddress('SEED99999999999999999999999999999999999999999999999999999999999999999999999999999',0);

 getBalance(address);

 function getBalance(address) {

     console.log(address);

     var command = {
     'command': 'getBalances',
     'addresses': [
     address
     ],
     'threshold':100
     }

     var options = {
     url: 'http://localhost:14265',
     method: 'POST',
     headers: {
     'Content-Type': 'application/json',
     'X-IOTA-API-Version': '1',
     'Content-Length': Buffer.byteLength(JSON.stringify(command))
     },
     json: command
     };

     request(options, function (error, response, data) {
         if (!error && response.statusCode == 200) {
         console.log(JSON.stringify(data,null,1));
         }
     });
 }
 ```
 
Instead of using the client libraries, you can configure the [IOTA Light Wallet](https://github.com/iotaledger/wallet/releases) to connect to your node at http://localhost:14265. Then, you can send and receive IOTA tokens on your network by interacting with the user interface.

**Note:** When you first log into the IOTA Light Wallet, go to **RECEIVE** > **ATTACH TO TANGLE** to see your full balance.

## Outstanding tasks

 - [x] Run a private tangle with a single `docker-compose up` command
 - [ ] Implement a way to prevent having to manually remove the `-bootstrap` flag after re-launch
 - [ ] Use the latest Docker image of Compass instead of a fixed release
 - [ ] Optional: build a nice wrapper around it with Docker/Docker-Compose included for even easier setup
 - [ ] Optional: include additional tools like a tangle explorer/visualizer/monitor
