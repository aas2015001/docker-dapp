# Create a DApp (Decentralized Application) with docker

This is a sample project for creating a DApp (Decentralized application) with docker

* [Prerequisites](#Prerequisites)
* [Instructions](#Instructions)

## Prerequisites

* Docker Toolbox for Windows or Docker

## Instructions

* Create a new directory and CD into
    ```docker
    mkdir -p docker-dapp/docker && cd docker-dapp/docker
    ```

* Create a docker-compose.yml file
    ```docker
    version: '3'
    services:
        ganachecli:
            image: trufflesuite/ganache-cli:latest
            command: bash -c "ganache-cli -h 0.0.0.0"
            ports:
            - 8545:8545
        truffleapp:
            build:
                context: ./docker-truffleapp
            command: bash
            stdin_open: true
            tty: true
            ports:
            - 8080:8080
            volumes:
            - ../source/:/source
    ```

* Create a Dockerfile for a Truffleapp into docker-truffleapp directory
    ```docker
    # Node image
    FROM node:latest

    # Create code directory
    RUN mkdir /source

    # Set working directory
    WORKDIR /source

    # Install Truffle
    RUN npm install -g truffle && npm config set bin-links false
    ```

* Run a docker-composer command to start containers
    ```docker
    docker-compose -f docker-compose.yml up -d
    ```

* Install metamask chrome extension and check if ethereum client (Ganache) works!

    1. [Metamask chrome extension](https://metamask.io/)
    2. Open Metamask and configure custom private network (http://dockerhost:8545)
    3. Gets a list accounts from ganache container
        ```docker
        docker logs -f docker_ganachecli_1
        ```
    3. Import an account with the private key retrevie from ganache container logs

* Create a Truffle application
    ```docker
    docker exec -it docker_truffleapp_1 bash
    truffle unbox webpack
    truffle compile
    truffle migrate
    ```

* Manual correction - Update the truffle.js file as mentioned below (host and port are changed)
    ```docker
    // Allows us to use ES6 in our migrations and tests.
    require('babel-register')

    module.exports = {
        networks: {
            development: {
                host: 'ganachecli',
                port: 8545,
                network_id: '*' // Match any network id
            }
        }
    }
    ```
* Recompile the Truffle application
    ```docker
    truffle compile
    truffle migrate
    ```

* Manual correction - Update the build and dev scripts in package.json file as mentioned below
    ```
    "build": "./node_modules/webpack/bin/webpack.js",
    "dev": "./node_modules/webpack-dev-server/bin/webpack-dev-server.js --host 0.0.0.0  --public dockerhost "
    ```

* Manual correction - Update the Web3 HttpProvider in app.js file as mentioned below
    ```
    window.web3 = new Web3(new Web3.providers.HttpProvider("http://dockerhost:8545"));
    ```
* Build and Run the truffle application
    ```
    npm run build
    npm run dev
    ```

* Open a webpage @ http://dockerhost:8080/