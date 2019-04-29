---
title: Exemplo de política de gerenciamento de API do Azure - Adicionar um cabeçalho Forwarded | Microsoft Docs
description: Exemplo de política de gerenciamento de API do Azure - Demonstra como adicionar um cabeçalho Forwarded na solicitação de entrada para permitir que a API de back-end crie URLs adequadas.
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
ms.openlocfilehash: b857d1780e9734ce891ce2c0ce4bedf50dfe13e9
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859487"
---
# <a name="add-a-forwarded-header"></a>Adicionar um cabeçalho Forwarded

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como adicionar um cabeçalho Forwarded na solicitação de entrada para permitir que a API de back-end crie URLs adequadas. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="code"></a>Código

Cole o código no bloco de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)
