---
title: Cancelar operação de API | Microsoft Docs
description: Cancela operações.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 18f00391beded0744c80eab73bb1efe1c6ab8dbc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60625033"
---
<a name="cancel-operation"></a>Cancelar operação 
=================

Essa API cancela uma operação atualmente em andamento na oferta. Use [Recuperar API de operações](./cloud-partner-portal-api-retrieve-operations.md) para obter um `operationId` para passar a essa API. Geralmente, o cancelamento é uma operação síncrona, no entanto, em alguns cenários complexos uma nova operação pode ser necessária para cancelar uma operação existente. Nesse caso, o corpo da resposta HTTP contém o local da operação que deve ser usado para consultar o status.

Você pode fornecer uma lista separada por vírgulas de endereços de email com a solicitação, e a API notificará esses endereços sobre o andamento da operação.

  `POST https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/cancel?api-version=2017-10-31`

<a name="uri-parameters"></a>Parâmetros de URI
--------------

|  **Nome**    |      **Descrição**                                  |    **Tipo de dados**  |
| ------------ |     ----------------                                  |     -----------   |
| publisherId  |  Identificador do publicar, por exemplo, `contoso`         |   Cadeia de caracteres          |
| offerId      |  Identificador da oferta                                     |   Cadeia de caracteres          |
| api-version  |  Versão atual da API                               |    Data           |
|  |  |  |


<a name="header"></a>Cabeçalho
------

|  **Nome**              |  **Valor**         |
|  ---------             |  ----------        |
|  Tipo de conteúdo          |  aplicativo/json  |
|  Autorização         |  TOKEN de seu portador |
|  |  |


<a name="body-example"></a>Exemplo de corpo
------------

### <a name="request"></a>Solicitação

``` json
{
   "metadata": {
     "notification-emails": "jondoe@contoso.com"
    }
}     
```

### <a name="request-body-properties"></a>Solicitar propriedades do corpo

|  **Nome**                |  **Descrição**                                               |
|  --------                |  ---------------                                               |
|  emails de notificação     | Lista separada por vírgula de IDs de email a serem notificados sobre o andamento da operação de publicação. |
|  |  |


### <a name="response"></a>Response

  `Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Cabeçalho de Resposta

|  **Nome**             |    **Valor**                       |
|  ---------            |    ----------                      |
| Operation-Location    | URL, que pode ser consultada para determinar o status atual da operação. |
|  |  |


### <a name="response-status-codes"></a>Códigos de status de resposta

| **Código**  |  **Descrição**                                                                       |
|  ------   |  ------------------------------------------------------------------------               |
|  200      | OK. A solicitação foi processada com êxito e a operação é cancelada de forma síncrona. |
|  202      | Aceita. A solicitação foi processada com êxito e a operação está em processo de cancelamento. A localização da operação de cancelamento é retornada no cabeçalho de resposta. |
|  400      | Solicitação incorreta/malformada. O corpo da resposta ao erro pode fornecer mais informações.  |
|  403      | Acesso proibido. O cliente não tem acesso ao namespace especificado na solicitação. |
|  404      | Não encontrado. A entidade especificada não existe. |
|  |  |
