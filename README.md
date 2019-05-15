One-command private test Tangle
================================

This repository allows you to set up your own IOTA network using a single command.

This network consists of one [IRI node](https://docs.iota.works/docs/iri/0.1/introduction/overview) and an instance of a Coordinator ([Compass](https://docs.iota.works/docs/compass/0.1/introduction/overview)).

Compass uses a pre-built [Merkle tree](https://docs.iota.works/docs/the-tangle/0.1/concepts/the-coordinator#milestones) (in the `layers` directory) with a depth of 20. This Merkle tree is large enough for Compass to send milestones for over a year at 30-second intervals. 

## Warning

The purpose of this repository is to allow you to quickly set up a test IOTA network. To do so, this repository uses a public Merkle tree. As a result, you should use this repository only for testing. Do not expose this network to the internet!

## Get started

You need at least 4GB RAM to run this code.

1. Make sure you have [`docker`, `docker-compose`](https://docs.docker.com/compose/install/) and [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) installed on your local machine
2. Clone this repository
3. In the `one-command-tangle` directory, execute the `docker-compose up` command. If you're using a Linux operating system, you may need to add `sudo` before this command.
 **Note:** Before running any Docker command, you need to run the Docker daemon on the host machine.
4. Interact with your private Tangle IRI node at http://localhost:14265
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
5. Set up the [IOTA Light Wallet](https://github.com/iotaledger/wallet/releases) to use your node at http://localhost:14265 with seed 
to gain full access to the 2.7Pi available on your testnet.

## Notes

- If you want to start Compass again after stopping it make sure to remove the `-bootstrap` line from `docker-compose.yml` before starting again.

## Outstanding tasks

 - [x] Run a private tangle with a single `docker-compose up` command
 - [ ] Implement a way to prevent having to manually remove the `-bootstrap` flag after re-launch
 - [ ] Use the latest Docker image of Compass instead of a fixed release
 - [ ] Optional: build a nice wrapper around it with Docker/Docker-Compose included for even easier setup
 - [ ] Optional: include additional tools like a tangle explorer/visualizer/monitor
