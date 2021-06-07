---
title: Recuperar a API de ofertas-Azure Marketplace
description: API para recuperar uma lista resumida de ofertas em um namespace do Publicador.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 39d07751c708d5555799ecbb3b3bc66d3f44f43a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87271953"
---
# <a name="retrieve-offers"></a>Recuperar ofertas

> [!NOTE]
> As APIs de Portal do Cloud Partner são integradas ao e continuarão funcionando no Partner Center. A transição apresenta pequenas alterações. Examine as alterações listadas em [portal do Cloud Partner referência de API](./cloud-partner-portal-api-overview.md) para garantir que seu código continue funcionando após a transição para o Partner Center. As APIs de CPP só devem ser usadas para produtos existentes que já foram integrados antes da transição para o Partner Center; os novos produtos devem usar as APIs de envio do Partner Center.

Recupera uma lista resumida de ofertas em um namespace do editor.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parâmetros do URI

| **Nome**         |  **Descrição**                         |  **Data type** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Identificador do editor, por exemplo `contoso` |   String    |
|  api-version     | Última versão da API                    |    Data        |
|  |  |

## <a name="header"></a>parâmetro

|  **Nome**        |         **Valor**       |
|  --------------- |       ----------------  |
|  Tipo de conteúdo    | `application/json`      |
|  Autorização   | `Bearer YOUR_TOKEN`     |
|  |  |

## <a name="body-example"></a>Exemplo de corpo

### <a name="response"></a>Resposta

``` json
  200 OK 
  [ 
      {  
          "offerTypeId": "microsoft-azure-virtualmachines",
          "publisherId": "contoso",
          "status": "published",
          "id": "059afc24-07de-4126-b004-4e42a51816fe",
          "version": 1,
          "definition": {
              "displayText": "Contoso Virtual Machine"
          },
          "changedTime":"2017-05-23T23:33:47.8802283Z"
      }
  ]
```

### <a name="response-body-properties"></a>Propriedades do corpo de resposta

|  **Nome**       |       **Descrição**                                                                                                  |
|  -------------  |      --------------------------------------------------------------------------------------------------------------    |
|  offerTypeId    | Identifica o tipo de oferta                                                                                           |
|  publisherId    | Identificador que identifica exclusivamente o editor                                                                      |
|  status         | Status da oferta. Para a lista de valores possíveis, consulte [Status da oferta](#offer-status) abaixo.                         |
|  id             | GUID que identifica exclusivamente a oferta no namespace do editor.                                                    |
|  version        | Versão atual da oferta. A propriedade de versão não pode ser modificada pelo cliente. Isso é incrementado após cada publicação. |
|  definição     | Contém uma visão resumida da definição real da carga de trabalho. Para obter uma definição detalhada, use a [API Recuperar oferta específica](./cloud-partner-portal-api-retrieve-specific-offer.md). |
|  changedTime    | Hora em UTC quando a oferta foi modificada pela última vez                                                                              |
|  |  |

### <a name="response-status-codes"></a>Códigos de status de resposta

| **Código**  |  **Descrição**                                                                                                   |
| -------   |  ----------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` - A solicitação foi processada com êxito e todas as ofertas do editor foram devolvidas ao cliente.  |
|  400      | `Bad/Malformed request` - O corpo da resposta de erro pode conter mais informações.                                    |
|  403      | `Forbidden` - O cliente não tem acesso ao namespace especificado.                                          |
|  404      | `Not found` - A entidade especificada não existe.                                                                 |
|  |  |

### <a name="offer-status"></a>Status da oferta

|  **Nome**                    | **Descrição**                                  |
|  ------------------------    | -----------------------------------------------  |
|  NeverPublished              | A oferta nunca foi publicada.                  |
|  NotStarted                  | A oferta é nova e não iniciada.                 |
|  WaitingForPublisherReview   | A oferta aguarda aprovação do editor.         |
|  Executando                     | O envio da oferta está sendo processado.             |
|  Com sucesso                   | O envio da oferta concluiu o processamento.       |
|  Canceled                    | O envio da oferta foi cancelado.                   |
|  Com falha                      | O envio da oferta falhou.                         |
|  |  |
