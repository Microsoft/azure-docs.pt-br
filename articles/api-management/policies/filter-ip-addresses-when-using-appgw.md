---
title: Política de gerenciamento de API de exemplo – filtro no endereço IP ao usar o gateway de aplicativo
titleSuffix: Azure API Management
description: Exemplo de política de gerenciamento de API do Azure – demonstra como filtrar por endereço IP de solicitação ao usar um gateway de aplicativo.
services: api-management
documentationcenter: ''
author: jftl6y
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: joscot
ms.custom: fasttrack-new
ms.openlocfilehash: 8249cc543c6334841c8e5152d5d1ceb84d4097dc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2021
ms.locfileid: "92076106"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>Filtrar por endereço IP de solicitação ao usar um gateway de aplicativo

Este artigo mostra um exemplo de política de gerenciamento de API do Azure que demonstra como filtrar o endereço IP da solicitação quando a instância de gerenciamento de API é acessada por meio de um gateway de aplicativo ou outro intermediário. Para definir ou editar um código de política, siga as etapas descritas em [Definir ou editar uma política](../set-edit-policies.md). Para ver outros exemplos, consulte [exemplos de política](../policy-reference.md).

## <a name="policy"></a>Política

Cole o código no bloco de **entrada**.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>Próximas etapas

Saiba mais sobre as políticas de APIM:

+ [Políticas de restrição de acesso](../api-management-access-restriction-policies.md)
+ [Exemplos de política](../policy-reference.md)