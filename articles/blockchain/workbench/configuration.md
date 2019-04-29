---
title: Referência de configuração do Azure Blockchain Workbench
description: Visão geral de configuração do aplicativo Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 04/15/2019
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: brendal
manager: femila
ms.openlocfilehash: 4d29d8e86a30f105c4aa50ec9615f8165fa238d3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60578973"
---
# <a name="azure-blockchain-workbench-configuration-reference"></a>Referência de configuração do Azure Blockchain Workbench

Os aplicativos do Azure Blockchain Workbench são fluxos de trabalho de várias partes definidos por metadados de configuração e código de contrato inteligente. Metadados de configuração definem os fluxos de trabalho de alto nível e modelo de interação do aplicativo de blockchain. Contratos inteligentes definem a lógica de negócios do aplicativo blockchain. O Workbench usa a configuração e o código de contrato inteligente para gerar as experiências de usuário do aplicativo blockchain.

Os metadados de configuração especificam as seguintes informações para cada aplicativo blockchain:

* Nome e descrição do aplicativo blockchain
* Funções exclusivas para usuários que podem atuar ou participar do aplicativo blockchain
* Um ou mais fluxos de trabalho. Cada fluxo de trabalho funciona como uma máquina de estado para controlar o fluxo da lógica de negócios. Os fluxos de trabalho podem ser independentes ou interagir entre si.

Cada fluxo de trabalho definido especifica o seguinte:

* O nome e a descrição do fluxo de trabalho
* Estados do fluxo de trabalho.  Cada estado é uma etapa no fluxo de controle da lógica de negócios. 
* Ações para fazer a transição para o próximo estado
* Funções de usuário permitidas para iniciar cada ação
* Contratos inteligentes que representam a lógica de negócios em arquivos de código

## <a name="application"></a>Aplicativo

Um aplicativo blockchain contém regras de configuração para metadados, fluxos de trabalho e usuário que podem funcionar ou participar dentro do aplicativo.

