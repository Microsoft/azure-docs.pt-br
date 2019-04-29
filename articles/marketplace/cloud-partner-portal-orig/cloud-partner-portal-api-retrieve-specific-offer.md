---
title: Recuperar uma API de oferta específica | Microsoft Docs
description: A API recupera a oferta especificada no namespace do editor.
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
ms.openlocfilehash: 9484cf0f549db94be8f1ac2363addca952a3cff3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61096056"
---
<a name="retrieve-a-specific-offer"></a>Recuperar uma oferta específica
=========================

Recupere a oferta especificada no namespace do editor.  

Também é possível recuperar uma versão específica da oferta, recuperar a oferta no rascunho, exibição, ou slot de produção. Se um slot não for especificado, o padrão será `draft`. A tentativa de recuperar uma oferta que não tenha sido visualizada ou publicada resultará em um erro `404 Not Found`.

> [!WARNING]
> Os valores secretos para campos do tipo secreto não serão recuperados por essa API.

``` http
    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/versions/<version>?api-version=2017-10-31

    GET https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/slot/<slotId>?api-version=2017-10-31

```


<a name="uri-parameters"></a>Parâmetros de URI
--------------


| **Nome**    | **Descrição**                                                                          | **Tipo de dados** |
|-------------|------------------------------------------------------------------------------------------|---------------|
| publisherId | publisherId. Po exemplo, Contoso                                                        | Cadeia de caracteres        |
| offerId     | Guid que identifica exclusivamente a oferta.                                                 | Cadeia de caracteres        |
| version     | Versão da oferta que está sendo recuperada. Por padrão, a versão mais recente da oferta é recuperada. | Número inteiro       |
| slotId      | O slot do qual a oferta deve ser recuperada pode ser um dos a seguir:      <br/>  - `Draft` (padrão) recupera a versão da oferta atualmente no rascunho.  <br/>  -  `Preview` recupera a versão da oferta atualmente na visualização.     <br/>  -  `Production` recupera a versão da oferta atualmente em produção.          |      enum |
| api-version | Última versão da API                                                                    | Data          |
|  |  |  |


<a name="header"></a>Cabeçalho
------

|  **Nome**          |   **Valor**            |
|  ---------------   |  --------------        |
|  Tipo de conteúdo      | `application/json`     |
|  Autorização     | `Bearer YOUR_TOKEN`    |
|  |  |


<a name="body-example"></a>Exemplo de corpo
------------

### <a name="response"></a>Response

