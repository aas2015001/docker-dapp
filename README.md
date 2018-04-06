# Create a DApp (Decentralized Application) with docker

This is a sample project for creating a DApp (Decentralized application) with docker

* [Prerequisites](#Prerequisites)
* [Instructions](#Instructions)

## Prerequisites

* Docker Toolbox for Windows or Docker

## Instructions

* Create a new directory and CD into
    ```docker
    mkdir docker-dapp && cd docker-dapp
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
        - ./source/:/source
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