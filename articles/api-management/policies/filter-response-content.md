---
title: Exemplo de política de gerenciamento de API do Azure - Filtrar conteúdo da resposta | Microsoft Docs
description: Exemplo de política de gerenciamento de API do Azure - Demonstra como filtrar elementos de dados do conteúdo da resposta com base no produto associado à solicitação.
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
ms.openlocfilehash: 26829dfc04acdac2a25fe0d4fdc3e95e4d219057
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60637056"
---
# <a name="filter-response-content"></a>Filtrar o conteúdo da resposta

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como filtrar elementos de dados do conteúdo da resposta com base no produto associado à solicitação. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco **outbound**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter response content based on product name.policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

