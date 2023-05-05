# Hyperledger bridge server

## How to run
Запускать bridge-server необходимо на сервере с нодой hyperledger fabric.

Для запуска сервера необходимо подготовить файл конфигурации подключения к ноде fabric, сертификат и секеретный ключь, для подписания транзакций.

### Prepare certificates

Для получения сертификаты и приватного ключа, необходимо обратиться к администратору вашей организации, к которой будет подключаться сервер. Сеть hyperledger может использовать разные центры сертификации, поэтому нельзя привести единый пример для получения сертификатов аккаунта.

Сертификат и секретный ключь необходимо положить в папку: `./wallets/{VAR}/`

**VAR**=папка где будут лежать сертификаты аккаунта, *Default*: "User"

***ВАЖНО***: Изначально сертификат и секретный ключь могут иметь другое название.

Файл сертификата должен иметь название: `cert.pem`

Файл серкетного ключа должен иметь название: `key.pem`

### Prepare config

Конфигурационный файл для подключения к сети hyperledger. При использование тестовой сети из репозитория: [fabric-samples](https://github.com/hyperledger/fabric-samples), он генерируеться автоматически. При подключение к рабочей сети, его придеться писать в ручную.

Файл конфигурации необходимо положить в папку `./config/`

Пример конфигурационого файла:
```json
{
    "name": "test-network-org1",
    "version": "1.0.0",
    "client": {
        //название организации к ноде которой, будет подключаться сервер
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
        //название канала с которым работает сервер
        "mychannel": {}
    },
    "organizations": {
        // настройки организации к которой подключается сервер
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
    //настройки ноды к которой подключается сервер
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
    //настройки центра сертификации организации
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

После настройки конфигурации, необходимо настроить .env

**CONFIG_NAME**=название файла конфигурации, который лежит в `./config/`

**USER_NAME**=название папки где лежат сертификаты аккаунта

**VAULT_CONTRACT_NAME**=название чейнкода vault


**GRPC_IP**=ip по которому будет работать сервер

**GRPC_PORT**=порт по которому будет работать сервер

Теперь можно запускать сервер.

### Starting the server

Для запуска вам понадобится версия node >16.X

Скачиваем зависимости: 
```
npm install
```

Скаичваем модуль pm2:
```
npm install -g pm2
```

Запускаем проект:
```
pm2 start index.js
```


## Architecture overview

Сервер является модулем реле. Он связываеться с сетью hyperledger fabric. Его цель доставать, проверять и подписываеть транзакции.

При запуске, он создает акаунт rele в hyperladger. С помощью него он подписывает транзакции, и на нем хранятся токены, которые проходят через bridg. Это сделано из-за ограничения hyperledger, в нем чейнкод не может хранить токены как в EVM сетях.
Сервер работает в режиме ожидания запросов от rele. Сервер работает по системе gRPC.