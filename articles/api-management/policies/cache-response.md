---
title: Exemplo de política de gerenciamento de API do Azure - Adicionar recursos a um serviço de back-end | Microsoft Docs
description: Exemplo de política de gerenciamento de API do Azure - Demonstra como adicionar recursos a um serviço de back-end. Por exemplo, aceitar um nome do local em vez de latitude e longitude em uma API de previsão do tempo.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 7c9edbf4b2d231453cd336521a04ba6b7714b696
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "61062200"
---
# <a name="add-capabilities-to-a-backend-service"></a>Adicionar recursos a um serviço de back-end

Este artigo mostra uma política de gerenciamento de API do Azure que demonstra como adicionar recursos a um serviço de back-end. Por exemplo, aceitar um nome do local em vez de latitude e longitude em uma API de previsão do tempo. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

