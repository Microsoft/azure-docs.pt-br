---
title: Exemplo de política de gerenciamento de API do Azure - Enviar erros para Stackify para registro em log | Microsoft Docs
description: Exemplo de política de gerenciamento de API do Azure - Demonstra como adicionar uma política de registro em log de erros para enviar erros para Stackify para registro em log.
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
ms.openlocfilehash: 07cc83830fe2d467c611622bb66dfbb8c9429c2d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60860531"
---
# <a name="send-errors-to-stackify-for-logging"></a>Enviar erros para o Stackify para registro em log

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como adicionar uma política de registro em log de erros para enviar erros para o Stackify para registro em log. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-samples.md).

## <a name="policy"></a>Política

Cole o código no bloco **on-error**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-samples.md)

