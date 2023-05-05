# Hyperledger bridge server

## How to run
You need to run bridge-server on a server with a hyperledger fabric node.

To start the server, you need to prepare a configuration file for connecting to the fabric node, a certificate and a secret key for signing transactions.

### Prepare certificates

To obtain a certificate and a private key, you need to contact the administrator of your organization to which the server will connect. The hyperledger network can use different certification authorities, so it is impossible to give a single example for obtaining account certificates.

The certificate and the secret key must be put in the folder: `./wallets/{VAR}/`

**VAR**=the folder where the account certificates will be located, *Default*: "User"

The certificate file must have a name: `cert.pem`

The secret key file must have a name: `key.pem`

### Prepare config

The configuration file must be placed in the folder `./config/`

Example of a configuration file:
```json
{
    "name": "test-network-org1",
    "version": "1.0.0",
    "client": {
        //the name of the organization to which the server will connect to the node
        "organization": "Org1",
        "connection": {
            "timeout": {
                "peer": {
                    "endorser": "300"
                }
            }
        }
    },
    "channels": {
        //the name of the channel the server is working with
        "mychannel": {}
    },
    "organizations": {
        //settings of the organization to which the server is connected
        "Org1": {
            "mspid": "Org1MSP",
            "peers": [
                "peer0.org1.example.com"
            ],
            "certificateAuthorities": [
                "ca.org1.example.com"
            ]
        }
    },
    //settings of the node to which the server is connected
    "peers": {
        "peer0.org1.example.com": {
            "url": "grpcs://localhost:7051",
            "tlsCACerts": {
                "pem": "-----BEGIN CERTIFICATE-----\nMIICODCCAd6gAwIBAgIUFbpDf0Do7pzR9AIAmu7MWAkm204wCgYIKoZIzj0EAwIw\ncDELMAkGA1UEBhMCVVMxFzAVBgNVBAgTDk5vcnRoIENhcm9saW5hMQ8wDQYDVQQH\nEwZEdXJoYW0xGTAXBgNVBAoTEG9yZzEuZXhhbXBsZS5jb20xHDAaBgNVBAMTE2Nh\nLm9yZzEuZXhhbXBsZS5jb20wHhcNMjMwNDEyMTQzMjAwWhcNMzgwNDA4MTQzMjAw\nWjBwMQswCQYDVQQGEwJVUzEXMBUGA1UECBMOTm9ydGggQ2Fyb2xpbmExDzANBgNV\nBAcTBkR1cmhhbTEZMBcGA1UEChMQb3JnMS5leGFtcGxlLmNvbTEcMBoGA1UEAxMT\nY2Eub3JnMS5leGFtcGxlLmNvbTBZMBMGByqGSM49AgEGCCqGSM49AwEHA0IABEWl\nG4tau2cjiuepPhUuXipbua4MRRtQ5i16KOmLhL3dlXRNpxE9Oqk3EAOYed64h2NU\nlo54XIpKEeihYEwrWsGjVjBUMA4GA1UdDwEB/wQEAwIBBjASBgNVHRMBAf8ECDAG\nAQH/AgEBMB0GA1UdDgQWBBTAY3do6BIjcBvUyq9eWlXt96wuozAPBgNVHREECDAG\nhwQFCWIsMAoGCCqGSM49BAMCA0gAMEUCIQDdrL4aPVPgPMOtRIVBXqU73wgKGIUd\nepAYd/Zq2yGlyAIgaw0tCn5k6IuTYpn54hOySuz5FYCGBzFZ8TeS5UtT3O8=\n-----END CERTIFICATE-----\n"
            },
            "grpcOptions": {
                "ssl-target-name-override": "peer0.org1.example.com",
                "hostnameOverride": "peer0.org1.example.com"
            }
        }
    },
    //organization's certification authority settings
    "certificateAuthorities": {
        "ca.org1.example.com": {
            "url": "https://localhost:7054",
            "caName": "ca-org1",
            "tlsCACerts": {
                "pem": ["-----BEGIN CERTIFICATE-----\nMIICODCCAd6gAwIBAgIUFbpDf0Do7pzR9AIAmu7MWAkm204wCgYIKoZIzj0EAwIw\ncDELMAkGA1UEBhMCVVMxFzAVBgNVBAgTDk5vcnRoIENhcm9saW5hMQ8wDQYDVQQH\nEwZEdXJoYW0xGTAXBgNVBAoTEG9yZzEuZXhhbXBsZS5jb20xHDAaBgNVBAMTE2Nh\nLm9yZzEuZXhhbXBsZS5jb20wHhcNMjMwNDEyMTQzMjAwWhcNMzgwNDA4MTQzMjAw\nWjBwMQswCQYDVQQGEwJVUzEXMBUGA1UECBMOTm9ydGggQ2Fyb2xpbmExDzANBgNV\nBAcTBkR1cmhhbTEZMBcGA1UEChMQb3JnMS5leGFtcGxlLmNvbTEcMBoGA1UEAxMT\nY2Eub3JnMS5leGFtcGxlLmNvbTBZMBMGByqGSM49AgEGCCqGSM49AwEHA0IABEWl\nG4tau2cjiuepPhUuXipbua4MRRtQ5i16KOmLhL3dlXRNpxE9Oqk3EAOYed64h2NU\nlo54XIpKEeihYEwrWsGjVjBUMA4GA1UdDwEB/wQEAwIBBjASBgNVHRMBAf8ECDAG\nAQH/AgEBMB0GA1UdDgQWBBTAY3do6BIjcBvUyq9eWlXt96wuozAPBgNVHREECDAG\nhwQFCWIsMAoGCCqGSM49BAMCA0gAMEUCIQDdrL4aPVPgPMOtRIVBXqU73wgKGIUd\nepAYd/Zq2yGlyAIgaw0tCn5k6IuTYpn54hOySuz5FYCGBzFZ8TeS5UtT3O8=\n-----END CERTIFICATE-----\n"]
            },
            "httpOptions": {
                "verify": false
            }
        }
    }
}
```

After configuring the configuration, you need to configure .env

**CONFIG_NAME**=the name of the configuration file that lies in `./config/`

**USER_NAME**=the name of the folder where the account certificates are located

**VAULT_CONTRACT_NAME**=the name of the vault chaincode


**GRPC_IP**=the ip on which the server will work

**GRPC_PORT**=the port on which the server will work

Now you can start the server.

### Starting the server

To run, you will need node version >16.X

Downloading dependencies: 
```
npm install
```

Download the pm2 module:
```
npm install -g pm2
```

Launching the project:
```
pm2 start index.js
```


## Architecture overview

The server is a relay module. It communicates with the hyperledger fabric network. Its purpose is to get, verify and sign transactions.

At startup, it creates a rele account in hyperladger. With it, he signs transactions, and tokens that pass through the bridge are stored on it. This is done due to the hyperledger limitation, the chaincode cannot store tokens as in EVM networks.
The server is in standby mode for requests from rele. The server runs on the gRPC system.