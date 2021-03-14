---
title: Planos de serviço e cotas para o Azure Spring Cloud
description: Saiba mais sobre cotas de serviço e planos de serviço para o Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: b02ccb3acb4546e08e7d58159ab9d85bca2d0eed
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/03/2021
ms.locfileid: "101711868"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Cotas e planos de serviço para o Azure Spring Cloud

**Este artigo aplica-se a:** ✔️ Java ✔️ C#

Todos os serviços do Azure definem limites e cotas padrão para recursos e recursos.   O Azure Spring Cloud oferece dois tipos de preço: básico e Standard. Vamos detalhar os limites de ambas as camadas neste artigo.

## <a name="azure-spring-cloud-service-tiers-and-limits"></a>Limites e camadas de serviço do Azure Spring Cloud

| Recurso | Escopo | Basic | Standard
------- | ------- | -------
vCPU | instância por aplicativo | 1 | 4
Memória | instância por aplicativo | 2 GB | 8 GB
Instâncias do serviço de nuvem do Azure Spring | por região por assinatura | 10 | 10
Total de instâncias do aplicativo | por instância do serviço de nuvem do Azure Spring | 25 | 500
Domínios personalizados | por instância do serviço de nuvem do Azure Spring | 0 | 25 
Volumes persistentes | por instância do serviço de nuvem do Azure Spring | aplicativos de 1 GB/aplicativo x 10 | 50 GB/aplicativo x 10 aplicativos

> [!TIP]
> Os limites listados para total de instâncias de aplicativo por instância de serviço se aplicam a aplicativos e implantações em qualquer Estado, incluindo o estado parado. Exclua aplicativos ou implantações que não estão em uso.

## <a name="next-steps"></a>Próximas etapas

Alguns limites padrão podem ser aumentados. Se a sua instalação exigir um aumento, [crie uma solicitação de suporte](../azure-portal/supportability/how-to-create-azure-support-request.md).
