---
title: Publicar uma oferta-Azure Marketplace
description: API para publicar a oferta especificada.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 60e75aff79913896bdf1dcdc8754b6ecf5620b06
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "87272038"
---
# <a name="publish-an-offer"></a>Publicar uma oferta

> [!NOTE]
> As APIs de Portal do Cloud Partner são integradas ao e continuarão funcionando no Partner Center. A transição apresenta pequenas alterações. Examine as alterações listadas em [portal do Cloud Partner referência de API](./cloud-partner-portal-api-overview.md) para garantir que seu código continue funcionando após a transição para o Partner Center. As APIs de CPP só devem ser usadas para produtos existentes que já foram integrados antes da transição para o Partner Center; os novos produtos devem usar as APIs de envio do Partner Center.

Inicia o processo de publicação da oferta especificada. Essa ligação é uma operação demorada.

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>Parâmetros do URI
--------------

|  **Nome**      |    **Descrição**                               |  **Data type** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | Identificador do editor, por exemplo `contoso`      |   String       |
|  offerId       | Identificador da oferta                                 |   String       |
|  api-version   | Última versão da API                        |   Data         |
|  |  |

## <a name="header"></a>parâmetro
------

|  **Nome**        |    **Valor**          |
|  --------        |    ---------          |
|  Tipo de conteúdo    | `application/json`    |
|  Autorização   |  `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>Exemplo de corpo
------------

### <a name="request"></a>Solicitação

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>Solicitar propriedades do corpo

|  **Nome**               |   **Descrição**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  emails de notificação    | Lista separada por vírgula de endereços de email para ser notificado sobre o andamento da operação de publicação. |
|  |  |

### <a name="response"></a>Resposta

#### <a name="migrated-offers"></a>Ofertas migradas

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>Ofertas não migradas

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`

### <a name="response-header"></a>Cabeçalho de Resposta

|  **Nome**             |    **Valor**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| Location    | O caminho relativo para recuperar o status da operação     |
|  |  |

### <a name="response-status-codes"></a>Códigos de status de resposta

| **Código** |  **Descrição**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` – a solicitação foi aceita com êxito. A resposta contém um local que pode ser usado para rastrear a operação iniciada. |
| 400   | `Bad/Malformed request` – O corpo da resposta de erro pode fornecer mais informações.                                                               |
| 422   | `Un-processable entity` – Indica que a entidade a ser publicada apresentou falha na validação.                                                        |
| 404   | `Not found` – A entidade especificada pelo cliente não existe.                                                                              |
|  |  |
