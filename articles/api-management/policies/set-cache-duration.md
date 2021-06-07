---
title: Política de gerenciamento de API de exemplo-definir duração do cache de resposta
titleSuffix: Azure API Management
description: Exemplo de política de gerenciamento de API do Azure - Demonstra como definir a duração do cache de resposta usando o valor de maxAge no cabeçalho Controle de Cache enviado pelo back-end.
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
ms.openlocfilehash: 088dfbcbfe00357c47de4662ac31b52e83d1d8ab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92070972"
---
# <a name="set-response-cache-duration"></a>Definir duração do cache de resposta

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como definir a duração do cache de resposta usando o valor de maxAge no cabeçalho Cache-Control enviado pelo back-end. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-reference.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-reference.md)