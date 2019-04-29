---
title: Go Live | Microsoft Docs
description: A API do Go Live inicia o processo de listagem de ofertas em tempo real.
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
ms.openlocfilehash: ea3f26d70c4a4ce07c988612890687504a4cf5ac
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60624965"
---
<a name="go-live"></a>Go Live
=======

Essa API inicia o processo de envio de um aplicativo para produção. Essa operação geralmente é demorada. Essa chamada usa a lista de email de notificação da operação da API [Publicar](./cloud-partner-portal-api-publish-offer.md).

 `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/golive?api-version=2017-10-31` 

<a name="uri-parameters"></a>Parâmetros de URI
--------------

|  **Nome**      |   **Descrição**                                                           | **Tipo de dados** |
|  --------      |   ---------------                                                           | ------------- |
| publisherId    | Identificador do publicador da oferta a ser recuperada, por exemplo `contoso`       |  Cadeia de caracteres       |
| offerId        | O identificador da oferta a ser recuperada                                   |  Cadeia de caracteres       |
| api-version    | Última versão da API                                                   |  Data         |
|  |  |  |


<a name="header"></a>Cabeçalho
------

|  **Nome**       |     **Valor**       |
|  ---------      |     ----------      |
| Tipo de conteúdo    | `application/json`  |
| Autorização   | `Bearer YOUR_TOKEN` |
|  |  |


<a name="body-example"></a>Exemplo de corpo
------------

### <a name="response"></a>Response

`Operation-Location: https://cloudpartner.azure.com/api/publishers/contoso/offers/contoso-virtualmachineoffer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8`


### <a name="response-header"></a>Cabeçalho de Resposta

|  **Nome**             |      **Valor**                                                            |
|  --------             |      ----------                                                           |
| Operation-Location    |  A URL a ser consultada para determinar o status atual da operação            |
|  |  |


### <a name="response-status-codes"></a>Códigos de status de resposta

| **Código** |  **Descrição**                                                                        |
| -------- |  ----------------                                                                        |
|  202     | `Accepted` – a solicitação foi aceita com êxito. A resposta contém um local para rastrear o status da operação. |
|  400     | `Bad/Malformed request` – as informações de erro adicionais são encontradas dentro do corpo da resposta. |
|  404     |  `Not found` – a entidade especificada não existe.                                       |
|  |  |
