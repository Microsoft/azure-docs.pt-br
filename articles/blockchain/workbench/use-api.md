---
title: Usando as APIs REST do Azure Blockchain Workbench
description: Cenários de como usar a API REST de visualização do Azure Blockchain Workbench
ms.date: 03/05/2020
ms.topic: how-to
ms.reviewer: brendal
ms.openlocfilehash: 696f1f2f96034f7a044f6a39182774c02804518f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004833"
---
# <a name="using-the-azure-blockchain-workbench-preview-rest-api"></a>Usando a API REST de visualização do Azure Blockchain Workbench

A API REST de visualização do Azure Blockchain Workbench fornece aos desenvolvedores e operadores de informações uma maneira de criar integrações avançadas para aplicativos Blockchain. Este artigo destaca vários cenários de como usar a API REST do Workbench. Por exemplo, suponha que você queira criar um cliente blockchain personalizado que permita que os usuários conectados exibam e interajam com seus aplicativos blockchain atribuídos. O cliente pode usar a API do Blockchain Workbench para exibir instâncias de contrato e executar ações em contratos inteligentes.

## <a name="blockchain-workbench-api-endpoint"></a>Ponto de extremidade da API do Blockchain Workbench

As APIs do Blockchain Workbench são acessadas por meio de um ponto de extremidade para sua implantação. Para obter a URL do ponto de extremidade da API para sua implantação:

