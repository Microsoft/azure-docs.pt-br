---
title: Política de gerenciamento de API de exemplo – gerar assinatura de acesso compartilhado
titleSuffix: Azure API Management
description: 'Exemplo de política de gerenciamento de API do Azure: demonstra como gerar uma Assinatura de Acesso Compartilhado usando expressões e como encaminhar a solicitação para o armazenamento do Azure com a política rewrite-uri.'
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
ms.openlocfilehash: f90c1e492d6da4f85fe86e4c5c76b89bdfaa4797
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078418"
---
# <a name="generate-shared-access-signature"></a>Gerar a Assinatura de Acesso Compartilhado

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como gerar uma [Assinatura de Acesso Compartilhado](../../storage/common/storage-sas-overview.md) usando expressões e encaminhar a solicitação para o armazenamento do Azure com a política rewrite-uri. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-reference.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Generate Shared Access Signature and forward request to Azure storage.policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de transformação](../api-management-transformation-policies.md)
+ [Exemplos de política](../policy-reference.md)