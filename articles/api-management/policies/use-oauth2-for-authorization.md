---
title: Política de gerenciamento de API do Azure de exemplo-use OAuth2 para autorização entre gateway e back-end
titleSuffix: Azure API Management
description: Exemplo de política de gerenciamento de API do Azure – Demonstra como usar OAuth2 para autorização entre o gateway e um back-end. Ele mostra como obter um token de acesso do AAD e encaminhá-lo para o back-end.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 0f5a10eb2ebc3b3a7c527dd718e37faf03c927bc
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076089"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Usar o OAuth2 para autorização entre o gateway e um back-end

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como usar OAuth2 para autorização entre o gateway e um back-end. Ele mostra como obter um token de acesso do AAD e encaminhá-lo para o back-end. 

Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-reference.md).

O script a seguir usa propriedades que aparecem em {{property}}. Para saber mais sobre propriedades e como usá-las nas políticas de Gerenciamento de API, consulte [este](../api-management-howto-properties.md) tópico.
 
## <a name="policy"></a>Política

Cole o código no bloco de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-reference.md)