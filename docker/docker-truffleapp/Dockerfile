# Node image
FROM node:latest

# Create code directory
RUN mkdir /source

# Set working directory
WORKDIR /source

# Install Truffle
RUN npm install -g truffle && npm config set bin-links false