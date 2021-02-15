---
title: Considerações sobre API-Azure Marketplace
description: Problemas de controle de versão, tratamento de erro e autorização ao usar as APIs do Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 0920bec160874f27c8b1b6f2132951b57719b31c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "88035273"
---
# <a name="api-considerations"></a>Considerações sobre API

<a name="api-versioning"></a>Controle de versão de API
--------------

> [!NOTE]
> As APIs de Portal do Cloud Partner são integradas ao e continuarão funcionando no Partner Center. A transição apresenta pequenas alterações. Examine as alterações listadas em [portal do Cloud Partner referência de API](./cloud-partner-portal-api-overview.md) para garantir que seu código continue funcionando após a transição para o Partner Center. As APIs de CPP só devem ser usadas para produtos existentes que já foram integrados antes da transição para o Partner Center; os novos produtos devem usar as APIs de envio do Partner Center.

É possível que existam várias versões da API disponíveis ao mesmo tempo. Os clientes devem indicar qual versão querem chamar usando o parâmetro `api-version` como parte da cadeia de caracteres de consulta.

   `GET https://cloudpartner.azure.com/api/offerTypes?api-version=2017-10-31`

A resposta a uma solicitação com uma versão de API desconhecida ou inválida é um código HTTP 400. Esse erro retorna a coleção de versões de API conhecidas no corpo da resposta.

``` json
    {
        "error": { 
            "code":"InvalidAPIVersion",
            "message":"Invalid api version. Allowed values are [2016-08-01-preview]"
        }
    }
```            

<a name="errors"></a>Errors
------

A API responde a erros com os códigos de status HTTP correspondentes e, opcionalmente, informações adicionais na resposta serializada como JSON.
Quando você receber um erro, especialmente um erro de classe 400, não tente a solicitação novamente antes de corrigir a causa subjacente. Por exemplo, na resposta de exemplo acima, corrija o parâmetro de versão da API antes de reenviar a solicitação.

<a name="authorization-header"></a>Cabeçalho de autorização
--------------------

Para todas as APIs nessa referência, você deve passar o cabeçalho de autorização junto com o token de portador obtido do Azure AD (Azure Active Directory). Esse cabeçalho é necessário para receber uma resposta válida e, se não estiver presente, um erro `401 Unauthorized` será retornado. 

``` HTTP
  GET https://cloudpartner.azure.com/api/offerTypes?api-version=2016-08-01-preview

    Accept: application/json 
    Authorization: Bearer <YOUR_TOKEN> 
```
