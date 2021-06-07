---
title: Política de gerenciamento de API de exemplo – enviar erros para Stackify para registro em log
titleSuffix: Azure API Management
description: Exemplo de política de gerenciamento de API do Azure - Demonstra como adicionar uma política de registro em log de erros para enviar erros para Stackify para registro em log.
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
ms.openlocfilehash: 89428e9a64c6d4ae78d333c0cf597531588b1638
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92072060"
---
# <a name="send-errors-to-stackify-for-logging"></a>Enviar erros para o Stackify para registro em log

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como adicionar uma política de registro em log de erros para enviar erros para o Stackify para registro em log. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-reference.md).

## <a name="policy"></a>Política

Cole o código no bloco **on-error**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-reference.md)