---
title: Política de gerenciamento de API de exemplo-adicionar recursos ao serviço de back-end
titleSuffix: Azure API Management
description: Exemplo de política de gerenciamento de API do Azure - Demonstra como adicionar recursos a um serviço de back-end. Por exemplo, aceitar um nome do local em vez de latitude e longitude em uma API de previsão do tempo.
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
ms.openlocfilehash: 64db4e5451425002eeaac11695ac011a96047d9b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92076174"
---
# <a name="add-capabilities-to-a-backend-service"></a>Adicionar recursos a um serviço de back-end

Este artigo mostra uma política de gerenciamento de API do Azure que demonstra como adicionar recursos a um serviço de back-end. Por exemplo, aceitar um nome do local em vez de latitude e longitude em uma API de previsão do tempo. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-reference.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-reference.md)