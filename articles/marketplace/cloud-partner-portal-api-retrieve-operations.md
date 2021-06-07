---
title: Recuperar a API de operações-Azure Marketplace
description: API para recuperar todas as operações na oferta ou para obter uma operação específica para a operationId especificada.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: cb44d977407a7e854603e6bbacf3591752b109c2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87271936"
---
# <a name="retrieve-operations"></a>Recuperar operações

> [!NOTE]
> As APIs de Portal do Cloud Partner são integradas ao e continuarão funcionando no Partner Center. A transição apresenta pequenas alterações. Examine as alterações listadas em [portal do Cloud Partner referência de API](./cloud-partner-portal-api-overview.md) para garantir que seu código continue funcionando após a transição para o Partner Center. As APIs de CPP só devem ser usadas para produtos existentes que já foram integrados antes da transição para o Partner Center; os novos produtos devem usar as APIs de envio do Partner Center.

Recuperar todas as operações na oferta ou obter uma operação específica para a operationId especificada. O cliente pode usar parâmetros de consulta para filtrar as operações em execução.

``` https

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/submissions/?api-version=2017-10-31&status=<filteredStatus>

  GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/operations/<operationId>?api-version=2017-10-31

```

## <a name="uri-parameters"></a>Parâmetros do URI

|  **Nome**          |      **Descrição**                                                                                           | **Data type** |
|  ----------------  |     --------------------------------------------------------------------------------------------------------   |  -----------  |
|  publisherId       |  Identificador do editor, por exemplo `Contoso`                                                                   |  String       |
|  offerId           |  Identificador da oferta                                                                                              |  String       |
|  operationId       |  GUID que identifica exclusivamente a operação na oferta. A operationId pode ser recuperada usando essa API e também é retornada no cabeçalho HTTP da resposta para qualquer operação longa, como a API [Publicar oferta](./cloud-partner-portal-api-publish-offer.md).  |   Guid   |
|  api-version       | Última versão da API |    Data      |
|  |  |  |

## <a name="header"></a>parâmetro

|  **Nome**          |  **Valor**           |
|  ---------------   | -------------------- |
|  Tipo de conteúdo      | `application/json`   |
|  Autorização     | `Bearer YOUR_TOKEN`  |
|  |  |

## <a name="body-example"></a>Exemplo de corpo

### <a name="response"></a>Resposta

#### <a name="get-operations"></a>Operações GET

``` json
    [
        {
            "id": "5a63deb5-925b-4ee0-938b-7c86fbf287c5",
            "offerId": "56615b67-2185-49fe-80d2-c4ddf77bb2e8",
            "offerVersion": 1,
            "offerTypeId": "microsoft-azure-virtualmachines",
            "publisherId": "contoso",
            "submissionType": "publish",
            "submissionState": "running",
            "publishingVersion": 2,
            "slot": "staging",
            "version": 636576975611768314,
            "definition": {
                "metadata": {
                    "emails": "jdoe@contoso.com"
                }
            },
            "changedTime": "2018-03-26T21:46:01.179948Z"
        }
    ]
```

#### <a name="get-operation"></a>Operação GET

``` json
    [
        {
            "status" : "running",
            "messages" : [],
            "publishingVersion" : 2,
            "offerVersion" : 1,
            "cancellationRequestState": "canCancel",
            "steps": [
                        {
                            "estimatedTimeFrame": "< 15 min",
                            "id": "displaydummycertify",
                            "stepName": "Validate Pre-Requisites",
                            "description": "Offer settings provided are validated",
                            "status": "complete",
                            "messages": 
                            [
                                {
                                    "messageHtml": "Step completed.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:36.500052Z"
                                }
                            ],
                            "progressPercentage": 100
                        },
                        {
                            "estimatedTimeFrame": "< 5 day",
                            "id": "displaycertify",
                            "stepName": "Certification",
                            "description": "Your offer is analyzed by our certification systems for issues.",
                            "status": "blocked",
                            "messages": 
                            [
                                {
                                    "messageHtml": "No virtual machine image was found for the plan contoso.",
                                    "level": "error",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                },
                                {
                                    "messageHtml": "This step has not started yet.",
                                    "level": "information",
                                    "timestamp": "2017-03-28T19:50:39.5506018Z"
                                }
                            ],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "< 1 day",
                            "id": "displayprovision",
                            "stepName": "Provisioning",
                            "description": "Your virtual machine is being replicated in our production systems.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "< 1 hour",
                            "id": "displaypackage",
                            "stepName": "Packaging and Lead Generation Registration",
                            "description": "Your virtual machine is packaged for being shown to your customers. Additionally, we hookup our lead generation systems to send leads for your offer.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "id": "publisher-signoff",
                            "stepName": "Publisher signoff",
                            "description": "Offer is available to preview. Ensure that everything looks good before making your offer live.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        },
                        {
                            "estimatedTimeFrame": "~2-5 days",
                            "id": "live",
                            "stepName": "Live",
                            "description": "Offer is publicly visible and is available for purchase.",
                            "status": "notStarted",
                            "messages": [],
                            "progressPercentage": 0
                        }
                    ],
                "previewLinks": [],
                "liveLinks": [],
            }
        }
    ]
```

### <a name="response-body-properties"></a>Propriedades do corpo de resposta

|  **Nome**                    |  **Descrição**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
|  id                          | GUID que identifica exclusivamente a operação                                                       |
|  submissionType              | Identifica o tipo de operação que está sendo relatada para a oferta, por exemplo, `Publish/GoLive`      |
|  createdDateTime             | Data e hora em UTC quando a operação foi criada                                                       |
|  lastActionDateTime          | Data e hora em UTC quando a última atualização foi feita na operação                                       |
|  status                      | Status da operação, seja `not started` \| `running` \| `failed` \| `completed` . Apenas uma operação pode ter um status `running` por vez. |
|  erro                       | Mensagem de erro para operações com falha                                                               |
|  |  |

### <a name="response-step-properties"></a>Propriedades da etapa de resposta

|  **Nome**                    |  **Descrição**                                                                                  |
|  --------------------        |  ------------------------------------------------------------------------------------------------ |
| estimatedTimeFrame | A duração estimada desta operação |
| id | O identificador exclusivo para o processo de etapa |
| descrição | Descrição da etapa |
| stepName | O nome amigável para a etapa |
| status | O status da etapa, seja `notStarted` \| `running` \| `failed` \|`completed` |
| da nuvem para o dispositivo | Quaisquer notificações ou avisos encontrados durante a etapa. Matriz de cadeia de caracteres |
| progressPercentage | Um inteiro de 0 a 100 indicando a progressão da etapa |
| | |

### <a name="response-status-codes"></a>Códigos de status de resposta

| **Código**  |   **Descrição**                                                                                  |
|  -------- |   -------------------------------------------------------------------------------------------------|
|  200      | `OK` - A solicitação foi processada com êxito e as operações solicitadas foram retornadas.        |
|  400      | `Bad/Malformed request` - O corpo da resposta de erro pode conter mais informações.                    |
|  403      | `Forbidden` - O cliente não tem acesso ao namespace especificado.                          |
|  404      | `Not found` – a entidade especificada não existe.                                                 |
|  |  |