1. Entre no [portal do Azure](https://portal.azure.com).
1. No painel de navegação à esquerda, selecione **grupos de recursos**.
1. Escolha o nome do grupo de recursos que você implantou o Blockchain Workbench.
1. Selecione a coluna **TIPO** para classificar a lista alfabeticamente por tipo.
1. Há dois recursos com o tipo **Serviço de Aplicativo**. Selecione o recurso do tipo **serviço de aplicativo** *com* o sufixo "-API".
1. Na **visão geral** do serviço de aplicativo, copie o valor da **URL** , que representa a URL do ponto de extremidade da API para o Blockchain Workbench implantado.

    ![URL do ponto de extremidade da API do serviço de aplicativo](media/use-api/app-service-api.png)

## <a name="authentication"></a>Autenticação

As solicitações para a API REST do Blockchain Workbench são protegidas com Azure Active Directory (Azure AD).

Para fazer uma solicitação autenticada para as APIs REST, o código do cliente requer autenticação com credenciais válidas para que você possa chamar a API. A autenticação é coordenada entre os vários atores pelo Azure AD e fornece ao cliente um [token de acesso](../../active-directory/develop/developer-glossary.md#access-token) como prova de autenticação. O token é então enviado no cabeçalho de autorização HTTP de solicitações da API REST. Para saber mais sobre a autenticação do Azure AD, consulte [Azure Active Directory para desenvolvedores](../../active-directory/develop/index.yml).

Consulte [exemplos de API REST](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples) para obter exemplos de como autenticar.

## <a name="using-postman"></a>Uso do Postman

Se você quiser testar ou experimentar as APIs do Workbench, poderá usar o [postmaster](https://www.postman.com) para fazer chamadas à API para sua implantação. [Baixe uma coleção do postmaster de exemplo das solicitações da API do Workbench](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/rest-api-samples/postman) do github. Consulte o arquivo LEIAme para obter detalhes sobre como autenticar e usar as solicitações de API de exemplo.

## <a name="create-an-application"></a>Criar um aplicativo

Você usa duas chamadas à API para criar um aplicativo do Blockchain Workbench. Esse método só pode ser executado por usuários que são administradores do Workbench.

Use a [API post de aplicativos](/rest/api/azure-blockchain-workbench/applications/applicationspost) para carregar o arquivo JSON do aplicativo e obter uma ID do aplicativo.

### <a name="applications-post-request"></a>Solicitação de POSTAgem de aplicativos

Use o parâmetro **appFile** para enviar o arquivo de configuração como parte do corpo da solicitação.

``` http
POST /api/v1/applications
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="appFile"; filename="/C:/smart-contract-samples/HelloWorld.json"
Content-Type: application/json
```

### <a name="applications-post-response"></a>Resposta de aplicativos POST

A ID do aplicativo criada é retornada na resposta. Você precisa da ID do aplicativo para associar o arquivo de configuração ao arquivo de código ao chamar a próxima API.

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
1
```

### <a name="contract-code-post-request"></a>Solicitação de POSTAgem de código de contrato

Use a [API post do código de contrato de aplicativos](/rest/api/azure-blockchain-workbench/applications/contractcodepost) passando a ID do aplicativo para carregar o arquivo de código de solidez do aplicativo. A carga pode ser um único arquivo de solidez ou um arquivo compactado contendo arquivos de solidez.

Substitua os seguintes valores:

| Parâmetro | Valor |
|-----------|-------|
| ApplicationId | Valor de retorno da API POST de aplicativos. |
| {ledgerid} | Índice do razão. O valor geralmente é 1. Você também pode verificar a [tabela de razão](data-sql-management-studio.md) para o valor. |

``` http
POST /api/v1/applications/{applicationId}/contractCode?ledgerId={ledgerId}
Content-Type: multipart/form-data;
Authorization : Bearer {access token}
Content-Disposition: form-data; name="contractFile"; filename="/C:/smart-contract-samples/HelloWorld.sol"
```

### <a name="contract-code-post-response"></a>Resposta da POSTAgem do código de contrato

Se for bem-sucedida, a resposta incluirá a ID de código de contrato criada da [tabela ContractCode](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
Content-Type: "application/json"
2
```

## <a name="assign-roles-to-users"></a>Atribuir funções aos usuários

Use as [atribuições de função de aplicativos pós API](/rest/api/azure-blockchain-workbench/applications/roleassignmentspost) passando a ID do aplicativo, a ID de usuário e a ID da função de aplicativo para criar um mapeamento de usuário para função no aplicativo blockchain especificado. Esse método só pode ser executado por usuários que são administradores do Workbench.

### <a name="role-assignments-post-request"></a>Solicitação POST de atribuições de função

Substitua os seguintes valores:

| Parâmetro | Valor |
|-----------|-------|
| ApplicationId | Valor de retorno da API POST de aplicativos. |
| ID | Valor da ID de usuário da [tabela de usuário](data-sql-management-studio.md). |
| {applicationRoleId} | Valor da ID da função do aplicativo associado à ID do aplicativo da [tabela ApplicationRole](data-sql-management-studio.md). |

``` http
POST /api/v1/applications/{applicationId}/roleAssignments
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "userId": {userId},
  "applicationRoleId": {applicationRoleId}
}
```

### <a name="role-assignments-post-response"></a>Resposta de POST de atribuições de função

Se for bem-sucedida, a resposta incluirá a ID de atribuição de função criada da [tabela RoleAssignment](data-sql-management-studio.md).

``` http
HTTP/1.1 200
1
```

## <a name="list-applications"></a>Listar aplicativos

Use a [API Get de aplicativos](/rest/api/azure-blockchain-workbench/applications/applicationsget) para recuperar todos os aplicativos do Blockchain Workbench para o usuário. Neste exemplo, o usuário conectado tem acesso a dois aplicativos:

- [Transferência de ativos](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer/readme.md)
- [Transporte refrigerado](https://github.com/Azure-Samples/blockchain/blob/master/blockchain-workbench/application-and-smart-contract-samples/refrigerated-transportation/readme.md)

### <a name="applications-get-request"></a>Solicitações de obtenção de aplicativos

``` http
GET /api/v1/applications
Authorization : Bearer {access token}
```

### <a name="applications-get-response"></a>Aplicativos OBTÊm resposta

A resposta lista todos os aplicativos blockchain aos quais um usuário tem acesso no Blockchain Workbench. Os administradores do Blockchain Workbench obtêm cada aplicativo blockchain. Os administradores que não são de Workbench obtêm todos os aplicativos blockchain para os quais têm pelo menos uma função de aplicativo associada ou uma função de instância de contrato inteligente associada.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications?skip=2",
    "applications": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
            "displayName": "Asset Transfer",
            "createdByUserId": 1,
            "createdDtTm": "2018-04-28T05:59:14.4733333",
            "enabled": true,
            "applicationRoles": null
        },
        {
            "id": 2,
            "name": "RefrigeratedTransportation",
            "description": "Application to track end-to-end transportation of perishable goods.",
            "displayName": "Refrigerated Transportation",
            "createdByUserId": 7,
            "createdDtTm": "2018-04-28T18:25:38.71",
            "enabled": true,
            "applicationRoles": null
        }
    ]
}
```

## <a name="list-workflows-for-an-application"></a>Listar fluxos de trabalho para um aplicativo

Use [os fluxos de trabalho de aplicativos obter API](/rest/api/azure-blockchain-workbench/applications/workflowsget) para listar todos os fluxos de trabalho de um aplicativo blockchain especificado ao qual um usuário tem acesso no blockchain Workbench. Cada aplicativo blockchain tem um ou mais fluxos de trabalho, e cada fluxo de trabalho tem zero ou instâncias de contrato inteligente. Para um aplicativo cliente blockchain que possui apenas um fluxo de trabalho, recomendamos ignorar o fluxo de experiência do usuário que permite aos usuários selecionar o fluxo de trabalho apropriado.

### <a name="application-workflows-request"></a>Solicitação de fluxos de trabalho de aplicativo

``` http
GET /api/v1/applications/{applicationId}/workflows
Authorization: Bearer {access token}
```

### <a name="application-workflows-response"></a>Resposta de fluxos de trabalho de aplicativo

Os administradores do Blockchain Workbench obtêm todos os fluxos de trabalho blockchain. Os administradores que não são do Workbench obtêm todos os fluxos de trabalho para os quais têm pelo menos uma função de aplicativo associada ou estão associados a uma função de instância de contrato inteligente.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/applications/1/workflows?skip=1",
    "workflows": [
        {
            "id": 1,
            "name": "AssetTransfer",
            "description": "Handles the business logic for the asset transfer scenario",
            "displayName": "Asset Transfer",
            "applicationId": 1,
            "constructorId": 1,
            "startStateId": 1
        }
    ]
}
```

## <a name="create-a-contract-instance"></a>Criar uma instância de contrato

Use o [Contracts v2 pós API](/rest/api/azure-blockchain-workbench/contractsv2/contractpost) para criar uma nova instância de contrato inteligente para um fluxo de trabalho. Os usuários só poderão criar uma nova instância de contrato inteligente se o usuário estiver associado a uma função de aplicativo, que pode iniciar uma instância de contrato inteligente para o fluxo de trabalho.

> [!NOTE]
> Neste exemplo, a versão 2 da API é usada. As APIs de contrato da versão 2 fornecem mais granularidade para os campos ProvisioningStatus associados.

### <a name="contracts-post-request"></a>Solicitação de POSTAgem de contratos

Substitua os seguintes valores:

| Parâmetro | Valor |
|-----------|-------|
| WorkflowId | O valor da ID do fluxo de trabalho é o Constructorid do contrato da [tabela de fluxo de trabalho](data-sql-management-studio.md). |
| {contractCodeId} | Valor da ID do código do contrato da [tabela ContractCode](data-sql-management-studio.md). Correlacione a ID do aplicativo e a ID do razão para a instância de contrato que você deseja criar. |
| ConnectionID | Valor da ID de conexão da [tabela de conexão](data-sql-management-studio.md). |

Para o corpo da solicitação, defina valores usando as seguintes informações:

| Parâmetro | Valor |
|-----------|-------|
| workflowFunctionID | ID da [tabela WorkflowFunction](data-sql-management-studio.md). |
| workflowActionParameters | Pares de nome valor dos parâmetros passados para o construtor. Para cada parâmetro, use o valor workflowFunctionParameterID da tabela [WorkflowFunctionParameter](data-sql-management-studio.md) . |

``` http
POST /api/v2/contracts?workflowId={workflowId}&contractCodeId={contractCodeId}&connectionId={connectionId}
Content-Type: application/json;
Authorization : Bearer {access token}

{
  "workflowFunctionID": 2,
  "workflowActionParameters": [
    {
      "name": "message",
      "value": "Hello, world!",
      "workflowFunctionParameterId": 3
    }
  ]
}
```

### <a name="contracts-post-response"></a>Resposta de contratos POST

Se for bem-sucedida, a API de atribuições de função retornará o ContractActionID da [tabela ContractActionParameter](data-sql-management-studio.md).

``` http
HTTP/1.1 200 OK
4
```

## <a name="list-smart-contract-instances-for-a-workflow"></a>Listar instâncias de contrato inteligentes para um fluxo de trabalho

Use [Contracts Get API](/rest/api/azure-blockchain-workbench/contractsv2/contractsget) para mostrar todas as instâncias de contrato inteligente para um fluxo de trabalho. Ou você pode permitir que os usuários mergulhem em qualquer uma das instâncias do contrato inteligente mostradas.

### <a name="contracts-request"></a>Solicitação de contratos

Neste exemplo, considere que um usuário gostaria de interagir com uma das instâncias de contrato inteligente para agir.

``` http
GET api/v1/contracts?workflowId={workflowId}
Authorization: Bearer {access token}
```

### <a name="contracts-response"></a>Resposta de contratos

A resposta lista todas as instâncias de contrato inteligente do fluxo de trabalho especificado. Os administradores do Workbench obtêm todas as instâncias do contrato inteligente. Os administradores que não são do Workbench obtêm cada instância de contrato inteligente para a qual eles têm pelo menos uma função de aplicativo associada ou estão associados a uma função de instância de contrato inteligente.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts?skip=3&workflowId=1",
    "contracts": [
        {
            "id": 1,
            "provisioningStatus": 2,
            "connectionID": 1,
            "ledgerIdentifier": "0xbcb6127be062acd37818af290c0e43479a153a1c",
            "deployedByUserId": 1,
            "workflowId": 1,
            "contractCodeId": 1,
            "contractProperties": [
                {
                    "workflowPropertyId": 1,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 2,
                    "value": "My first car"
                },
                {
                    "workflowPropertyId": 3,
                    "value": "54321"
                },
                {
                    "workflowPropertyId": 4,
                    "value": "0"
                },
                {
                    "workflowPropertyId": 5,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 6,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 7,
                    "value": "0x0000000000000000000000000000000000000000"
                },
                {
                    "workflowPropertyId": 8,
                    "value": "0xd882530eb3d6395e697508287900c7679dbe02d7"
                }
            ],
            "transactions": [
                {
                    "id": 1,
                    "connectionId": 1,
                    "transactionHash": "0xf3abb829884dc396e03ae9e115a770b230fcf41bb03d39457201449e077080f4",
                    "blockID": 241,
                    "from": "0xd882530eb3d6395e697508287900c7679dbe02d7",
                    "to": null,
                    "value": 0,
                    "isAppBuilderTx": true
                }
            ],
            "contractActions": [
                {
                    "id": 1,
                    "userId": 1,
                    "provisioningStatus": 2,
                    "timestamp": "2018-04-29T23:41:14.9333333",
                    "parameters": [
                        {
                            "name": "Description",
                            "value": "My first car"
                        },
                        {
                            "name": "Price",
                            "value": "54321"
                        }
                    ],
                    "workflowFunctionId": 1,
                    "transactionId": 1,
                    "workflowStateId": 1
                }
            ]
        }
    ]
}
```

## <a name="list-available-actions-for-a-contract"></a>Listar ações disponíveis para um contrato

Use a [API Get da ação de contrato](/rest/api/azure-blockchain-workbench/contractsv2/contractactionget) para mostrar as ações de usuário disponíveis de acordo com o estado do contrato. 

### <a name="contract-action-request"></a>Solicitação de ação de contrato

Neste exemplo, o usuário está examinando todas as ações disponíveis para um novo contrato inteligente que eles criaram.

``` http
GET /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
```

### <a name="contract-action-response"></a>Resposta de ação de contrato

Resposta lista todas as ações que um usuário pode executar levando em consideração o estado atual da instância do contrato inteligente especificado.

* Modificar: permite que o usuário modifique a descrição e o preço de um ativo.
* Encerrar: permite que o usuário finalize o contrato do ativo.

Os usuários obterão todas as ações aplicáveis se o usuário tiver uma função de aplicativo associada ou estiver associado a uma função de instância de contrato inteligente para o estado atual da instância de contrato inteligente especificada.

``` http
HTTP/1.1 200 OK
Content-type: application/json
{
    "nextLink": "/api/v1/contracts/1/actions?skip=2",
    "workflowFunctions": [
        {
            "id": 2,
            "name": "Modify",
            "description": "Modify the description/price attributes of this asset transfer instance",
            "displayName": "Modify",
            "parameters": [
                {
                    "id": 1,
                    "name": "description",
                    "description": "The new description of the asset",
                    "displayName": "Description",
                    "type": {
                        "id": 2,
                        "name": "string",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                },
                {
                    "id": 2,
                    "name": "price",
                    "description": "The new price of the asset",
                    "displayName": "Price",
                    "type": {
                        "id": 3,
                        "name": "money",
                        "elementType": null,
                        "elementTypeId": 0
                    }
                }
            ],
            "workflowId": 1
        },
        {
            "id": 3,
            "name": "Terminate",
            "description": "Used to cancel this particular instance of asset transfer",
            "displayName": "Terminate",
            "parameters": [],
            "workflowId": 1
        }
    ]
}
```

## <a name="execute-an-action-for-a-contract"></a>Executar uma ação para um contrato

Use a [API pós-ação de contrato](/rest/api/azure-blockchain-workbench/contractsv2/contractactionpost) para executar uma ação para a instância de contrato inteligente especificada.

### <a name="contract-action-post-request"></a>Solicitação POST de ação de contrato

Nesse caso, considere o cenário em que um usuário gostaria de modificar a descrição e o preço de um ativo.

``` http
POST /api/v1/contracts/{contractId}/actions
Authorization: Bearer {access token}
actionInformation: {
    "workflowFunctionId": 2,
    "workflowActionParameters": [
        {
            "name": "description",
            "value": "My updated car"
        },
        {
            "name": "price",
            "value": "54321"
        }
    ]
}
```

Os usuários podem executar a ação somente com base no estado atual da instância de contrato inteligente especificada e na função de aplicativo associada do usuário ou na função de instância de contrato inteligente.

### <a name="contract-action-post-response"></a>Resposta de ação do contrato POST

Se a publicação for bem-sucedida, uma resposta HTTP 200 OK é retornada sem corpo de resposta.

``` http
HTTP/1.1 200 OK
Content-type: application/json
```

## <a name="next-steps"></a>Próximas etapas

Para obter informações de referência sobre as APIs do Blockchain Workbench, consulte a [referência da API REST do Azure Blockchain Workbench](/rest/api/azure-blockchain-workbench).