| Campo | DESCRIÇÃO | Obrigatório |
|-------|-------------|:--------:|
| ApplicationName | Nome de aplicativo exclusivo. O contrato inteligente correspondente deve usar o mesmo **ApplicationName** para a classe de contrato aplicável.  | Sim |
| DisplayName | O nome de exibição amigável do aplicativo. | Sim |
| DESCRIÇÃO | Descrição do aplicativo. | Não  |
| ApplicationRoles | Coleção de [ApplicationRoles](#application-roles). Funções de usuário que podem funcionar ou participar no aplicativo.  | Sim |
| Fluxos de trabalho | Coleção de [Fluxos de trabalho](#workflows). Cada fluxo de trabalho funciona como uma máquina de estado para controlar o fluxo da lógica de negócios. | Sim |

Para obter um exemplo, consulte [exemplo de arquivo de configuração](#configuration-file-example).

## <a name="workflows"></a>Fluxos de trabalho

Uma lógica de negócios de um aplicativo pode ser modelada como uma máquina de estado em que executar uma ação faz com que o fluxo da lógica de negócios mova de um estado para outro. Um fluxo de trabalho é uma coleção destes estados e ações. Cada fluxo de trabalho consiste em um ou mais contratos inteligentes que representam a lógica de negócios nos arquivos de código. Um contrato executável é uma instância de fluxo de trabalho.

| Campo | DESCRIÇÃO | Obrigatório | Comprimento máximo |
|-------|-------------|:--------:|-----------:|
| NOME | Nome do fluxo de trabalho exclusivo. O contrato inteligente correspondente deve usar o mesmo **Nome** para a classe de contrato aplicável. | Sim | 50 |
| DisplayName | Nome de exibição amigável do fluxo de trabalho. | Sim | 255 |
| DESCRIÇÃO | Descrição do fluxo de trabalho. | Não  | 255 |
| Iniciadores | Coleção de [ApplicationRoles](#application-roles). Funções que são atribuídas a usuários que estão autorizados a criar contratos no fluxo de trabalho. | Sim | |
| StartState | Nome do estado inicial do fluxo de trabalho. | Sim | |
| propriedades | Coleção de [identificadores](#identifiers). Representa os dados que podem ser lidos visualizados em cadeia em uma ferramenta de experiência do usuário. | Sim | |
| Construtor | Define os parâmetros de entrada para criar uma instância de fluxo de trabalho. | Sim | |
| Funções | Uma coleção de [funções](#functions) que podem ser executadas no fluxo de trabalho. | Sim | |
| Estados | Uma coleção de [estados](#states) de fluxo de trabalho. | Sim | |

Para obter um exemplo, consulte [exemplo de arquivo de configuração](#configuration-file-example).

## <a name="type"></a>Type

Tipos de dados com suporte.

| Type | DESCRIÇÃO |
|-------|-------------|
| endereço  | Tipo de endereço de blockchain, como *contratos* ou *usuários*. |
| matriz    | Matriz de nível único de tipo inteiro, bool, money ou time. As matrizes podem ser estáticas ou dinâmicas. Use **ElementType** para especificar o tipo de dados dos elementos dentro da matriz. Confira o [exemplo de configuração](#example-configuration-of-type-array). |
| bool     | Tipo de dados Boolean. |
| contrato | Endereço do contrato de tipo. |
| enum     | Conjunto enumerado de valores nomeados. Ao usar o tipo de enumeração, você também especifica uma lista de EnumValues. Cada valor é limitado a 255 caracteres. Os caracteres de valor válidos incluem letras maiúsculas e minúsculas (A-Z, a-z) e números (0-9). Confira o [exemplo de configuração e uso em Solidity](#example-configuration-of-type-enum). |
| int      | Tipo de dados Integer. |
| money    | Tipo de dados money. |
| state    | Estado do fluxo de trabalho. |
| string  | Tipos de dados String. No máximo 4000 caracteres. Confira o [exemplo de configuração](#example-configuration-of-type-string). |
| usuário     | Endereços do usuário de tipo. |
| time     | Tipo de dados time. |
|`[ Application Role Name ]`| Qualquer nome especificado na função de aplicativo. Limita os usuários desse tipo de função. |

### <a name="example-configuration-of-type-array"></a>Exemplo de configuração da matriz de tipos

```json
{
  "Name": "Quotes",
  "Description": "Market quotes",
  "DisplayName": "Quotes",
  "Type": {
    "Name": "array",
    "ElementType": {
        "Name": "int"
    }
  }
}
```

#### <a name="using-a-property-of-type-array"></a>Usar uma propriedade da matriz de tipos

Se você definir uma propriedade como a matriz de tipos na configuração, será preciso incluir uma função get explícita para retornar a propriedade pública do tipo de matriz em Solidity. Por exemplo: 

```
function GetQuotes() public constant returns (int[]) {
     return Quotes;
}
```

### <a name="example-configuration-of-type-string"></a>Exemplo de configuração do tipo de cadeia de caracteres

``` json
{
  "Name": "description",
  "Description": "Descriptive text",
  "DisplayName": "Description",
  "Type": {
    "Name": "string"
  }
}
```

### <a name="example-configuration-of-type-enum"></a>Exemplo de configuração da enumeração de tipo

``` json
{
  "Name": "PropertyType",
  "DisplayName": "Property Type",
  "Description": "The type of the property",
  "Type": {
    "Name": "enum",
    "EnumValues": ["House", "Townhouse", "Condo", "Land"]
  }
}
```

#### <a name="using-enumeration-type-in-solidity"></a>Usando o tipo de enumeração em Solidity

Depois que uma enumeração é definida na configuração, é possível usar tipos de enumeração em Solidity. Por exemplo, é possível uma enumeração chamada PropertyTypeEnum.

```
enum PropertyTypeEnum {House, Townhouse, Condo, Land} PropertyTypeEnum public PropertyType; 
```

É preciso haver uma correspondência entre a lista de cadeia de caracteres, a configuração e o contrato inteligente para serem declarações válidas e consistentes no Blockchain Workbench.

Exemplo de atribuição:

```
PropertyType = PropertyTypeEnum.Townhouse;
```

Exemplo de parâmetro de função: 

``` 
function AssetTransfer(string description, uint256 price, PropertyTypeEnum propertyType) public
{
    InstanceOwner = msg.sender;
    AskingPrice = price;
    Description = description;
    PropertyType = propertyType;
    State = StateType.Active;
    ContractCreated();
}

```

## <a name="constructor"></a>Construtor

Define os parâmetros de entrada para uma instância de fluxo de trabalho.

| Campo | DESCRIÇÃO | Obrigatório |
|-------|-------------|:--------:|
| parâmetros | Coleção de [identificadores](#identifiers) necessários para iniciar um contrato inteligente. | Sim |

### <a name="constructor-example"></a>Exemplo de construtor

``` json
{
  "Parameters": [
    {
      "Name": "description",
      "Description": "The description of this asset",
      "DisplayName": "Description",
      "Type": {
        "Name": "string"
      }
    },
    {
      "Name": "price",
      "Description": "The price of this asset",
      "DisplayName": "Price",
      "Type": {
        "Name": "money"
      }
    }
  ]
}
```

## <a name="functions"></a>Funções

Define as funções que podem ser executadas no fluxo de trabalho.

| Campo | DESCRIÇÃO | Obrigatório | Comprimento máximo |
|-------|-------------|:--------:|-----------:|
| NOME | O nome exclusivo da função. O contrato inteligente correspondente deve usar o mesmo **Nome** para a função aplicável. | Sim | 50 |
| DisplayName | Nome de exibição amigável da função. | Sim | 255 |
| DESCRIÇÃO | Descrição da função | Não  | 255 |
| parâmetros | Coleção de [identificadores](#identifiers) correspondente aos parâmetros da função. | Sim | |

### <a name="functions-example"></a>Exemplo de funções

``` json
"Functions": [
  {
    "Name": "Modify",
    "DisplayName": "Modify",
    "Description": "Modify the description/price attributes of this asset transfer instance",
    "Parameters": [
      {
        "Name": "description",
        "Description": "The new description of the asset",
        "DisplayName": "Description",
        "Type": {
          "Name": "string"
        }
      },
      {
        "Name": "price",
        "Description": "The new price of the asset",
        "DisplayName": "Price",
        "Type": {
          "Name": "money"
        }
      }
    ]
  },
  {
    "Name": "Terminate",
    "DisplayName": "Terminate",
    "Description": "Used to cancel this particular instance of asset transfer",
    "Parameters": []
  }
]

```

## <a name="states"></a>Estados

Uma coleção de estados exclusivos dentro de um fluxo de trabalho. Cada estado captura uma etapa no fluxo de controle da lógica de negócios. 

| Campo | DESCRIÇÃO | Obrigatório | Comprimento máximo |
|-------|-------------|:--------:|-----------:|
| NOME | Nome único do estado. O contrato inteligente correspondente deve usar o mesmo **Nome** para o estado aplicável. | Sim | 50 |
| DisplayName | Nome de exibição amigável do estado. | Sim | 255 |
| DESCRIÇÃO | Descrição do estado. | Não  | 255 |
| PercentComplete | Um valor inteiro exibido na interface do usuário Blockchain Workbench para mostrar o progresso dentro do fluxo de controle de lógica de negócios. | Sim | |
| Estilo | Dica visual que indica se o estado representa um estado de êxito ou falha. Há dois valores válidos: `Success` ou `Failure`. | Sim | |
| Transições | Coleção de [transições](#transitions) disponíveis do estado atual para o próximo conjunto de estados. | Não  | |

### <a name="states-example"></a>Exemplo de estados

``` json
"States": [
    {
      "Name": "Active",
      "DisplayName": "Active",
      "Description": "The initial state of the asset transfer workflow",
      "PercentComplete": 20,
      "Style": "Success",
      "Transitions": [
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Cancels this instance of asset transfer",
          "Function": "Terminate",
          "NextStates": [ "Terminated" ],
          "DisplayName": "Terminate Offer"
        },
        {
          "AllowedRoles": [ "Buyer" ],
          "AllowedInstanceRoles": [],
          "Description": "Make an offer for this asset",
          "Function": "MakeOffer",
          "NextStates": [ "OfferPlaced" ],
          "DisplayName": "Make Offer"
        },
        {
          "AllowedRoles": [],
          "AllowedInstanceRoles": [ "InstanceOwner" ],
          "Description": "Modify attributes of this asset transfer instance",
          "Function": "Modify",
          "NextStates": [ "Active" ],
          "DisplayName": "Modify"
        }
      ]
    },
    {
      "Name": "Accepted",
      "DisplayName": "Accepted",
      "Description": "Asset transfer process is complete",
      "PercentComplete": 100,
      "Style": "Success",
      "Transitions": []
    },
    {
      "Name": "Terminated",
      "DisplayName": "Terminated",
      "Description": "Asset transfer has been canceled",
      "PercentComplete": 100,
      "Style": "Failure",
      "Transitions": []
    }
  ]
```

## <a name="transitions"></a>Transições

Ações disponíveis para o próximo estado. Uma ou mais funções de usuário podem executar uma ação em cada estado, onde uma ação transicionar um estado para outro estado no fluxo de trabalho. 

| Campo | DESCRIÇÃO | Obrigatório |
|-------|-------------|:--------:|
| AllowedRoles | Lista de funções de aplicativos permitidos para iniciar a transição. Todos os usuários da função especificada podem ser capazes de executar a ação. | Não  |
| AllowedInstanceRoles | Lista de funções de usuário participante ou especificado no contrato inteligente permitido para iniciar a transição. As funções de instância são definidas em **Propriedades** nos fluxos de trabalho. AllowedInstanceRoles representa um usuário participando de uma instância de um contrato inteligente. AllowedInstanceRoles oferece a capacidade de restringir ao tomar uma medida para uma função de usuário em uma instância do contrato.  Por exemplo, você pode desejar somente permitir que o usuário que criou o contrato (InstanceOwner) possa encerrar em vez de todos os usuários no tipo de função (proprietário), se você especificou a função na AllowedRoles. | Não  |
| DisplayName | Nome de exibição amigável da transição. | Sim |
| DESCRIÇÃO | Descrição da transição. | Não  |
| Função | Nome da função para iniciar a transição. | Sim |
| NextStates | Uma coleção de potenciais próximos estados após uma transição com êxito. | Sim |

### <a name="transitions-example"></a>Exemplo de transições

``` json
"Transitions": [
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Cancels this instance of asset transfer",
    "Function": "Terminate",
    "NextStates": [ "Terminated" ],
    "DisplayName": "Terminate Offer"
  },
  {
    "AllowedRoles": [ "Buyer" ],
    "AllowedInstanceRoles": [],
    "Description": "Make an offer for this asset",
    "Function": "MakeOffer",
    "NextStates": [ "OfferPlaced" ],
    "DisplayName": "Make Offer"
  },
  {
    "AllowedRoles": [],
    "AllowedInstanceRoles": [ "InstanceOwner" ],
    "Description": "Modify attributes of this asset transfer instance",
    "Function": "Modify",
    "NextStates": [ "Active" ],
    "DisplayName": "Modify"
  }
]

```

## <a name="application-roles"></a>Funções de aplicativo

Funções de aplicativo definem um conjunto de funções que podem ser atribuídos a usuários que desejam agir ou participar do aplicativo. Funções de aplicativo podem ser usadas para restringir as ações e participação dentro do aplicativo blockchain e fluxos de trabalho correspondente. 

| Campo | DESCRIÇÃO | Obrigatório | Comprimento máximo |
|-------|-------------|:--------:|-----------:|
| NOME | O nome exclusivo da função do aplicativo. O contrato inteligente correspondente deve usar o mesmo **Nome** para a função aplicável. Nomes de tipo de base são reservados. Você não pode nomear uma função de aplicativo com o mesmo nome como [Tipo](#type)| Sim | 50 |
| DESCRIÇÃO | Descrição da função do aplicativo. | Não  | 255 |

### <a name="application-roles-example"></a>Exemplo de funções de aplicativo

``` json
"ApplicationRoles": [
  {
    "Name": "Appraiser",
    "Description": "User that signs off on the asset price"
  },
  {
    "Name": "Buyer",
    "Description": "User that places an offer on an asset"
  }
]
```
## <a name="identifiers"></a>Identificadores

Identificadores representam uma coleção de informações usadas para descrever as propriedades de fluxo de trabalho, construtor e parâmetros de função. 

| Campo | DESCRIÇÃO | Obrigatório | Comprimento máximo |
|-------|-------------|:--------:|-----------:|
| NOME | O nome exclusivo da propriedade ou parâmetro. O contrato inteligente correspondente deve usar o mesmo **Nome** para a propriedade ou parâmetro aplicável. | Sim | 50 |
| DisplayName | Nome para exibição amigável para a propriedade ou parâmetro. | Sim | 255 |
| DESCRIÇÃO | Descrição da propriedade ou do parâmetro. | Não  | 255 |

### <a name="identifiers-example"></a>Exemplo de identificadores

``` json
"Properties": [
  {
    "Name": "State",
    "DisplayName": "State",
    "Description": "Holds the state of the contract",
    "Type": {
      "Name": "state"
    }
  },
  {
    "Name": "Description",
    "DisplayName": "Description",
    "Description": "Describes the asset being sold",
    "Type": {
      "Name": "string"
    }
  }
]
```

## <a name="configuration-file-example"></a>Exemplo de arquivo de configuração

Transferência de ativo é um cenário de contrato inteligente para comprar e vender ativos de alto valor, que requerem um inspetor e avaliador. Os vendedores podem listar seus ativos instanciando um contrato inteligente de transferência do ativo. Compradores podem fazer ofertas ao executar uma ação no contrato de smart e outras partes podem executar ações para inspecionar ou avaliar o ativo. Depois que o ativo é marcado como inspecionado e avaliado, o comprador e vendedor confirmarão a venda novamente antes de o contrato ser definido para ser concluído. Em cada ponto no processo, todos os participantes têm visibilidade para o estado do contrato conforme é atualizado. 

Para mais informações, inclusive os arquivos de código, consulte [exemplo de transferência de ativo para oWorbench do Azure Blockchain](https://github.com/Azure-Samples/blockchain/tree/master/blockchain-workbench/application-and-smart-contract-samples/asset-transfer)

O arquivo de configuração a seguir para a amostra de transferência do ativo:

``` json
{
  "ApplicationName": "AssetTransfer",
  "DisplayName": "Asset Transfer",
  "Description": "Allows transfer of assets between a buyer and a seller, with appraisal/inspection functionality",
  "ApplicationRoles": [
    {
      "Name": "Appraiser",
      "Description": "User that signs off on the asset price"
    },
    {
      "Name": "Buyer",
      "Description": "User that places an offer on an asset"
    },
    {
      "Name": "Inspector",
      "Description": "User that inspects the asset and signs off on inspection"
    },
    {
      "Name": "Owner",
      "Description": "User that signs off on the asset price"
    }
  ],
  "Workflows": [
    {
      "Name": "AssetTransfer",
      "DisplayName": "Asset Transfer",
      "Description": "Handles the business logic for the asset transfer scenario",
      "Initiators": [ "Owner" ],
      "StartState":  "Active",
      "Properties": [
        {
          "Name": "State",
          "DisplayName": "State",
          "Description": "Holds the state of the contract",
          "Type": {
            "Name": "state"
          }
        },
        {
          "Name": "Description",
          "DisplayName": "Description",
          "Description": "Describes the asset being sold",
          "Type": {
            "Name": "string"
          }
        },
        {
          "Name": "AskingPrice",
          "DisplayName": "Asking Price",
          "Description": "The asking price for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "OfferPrice",
          "DisplayName": "Offer Price",
          "Description": "The price being offered for the asset",
          "Type": {
            "Name": "money"
          }
        },
        {
          "Name": "InstanceAppraiser",
          "DisplayName": "Instance Appraiser",
          "Description": "The user that appraises the asset",
          "Type": {
            "Name": "Appraiser"
          }
        },
        {
          "Name": "InstanceBuyer",
          "DisplayName": "Instance Buyer",
          "Description": "The user that places an offer for this asset",
          "Type": {
            "Name": "Buyer"
          }
        },
        {
          "Name": "InstanceInspector",
          "DisplayName": "Instance Inspector",
          "Description": "The user that inspects this asset",
          "Type": {
            "Name": "Inspector"
          }
        },
        {
          "Name": "InstanceOwner",
          "DisplayName": "Instance Owner",
          "Description": "The seller of this particular asset",
          "Type": {
            "Name": "Owner"
          }
        }
      ],
      "Constructor": {
        "Parameters": [
          {
            "Name": "description",
            "Description": "The description of this asset",
            "DisplayName": "Description",
            "Type": {
              "Name": "string"
            }
          },
          {
            "Name": "price",
            "Description": "The price of this asset",
            "DisplayName": "Price",
            "Type": {
              "Name": "money"
            }
          }
        ]
      },
      "Functions": [
        {
          "Name": "Modify",
          "DisplayName": "Modify",
          "Description": "Modify the description/price attributes of this asset transfer instance",
          "Parameters": [
            {
              "Name": "description",
              "Description": "The new description of the asset",
              "DisplayName": "Description",
              "Type": {
                "Name": "string"
              }
            },
            {
              "Name": "price",
              "Description": "The new price of the asset",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Terminate",
          "DisplayName": "Terminate",
          "Description": "Used to cancel this particular instance of asset transfer",
          "Parameters": []
        },
        {
          "Name": "MakeOffer",
          "DisplayName": "Make Offer",
          "Description": "Place an offer for this asset",
          "Parameters": [
            {
              "Name": "inspector",
              "Description": "Specify a user to inspect this asset",
              "DisplayName": "Inspector",
              "Type": {
                "Name": "Inspector"
              }
            },
            {
              "Name": "appraiser",
              "Description": "Specify a user to appraise this asset",
              "DisplayName": "Appraiser",
              "Type": {
                "Name": "Appraiser"
              }
            },
            {
              "Name": "offerPrice",
              "Description": "Specify your offer price for this asset",
              "DisplayName": "Offer Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Reject",
          "DisplayName": "Reject",
          "Description": "Reject the user's offer",
          "Parameters": []
        },
        {
          "Name": "AcceptOffer",
          "DisplayName": "Accept Offer",
          "Description": "Accept the user's offer",
          "Parameters": []
        },
        {
          "Name": "RescindOffer",
          "DisplayName": "Rescind Offer",
          "Description": "Rescind your placed offer",
          "Parameters": []
        },
        {
          "Name": "ModifyOffer",
          "DisplayName": "Modify Offer",
          "Description": "Modify the price of your placed offer",
          "Parameters": [
            {
              "Name": "offerPrice",
              "DisplayName": "Price",
              "Type": {
                "Name": "money"
              }
            }
          ]
        },
        {
          "Name": "Accept",
          "DisplayName": "Accept",
          "Description": "Accept the inspection/appraisal results",
          "Parameters": []
        },
        {
          "Name": "MarkInspected",
          "DisplayName": "Mark Inspected",
          "Description": "Mark the asset as inspected",
          "Parameters": []
        },
        {
          "Name": "MarkAppraised",
          "DisplayName": "Mark Appraised",
          "Description": "Mark the asset as appraised",
          "Parameters": []
        }
      ],
      "States": [
        {
          "Name": "Active",
          "DisplayName": "Active",
          "Description": "The initial state of the asset transfer workflow",
          "PercentComplete": 20,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancels this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate Offer"
            },
            {
              "AllowedRoles": [ "Buyer" ],
              "AllowedInstanceRoles": [],
              "Description": "Make an offer for this asset",
              "Function": "MakeOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Make Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Modify attributes of this asset transfer instance",
              "Function": "Modify",
              "NextStates": [ "Active" ],
              "DisplayName": "Modify"
            }
          ]
        },
        {
          "Name": "OfferPlaced",
          "DisplayName": "Offer Placed",
          "Description": "Offer has been placed for the asset",
          "PercentComplete": 30,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Accept the proposed offer for the asset",
              "Function": "AcceptOffer",
              "NextStates": [ "PendingInspection" ],
              "DisplayName": "Accept Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you previously placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Modify the price that you specified for your offer",
              "Function": "ModifyOffer",
              "NextStates": [ "OfferPlaced" ],
              "DisplayName": "Modify Offer"
            }
          ]
        },
        {
          "Name": "PendingInspection",
          "DisplayName": "Pending Inspection",
          "Description": "Asset is pending inspection",
          "PercentComplete": 40,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark this asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "Inspected" ],
              "DisplayName": "Mark Inspected"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "Appraised" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Inspected",
          "DisplayName": "Inspected",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceAppraiser" ],
              "Description": "Mark this asset as appraised",
              "Function": "MarkAppraised",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Appraised"
            }
          ]
        },
        {
          "Name": "Appraised",
          "DisplayName": "Appraised",
          "Description": "Asset has been appraised, now awaiting inspection",
          "PercentComplete": 45,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the offer",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel the offer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceInspector" ],
              "Description": "Mark the asset as inspected",
              "Function": "MarkInspected",
              "NextStates": [ "NotionalAcceptance" ],
              "DisplayName": "Mark Inspected"
            }
          ]
        },
        {
          "Name": "NotionalAcceptance",
          "DisplayName": "Notional Acceptance",
          "Description": "Asset has been inspected and appraised, awaiting final sign-off from buyer and seller",
          "PercentComplete": 50,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "SellerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "BuyerAccepted" ],
              "DisplayName": "BuyerAccept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "BuyerAccepted",
          "DisplayName": "Buyer Accepted",
          "Description": "Buyer has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "SellerAccepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Reject the proposed offer for the asset",
              "Function": "Reject",
              "NextStates": [ "Active" ],
              "DisplayName": "Reject"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceOwner" ],
              "Description": "Cancel this instance of asset transfer",
              "Function": "Terminate",
              "NextStates": [ "Terminated" ],
              "DisplayName": "Terminate"
            }
          ]
        },
        {
          "Name": "SellerAccepted",
          "DisplayName": "Seller Accepted",
          "Description": "Seller has signed-off on inspection and appraisal",
          "PercentComplete": 75,
          "Style": "Success",
          "Transitions": [
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Sign-off on inspection and appraisal",
              "Function": "Accept",
              "NextStates": [ "Accepted" ],
              "DisplayName": "Accept"
            },
            {
              "AllowedRoles": [],
              "AllowedInstanceRoles": [ "InstanceBuyer" ],
              "Description": "Rescind the offer you placed for this asset",
              "Function": "RescindOffer",
              "NextStates": [ "Active" ],
              "DisplayName": "Rescind Offer"
            }
          ]
        },
        {
          "Name": "Accepted",
          "DisplayName": "Accepted",
          "Description": "Asset transfer process is complete",
          "PercentComplete": 100,
          "Style": "Success",
          "Transitions": []
        },
        {
          "Name": "Terminated",
          "DisplayName": "Terminated",
          "Description": "Asset transfer has been canceled",
          "PercentComplete": 100,
          "Style": "Failure",
          "Transitions": []
        }
      ]
    }
  ]
}
```
## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Referência de API REST do Azure Blockchain Workbench](https://docs.microsoft.com/rest/api/azure-blockchain-workbench)

