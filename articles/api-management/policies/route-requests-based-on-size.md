---
title: Política de gerenciamento de API de exemplo – solicitação de rota com base no tamanho do corpo da mensagem
titleSuffix: Azure API Management
description: 'Exemplo de política de gerenciamento de API do Azure: demonstra como rotear solicitações com base no tamanho dos corpos.'
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
ms.openlocfilehash: e59aece0be39bf671e3d1e62f1a0e4c2a42a1ec8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92076463"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>Rotear a solicitação com base no tamanho do corpo

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como rotear solicitações com base no tamanho do corpo. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-reference.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-reference.md)