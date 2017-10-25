---
title: "Perguntas frequentes de conectores personalizados – Aplicativos Lógicos do Azure | Microsoft Docs"
description: Perguntas Frequentes sobre requisitos, gatilhos e assim por diante sobre como criar conectores personalizados
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 225e56de3985acae871ddec447b763e7de61cb80
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="faq-custom-connectors"></a>Perguntas Frequentes: Conectores personalizados

## <a name="requirements"></a>Requisitos

**P:** Eu posso criar um conector sem APIs REST? </br>
**R:** Não, para criar um conector, você deve dar suporte a APIs REST de HTTP estável para o serviço. 

**P:** Quais ferramentas posso usar para criar um conector? </br>
**R:** O Azure tem recursos e serviços que você pode usar para expor qualquer serviço como uma API, como o Serviço de Aplicativo do Azure para hospedar, Gerenciamento de API e muito mais.

**P:** Quais tipos de autenticação têm suporte? </br>
**R:** Você pode usar estes padrões de autenticação com suporte:

* [OAuth 2.0](https://oauth.net/2/), incluindo [Azure Active Directory](https://azure.microsoft.com/develop/identity/) ou serviços específicos, como o Dropbox, SalesForce e GitHub
* OAuth 2.0 genérico
* [Autenticação básica](https://swagger.io/docs/specification/authentication/basic-authentication/)
* [Chave de API](https://swagger.io/docs/specification/authentication/api-keys/)

## <a name="triggers"></a>Gatilhos

**P:** Eu posso criar gatilhos sem webhooks? </br>
**R:** Não, conectores personalizados para os Aplicativos Lógicos do Azure e Microsoft Flow oferecem suporte apenas a gatilhos com base em webhook. Se desejar solicitar outros padrões para implementação, entre em contato com [condevhelp@microsoft.com](mailto:condevhelp@microsoft.com) com mais detalhes sobre a sua API.

## <a name="certification"></a>Certificação

**P**: Eu não sou um parceiro da Microsoft ou o fornecedor de Software independente (ISV). Ainda posso criar conectores? </br>
**R**: Sim, você pode registrar esses conectores para uso interno em sua organização, mas se desejar verificar e lançar publicamente um conector, será necessário ter o serviço subjacente ou apresentar direitos explícitos para usar a API.

## <a name="other"></a>outro

**P:** Minhas APIs usam um host dinâmico. Como posso implementá-los com OpenAPI? </br>
**R:** Conectores personalizados não oferecem suporte a hosts dinâmicos. Em vez disso, use um host estático para fins de teste e desenvolvimento. Se desejar certificar seu conector, pergunte ao seu contato da Microsoft sobre a implementação dinâmica.

**P:** Vocês oferecem suporte à coleção V2 do Postman? </br>
**R:** Não, somente a coleção V1 do Postman tem suporte atualmente.

**P:** Vocês dão suporte ao OpenAPI 3.0? </br>
**R:** Não, somente OpenAPI 2.0 tem suporte atualmente.