---
title: Recuperar API de ofertas | Microsoft Docs
description: API recupera uma lista resumida de ofertas em um namespace do editor.
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
ms.openlocfilehash: de9261548ec79e206b0db87caabc1fa4c9ad6771
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61094476"
---
<a name="retrieve-offers"></a>Recuperar ofertas
===============

Recupera uma lista resumida de ofertas em um namespace do editor.

 `GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers?api-version=2017-10-31`


<a name="uri-parameters"></a>Parâmetros de URI
--------------

| **Nome**         |  **Descrição**                         |  **Tipo de dados** |
| -------------    |  ------------------------------------    |  -----------   |
|  publisherId     | Identificador do editor, por exemplo `contoso` |   Cadeia de caracteres    |
|  api-version     | Última versão da API                    |    Data        |
|  |  |


<a name="header"></a>Cabeçalho
------

|  **Nome**        |         **Valor**       |
|  --------------- |       ----------------  |
|  Tipo de conteúdo    | `application/json`      |
|  Autorização   | `Bearer YOUR_TOKEN`     |
|  |  |


<a name="body-example"></a>Exemplo de corpo
------------

### <a name="response"></a>Response

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
|  ID             | GUID que identifica exclusivamente a oferta no namespace do editor.                                                    |
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
|  Bem-sucedida                   | O envio da oferta concluiu o processamento.       |
|  Cancelado                    | O envio da oferta foi cancelado.                   |
|  Com falha                      | O envio da oferta falhou.                         |
|  |  |
