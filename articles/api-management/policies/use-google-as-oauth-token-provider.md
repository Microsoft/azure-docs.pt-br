---
title: Política de gerenciamento de API de exemplo – autorizar o acesso usando o token OAuth do Google
titleSuffix: Azure API Management
description: Exemplo de política de gerenciamento de API do Azure – Demonstra como autorizar o acesso aos pontos de extremidade usando o Google como um provedor de token OAuth.
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
ms.openlocfilehash: 0f6c9fe2146414f78e90d6ade1a00045cdf3a04f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92078010"
---
# <a name="authorize-access-using-google-oauth-token"></a>Autorizar o acesso usando o token OAuth do Google

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como autorizar o acesso aos pontos de extremidade usando o Google como um provedor de token OAuth. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-reference.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-reference.md)