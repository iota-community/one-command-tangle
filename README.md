One-command private test Tangle
================================

This application allows you to set up your own IOTA network by using a single [Docker](https://www.docker.com/why-docker) command. When you run this command, you'll have your own IOTA test network and [2.7Pi](https://docs.iota.org/docs/iota-basics/0.1/references/units-of-iota-tokens) (the maximum amount) of test IOTA tokens to use. These tokens will be stored on the first address of this seed: `SEED99999999999999999999999999999999999999999999999999999999999999999999999999999`.

![IOTA wallet for the test network](docs-static/light-wallet-test-tangle.png)

You can use this network to test your ideas and applications without risking any monetary value.

The test network consists of one [IRI node](https://docs.iota.works/docs/iri/0.1/introduction/overview) and an instance of a [Compass](https://docs.iota.works/docs/compass/0.1/introduction/overview). The IRI node receives transactions, validates them, and keeps an up-to-date record of users' balances. At regular intervals, Compass sends the IRI node zero-value transactions called [milestones](https://docs.iota.org/docs/the-tangle/0.1/concepts/the-coordinator#milestones) that reference other transactions. Any transaction that's referenced by a milestone is considered confirmed. At this point, the node updates any balances that were affected by the confirmed transaction.

Compass uses a pre-built [Merkle tree](https://docs.iota.works/docs/the-tangle/0.1/concepts/the-coordinator#milestones) (in the `layers` directory) with a depth of 20. This Merkle tree is large enough for Compass to send milestones for over a year at 30-second intervals.

**Warning:** The purpose of this application is to allow you to quickly set up a test IOTA network. To do so, this application uses a pre-calculated Merkle tree. As a result, you should use this application only for testing. Do not expose this network to the Internet!

## Dependencies

[Docker and Docker Compose](https://docs.docker.com/compose/install/).

## 1. Run the application

You need at least 4GB RAM to run this application.

1. Clone this repository
2. In the `one-command-tangle` directory, execute the `docker-compose up` command. If you're using a Linux operating system, you may need to add `sudo` before this command.

 In the console, you should see that the IRI node is running and receiving milestones from Compass.

 ![Compass and IRI node logs](docs-static/cli.gif)

**Note:** If you want to stop Compass and start it again, press **Ctrl + C**, and remove the `-bootstrap` extra flag from the `.env` configuration file before running the command again.

## 2. Interact with the network

When the application is running, you can interact with the network through the IRI node's API port at the following address http://localhost:14265.

For a list of API endpoints see the [IOTA documentation](https://docs.iota.org/docs/iri/0.1/references/api-reference).

### GetBalances

For example, using the [JavaScript client library](https://docs.iota.org/docs/client-libraries/0.1/introduction/overview) with Node.js, you can call the [`getBalances`](https://docs.iota.org/docs/iri/0.1/references/api-reference#getbalances) endpoint to get the total balance of the `SEED99999999999999999999999999999999999999999999999999999999999999999999999999999` seed. If you've never used the IOTA client libraries before, we recommend completing [this tutorial](https://docs.iota.works/docs/getting-started/0.1/tutorials/send-a-zero-value-transaction-with-nodejs).

 ```js
 var request = require('request');

 const iota = require('@iota/core');

 Iota = iota.composeAPI({
     provider: 'http://localhost:14265'
 });

 var address = iota.generateAddress('SEED99999999999999999999999999999999999999999999999999999999999999999999999999999',0);

 getBalance(address);

 function getBalance(address) {

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

 ### Response

 ```json
{
 "balances": [
  "2779530283277761"
 ],
 "references": [
  "BDZPAONKWQTVCXFFO9GBTJ9GGWPRLITXZ9BMYALTCVWNOLFYPNHFJHPDWICRPGCZWUNDQHV9UDEXGW999"
 ],
 "milestoneIndex": 7,
 "duration": 1
}
```

If you want to send and receive transactions on the network through a user interface, you can configure the [IOTA Light Wallet](https://github.com/iotaledger/wallet/releases) to connect to your node at http://localhost:14265 and log in with your seed: `SEED99999999999999999999999999999999999999999999999999999999999999999999999999999`.

To connect to your node, go to **Tools** > **Edit Node Configuration**, and enter the URL of your node (http://localhost:14265).

![IOTA wallet configuration](docs-static/light-wallet-node-configuration.png)

**Note:** When you first log into the IOTA Light Wallet, go to **RECEIVE** > **ATTACH TO TANGLE** to see your full balance.

## Optional: Enabling Tangle Utils

The [Tangle Utils](https://utils.iota.org/) can be served for your own One Command Tangle as well. If you wish to utilize this feature you can run `docker-compose` with a additional flag to start the tangle utils with your private tangle.
To do this run `docker-compose` like this:

```
docker-compose -f docker-compose.yml -f docker-compose-tools.yml up
```

The build process might take a while but as soon as the services are started you should be able to go to http://localhost:4001 to utilize the Tangle Utils.
If you wish you can modify the configuration files for the Tangle Utils in the `config/tools` folder, for example to add API keys for additional functionality.


## Optional: SSL Support en reverse proxy

If you wish to have SSL support for your node we provide an additional `docker-compose` configuration file you can use.
This will spin up a Caddy webserver instance for you including Let's Encrypt SSL certificates and a reverse proxy to your
IRI server. For this to work you need to do a couple of things first:

 - Make sure the A-record in the DNS config of the domain name you want to use points to your public IP
 - Make sure the machine you are running the one-command-tangle on can be reached on that public IP (you might need port forwards)
 - Change the `your-domain.com` and `your@email.com` in the `Caddyfile` to your chosen domain and e-mail address (both required)
 - Make sure port 80 and 443 are available for binding on the machine you will run the one-command-tangle on.

After this has been done just run the one-command-tangle with the following command:

```
docker-compose -f docker-compose.yml -f docker-compose-ssl.yml up
```

If all went well you should be able to reach your node over SSL.

**Note:** By enabling SSL and setting this up you are exposing your private testnet to the internet, if you do not want this you might be better off with the version without SSL.

If you wish to use a fixed SSL certificate and key instead so you can keep it all offline simple replace the `tls your@email.com` line in the `Caddyfile` with `tls /etc/chain.pem /etc/key.pem`
Make sure to include both files in this folder if you wish to do this. You also need to add the volumes for both files to the `docker-compose-ssl.yml` file under `Volumes` in the `proxy` section:

```
      - ./chain.pem:/etc/chain.pem
      - ./key.pem:/etc/key.pem
```

More about the available configuration options in the `Caddyfile` file can be found here: https://caddyserver.com/docs/tls

## Outstanding tasks

 - [x] Run a private tangle with a single `docker-compose up` command
 - [x] Support SSL and Let's Encrypt
 - [x] Include a tangle explorer
 - [ ] Implement a way to prevent having to manually remove the `-bootstrap` flag after re-launch
 - [ ] Use the latest Docker image of Compass instead of a fixed release
 - [ ] Optional: build a nice wrapper around it with Docker/Docker-Compose included for even easier setup
 - [ ] Optional: include additional tools like a tangle visualizer/monitor
