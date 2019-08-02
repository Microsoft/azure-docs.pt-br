---
title: Visão geral da integração de mensagens do Azure Blockchain Workbench
description: Visão geral do uso de mensagens no Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 05/09/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 49b2bdd1780caa4ae04efbc979e2ea33e2c13c4c
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/14/2019
ms.locfileid: "67147227"
---
# <a name="azure-blockchain-workbench-messaging-integration"></a>Integração de mensagens do Azure Blockchain Workbench

Além de fornecer uma API REST, o Azure Blockchain Workbench também fornece integração baseada em mensagens. O workbench publica eventos centrados em razão via Grade de Eventos do Azure, possibilitando que os clientes downstream ingiram dados ou tomem ação baseados nesses eventos. Para os clientes que exigem troca de mensagem confiável, o Azure Blockchain Workbench também entrega mensagens a um ponto de extremidade do Azure Service Bus.

## <a name="input-apis"></a>Entrada APIs

Se você quiser iniciar transações de sistemas externos para criar usuários, criar contratos e atualizar contratos, poderá usar APIs de entrada de mensagens para executar transações em um razão. Ver [exemplos de integração de mensagens](https://aka.ms/blockchain-workbench-integration-sample) para obter um exemplo que demonstra as APIs de entrada.

A seguir estão as APIs de entrada disponíveis no momento.

### <a name="create-user"></a>Criar usuário

Cria um novo usuário.

A solicitação exige os seguintes campos:

| **Nome**             | **Descrição**                                      |
|----------------------|------------------------------------------------------|
| requestId            | GUID do cliente fornecido                                |
| firstName            | Primeiro nome do usuário                              |
| lastName             | Sobrenome do usuário                               |
| emailAddress         | Endereço de email do usuário                           |
| externalId           | Microsoft Azure Active Directory do usuário                      |
| connectionId         | Identificador exclusivo para a conexão blockchain |
| messageSchemaVersion | Versão do esquema de mensagens                            |
| messageName          | **CreateUserRequest**                               |

Exemplo:

``` json
{
    "requestId": "e2264523-6147-41fc-bbbb-edba8e44562d",
    "firstName": "Ali",
    "lastName": "Alio",
    "emailAddress": "aa@contoso.com",
    "externalId": "6a9b7f65-ffff-442f-b3b8-58a35abd1bcd",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateUserRequest"
}
```

Blockchain Workbench retorna uma resposta com os seguintes campos:

| **Nome**              | **Descrição**                                                                                                             |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------|
| requestId             | GUID do cliente fornecido |
| userId                | ID do usuário que foi criado |
| userChainIdentifier   | Endereço do usuário que foi criado na rede blockchain. No Ethereum, o endereço é o endereço  **on-chain**  do usuário. |
| connectionId          | Identificador exclusivo para a conexão blockchain|
| messageSchemaVersion  | Versão do esquema de mensagens |
| messageName           | **CreateUserUpdate** |
| status                | Status da solicitação de criação do usuário.  Se bem sucedido, o valor é **Sucesso**. Em caso de falha, o valor é **falha**.     |
| additionalInformation | Informações adicionais fornecidas com base no status |

Exemplo bem-sucedida **criar usuário** resposta do Blockchain Workbench:

``` json
{ 
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Success", 
    "additionalInformation": { } 
} 
```

Se a solicitação não foi bem-sucedida, os detalhes sobre a falha são incluídos nas informações adicionais.

``` json
{
    "requestId": "e2264523-6147-41fc-bb59-edba8e44562d", 
    "userId": 15, 
    "userChainIdentifier": null, 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateUserUpdate", 
    "status": "Failure", 
    "additionalInformation": { 
        "errorCode": 4000, 
        "errorMessage": "User cannot be provisioned on connection." 
    }
}
```

### <a name="create-contract"></a>Criar contrato

Cria um novo contrato.

A solicitação exige os seguintes campos:

| **Nome**             | **Descrição**                                                                                                           |
|----------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId            | GUID do cliente fornecido |
| userChainIdentifier  | Endereço do usuário que foi criado na rede blockchain. No Ethereum, esse endereço é o endereço **do usuário na cadeia**. |
| applicationName      | Nome do aplicativo |
| version              | Versão do aplicativo. Necessário se você tiver várias versões do aplicativo habilitadas. Caso contrário, a versão é opcional. Para obter mais informações sobre o controle de versão do aplicativo, confira [Controle de versão do aplicativo do Azure Blockchain Workbench](version-app.md). |
| workflowName         | Nome do fluxo de trabalho |
| parameters           | Parâmetros de entrada para criação de contrato |
| connectionId         | Identificador exclusivo para a conexão blockchain |
| messageSchemaVersion | Versão do esquema de mensagens |
| messageName          | **CreateContractRequest** |

Exemplo:

``` json
{ 
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211", 
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398", 
    "applicationName": "AssetTransfer",
    "version": "1.0",
    "workflowName": "AssetTransfer", 
    "parameters": [ 
        { 
            "name": "description", 
            "value": "a 1969 dodge charger" 
        }, 
        { 
            "name": "price", 
            "value": "12345" 
        } 
    ], 
    "connectionId": 1, 
    "messageSchemaVersion": "1.0.0", 
    "messageName": "CreateContractRequest" 
}
```

Blockchain Workbench retorna uma resposta com os seguintes campos:

| **Nome**                 | **Descrição**                                                                   |
|--------------------------|-----------------------------------------------------------------------------------|
| requestId                | GUID do cliente fornecido                                                             |
| contractId               | Identificador exclusivo para o contrato dentro do Azure Blockchain Workbench |
| contractLedgerIdentifier | Endereço do contrato no razão                                            |
| connectionId             | Identificador exclusivo para a conexão blockchain                               |
| messageSchemaVersion     | Versão do esquema de mensagens                                                         |
| messageName              | **CreateContractUpdate**                                                      |
| status                   | Status da solicitação de criação do contrato.  Valores possíveis: **Enviado**, **Confirmado**, **Falha**.  |
| additionalInformation    | Informações adicionais fornecidas com base no status                              |

Exemplo de um enviado **criar contrato** resposta do Blockchain Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Exemplo de um compromisso **criar contrato** resposta do Blockchain Workbench:

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Se a solicitação não foi bem-sucedida, os detalhes sobre a falha são incluídos nas informações adicionais.

``` json
{
    "requestId": "ce3c429b-a091-4baa-b29b-5b576162b211",
    "contractId": 55,
    "contractLedgerIdentifier": null,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract cannot be provisioned on connection."
    }
}
```

### <a name="create-contract-action"></a>Criar ação de contrato

Cria uma nova ação de contrato.

A solicitação exige os seguintes campos:

| **Nome**                 | **Descrição**                                                                                                           |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------|
| requestId                | GUID do cliente fornecido |
| userChainIdentifier      | Endereço do usuário que foi criado na rede blockchain. No Ethereum, esse endereço é o endereço **do usuário na cadeia**. |
| contractLedgerIdentifier | Endereço do contrato no razão |
| version                  | Versão do aplicativo. Necessário se você tiver várias versões do aplicativo habilitadas. Caso contrário, a versão é opcional. Para obter mais informações sobre o controle de versão do aplicativo, confira [Controle de versão do aplicativo do Azure Blockchain Workbench](version-app.md). |
| workflowFunctionName     | Nome da função de fluxo de trabalho |
| parameters               | Parâmetros de entrada para criação de contrato |
| connectionId             | Identificador exclusivo para a conexão blockchain |
| messageSchemaVersion     | Versão do esquema de mensagens |
| messageName              | **CreateContractActionRequest** |

Exemplo:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "userChainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f248398",
    "contractLedgerIdentifier": "0xde0B295669a9FD93d5F28D9Ec85E40f4cb697BAe",
    "version": "1.0",
    "workflowFunctionName": "modify",
    "parameters": [
        {
            "name": "description",
            "value": "a 1969 dodge charger"
        },
        {
            "name": "price",
            "value": "12345"
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionRequest"
}
```

Blockchain Workbench retorna uma resposta com os seguintes campos:

| **Nome**              | **Descrição**                                                                   |
|-----------------------|-----------------------------------------------------------------------------------|
| requestId             | GUID do cliente fornecido|
| contractId            | Identificador exclusivo para o contrato dentro do Azure Blockchain Workbench |
| connectionId          | Identificador exclusivo para a conexão blockchain |
| messageSchemaVersion  | Versão do esquema de mensagens |
| messageName           | **CreateContractActionUpdate** |
| status                | Status da solicitação de ação do contrato. Valores possíveis: **Enviado**, **Confirmado**, **Falha**.                         |
| additionalInformation | Informações adicionais fornecidas com base no status |

Exemplo de uma resposta de **ação de contrato criada** enviada pelo Blockchain Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Submitted",
    "additionalInformation": { }
}
```

Exemplo de uma resposta de **ação de contrato** confirmada do Blockchain Workbench:

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Committed",
    "additionalInformation": { }
}
```

Se a solicitação não foi bem-sucedida, os detalhes sobre a falha são incluídos nas informações adicionais.

``` json
{
    "requestId": "a5530932-9d6b-4eed-8623-441a647741d3",
    "contractId": 105,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "CreateContractActionUpdate",
    "status": "Failure",
    "additionalInformation": {
        "errorCode": 4000,
        "errorMessage": "Contract action cannot be provisioned on connection."
    }
}
```

### <a name="input-api-error-codes-and-messages"></a>Mensagens e códigos de erro da API entrados

**Código de erro 4000: Erro de solicitação inválida**
- ID de conexão inválida
- Falha na desserialização CreateUserRequest
- Falha na desserialização CreateContractRequest
- Falha na desserialização CreateContractActionRequest
- O aplicativo {identificado pelo nome do aplicativo} não existe
- Aplicativo {identificado pelo nome do aplicativo} não tem fluxo de trabalho
- UserChainIdentifier não existe
- O contrato {identificado pelo identificador do razão} não existe
- Contrato {identificado pelo identificador do razão} não tem função {nome da função do fluxo de trabalho}
- UserChainIdentifier não existe

**Código de erro 4090: Erro de conflito**
- O usuário já existe
- Já existe um contrato
- Ação de contrato já existe

**Código de erro 5000: Erro interno do servidor**
- Mensagens de exceção

## <a name="event-notifications"></a>Notificações de eventos

As notificações de eventos podem ser usadas para notificar usuários e sistemas downstream de eventos que acontecem no Blockchain Workbench e na rede de blockchain a que está conectado. As notificações de evento pode ser consumidas diretamente no código ou usadas com ferramentas como Aplicativos Lógicos e seguir para acionar o fluxo de dados dos sistemas downstream.

Consulte [Referência de mensagem de notificação](#notification-message-reference) para obter detalhes de várias mensagens que podem ser recebidas.

### <a name="consuming-event-grid-events-with-azure-functions"></a>Consumindo eventos da Grade de Eventos com o Azure Functions

Se um usuário deseja usar a Grade de Eventos para ser notificado sobre eventos que ocorrem no Blockchain Workbench, é possível consumir eventos de grade de eventos usando o Azure Functions.

1. Criar um **Aplicativo de funções no Azure** no portal do Azure.
2. Criar uma nova função.
3. Localize o modelo para a Grade de Eventos. Código do modelo básico para ler a mensagem que é mostrada. Modifique o código conforme necessário.
4. Salve a função. 
5. Selecione a Grade de Eventos do grupo de recursos do Blockchain Workbench.

### <a name="consuming-event-grid-events-with-logic-apps"></a>Consumindo eventos da Grade de Eventos com os Aplicativos Lógicos

1. Criar um novo **Aplicativo Lógico no Azure** no portal do Azure.
2. Ao abrir o Aplicativo Lógico do Azure no portal, será solicitado a selecionar um gatilho. Selecione **Grade de Eventos do Azure – quando ocorrer um evento de recurso**.
3. Quando o designer de fluxo de trabalho for exibido, você será solicitado a entrar.
4. Selecione a Assinatura. Recursos como **Microsoft.EventGrid.Topics**. Selecione o **Nome do Recurso** do nome do recurso do grupo de recursos do Azure Blockchain Workbench.
5. Selecione a Grade de Eventos do grupo de recursos do Blockchain Workbench.

## <a name="using-service-bus-topics-for-notifications"></a>Usando tópicos do Barramento de Serviço para notificações

Os Tópicos do Barramento de Serviço podem ser usados para notificar os usuários sobre eventos que ocorrem no Blockchain Workbench. 

1. Navegue até o Barramento de Serviço no grupo de recursos do Workbench.
2. Selecionar **Tópicos**.
3. Selecione **tópico-saída**.
4. Criar uma nova assinatura a esse tópico. Obter uma chave para isso.
5. Crie um programa que assina eventos dessa assinatura.

### <a name="consuming-service-bus-messages-with-logic-apps"></a>Consumir Mensagens de Barramento de Serviço com Aplicativos Lógicos

1. Criar um novo **Aplicativo Lógico no Azure** no portal do Azure.
2. Ao abrir o Aplicativo Lógico do Azure no portal, será solicitado a selecionar um gatilho. Digite **Barramento de Serviço** na caixa de pesquisa e selecione o gatilho apropriado para o tipo de interação para a qual você deseja ter com o Barramento de Serviço. Por exemplo, **Barramento de Serviço -- Quando uma mensagem é recebida em uma assinatura de tópico (auto-preenchimento)** .
3. Quando o designer de fluxo de trabalho é exibido, especifique as informações de conexão para o Barramento de Serviço.
4. Selecione sua assinatura e especifique o tópico de **workbench-externo**.
5. Desenvolva a lógica do seu aplicativo que utiliza a mensagem do gatilho.

## <a name="notification-message-reference"></a>Referência da mensagem de notificação

Dependendo de **messageName**, as mensagens de notificação tem um dos seguintes tipos de mensagem.

### <a name="block-message"></a>Mensagem de bloco

Contém informações sobre blocos individuais. A *BlockMessage* inclui uma seção com informações no nível de bloco e uma seção com informações sobre a transação.

| NOME | DESCRIÇÃO |
|------|-------------|
| block | Contém [informações sobre o bloco](#block-information) |
| transactions | Contém [informações sobre a transação](#transaction-information) de uma coleção para o bloco |
| connectionId | Identificador exclusivo para a conexão |
| messageSchemaVersion | Versão do esquema de mensagens |
| messageName | **BlockMessage** |
| additionalInformation | Informações adicionais fornecidas |

#### <a name="block-information"></a>Informações sobre o bloco

| NOME              | DESCRIÇÃO |
|-------------------|-------------|
| blockId           | Identificador exclusivo para o bloco dentro do Azure Blockchain Workbench |
| blockNumber       | Identificador exclusivo para um bloco no ledger |
| blockHash         | O resumo da mensagem do bloco |
| previousBlockHash | O hash do bloco anterior |
| blockTimestamp    | O carimbo de hora do bloco |

#### <a name="transaction-information"></a>Informações sobre a transação

| NOME               | DESCRIÇÃO |
|--------------------|-------------|
| transactionId      | Identificador exclusivo da transação no Azure Blockchain Workbench |
| transactionHash    | O resumo da mensagem de transação na razão |
| from               | Identificador exclusivo da origem da transação no razão |
| para                 | Identificador exclusivo do destino da transação no razão |
| provisioningStatus | Identifica o status atual do processo de provisionamento para a transação. Os valores possíveis são: </br>0 – a transação foi criada pela API no banco de dados</br>1 – a transação foi enviada para o razão</br>2 – a transação foi confirmada com êxito no razão</br>3 ou 4 – ocorreu uma falha ao confirmar a transação no razão</br>5 – a transação foi confirmada com êxito no razão |

Exemplo de uma *BlockMessage* do Blockchain Workbench:

``` json
{
    "block": {
        "blockId": 123,
        "blockNumber": 1738312,
        "blockHash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
        "previousBlockHash": null,
        "blockTimestamp": "2018-10-09T23:35:58Z",
    },
    "transactions": [
        {
            "transactionId": 234,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": null,
            "provisioningStatus": 1
        },
        {
            "transactionId": 235,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xadd97e1e595916e29ea94fda894941574000ffff",
            "to": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff",
            "provisioningStatus": 2
        }
    ],
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "BlockMessage",
    "additionalInformation": {}
}
```

### <a name="contract-message"></a>Mensagem de contrato

Contém informações sobre um contrato. A mensagem inclui uma seção com propriedades de contrato e uma seção com informações sobre a transação. Todas as transações que modificaram o contrato para o bloco específico são incluídas na seção da transação.

| NOME | DESCRIÇÃO |
|------|-------------|
| blockId | Identificador exclusivo para o bloco dentro do Azure Blockchain Workbench |
| blockHash | Hash do bloco |
| modifyingTransactions | [As transações que modificaram](#modifying-transaction-information) o contrato |
| contractId | Identificador exclusivo para o contrato dentro do Azure Blockchain Workbench |
| contractLedgerIdentifier | Identificador exclusivo do contrato no razão |
| contractProperties | [Propriedades do contrato](#contract-properties) |
| isNewContract | Indica se esse contrato foi ou não criado recentemente. Os valores possíveis são: true: esse contrato foi um novo contrato criado. false: esse contrato é uma atualização de contrato. |
| connectionId | Identificador exclusivo para a conexão |
| messageSchemaVersion | Versão do esquema de mensagens |
| messageName | **ContractMessage** |
| additionalInformation | Informações adicionais fornecidas |

#### <a name="modifying-transaction-information"></a>Modificando informações sobre a transação

| NOME               | DESCRIÇÃO |
|--------------------|-------------|
| transactionId | Identificador exclusivo da transação no Azure Blockchain Workbench |
| transactionHash | O resumo da mensagem de transação na razão |
| from | Identificador exclusivo da origem da transação no razão |
| para | Identificador exclusivo do destino da transação no razão |

#### <a name="contract-properties"></a>Propriedades do contrato

| NOME               | DESCRIÇÃO |
|--------------------|-------------|
| workflowPropertyId | Identificador exclusivo da propriedade de fluxo de trabalho no Azure Blockchain Workbench |
| name | Nome da propriedade de fluxo de trabalho |
| value | Valor da propriedade de fluxo de trabalho |

Exemplo de uma *ContractMessage* do Blockchain Workbench:

``` json
{
    "blockId": 123,
    "blockhash": "0x03a39411e25e25b47d0ec6433b73b488554a4a5f6b1a253e0ac8a200d13fffff",
    "modifyingTransactions": [
        {
            "transactionId": 234,
            "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07ffff"
        },
        {
            "transactionId": 235,
            "transactionHash": "0xa4d9c95b581f299e41b8cc193dd742ef5a1d3a4ddf97bd11b80d123fec27ffff",
            "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
            "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
        }
    ],
    "contractId": 111,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "contractProperties": [
        {
            "workflowPropertyId": 1,
            "name": "State",
            "value": "0"
        },
        {
            "workflowPropertyId": 2,
            "name": "Description",
            "value": "1969 Dodge Charger"
        },
        {
            "workflowPropertyId": 3,
            "name": "AskingPrice",
            "value": "30000"
        },
        {
            "workflowPropertyId": 4,
            "name": "OfferPrice",
            "value": "0"
        },
        {
            "workflowPropertyId": 5,
            "name": "InstanceAppraiser",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 6,
            "name": "InstanceBuyer",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 7,
            "name": "InstanceInspector",
            "value": "0x0000000000000000000000000000000000000000"
        },
        {
            "workflowPropertyId": 8,
            "name": "InstanceOwner",
            "value": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
        },
        {
            "workflowPropertyId": 9,
            "name": "ClosingDayOptions",
            "value": "[21,48,69]"
        }
    ],
    "isNewContract": false,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "ContractMessage",
    "additionalInformation": {}
}
```

### <a name="event-message-contract-function-invocation"></a>Mensagem do evento: Invocação de função do contrato

Contém informações sobre quando uma função de contrato é invocada, como o nome da função, a entrada de parâmetros e o chamador da função.

| NOME | DESCRIÇÃO |
|------|-------------|
| eventName                   | **ContractFunctionInvocation** |
| chamador                      | [Informações sobre o chamador](#caller-information) |
| contractId                  | Identificador exclusivo para o contrato dentro do Azure Blockchain Workbench |
| contractLedgerIdentifier    | Identificador exclusivo do contrato no razão |
| functionName                | Nome da função |
| parameters                  | [Informações sobre parâmetros](#parameter-information) |
| transaction                 | Informações sobre a transação |
| inTransactionSequenceNumber | O número de sequência da transação no bloco |
| connectionId                | Identificador exclusivo para a conexão |
| messageSchemaVersion        | Versão do esquema de mensagens |
| messageName                 | **EventMessage** |
| additionalInformation       | Informações adicionais fornecidas |

#### <a name="caller-information"></a>Informações sobre o chamador

| NOME | DESCRIÇÃO |
|------|-------------|
| type | Tipo do chamador, como um usuário ou um contrato |
| id | Identificador exclusivo do chamador no Azure Blockchain Workbench |
| ledgerIdentifier | Identificador exclusivo do chamador no razão |

#### <a name="parameter-information"></a>Informações sobre parâmetros

| NOME | DESCRIÇÃO |
|------|-------------|
| name | Nome do parâmetro |
| value | Valor de parâmetro |

#### <a name="event-message-transaction-information"></a>Informações sobre a transação da mensagem de evento

| NOME               | DESCRIÇÃO |
|--------------------|-------------|
| transactionId      | Identificador exclusivo da transação no Azure Blockchain Workbench |
| transactionHash    | O resumo da mensagem de transação na razão |
| from               | Identificador exclusivo da origem da transação no razão |
| para                 | Identificador exclusivo do destino da transação no razão |

Exemplo de uma *EventMessage ContractFunctionInvocation* do Blockchain Workbench:

``` json
{
    "eventName": "ContractFunctionInvocation",
    "caller": {
        "type": "User",
        "id": 21,
        "ledgerIdentifier": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60ffff"
    },
    "contractId": 34,
    "contractLedgerIdentifier": "0xf8559473b3c7197d59212b401f5a9f07b429ffff",
    "functionName": "Modify",
    "parameters": [
        {
            "name": "description",
            "value": "a new description"
        },
        {
            "name": "price",
            "value": "4567"
        }
    ],
    "transaction": {
        "transactionId": 234,
        "transactionHash": "0x5c1fddea83bf19d719e52a935ec8620437a0a6bdaa00ecb7c3d852cf92e1ffff",
        "from": "0xd85e7262dd96f3b8a48a8aaf3dcdda90f60dffff",
        "to": "0xf8559473b3c7197d59212b401f5a9f07b429ffff"
    },
    "inTransactionSequenceNumber": 1,
    "connectionId": 1,
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

### <a name="event-message-application-ingestion"></a>Mensagem do evento: Ingestão de aplicativo

Contém informações sobre quando um aplicativo é carregado no Workbench, como o nome e a versão do aplicativo carregado.

| NOME | DESCRIÇÃO |
|------|-------------|
| eventName | **ApplicationIngestion** |
| applicationId | Identificador exclusivo do aplicativo no Azure Blockchain Workbench |
| applicationName | Nome do aplicativo |
| applicationDisplayName | Nome de exibição do aplicativo |
| applicationVersion | Versão do aplicativo |
| applicationDefinitionLocation | URL na qual o arquivo de configuração de aplicativo está localizado |
| contractCodes | Coleção de [códigos de contrato](#contract-code-information) para o aplicativo |
| applicationRoles | Coleção de [funções de aplicativo](#application-role-information) para o aplicativo |
| applicationWorkflows | Coleção de [fluxos de trabalho de aplicativo](#application-workflow-information) para o aplicativo |
| connectionId | Identificador exclusivo para a conexão |
| messageSchemaVersion | Versão do esquema de mensagens |
| messageName | **EventMessage** |
| additionalInformation | As informações adicionais fornecidas aqui incluem os estados do fluxo de trabalho do aplicativo e informações sobre a transição. |

#### <a name="contract-code-information"></a>Informações sobre o código de contrato

| NOME | DESCRIÇÃO |
|------|-------------|
| id | Identificador exclusivo do arquivo de código de contrato no Azure Blockchain Workbench |
| ledgerId | Identificador exclusivo do razão no Azure Blockchain Workbench |
| location | URL na qual o arquivo de código de contrato está localizado |

#### <a name="application-role-information"></a>Informações sobre a função de aplicativo

| NOME | DESCRIÇÃO |
|------|-------------|
| id | Identificador exclusivo da função de aplicativo no Azure Blockchain Workbench |
| name | Nome da função de aplicativo |

#### <a name="application-workflow-information"></a>Informações sobre o fluxo de trabalho do aplicativo

| NOME | DESCRIÇÃO |
|------|-------------|
| id | Identificador exclusivo do fluxo de trabalho do aplicativo no Azure Blockchain Workbench |
| name | Nome do fluxo de trabalho do aplicativo |
| displayName | Nome de exibição do fluxo de trabalho do aplicativo |
| funções | Coleção de [funções para o fluxo de trabalho do aplicativo](#workflow-function-information)|
| estados | Coleção de [estados do fluxo de trabalho do aplicativo](#workflow-state-information) |
| propriedades | [Informações sobre as propriedades de fluxo de trabalho](#workflow-property-information) do aplicativo |

##### <a name="workflow-function-information"></a>Informações sobre a função de fluxo de trabalho

| NOME | DESCRIÇÃO |
|------|-------------|
| id | Identificador exclusivo da função de fluxo de trabalho do aplicativo no Azure Blockchain Workbench |
| name | Nome da função |
| parameters | Parâmetros da função |

##### <a name="workflow-state-information"></a>Informações do estado do fluxo de trabalho

| NOME | DESCRIÇÃO |
|------|-------------|
| name | Nome do estado |
| displayName | Nome de exibição do estado |
| estilo | Estilo do estado (êxito ou falha) |

##### <a name="workflow-property-information"></a>Informações sobre a propriedade de fluxo de trabalho

| NOME | DESCRIÇÃO |
|------|-------------|
| id | Identificador exclusivo da propriedade de fluxo de trabalho do aplicativo no Azure Blockchain Workbench |
| name | Nome da propriedade |
| type | Tipo de propriedade |

Exemplo de uma *EventMessage ApplicationIngestion* do Blockchain Workbench:

``` json
{
    "eventName": "ApplicationIngestion",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": “1.0”,
    "applicationDefinitionLocation": "http://url",
    "contractCodes": [
        {
            "id": 23,
            "ledgerId": 1,
            "location": "http://url"
        }
    ],
    "applicationRoles": [
            {
                "id": 134,
                "name": "Buyer"
            },
            {
                "id": 135,
                "name": "Seller"
            }
       ],
    "applicationWorkflows": [
        {
            "id": 89,
            "name": "AssetTransfer",
            "displayName": "Asset Transfer",
            "functions": [
                {
                    "id": 912,
                    "name": "",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                },
                {
                    "id": 913,
                    "name": "modify",
                    "parameters": [
                        {
                            "name": "description",
                            "type": {
                                "name": "string"
                             }
                        },
                        {
                            "name": "price",
                            "type": {
                                "name": "int"
                            }
                        }
                    ]
                }
            ],
            "states": [ 
                 {
                      "name": "Created",
                      "displayName": "Created",
                      "style" : "Success"
                 },
                 {
                      "name": "Terminated",
                      "displayName": "Terminated",
                      "style" : "Failure"
                 }
            ],
            "properties": [
                {
                    "id": 879,
                    "name": "Description",
                    "type": {
                                "name": "string"
                     }
                },
                {
                    "id": 880,
                    "name": "Price",
                    "type": {
                                "name": "int"
                     }
                }
            ]
        }
    ],
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation":
        {
            "states" :
            [
                {
                    "Name": "BuyerAccepted",
                    "Transitions": [
                        {
                            "DisplayName": "Accept",
                            "AllowedRoles": [ ],
                            "AllowedInstanceRoles": [ "InstanceOwner" ],
                            "Function": "Accept",
                            "NextStates": [ "SellerAccepted" ]
                        }
                    ]
                }
            ]
        }
}
```

### <a name="event-message-role-assignment"></a>Mensagem do evento: Atribuição de função

Contém informações sobre quando um usuário recebe uma função no Workbench, como quem executou a atribuição de função e o nome da função e o aplicativo correspondente.

| NOME | DESCRIÇÃO |
|------|-------------|
| eventName | **RoleAssignment** |
| applicationId | Identificador exclusivo do aplicativo no Azure Blockchain Workbench |
| applicationName | Nome do aplicativo |
| applicationDisplayName | Nome de exibição do aplicativo |
| applicationVersion | Versão do aplicativo |
| applicationRole        | Informações sobre a [função de aplicativo](#roleassignment-application-role) |
| assigner               | Informações sobre o [atribuidor](#roleassignment-assigner) |
| assignee               | Informações sobre o [destinatário](#roleassignment-assignee) |
| connectionId           | Identificador exclusivo para a conexão |
| messageSchemaVersion   | Versão do esquema de mensagens |
| messageName            | **EventMessage** |
| additionalInformation  | Informações adicionais fornecidas |

#### <a name="roleassignment-application-role"></a>Função de aplicativo da RoleAssignment

| NOME | DESCRIÇÃO |
|------|-------------|
| id | Identificador exclusivo da função de aplicativo no Azure Blockchain Workbench |
| name | Nome da função de aplicativo |

#### <a name="roleassignment-assigner"></a>Atribuidor da RoleAssignment

| NOME | DESCRIÇÃO |
|------|-------------|
| id | Identificador exclusivo do usuário no Azure Blockchain Workbench |
| type | Tipo do atribuidor |
| chainIdentifier | Identificador exclusivo do usuário no razão |

#### <a name="roleassignment-assignee"></a>Destinatário da RoleAssignment

| NOME | DESCRIÇÃO |
|------|-------------|
| id | Identificador exclusivo do usuário no Azure Blockchain Workbench |
| type | Tipo do destinatário |
| chainIdentifier | Identificador exclusivo do usuário no razão |

Exemplo de uma *EventMessage RoleAssignment* do Blockchain Workbench:

``` json
{
    "eventName": "RoleAssignment",
    "applicationId": 31,
    "applicationName": "AssetTransfer",
    "applicationDisplayName": "Asset Transfer",
    "applicationVersion": “1.0”,
    "applicationRole": {
        "id": 134,
        "name": "Buyer"
    },
    "assigner": {
        "id": 1,
        "type": null,
        "chainIdentifier": "0xeFFC7766d38aC862d79706c3C5CEEf089564ffff"
    },
    "assignee": {
        "id": 3,
        "type": null,
        "chainIdentifier": "0x9a8DDaCa9B7488683A4d62d0817E965E8f24ffff"
    },
    "connectionId": [ ],
    "messageSchemaVersion": "1.0.0",
    "messageName": "EventMessage",
    "additionalInformation": { }
}
```

## <a name="next-steps"></a>Próximas etapas

- [Padrões de integração e contratos inteligentes](integration-patterns.md)
