One command private test Tangle
================================

This repository provides you with a way to set up a development/testing IOTA network using a single command.
It installs a IRI docker container and a Compass docker container. It also includes a pre-built merkle tree with a depth of 20,
Enough to let a network run for over a year with 30 second milestone intervals.

## Warning

The sole purpose of this is to set up a quick testnet; It uses a pre-shared merkle tree and Coordinator seed. Do not use this for
anything else besides local testing since it would be insecure to use this otherwise. Do not expose this testnet to the internet!

## Getting started

1. Make sure you have `docker` and `docker-compose` installed on your local machine
2. Within this folder execute `docker-compose up`
3. Interact with your private tangle IRI node at http://localhost:14265
4. Set up [Trinity](https://trinity.iota.org/) or the [IOTA Light Wallet](https://github.com/iotaledger/wallet/releases) to use your node at http://localhost:14265 with seed `SEED99999999999999999999999999999999999999999999999999999999999999999999999999999`
to gain full access to the 2.7Pi available on your testnet.

## Notes

- If you want to start Compass again after stopping it make sure to remove the `-bootstrap` line from `docker-compose.yml` before starting again.
- You need enough RAM to run this setup, at least 4GB of free RAM is recommended.

## Outstanding tasks

 - [x] Run a private tangle with a single `docker-compose up` command
 - [ ] Implement a way to prevent having to manually remove the `-bootstrap` flag after re-launch
 - [ ] Use the latest Docker image of Compass instead of a fixed release
 - [ ] Optional: build a nice wrapper around it with Docker/Docker-Compose included for even easier setup
 - [ ] Optional: include additional tools like a tangle explorer/visualizer/monitor
