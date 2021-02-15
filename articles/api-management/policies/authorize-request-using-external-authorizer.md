---
title: Política de gerenciamento de API de exemplo – autorizar solicitação usando o autorizador externo
titleSuffix: Azure API Management
description: Exemplo de política de gerenciamento de API do Azure – demonstra como autorizar solicitações usando um autorizador externo encapsulando uma lógica de autenticação/autorização personalizada ou herdada.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: e38d92a13c9a66defc2d5090990b44a889cfd21c
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076225"
---
# <a name="authorize-requests-using-external-authorizer"></a>Autorizar solicitações usando um autorizador externo

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como proteger o acesso à API usando um autorizador externo que encapsula uma lógica de autenticação/autorização personalizada. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-reference.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de restrição de acesso](../api-management-access-restriction-policies.md)
+ [Exemplos de política](../policy-reference.md)