``` json
{
    "offerTypeId": "microsoft-azure-virtualmachines",
    "publisherId": "contoso",
    "status": "failed",
    "id": "059afc24-07de-4126-b004-4e42a51816fe",
    "version": 5,
    "definition": {
        "displayText": "Contoso Virtual Machine Offer",
        "offer": {
            "microsoft-azure-marketplace-testdrive.enabled": false,
            "microsoft-azure-marketplace-testdrive.videos": [],
            "microsoft-azure-marketplace.title": "Contoso App",
            "microsoft-azure-marketplace.summary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.longSummary": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.description": "Contoso App makes dev ops a breeze",
            "microsoft-azure-marketplace.offerMarketingUrlIdentifier": "contosoapp",
            "microsoft-azure-marketplace.allowedSubscriptions": [
                "59160c40-2e25-4dcf-a2fd-6514cb08bf08"
            ],
            "microsoft-azure-marketplace.usefulLinks": [
            {
                "linkTitle": "Contoso App for Azure",
                "linkUrl": "https://azuremarketplace.microsoft.com"
            }
            ],
            "microsoft-azure-marketplace.categories": [
                "devService",
                "networking",
                "database",
                "cache",
                "security"
            ],
            "microsoft-azure-marketplace.smallLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/6218c455-9cbc-450c-9920-f2e7a69ee132.png?sv=2014-02-14&sr=b&sig=6O8MM9dgiJ48VK0MwddkyVbprRAnBszyhVkVHGShhkI%3D&se=2019-03-28T19%3A46%3A50Z&sp=r",
            "microsoft-azure-marketplace.mediumLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/557e714b-2f31-4e12-b0cc-e48dd840edf4.png?sv=2014-02-14&sr=b&sig=NwL67NTQf9Gc9VScmZehtbHXpYmxhwZc2foy3o4xavs%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.largeLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/142485da-784c-44cb-9523-d4f396446258.png?sv=2014-02-14&sr=b&sig=xaMxhwx%2FlKYfz33mJGIg8UBdVpsOwVvqhjTJ883o0iY%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.wideLogo": "https://publishingapistore.blob.core.windows.net/testcontent/D6191_publishers_contoso/contosovirtualmachine/48af9013-1df7-4c94-8da8-4626e5039ce0.png?sv=2014-02-14&sr=b&sig=%2BnN7f2tprkrqb45ID6JlT01zXcy1PMTkWXtLKD6nfoE%3D&se=2019-03-28T19%3A46%3A49Z&sp=r",
            "microsoft-azure-marketplace.screenshots": [],
            "microsoft-azure-marketplace.videos": [],
            "microsoft-azure-marketplace.leadDestination": "None",
            "microsoft-azure-marketplace.tableLeadConfiguration": {},
            "microsoft-azure-marketplace.blobLeadConfiguration": {},
            "microsoft-azure-marketplace.salesForceLeadConfiguration": {},
            "microsoft-azure-marketplace.crmLeadConfiguration": {},
            "microsoft-azure-marketplace.httpsEndpointLeadConfiguration": {},
            "microsoft-azure-marketplace.marketoLeadConfiguration": {},
            "microsoft-azure-marketplace.privacyURL": "https://azuremarketplace.microsoft.com",
            "microsoft-azure-marketplace.termsOfUse": "Terms of use",
            "microsoft-azure-marketplace.engineeringContactName": "Jon Doe",
            "microsoft-azure-marketplace.engineeringContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.engineeringContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.supportContactName": "Jon Doe",
            "microsoft-azure-marketplace.supportContactEmail": "jondoe@outlook.com",
            "microsoft-azure-marketplace.supportContactPhone": "555-555-5555",
            "microsoft-azure-marketplace.publicAzureSupportUrl": "",
            "microsoft-azure-marketplace.fairfaxSupportUrl": ""
            },
            "plans": [
            {
                "planId": "contososkuidentifier",
                "microsoft-azure-virtualmachines.skuTitle": "Contoso App",
                "microsoft-azure-virtualmachines.skuSummary": "Contoso App makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.skuDescription": "This is a description for the Contoso App that makes dev ops a breeze.",
                "microsoft-azure-virtualmachines.hideSKUForSolutionTemplate": false,
                "microsoft-azure-virtualmachines.cloudAvailability": [
                    "PublicAzure"
                ],
                "microsoft-azure-virtualmachines.certificationsFairfax": [],
                "virtualMachinePricing": {
                    "isByol": true,
                    "freeTrialDurationInMonths": 0
                },
                "microsoft-azure-virtualmachines.operatingSystemFamily": "Windows",
                "microsoft-azure-virtualmachines.windowsOSType": "Other",
                "microsoft-azure-virtualmachines.operationSystem": "Contoso App",
                "microsoft-azure-virtualmachines.recommendedVMSizes": [
                    "a0-basic",
                    "a0-standard",
                    "a1-basic",
                    "a1-standard",
                    "a2-basic",
                    "a2-standard"
                ],
                "microsoft-azure-virtualmachines.openPorts": [],
                "microsoft-azure-virtualmachines.vmImages": {
                    "1.0.1": {
                    "osVhdUrl": "http://contosoteststorage.blob.core.windows.net/test/contosoVM.vhd?sv=2014-02-14&sig=WlDo6Q4xwYH%2B5QEJbItPUVdgHhBcrVxPBmntZ2vU96w%3D&st=2016-06-25T18%3A30%3A00Z&se=2017-06-25T18%3A30%3A00Z&sp=rl",
                    "lunVhdDetails": []
                    }
                },
                "regions": [
                    "DZ",
                    "AR"
                ]
            }
            ]
        },
        "changedTime": "2017-06-07T06:15:39.7349221Z"
    }
}
```


### <a name="response-body-properties"></a>Propriedades do corpo de resposta

|  **Nome**       |   **Descrição**                                                                                                               |
|  -------------  |   -----------------------------------------------------------------------------------------------------                         |
|  offerTypeId    | Identifica o tipo de oferta                                                                                                    |
|  publisherId    | Identificador exclusivo do editor                                                                                              |
|  status         | Status da oferta. Para a lista de valores possíveis, consulte [Status da oferta](#offer-status) abaixo.                                  |
|  ID             | GUID que identifica exclusivamente a oferta                                                                                         |
|  version        | Versão atual da oferta. A propriedade de versão não pode ser modificada pelo cliente. Isso é incrementado após cada publicação.    |
|  definição     | Definição real da carga de trabalho                                                                                               |
|  changedTime    | Data e hora em UTC de quando a oferta foi modificada pela última vez                                                                                   |
|  |  |


### <a name="response-status-codes"></a>Códigos de status de resposta

| **Código**  | **Descrição**                                                                                                                 |
|  ------   | ------------------------------------------------------------------------------------------------------------------------------- |
|  200      | `OK` - A solicitação foi processada com êxito e todas as ofertas do editor foram devolvidas ao cliente.               |
|  400      | `Bad/Malformed request` - O corpo da resposta de erro pode conter mais informações.                                                 |
|  403      | `Forbidden` - O cliente não tem acesso ao namespace especificado.                                                        |
|  404      | `Not found` - A entidade especificada não existe. O cliente deve verificar o publisherId, o offerId e a versão (se especificado).      |
|  |  |


### <a name="offer-status"></a>Status da oferta

|  **Nome**                   |   **Descrição**                             |
| --------------------------- |  -------------------------------------------- |
|  NeverPublished             | A oferta nunca foi publicada.               |
|  NotStarted                 | A oferta é nova e não iniciada.              |
|  WaitingForPublisherReview  | A oferta aguarda aprovação do editor.      |
|  Executando                    | O envio da oferta está sendo processado.          |
|  Bem-sucedida                  | O envio da oferta concluiu o processamento.    |
|  Cancelado                   | O envio da oferta foi cancelado.                |
|  Com falha                     | O envio da oferta falhou.                      |
|  |  |
